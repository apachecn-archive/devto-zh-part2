# 如何在 Go 中实现一个信号处理器

> 原文：<https://dev.to/syossan27/how-to-implement-a-signal-handler-in-go--582c>

这篇文章解释了“如何在 Go 中实现一个信号处理器”。

还有，我介绍一下我做的一个小库。

## 简介

该工具被假定如下。

*   发布订阅中的订户
*   显示关机消息
*   等等...

如果知道如何实现信号处理程序，你就可以在 Go 中创建多样性的程序。

## 如何实现

实施要点如下。

*   捕捉信号
*   等待捕捉信号

### 捕捉信号

首先，让我们为捕捉信号写程序。

用“chan”表示捕捉信号。

```
package main

import (
    "os"
    "os/signal"
)

func main() {
    signalCh := make(chan os.Signal, 1)
    signal.Notify(signalCh, os.Interrupt)
    <-signalCh
} 
```

Enter fullscreen mode Exit fullscreen mode

这个程序太简单了。

创建 os。信号通道和接收操作系统。中断(=系统调用。通知使用的信号。
作为旁注，`<-signalCh`是等待 signalCh 接收中断信号。

### 在 goroutine 中等待捕捉到的信号

因为在收到信号之前您不能做任何事情，所以请改进这个程序，以便您可以用 goroutine 接收信号。

```
package main

import (
    "os"
    "os/signal"
)

func main() {
    doneCh := make(chan struct{})

    signalCh := make(chan os.Signal, 1)
    signal.Notify(signalCh, os.Interrupt)

    go receive(signalCh, doneCh)

    <-doneCh
}

func receive(signalCh chan os.Signal, doneCh chan struct{}) {
    for {
        select {
        // Example. Process to receive a message
        // case msg := <-receiveMessage():
        case <-signalCh:
            doneCh <- struct{}{}
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 goroutine 执行接收功能。

接收功能用于选择并继续移动，直到捕捉到信号。

当捕捉到一个信号时，它向 doneCh 发送一个空结构，告诉你你捕捉到了信号。

作为一个例子，在 PubSub 的情况下，我们编写了一个用 receive 函数捕捉消息的过程。

# 调查

我调查了是否有可以轻松实现信号处理的库。

我找到了这个图书馆。

[https://github.com/vrecan/death](https://github.com/vrecan/death)

然而，在这个库中，生成结构对象和创建固定的结构方法很麻烦。

# 创建

因此，我做了一个叫 Tebata 的库(手旗).

[https://github.com/syossan27/tebata](https://github.com/syossan27/tebata)

Tebata 可以轻松灵活地创建信号处理程序。

```
func main() {
    doneCh := make(chan struct{})

    t := tebata.New(os.Interrupt)
    t.Receive(func(ch chan struct{}){
        fmt.Println("Catch signal!")
        ch <- struct{}{}
    }, doneCh)

    <-doneCh
} 
```

Enter fullscreen mode Exit fullscreen mode

在新函数的参数中指定要捕捉的信号。

然后，指定要执行的函数和参数作为接收函数的参数。

仅此而已。

如果你想实现一个信号处理器，请试试这个库。