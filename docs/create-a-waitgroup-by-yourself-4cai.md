# 自己创建一个等待组

> 原文：<https://dev.to/dannypsnl/create-a-waitgroup-by-yourself-4cai>

如果你在 Go 中写了并发代码。我觉得你以前看起来很像。今天的重点是通过频道技巧创建一个等待组。

怎么会？

首先，您需要一个没有缓冲区的通道。

```
func main() {
    wait := make(chan struct{})
} 
```

Enter fullscreen mode Exit fullscreen mode

然后从中获取一些东西，这样如果在`wait`中没有值，这个过程继续进行。但是如果只是试图从一个空的通道中得到一些东西。您将会阻塞并导致死锁。然后走了会慌的。所以我们必须稍微修改一下代码，但也要在下一次迭代中更具扩展性。

```
// ...
n := 0
wait := make(chan struct{})

for i := 0; i < n; i++ {
    <-wait
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建 works 循环。

```
import (
    "time"
)
// ...
n := 10000
wait := make(chan struct{})

for i := 0; i < n; i++ {
    time.Sleep(time.Microsecond)
    wait <- struct{}{}
}

for i := 0; i < n; i++ {
    <-wait
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

ps。这段代码有个小 bug(你可以试着找找，或者看最后的答案)

现在我们可以看到它的工作。这个代码能够工作的原因是因为大小 n 是我们期望的工人数量。每个工人完成工作后。他们会发送一些东西(这里是`struct{}{}`，但确切地说是无关紧要的东西)到我们的`wait`通道。我们只从`wait`中读到`n`的东西。

所以在`n`事情被读完之后。即使`wait`有了新东西，我们也不会再被屏蔽了。否则我们必须等待`wait`。

整个代码都依赖于这个事实。有了这些知识，我们现在就可以创造我们自己的。

```
type waitGroup struct {
    n    int
    wait chan struct{}
}

func WaitGroup() *waitGroup {
    return &waitGroup{
        n:    0,
        wait: make(chan struct{}),
    }
}

func (wg *waitGroup) Add(n int) {
    wg.n = wg.n + n
}

func (wg *waitGroup) Done() {
    wg.wait <- struct{}{}
}

func (wg *waitGroup) Wait() {
    defer close(wg.wait)
    for i := 0; i < wg.n; i++ {
        <-wg.wait
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们使用一种类型包装所有我们需要的东西。(是基本的习语，所以不想说为什么)

然后方法`Add`是为我们之前谈过的`n`做准备。以动态的方式添加这些东西。

接下来`Done`像我们在前面的代码中手动做的那样做。

而`Wait`则是读出的数等于最终的`n`。

最后让我们说说在前面的代码中发生了什么。你应该总是关闭`channel`。所以代码将是:

```
// ...
wait := make(chan interface{})
defer close(wait)
// ... 
```

Enter fullscreen mode Exit fullscreen mode

也许你会对这部分感到困惑。原因是`channel`不会被 GC 自动收集(如果可以，那就是另一个地狱)。所以总是关闭它是很重要的。

ps。在生产代码中，请仍然使用`sync.WaitGroup`，我做了一个测试，`sync.WaitGroup`比你在这里看到的版本快 25%。

### 参考文献:

#### [围棋编程语言](http://www.gopl.io/)

*   作者:艾伦 Alan 多诺万和布莱恩·w·柯尼根
*   国际标准书号:978-986-476-133-3

#### [并发于围棋](http://shop.oreilly.com/product/0636920046189.do)

*   作者:凯瑟琳·考克斯-布戴
*   国际标准书号:978-1-491-94119-5