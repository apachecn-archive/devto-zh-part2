# 带 Rust 服务的 ASP.NET 核心通用主机

> 原文：<https://dev.to/jeikabu/aspnet-core-generic-host-with-rust-services-4eab>

这已经过去了很长时间，以前我们:

*   [节俭的客户在生锈](//./rust-w-apache-thrift-h8a)
*   [将 FFI 绑定到 NNG](//./rust-ffi-to-nng-5ehk)
*   [ASP.NET 核心的通用主机与 NNG](//./microservice-based-application-with-aspnet-core-generic-host-cel)

现在我们将加载一个 Rust 二进制文件作为我们。NET 核心应用程序，让 C#和 Rust 与 NNG 和 Thrift 进行交流。

## C#互操作

偶然发现了这个高度相关的博客。启动一个新的铁锈库:

```
cargo new --lib --name rust_input 
```

Enter fullscreen mode Exit fullscreen mode

默认生成静态库。为了生成一个动态/共享库，在`Cargo.toml`中添加:

```
[lib]
crate-type = ["dylib"] 
```

Enter fullscreen mode Exit fullscreen mode

这将在 OSX 上产生一个`.dylib`(可能在 Linux 上产生一个`.so`，在 Windows 上产生一个`.dll`)。另见[货物单据](https://doc.rust-lang.org/cargo/reference/manifest.html#building-dynamic-or-static-libraries)。

在`lib.rs` :

```
#[no_mangle]
pub extern fn start() -> i32 {
    println!("Start!");
    0
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`cargo build`。

为了立即得到满足，我将生成的`target/debug/librust_input.dylib`复制到我的。net 输出文件夹，但我需要研究一下[加载它作为一个本地程序集](//./loading-native-libraries-in-c-fh6)。

在 C#中，我们将创建[一个后台服务](//./microservice-based-application-with-aspnet-core-generic-host-cel) :

```
public class InputXy : BackgroundService
{
    [DllImport("rust_input")]
    static extern int start();

    protected override Task ExecuteAsync(CancellationToken token)
    {
        return Task.Run(async () => {
            // Arbitrary sleep to give the broker time to start
            await Task.Delay(500);
            // Call `start()` in "rust_input" shared library
            start();
            while (!token.IsCancellationRequested)
            {
                await Task.Delay(TimeSpan.FromMilliseconds(200));
            }
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意没有带`DllImport`的文件扩展名(或`lib`前缀)。这允许在任何平台(即 OSX、Linux、Windows)上找到正确的库。

这是可行的，但是还没有做任何有趣的事情。

## 配置

我们的。Net 应用程序[使用来自`appsettings.json`](//./microservice-based-application-with-aspnet-core-generic-host-cel#configuration) 的配置，我们希望在 Rust 中使用相同的设置。

给定以下`appsettings.json` :

```
{  "zxy":{  "http":{  "port":8283  },  "nng":{  "brokerIn":  "tcp://localhost:10110",  "brokerOut":  "tcp://localhost:10111"  }  },  } 
```

Enter fullscreen mode Exit fullscreen mode

为了反序列化它，我们可以使用 [Serde](https://serde.rs/) ，特别是 [serde_json](https://github.com/serde-rs/json) 。

在`Cargo.toml` :

```
[dependencies]
serde = "1.0.79"
serde_json = "1.0.31"
serde_derive = "1.0.79" 
```

Enter fullscreen mode Exit fullscreen mode

在`lib.rs` :

```
extern crate serde;
extern crate serde_json;
// Import macros from serde_derive. Must appear before first use.
#[macro_use]
extern crate serde_derive;

use std::fs::File;

#[derive(Deserialize,Debug)]
struct AppSettings {
    zxy: ZxySettings
}

#[derive(Deserialize,Debug)]
struct ZxySettings {
    http: HttpSettings,
    nng: NngSettings,
}

#[derive(Deserialize,Debug)]
struct HttpSettings {
    port: u16
}

#[derive(Deserialize,Debug)]
struct NngSettings {
    brokerIn: String,
    brokerOut: String,
}

fn load_settings() -> AppSettings {
    let file = File::open("appsettings.json").unwrap();
    // Deserialize AppSettings from file
    let settings: AppSettings = serde_json::from_reader(file).unwrap();
    println!("{:?}", settings);
    settings
} 
```

Enter fullscreen mode Exit fullscreen mode

`#[macro_use]`位置的重要性来自于[这个所以](https://stackoverflow.com/questions/29068716/how-do-you-use-a-macro-from-inside-its-own-crate)。

这产生了包含来自`appsettings.json`的值的`AppSettings`结构。现在我们可以很容易地在 C#和 Rust 中使用相同的配置。

## NNG

使用我们在之前的[运行箱](https://crates.io/crates/runng) [创建一个](//./rust-ffi-to-nng-5ehk)[【推送】](https://nanomsg.github.io/nng/man/v1.0.0/nng_push.7.html)节点，连接到我们的 C#代理:

```
extern crate runng;
extern crate futures;
use runng::{Factory, Dial};
use runng::protocol::{AsyncPublish, AsyncSocket};
use runng::msg::NngMsg;
use futures::future::Future;

#[no_mangle]
pub extern fn start() -> i32 {
    println!("Start!");

    let setting = load_settings();

    let factory = runng::Latest::new();
    let pusher = factory.pusher_open().unwrap();
    println!("Connecting....");
    pusher.dial(&setting.zxy.nng.brokerIn).unwrap();
    println!("Connected!");
    let mut pusher = pusher.create_async_context().unwrap();
    let mut msg = NngMsg::new().unwrap();
    msg.append_u32(0).unwrap(); // For topic appends 4 bytes: 0 0 0 0
    println!("Sending...");
    pusher.send(msg).wait().unwrap().unwrap();
    println!("Sent!");

    0
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以从 C#订阅这个:

```
// Load configuration and NNG native dll
var config = new ConfigurationBuilder()
    .AddJsonFile("appsettings.json")
    .Build();
var brokerOut = config.GetSection("zxy:nng").GetValue<string>("brokerOut");
var factory = LoadNngFactory();

// Create subscriber that connects to output/publishing end of broker
using (var subscriber = factory.SubscriberCreate(brokerOut).Unwrap().CreateAsyncContext(factory).Unwrap())
{
    // Use 0x00000000 (four bytes) for our topic
    var topic = new byte[]{0, 0, 0, 0};
    subscriber.Subscribe(topic);
    Console.WriteLine("Receiving...");
    var msg = await subscriber.Receive(cts.Token);
    Console.WriteLine("Received!");
} 
```

Enter fullscreen mode Exit fullscreen mode

## 景区路线

现在我们到了延迟这篇文章的部分。

类似于我们如何构建我们的 SDK，我们希望在一个中央库中有我们从各种服务中引用的所有节俭接口。

`cargo new --lib --name zxy`和`Cargo.toml` :

```
[package]
name = "zxy"
version = "0.1.0"

[dependencies]
thrift = "0.0.4"
try_from = "0.2"
ordered-float = "1.0" 
```

Enter fullscreen mode Exit fullscreen mode

`lib.rs` :

```
extern crate ordered_float;
extern crate try_from;
extern crate thrift; 
```

Enter fullscreen mode Exit fullscreen mode

回到`rust_input/Cargo.toml` :

```
[dependencies]
runng = { version = "0.1.1", path = "../../../../rust/runng/runng" }
zxy = { path = "../../zxy" } 
```

Enter fullscreen mode Exit fullscreen mode

运行和… **失败** :

```
Exception has occurred: CLR/System.DllNotFoundException
Exception thrown: 'System.DllNotFoundException' in input.dll: 'Unable to load shared library 'rust_input' or one of its dependencies. In order to help diagnose loading problems, consider setting the DYLD_PRINT_LIBRARIES environment variable: dlopen(librust_input, 1): image not found' 
```

Enter fullscreen mode Exit fullscreen mode

启用`DYLD_PRINT_LIBRARIES`时:

```
dyld: loaded: /XXX/zxy/output/Debug/plugins/netstandard2.0/librust_input.dylib
dyld: unloaded: /XXX/zxy/output/Debug/plugins/netstandard2.0/librust_input.dylib 
```

Enter fullscreen mode Exit fullscreen mode

不是特别有帮助。

在 OSX 上使用`otool`来检查依赖关系(在 Windows 上我们通常使用[dependency Walker](http://dependencywalker.com/)):

```
$ otool -L target/debug/librust_input.dylib
target/debug/librust_input.dylib:
        /XXX/zxy/target/debug/deps/librust_input.dylib (compatibility version 0.0.0, current version 0.0.0)
        @rpath/libstd-ffe37452bb8eb44d.dylib (compatibility version 0.0.0, current version 0.0.0)
        /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.200.5)
        /usr/lib/libresolv.9.dylib (compatibility version 1.0.0, current version 1.0.0) 
```

Enter fullscreen mode Exit fullscreen mode

从`[dependencies]`和`cargo build` :
中移除`zxy`

```
$ otool -L target/debug/librust_input.dylib
target/debug/librust_input.dylib:
        /XXX/zxy/target/debug/deps/librust_input.dylib (compatibility version 0.0.0, current version 0.0.0)
        /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.200.5)
        /usr/lib/libresolv.9.dylib (compatibility version 1.0.0, current version 1.0.0) 
```

Enter fullscreen mode Exit fullscreen mode

啊，由于某些原因，当我们添加 zxy 机箱时，我们获得了对 libstd 共享库的依赖。

尝试了几件事:

*   将`#![no_std]`添加到`zxy/Cargo.toml`的顶部
*   将 zxy 板条箱更改为`crate-type = ["dylib"]`

但是依赖性依然存在。我们最终设置了`LD_LIBRARY_PATH`，但是我怀疑有更好的(也就是更“正确的”)方式来处理这个问题。我用的是 VS 代码，所以在`launch.json` :

```
"configurations":  [  {  "env":  {  "LD_LIBRARY_PATH":  "/XXX/.rustup/toolchains/stable-x86_64-apple-darwin/lib/rustlib/x86_64-apple-darwin/lib/"  }  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

## 节俭连接

创建`input.thrift` :

```
namespace * zxy.SDK.Input

service Input {
    bool Test(),
} 
```

Enter fullscreen mode Exit fullscreen mode

定义一个节俭器`service`可以用来创建[请求-响应](https://en.wikipedia.org/wiki/Request%E2%80%93response)T3】客户机-服务器 RPC。

生成。网芯和铁锈绑定:

```
thrift -gen netcore -out . thrift/input.thrift
thrift -gen rs -out src thrift/input.thrift 
```

Enter fullscreen mode Exit fullscreen mode

在 C#中:

```
public class InputXy : BackgroundService
{
    [DllImport("rust_input")]
    static extern int start();

    public InputXy(IConfiguration configuration, ILogger<InputXy> logger, IZxyContext context)
    {
        var processor = new zxy.SDK.Input.Input.AsyncProcessor(new Processor(logger));
        zxyContext.RegisterProcessor(InputPlugin.ServiceName, processor);
        //...
    }

    //...
}

class Processor : zxy.SDK.Input.Input.IAsync
{
    public Processor(ILogger logger)
    {
        this.logger = logger;
    }
    public Task<bool> TestAsync(CancellationToken cancellationToken)
    {
        logger.LogInformation("Test");
        return Task.FromResult(true);
    }
    ILogger logger;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 rust_input 机箱中，创建我们的节俭客户端[，类似于之前的](//./rust-w-apache-thrift-h8a) :

```
extern crate zxy;
extern crate thrift;

use thrift::protocol::{TBinaryInputProtocol, TBinaryOutputProtocol, TMultiplexedOutputProtocol};
use thrift::transport::{TTcpChannel, TIoChannel};
use zxy::input::TInputSyncClient;

fn do_thrift(settings: &AppSettings) {
    // Create TCP transport "channel" to local server
    let mut channel = TTcpChannel::new();
    println!("Connecting to TCP...");
    channel.open(&format!("127.0.0.1:{}", settings.zxy.api_bridge.TCPport)).unwrap();

    // Decompose TCP channel into read/write-halves for in/out protocols
    let (readable, writable) = channel.split().unwrap();
    let in_proto = TBinaryInputProtocol::new(readable, true);
    let out_proto = TBinaryOutputProtocol::new(writable, true);

    // Multiple clients can be multiplexed over a single transport
    let out_proto = TMultiplexedOutputProtocol::new("input", out_proto);

    // Initialize the client
    let mut client = zxy::input::InputSyncClient::new(in_proto, out_proto);

    // RPC to the "server"
    println!("RPC...");
    client.test().unwrap();
    println!("DONE!");
} 
```

Enter fullscreen mode Exit fullscreen mode

并且……C #服务器发出我们期望的甜美的日志输出:

```
info: zxy.zxy0.InputXy[0]
      Test 
```

Enter fullscreen mode Exit fullscreen mode

需要明确的是，这里没有使用 NNG；是节俭过 TCP。

## 待续…

我们的微服务架构的一个奇妙之处是，一旦我们启动了 Rust 服务，我们就可以像与 C#服务一样与之交互(例如，通过 Thrift RPC 或 NNG 发布/订阅)。不需要处理托管到非托管的互操作，这对于非普通类型来说[变得非常棘手。](//./windows-service-in-c-1m79#failure-actions)

我想到的第一件事实际上是用 Rust 实现替换 C#代理，这样[它就不会受到讨厌的垃圾收集器](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals#what-happens-during-a-garbage-collection)的影响。