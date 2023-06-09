# Go 与 Erlang 中的并发性

> 原文：<https://dev.to/pancy/concurrency-in-go-vs-erlang-595a>

[![night view of cars on a bridge](img/c2a2a11cea41c536adad5298f6bd258d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yDw3JxY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1508980557051-0c1fa6f01c1f%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3De19e72e5204395968c4f2d069fc03247%26auto%3Dformat%26fit%3Dcrop%26w%3D1651%26q%3D80)

我写 Go 已经一年多了，并且已经成功地写了一个相当大的库。

然而，我最近对函数式编程很感兴趣，并且偶尔使用 Go 来勾画快速 API 服务器。我开始学习 Erlang，并在编写并发程序方面对这两者进行了一些比较。

对于许多人来说，Erlang 是一种非常古怪的语言，尤其是那些更熟悉 C 风格命令式编程的程序员。

我们都知道 Go 非常受欢迎，不仅仅是因为它的简单，还因为它的两个并发的构造让 Node.js 相形见绌——channels 和 goroutines。在 Go 中，生成一个并行例程并与之交互是如此简单，可以通过以下方式完成:

```
func main() {
        numchan := make(chan int, 1)
        donechan := make(chan struct{})

        go func() {
                fmt.Println(<-numchan + 1)
                donechan <- struct{}{}  
        }() 
        numchan <- 8
        <-donechan
} 
```

Enter fullscreen mode Exit fullscreen mode

Point is a channel 是一个天赐的通道，您可以将来自任何 goroutine 的值发送到该通道，许多其他 go routine 可以在没有数据竞争的情况下使用它。Go 通道确保每个向它请求值的例程都受到约束，并且排列整齐(它实现了互斥和 semophore)。作为程序员，你只要相信渠道就行了。您无法控制也不知道哪个例程先到达通道。

如果你仔细想想，这并不意外。每个例程都是并行执行的(希望如此),它们不应该涉及彼此的业务。举个例子，当您有几个 URL 需要分发给几个 goroutines 来充当 workers 时，每个 URL 在发送完一个 HTTP 请求后，打印它从 URL 收到的响应中的状态代码:

```
func printStatusCode(url string) {
        res, _ := http.Get(url)
        fmt.Printf("%d: %d\n", pid, res.StatusCode)
}

func main() {
        var wg sync.WaitGroup
        urls := [...]string{
                "https://google.com",
                "https://facebook.com",
                "https://dev.to",
                // ... thousands more
        }
        for i, url := range urls {
                wg.Add(1)
                go func(pid int, url string) {
                        printStatusCode(url)
                        wg.Done()
                }(i, url)
        }
        wg.Wait()
} 
```

Enter fullscreen mode Exit fullscreen mode

每个 goroutine 打印出分配给它的`pid`(它只是 url 在`urls`数组中的索引)和接收到的响应的状态代码。将索引打印出来是很有趣的，因为它让您看到 goroutines 是并行执行的，而不是同步执行的。使用了`sync.WaitGroup`来代替前面示例中使用的信令信道。这使得等待许多 goroutines 更容易。

这里的关键要点是 Go 通过通道来抽象并发性。您可以使用通道向一个或多个例程发送数据，并在不同的例程之间进行同步/发送信号。

让我们看看如何在 Erlang 中实现同样的事情。

在 Erlang 中，并行执行被称为进程。它们和围棋中的套路是一样的(至少就本帖的目的而言)。

Erlang 没有像 Go channel 那样的中介，但是它采用了一个非常强大的概念，即演员模型。在这个世界上，进程是一个独立的参与者。它不在乎外界。这就像一个囚犯翻弄着自己的东西，等待着什么东西被送进监狱的大门，或者更具体地说，是*邮箱*。

进程的邮箱类似于 Go 的通道，但是它不是共享的。它是一个私有的数据缓冲区，让特定的进程一个接一个地同步进行。当一个进程向另一个进程的邮箱发送一些东西时，这些东西会被存储在那里，直到那个进程可以使用它们。

这里有一个小例子，做的事情和第一个 Go 例子一样:

```
main() ->
    P1 = spawn(fun() ->
        receive
            Num -> io:format("~p~n", [Num + 1])
        end
    end),

    P1 ! 8,
    ok. 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？用这么少的代码你怎么能做到呢？首先，函数式语言的设计比大多数命令式语言高一个层次。在命令式编程中(在 Go 的情况下，是结构化的，这是命令式的)，你主要编程来改变一些状态，因此你必须关心*何时*每行代码开始运行(`A`改变状态`X`，然后`B`读取`X`必然不同于`B`读取`X`，然后`A`改变`X`)。在声明式编程中，人们表达逻辑而不关心控制流，因为每个表达式/函数没有副作用，也就是说不会改变自身之外的状态。

这也是为什么二郎的演员模型如此简单强大的原因。它永远不必关心数据竞争或同步，因为每个进程永远无法访问任何外部数据。

每个 Erlang 进程占用的内存很少，并且可以动态地增长/收缩。它不共享内存，只通过消息传递进行通信。这非常类似于 Go 的并发解决方案。下面是另一个相当于第二个 Go 代码的 Erlang 示例:

```
printStatusCode(Url) ->
    {_, Res} = httpc:request(Url),
    {{_, StatusCode, _}, _, _} = Res,
    io:format("~p: ~p~n", [self(), Code]).

main() ->
    inets:start(),
    Urls = [ 
        "https://google.com", 
        "http://facebook.com", 
        "https://dev.to" 
    ],
    lists:foreach(fun(Url) -> spawn(?MODULE, printStatusCode, [Url]) end, Urls). 
```

Enter fullscreen mode Exit fullscreen mode

现在这变得有点令人毛骨悚然，但这是因为 Erlang 的括号元组文字比它做事情的方式。这里突出的是在括号包围的几个地方重复出现的下划线`_`。这就是所谓的模式匹配，一种在函数世界中很常见的技术。这是因为在 Erlang 中,`=`的意思是“绑定”,而不是 Go 中的“赋值”。因此，您可以将数据的左边形状与右边形状匹配，如下所示:

```
{X, Y, _} = {"Joe", "Jim", "Jam"},
%% `X` is bound to "Joe" and `Y` is bound to "Jim" [F | _ | T | _] = [1, 5, 2, 10, 54, 0, 98].
%% `F` is bound to 1 and T is bound to 2 
```

Enter fullscreen mode Exit fullscreen mode

围棋有一种接近这种行为的东西，叫做多回。

```
// Aw, not even close :(
a, b := func() (string, string) {
        return "Joe", "Jim"
}() 
```

Enter fullscreen mode Exit fullscreen mode

由于 Go 没有元组表达式，所以它需要在函数表达式中包装多个返回值。您不能对数组或切片进行模式匹配。

需要注意的一个区别是 Go 是一种静态类型的编译语言，而 Erlang 是一种动态类型的字节码编译语言。在图像处理等算术运算上，Go 当然会比 Erlang 更胜一筹。并且被编译成可执行的 Go 程序比 Erlang 更具有移植性，不需要虚拟机。

欢迎提问、反馈和建议。请在评论中留下它们。