# 氧化还原操作系统编程

> 原文：<https://dev.to/legolord208/programming-for-redox-os-4124>

如果你不知道什么是 Redox OS，那就是用 Rust 写的整个操作系统。这是因为 Rust 在内存和线程安全方面有一些惊人的保证。更安全的是，它是一个微内核，而不是一个整体内核。这意味着，例如驱动程序和类似程序在用户空间中运行，不会以同样的方式使整个计算机崩溃。

为了在进程和内核之间进行通信，Redox OS 有定制的方案。它遵循了让[所有东西都是 URL](https://doc.redox-os.org/book/design/url_scheme_resource/everything_is_a_url.html) 的传统。方案非常简单，超级棒。你打开一个路径为`scheme:path`的文件。如果没有指定方案，它默认为当前工作目录中的一个——通常是`file:`。

## TCP

该方案的一个例子是 TCP 协议。尽管网络被认为是困难的，但在氧化还原中却非常简单。你刚刚`open("tcp:address", O_RDWR)`。
这里有一个例子，example.com 没有任何库(不是 http 协议的惯用用法——更喜欢用库来处理 http 请求):

```
let mut file = File::open("tcp:93.184.216.34:80")?; // example.com
file.write(b"GET / HTTP/1.1\nHost: example.com\nConnection: close\n\n")?;

io::copy(&mut file, &mut io::stdout())?; 
```

Enter fullscreen mode Exit fullscreen mode

## 程序 IPC

另一个例子是我自己写的 ipcd 守护进程。这是一个简单的进程间通信方案。您调用`open("chan:name", O_CREAT)`来创建服务器，调用`dup("listen")`来接受连接。`dup` syscall 用于复制文件，但是可以选择从方案中使用的路径。
这是一个使用 ipcd 的简单 hello world 服务器——这次我们需要用于 redox 的 syscall 库来复制文件。

```
let server = File::create("chan:hello")?;

loop {
    let stream = syscall::dup(server.as_raw_fd(), b"listen")?;
    let mut stream = unsafe { File::from_raw_fd(stream) };

    stream.write(b"Hello World!\n")?;
} 
```

Enter fullscreen mode Exit fullscreen mode

要从该 ipc 通道中读取，您只需`cat chan:hello`。

## 事件

这个方案有些特殊，不是因为它有任何特殊的硬编码例外，而是因为它到处都在使用，甚至在其他方案中也是如此。
事件方案有点像 linux 中的 epoll。你`write`一个事件到一个实例，它注册监听来自文件(或者一个方案的实例)的事件，然后你`read`它阻塞直到第一个事件。
在[事件检修 RFC](https://gitlab.redox-os.org/redox-os/rfcs/blob/master/text/0002-event-overhaul.md#detailed-design) 中有一个很好的例子。

该方案与`time:`方案配合非常好，可用于设置读取超时。时间方案很简单，可以读取当前时间并写一个截止日期，当该时间过去时触发一个事件。
这里有一个将读取超时设置为 2 秒的例子。

```
const TOKEN_TIME: usize = 0;
const TOKEN_FILE: usize = 1;

let mut selector = File::open("event:")?;
let mut some_file = /* anything you want to read from here, like a tcp stream */;

// Deadline
let mut timeout = File::open("time:")?;
let mut time = TimeSpec::default();
timeout.read(&mut time)?;
time.tv_sec += 2; // deadline to 2 seconds in the future
timeout.write(&time)?;

selector.write(Event {
    id: timeout.as_raw_fd(),
    flags: EVENT_READ,
    data: TOKEN_TIME
})?;
selector.write(Event {
    id: some_file,
    flags: EVENT_READ,
    data: TOKEN_FILE
})?;

let mut event = Event::default();
selector.read(&mut event)?;

match event.data {
    TOKEN_TIME => {
        // The timeout of 2 seconds was exceeded
    },
    TOKEN_FILE => {
        // The file can now be read!
        // How much can be read is undefined and depends on the scheme.
        // Most built-in schemes are nowadays edge triggered and you should
        // have the file be non-blocking and read over and over again
        // until you hit EAGAIN.
        // This matches the behavior of linux.
        let mut buf = [0; 16];
        loop {
            match some_file.read(&mut buf)? {
                Ok(0) => break, // EOF, we read everything!
                Ok(n) => {
                    // Handle the read here.
                    io::stdout().write(&buf[..n])?;
                },
                Err(ref e) if e.kind() == ErrorKind::WouldBlock => break, // can't read any more this round.
                Err(e) => return Err(e)
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 它是如何工作的？

*注意:编写自定义方案需要 root*

## 主循环

这个方案是使用根方案注册的——它没有名字。注册方案并返回一个你可以从中读取数据包的文件。
你们是如何处理这些包裹的？这就是特征家族的由来！他们有一个已经实现的名为`handle`的函数，它读取数据包并调用正确的 trait 函数。
一个简单的循环可能是这样的:

```
let mut handler = Handler; // handler implements SchemeMut
let mut scheme = File::create(":hello")?;
loop {
    let mut packet = Packet::default();
    scheme.read(&mut packet)?;

    handler.handle(&mut packet);
    scheme.write(&packet)?;
} 
```

Enter fullscreen mode Exit fullscreen mode

遗憾的是，大多数模式的循环看起来更加困难，因为它们可能需要同时读取多个模式，或者重新调度阻塞函数。

## 唯一 id

该方案具有如下功能:

```
fn open(&mut self, path: &[u8], flags: usize, _uid: u32, _gid: u32) -> Result<usize>; 
```

Enter fullscreen mode Exit fullscreen mode

在这里你可以返回任何你想要的整数！
氧化还原方案很酷的一点是方案的每个实例(文件描述符)都被映射到一个 ID，多个文件描述符可以有相同的 ID(所以你不必自己实现没有路径的`dup`)。
这里几乎所有的方案都是保存一个指向下一个 id 的整数，然后在`open`上，它将 id 和一些包含句柄数据的结构一起添加到一个内部映射中，最后递增整数。
后面的函数用 id:
调用

```
fn read(&mut self, id: usize, buf: &mut [u8]) -> Result<usize> 
```

Enter fullscreen mode Exit fullscreen mode

## 闭锁

像 read 这样的阻塞函数在失败时通常会被 scheme 重新调度。通常，它们在内部使用 EWOULDBLOCK 错误将数据包添加到 todos 列表中，然后(在另一个方案事件或某个计时器上)再次尝试处理该数据包。为了缓解这个问题，我们现在有了一个 SchemeBlock 特征，它可以返回一个 None 来表示它应该被重新调度。关于一个真正简单的阻塞循环的例子，请查看 ipcd 的主文件。

## 事件

为了让你的方案支持事件，你只需要用 a=SYS_FEVENT，b=id，c=flag，d=count 的值写一个包到方案文件中(我认为*不再使用* count 了，你可以把它留为 1)。还有一个`fevent` scheme 函数，当有人将您的方案注册到事件实例时，它会让您知道。这可用于重置变量，以跟踪发送边缘触发事件的方案中的通知状态。