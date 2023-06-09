# syg:简单地用信号通知 Go 中的回调映射

> 原文：<https://dev.to/nasa9084/syg-simply-signal-to-callback-mapping-in-go-24kf>

当你想在 Go 中捕捉系统信号时，可以使用`os`包、`os/signal`包、`syscall`包。

下面是一个捕捉系统信号然后优雅地关闭 HTTP 服务器的例子:

```
func main() {
    sigCh := make(chan os.Signal, 1)
    signal.Notify(sigCh, syscall.SIGINT)

    s := &http.Server{
        // configure server properties
    }

    go func() {
        <-sigCh
        s.Shutdown(context.Background())
    }()

    if err := s.ListenAndServe(); err != http.ErrServerClosed {
        // error handling
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，这是一个非常简单的模式。
如果服务器在发送信号前由于某种错误而停机，监听信号的路由器将会泄漏。
所以，我们使用`context`和 for-select 循环。

```
func main() {
    sigCh := make(chan os.Signal, 1)
    signal.Notify(sigCh, syscall.SIGINT)

    s := &http.Server{
        // configure server properties
    }
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    go func() {
        for {
            select {
                case <-sigCh:
                    s.Shutdown(context.Background())
                case <-ctx.Done():
                    return
            }
        }
    }()

    if err := s.ListenAndServe(); err != http.ErrServerClosed {
        // error handling
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯....这也是一个非常简单的模式，没有错误处理代码，没有服务器配置，也没有标志解析。然而，代码是如此复杂。我不记得这个信号了，习语。

所以我创建了 [`github.com/nasa9084/syg`](https://github.com/nasa9084/syg) 包来监听信号和调用回调函数。

## github.com/nasa9084/syg

重新思考示例代码。
我用`syg`包:
重写了示例代码

```
func main() {
    s := &http.Server{
        // configure server properties
    }

    cancel := syg.Listen(func(os.Signal) {
        s.Shutdown(context.Background())
    }, syscall.SIGINT)
    defer cancel()

    if err := s.ListenAndServe(); err != http.ErrServerClosed {
        // error handling
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

哇，非常短。

`syg`包只有两个功能，`Listen()`和`ListenContext()`。
如你所想，这些本质上是相同的功能。
`ListenContext()`带`context.Context`，而`Listen()`不是。
`Listen()`在函数内部调用`context.Background()`，用那个上下文调用`ListenContext()`。

这些函数调用`signal.Notify()`并生成等待信号的 goroutine，并返回取消函数。

`syg`包很小，但是很有用。
如果您发现问题或改进，请使用此包并制作问题或 PR！