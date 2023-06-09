# 阿辛克；时序数据的异步接收器

> 原文：<https://dev.to/termoshtt/asink-async-sink-for-time-series-data--2nl>

我想分享我的小图书馆，用来存储数值模拟产生的时间序列数据。

[https://github.com/termoshtt/asink](https://github.com/termoshtt/asink)

asink 发表于 [crates.io](https://crates.io/crates/asink) :

```
[dependencies]
asink = "0.1.1" 
```

Enter fullscreen mode Exit fullscreen mode

并且整个文档放在 [docs.rs](https://docs.rs/asink) 上

## Sink 和 mpsc::channel

这个库定义了`Sink`，像大多数日志库一样，它将数据存储到存储器中。

```
let sink = msgpack::MsgpackSink::from_str("test.msg");
let (s, th) = sink.run();
// execute simulation...
th.join().unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

*   由`sink.run()`生成的`s`是属于`std::sync::mpsc`的一个`Sender<T>`。顾名思义，“多生产者，单消费者”，`mpsc::channel`创造了一对可复制的`Sender<T>`和不可复制的`Receiver<T>`。接收器启动一个线程，通过这个通道将从主线程接收的数据写入存储。
*   第二个值`th`是加入这个线程的线程处理程序。当所有复制的`Sender`都被丢弃时，通道被关闭，一个终结接收线程。

在这种情况下，接收器是 msgpack 文件。从主线程接收的数据被编码到 msgpack 中并写入该文件。多亏了 serde 库，我们可以将任何实现`Serialize`的结构序列化到 JSON/msgpack/BSON 中。或者，您可以使用
为您的结构自动实现`Serialize` trait

```
extern crate serde;
#[macro_use]
extern crate serde_derive;

#[derive(Serialize)]
struct Doc {
    time: f64,
    data: Vec<f64>,
} 
```

Enter fullscreen mode Exit fullscreen mode

## 工作示例

```
extern crate asink;
extern crate serde;
#[macro_use]
extern crate serde_derive;

use asink::*;
use std::sync::mpsc::Sender;

/// This will be serialized into msgpack
#[derive(Serialize)]
struct Doc {
    id: usize,
    data: Vec<f64>,
}

fn experiment(s: Sender<Doc>) {
    for i in 0..5 {
        let doc = Doc {
            id: i,
            data: vec![i as f64],
        };
        s.send(doc).unwrap(); // Send data to sink
    }
}

fn main() {
    let sink = msgpack::MsgpackSink::from_str("test.msg");
    let (s, th) = sink.run(); // Sink start thread to write recieved data into msgpack
    experiment(s);
    th.join().unwrap();
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过替换一行来切换 sink:

```
let sink = json::JsonSink::from_str("test.json"); // output to JSON file
let sink = mongo::MongoSink:::local("mydb", "collection1"); // output to MongoDB on localhost 
```

Enter fullscreen mode Exit fullscreen mode

该库对于存储大型二进制数据(如速度/磁场)和测井数据(如 BiCGStab 等迭代方法的残差)都很有用。

我想得到你的反馈。谢了。