# Golang:通过 OR、AND 组合信号

> 原文：<https://dev.to/hauxe/golang-combining-signals-by-or-and-48dd>

在 Golang 中，如果我们想避免 goroutine 泄漏，通常我们会使用一个特殊的 *done* 通道来通知特定的 goroutine 需要关闭。如果我们处理许多模块，这些模块都有自己的通道，那么我们可能希望将这些信号合并成一个“或”或“与”逻辑形式的信号

### 或组合信号

> 如果接收到任何信号，则发射组合信号

下面的实现使用数量为 2 的最大通道进行组合，您可以更改为任何您想要的数量:

```
// OR function combines all signal from channels into a single channel with Or condition
func OR(channels ...<-chan interface{}) <-chan interface{} {
    switch len(channels) {
    case 0:
        return nil
    case 1:
        return channels[0]
    }
    orDone := make(chan interface{})
    go func() {
        defer close(orDone)
        switch len(channels) {
        case 2:
            select {
            case <-channels[0]:
            case <-channels[1]:
            }
        default:
            select {
            case <-channels[0]:
            case <-channels[1]:
            case <-channels[2]:
            case <-OR(append(channels[3:], orDone)...):
            }
        }
    }()
    return orDone
} 
```

Enter fullscreen mode Exit fullscreen mode

switch 语句的第一种情况是检查通道长度是否为 0，我们将通过返回一个 **nil** 通道来永久阻塞，这是在没有任何信号时的正确行为

switch 语句的第二种情况是，如果只有一个通道被传递给这个函数，我们将简单地返回它！

然后我们创建一个 **orChan** 来合并所有来自通道的信号，然后派生一个新的 goroutine 来合并最多 2 个通道到 **orChan**

请注意，当我们递归调用**或**函数时，由于逻辑 OR，我们再次将 **orChan** 传递给该函数，我们需要考虑前面组合中的 **orChan**

### 与组合信号

> 如果接收到所有的信号，则发射组合信号

该实现使用了 Golang 中非常激动人心的知识:在 select 语句中，如果写入通道的右侧是接收通道，select 语句将等待所有接收通道完成，然后发送到左侧通道

```
// AND function combines all signal from channels into a single channel with And condition
func AND(channels ...<-chan interface{}) <-chan interface{} {
    switch len(channels) {
    case 0:
        return nil
    case 1:
        return channels[0]
    }
    andDone := make(chan interface{})
    collector := make(chan interface{}, len(channels))
    go func() {
        defer close(andDone)
        switch len(channels) {
        case 2:
            select {
            case collector <- <-channels[0]:
            case collector <- <-channels[1]:
            }
        default:
            select {
            case collector <- <-channels[0]:
            case collector <- <-channels[1]:
            case collector <- <-channels[2]:
            case collector <- <-AND(channels[3:]...):
            }
        }
    }()
    return andDone
} 
```

Enter fullscreen mode Exit fullscreen mode

它与 OR 实现版本非常相似，有三个重要的不同点:

*   我们创建一个缓冲的**收集器**通道，用于收集来自组合通道的信号

*   我们通过**集电极**通道作为*选择*语句的左侧

*   我们递归地调用**和**函数，而不传递**和**到它的参数列表中，因为我们在等待所有的信号，没有必要再考虑这个信号

我在这个 [Repo](https://github.com/hauxe/GoM/blob/master/event/combine_signal.go) 中实现了这些功能，你可以自己随意使用！谢谢