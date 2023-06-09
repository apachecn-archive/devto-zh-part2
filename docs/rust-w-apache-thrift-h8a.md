# Rust w/ Apache 节俭

> 原文：<https://dev.to/jeikabu/rust-w-apache-thrift-h8a>

使用 [Apache Thrift](https://thrift.apache.org/) 使我们能够为针对[各种语言](https://thrift.apache.org/docs/Languages)的 SDK (仍然非常-WIP)生成客户端库[。我将为](https://github.com/subor/sdk) [Rust](https://www.rust-lang.org/en-US/) 创建一个测试库，它对我们的[后台服务](//./windows-service-in-c-1m79)进行简单的 RPC 调用。

## 代

我们引入 [API 工具](https://github.com/subor/sdk/blob/master/docs/topics/build_sdk_source.md#thrift)的原因之一是让它更容易与我们的[一起工作。节俭接口定义文件](https://github.com/subor/sdk/tree/master/ThriftFiles) :

[![](img/730a745f9d5b3df73c25c4fcbbd16c8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hSolpWEs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/devtool_apitool_rs.png)

点击**生成**处理所有节俭文件:

```
18/08/22 12:54:53 Info ApiTool --ThriftFiles="D:\ruyi\sdk\ThriftFiles" --ThriftExe="D:\ruyi\..\tools\thrift\thrift.exe" --CommonOutput="D:\ruyi\sdk\SDK.Gen.CommonAsync" --ServiceOutput="D:\ruyi\sdk\SDK.Gen.ServiceAsync" --Gen="rs" --Generate
18/08/22 12:54:53 Info -gen rs -out D:\ruyi\sdk\SDK.Gen.ServiceAsync D:\ruyi\sdk\ThriftFiles\BrainCloudService\BrainCloudServiceSDKDataTypes.thrift
18/08/22 12:54:53 Info -gen rs -out D:\ruyi\sdk\SDK.Gen.ServiceAsync D:\ruyi\sdk\ThriftFiles\BrainCloudService\BrainCloudServiceSDKServices.thrift
18/08/22 12:54:54 Info -gen rs -out D:\ruyi\sdk\SDK.Gen.CommonAsync D:\ruyi\sdk\ThriftFiles\CommonType\CommonTypeSDKDataTypes.thrift 
```

Enter fullscreen mode Exit fullscreen mode

注意显示对`thrift.exe`调用的`-gen rs ...`输出。

我们平台的细节对于这个练习并不重要。你可以用节俭教程来代替。

## 锈迹斑斑的婴儿步伐

作为第一步，我想建立一个包含生成的源文件的 rust 库。

1.  启动一个新的“subor”库:`cargo new --lib subor`

2.  启动 [Visual Studio 代码](https://code.visualstudio.com/)，安装 [Rust support](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust) 。打开货物创建的`subor/`文件夹。

3.  复制所有生成的。rs 文件放入`src/`目录。

就在`lib.rs`旁边，`localization_service_s_d_k_data_types.rs`吸引了我的目光，似乎是一个不错的起点。它包含:

```
impl LanguageChangedMsg {
  pub fn new<F1, F2>(new_language: F1, old_language: F2) -> LanguageChangedMsg where F1: Into<Option<String>>, F2: Into<Option<String>> {
    LanguageChangedMsg {
      new_language: new_language.into(),
      old_language: old_language.into(),
    }
  }
  //... 
```

Enter fullscreen mode Exit fullscreen mode

它是由[localizationservicesdkdatatypes . thrift](https://github.com/subor/sdk/blob/master/ThriftFiles/LocalizationService/LocalizationServiceSDKDataTypes.thrift):
生成的

```
struct LanguageChangedMsg {
    1: string newLanguage,
    2: string oldLanguage,
} 
```

Enter fullscreen mode Exit fullscreen mode

要将该文件纳入范围，请在`lib.rs`的顶部添加:

```
mod localization_service_s_d_k_data_types; 
```

Enter fullscreen mode Exit fullscreen mode

用`^'`调出 VS 代码终端(也就是 Ctrl+back tick——或者“重音符”，如果你喜欢的话)。

用`cargo build --tests` :
构建测试

```
error[E0463]: can't find crate for `ordered_float`
 --> src/localization_service_s_d_k_data_types.rs:9:1
  |
9 | extern crate ordered_float;
  | ^^^^^^^^^^^^^^^^^^^^^^^^^^^ can't find crate 
```

Enter fullscreen mode Exit fullscreen mode

检查 [crates.io](https://crates.io/) 的外部依赖性。要修复这个错误和接下来的几个错误，请在`Cargo.ml`中添加:

```
[dependencies]
ordered-float = "0.5.0"
thrift = "0.0.4"
try_from = "0.2.2" 
```

Enter fullscreen mode Exit fullscreen mode

构建:

```
error[E0432]: unresolved import `ordered_float`
  --> src/localization_service_s_d_k_data_types.rs:13:5
   |
13 | use ordered_float::OrderedFloat;
   | ^^^^^^^^^^^^^ Did you mean `self::ordered_float`? 
```

Enter fullscreen mode Exit fullscreen mode

板条箱在子模块中`extern`d(即`localization_service_s_d_k_data_types.rs`)，所以你要么照它说的做，在任何地方都加上`self::`(*到*)。或者，在 lib.rs 的顶部添加:

```
extern crate ordered_float;
extern crate thrift;
extern crate try_from; 
```

Enter fullscreen mode Exit fullscreen mode

现在，尝试在测试中使用`LanguageChangedMsg`类型:

```
#[cfg(test)]
mod tests {
    // Bring entire contents of module into scope
    use super::localization_service_s_d_k_data_types::*;
    #[test]
    fn it_works() {
      let msg = LanguageChangedMsg::new("stuff".to_owned(), "this".to_owned()); 
```

Enter fullscreen mode Exit fullscreen mode

使用 [glob 操作符](https://doc.rust-lang.org/book/2018-edition/ch07-03-importing-names-with-use.html)(也可参见[测试示例](https://doc.rust-lang.org/book/2018-edition/ch11-01-writing-tests.html#checking-results-with-the-assert-macro))将该文件中的所有内容纳入范围。

在`it_works()`里面函数类型`let msg = L`(注:大写**“L”**)和“intellisense”应该建议剩下的。

最后，`cargo test`运行测试，它应该会通过。

## 客户端

相信我们可以构建东西，让我们做一个成熟的客户端，这样我们就可以做 RPC 了。

`LocalizationServiceSDKServices.thrift` :
中的规格

```
service LocalizationService {
  // ...
  string GetCurrentLanguage(),
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

生成`localization_service_s_d_k_services.rs` :

```
pub trait TLocalizationServiceSyncClient {
  // ...

  fn get_current_language(&mut self) -> thrift::Result<String>;

  // ...
}

impl <IP, OP> LocalizationServiceSyncClient<IP, OP> where IP: TInputProtocol, OP: TOutputProtocol {
  pub fn new(input_protocol: IP, output_protocol: OP) -> LocalizationServiceSyncClient<IP, OP> {
    LocalizationServiceSyncClient { _i_prot: input_protocol, _o_prot: output_protocol, _sequence_number: 0 }
  }
}

impl <C: TThriftClient + TLocalizationServiceSyncClientMarker> TLocalizationServiceSyncClient for C {
  // ...
  fn get_current_language(&mut self) -> thrift::Result<String> {
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

如果你熟悉节俭和生锈，这将是有意义的:

*   `TLocalizationServiceSyncClient`定义访问“服务”的接口
*   它指定了几个 RPC 调用，包括`get_current_language()`
*   给定一个输入和输出协议，可以用`LocalizationServiceSyncClient::new()`创建一个客户机实例

[节俭::礼宾模块文档](https://docs.rs/thrift/0.0.4/thrift/protocol/index.html)展示入门:

```
use thrift::protocol::{TBinaryInputProtocol, TBinaryOutputProtocol, TMultiplexedOutputProtocol};
use thrift::transport::{TTcpChannel, TIoChannel};

use super::localization_service_s_d_k_services::*;

#[test]
fn client() {
    // Create TCP transport "channel" to local server
    let mut channel = TTcpChannel::new();
    channel.open("127.0.0.1:11290").unwrap();

    // Decompose TCP channel into read/write-halves for in/out protocols
    let (readable, writable) = channel.split().unwrap();

    // These take ownership of their first argument, so using TCP channel 
    // directly would require multiple TCP connections
    let in_proto = TBinaryInputProtocol::new(readable, true);
    let out_proto = TBinaryOutputProtocol::new(writable, true);

    // Multiple clients can be multiplexed over a single transport.
    // The server side of our application is expecting "SER_xxx" to 
    // route to the correct service.
    let out_proto = TMultiplexedOutputProtocol::new("SER_L10NSERVICE", out_proto);

    let mut client = LocalizationServiceSyncClient::new(in_proto, out_proto);

    // RPC to server
    client.get_current_language().unwrap();
} 
```

Enter fullscreen mode Exit fullscreen mode

像`TTcpChannel`这样的双向通道实现 [TIoChannel::split()](https://docs.rs/thrift/0.0.4/thrift/transport/trait.TIoChannel.html) 来创建“可读”和“可写”的两半。然后，每个二进制协议可以获得自己那一半的所有权。

用 [`TMultiplexedOutputProtocol`](https://docs.rs/thrift/0.0.4/thrift/protocol/struct.TMultiplexedOutputProtocol.html) 包装输出协议，这样我们可以有多个`T*SyncClient`共享一个 TCP 连接(或其他传输)。第一个参数`service_name`，是应用程序为服务定义的名称——这里是`"SER_L10NSERVICE"`。尽管这不是一个节俭的需求，但是我们的应用程序的服务器端却在期待它。

要进行 RPC，请通过客户端方法发出请求。如果您检查生成的 C#和 rust 源代码，请注意:

*   锈蚀方法使用蛇纹:`get_current_language()`
*   `async` C#方法追加`Async`后缀:`GetCurrentLanguageAsync()`
*   序列化消息通过 thrift 规范中的名称指定方法:`GetCurrentLanguage`
*   复用增加服务名:`SER_L10NSERVICE:GetCurrentLanguage`

## 服务器

对于服务器端，我正在用我们最新发布的[第 0 层](//./windows-service-in-c-1m79)的[进行测试。](https://github.com/subor/sdk/releases)

这里有一个兼容的 C#服务器，使用相同的节俭文件:

```
class Program
{
    static async Task Main(string[] args)
    {
        var server = new Thrift.Transports.Server.TServerSocketTransport(11290);
        server.Listen();
        // Create service processor and register with multiplexor
        var mux = new TMultiplexedProcessor();
        var processor = new LocalizationService.AsyncProcessor(new SettingHandler());
        mux.RegisterProcessor("SER_L10NSERVICE", processor);
        while (true){
            // Accept client connection, wrap with protocol, and hand to multiplexor
            var client = await server.AcceptAsync();
            await Task.Run(async () =>
            {
                var protocol = new Thrift.Protocols.TBinaryProtocol(client);
                Console.WriteLine(await mux.ProcessAsync(protocol, protocol));
            });
        }
    }
}

class SettingHandler : LocalizationService.IAsync
{
    //...

    public Task<string> GetCurrentLanguageAsync(CancellationToken cancellationToken)
    {
        return Task.FromResult("en-US");
    }

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

当客户端连接并发送请求时`SER_L10NSERVICE:GetCurrentLanguage`:

1.  `TMultiplexedProcessor.ProcessAsync()`提取服务名(`SER_L10NSERVICE`)并匹配到添加了`RegisterProcessor()`的`ITAsyncProcessor`
2.  匹配处理器的`ProcessAsync()`被调用。它提取方法名(`GetCurrentLanguage`)并匹配相应的方法。
3.  参数(如果有)被反序列化，传递给处理器的`IAsync`处理程序实例的方法被调用(`SettingHandler.GetCurrentLanguageAsync()`

## 减速带

不幸的是，看起来 VS 代码中的 Rust“智能感知”还没有完全实现。键入`client.`后，我期望`get_current_language()`等人被建议，但只出现了`TThriftClient`水暖:

[![](img/e121755e9544e597e3231c31f45d6f47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jDN5hUNa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/vscode_rust_intellisense_fail.png)

如果客户端 RPC 调用失败:

```
ApplicationError { kind: WrongMethodName, message: "expected GetCurrentLanguage got SER_L10NSERVICE:GetCurrentLanguage" }', libcore/result.rs:945:5 
```

Enter fullscreen mode Exit fullscreen mode

检查用`TMultiplexedOutputProtocol::new()`(客户端)和`RegisterProcessor()`(服务器)注册的名称是否匹配。

总的来说，我对 Rust 的体验就像我对 F#的体验一样；如果它编译了，它就工作了。