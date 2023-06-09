# 通过编写测试了解 Go:并发性

> 原文：<https://dev.to/gypsydave5/learn-go-by-writing-tests-concurrency--2ebk>

这是取自 WIP 项目 [*的第 6 篇文章，通过编写测试*](https://quii.gitbook.io/learn-go-with-tests/) 来学习围棋，其目的是熟悉围棋并学习 TDD 相关的技术
。

[第一篇文章让你熟悉了 TDD](https://dev.to/quii/learn-go-by-writing-tests--m63)
[第二篇文章讨论了数组和切片](https://dev.to/quii/learn-go-by-writing-tests-arrays-and-slices-ahm)
[第三篇文章讲述了结构、方法、接口&表驱动测试](https://dev.to/quii/learn-go-by-writing-tests-structs-methods-interfaces--table-driven-tests-1p01)
[第四篇文章展示了如何处理错误以及为什么指针有用](https://dev.to/quii/learn-go-by-writing-tests-pointers-and-errors-2kp6)
[第五篇文章向你展示了如何以及为什么要进行依赖注入](https://dev.to/quii/learn-go-by-writing-tests-dependency-injection-n7j)

这一章是关于并发性的。

* * *

# 并发

设置是这样的:一个同事编写了一个函数`CheckWebsites`，这个函数
检查 URL 列表的状态。

```
package concurrency

type WebsiteChecker func(string) bool

func CheckWebsites(wc WebsiteChecker, urls []string) map[string]bool {
    results := make(map[string]bool)

    for _, url := range urls {
        results[url] = wc(url)
    }

    return results
} 
```

Enter fullscreen mode Exit fullscreen mode

它返回每个 URL 的映射，检查布尔值- `true`表示好的
响应，`false`表示坏的响应。

您还必须传入一个`WebsiteChecker`,它接受一个 URL 并返回一个布尔值给
。这个函数使用它来检查所有的网站。

使用[依赖注入](https://dev.to/quii/learn-go-by-writing-tests-dependency-injection-n7j)允许他们测试功能
而不需要进行真正的 HTTP 调用，这使得它可靠而快速。

这是他们写的测试:

```
package concurrency

import (
    "reflect"
    "testing"
)

func mockWebsiteChecker(url string) bool {
    if url == "waat://furhurterwe.geds" {
        return false
    }
    return true
}

func TestCheckWebsites(t *testing.T) {
    websites := []string{
        "http://google.com",
        "http://blog.gypsydave5.com",
        "waat://furhurterwe.geds",
    }

    actualResults := CheckWebsites(mockWebsiteChecker, websites)

    want := len(websites)
    got := len(actualResults)
    if want != got {
        t.Fatalf("Wanted %v, got %v", want, got)
    }

    expectedResults := map[string]bool{
        "http://google.com":          true,
        "http://blog.gypsydave5.com": true,
        "waat://furhurterwe.geds":    false,
    }

    if !reflect.DeepEqual(expectedResults, actualResults) {
        t.Fatalf("Wanted %v, got %v", expectedResults, actualResults)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该功能正在生产中，并被用于检查数百个网站。但是你的同事开始抱怨速度太慢，所以他们请你帮忙加快速度。

### 写一个测试

让我们用一个基准来测试一下`CheckWebsites`的速度，这样我们就可以看到我们的改变的效果。

```
package concurrency

import (
    "testing"
    "time"
)

func slowStubWebsiteChecker(_ string) bool {
    time.Sleep(20 * time.Millisecond)
    return true
}

func BenchmarkCheckWebsites(b *testing.B) {
    urls := make([]string, 100)
    for i := 0; i < len(urls); i++ {
        urls[i] = "a url"
    }

    for i := 0; i < b.N; i++ {
        CheckWebsites(slowStubWebsiteChecker, urls)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

基准测试使用 100 个 URL 中的一部分来测试`CheckWebsites`，并使用一个新的伪实现`WebsiteChecker`。`slowStubWebsiteChecker`故意拖泥带水。它使用`time.Sleep`等待 20 毫秒，然后返回 true。

当我们使用`go test -bench=.` :
运行基准测试时

```
pkg: github.com/gypsydave5/learn-go-with-tests/concurrency/v0
BenchmarkCheckWebsites-4               1        2249228637 ns/op
PASS
ok      github.com/gypsydave5/learn-go-with-tests/concurrency/v0        2.268s 
```

Enter fullscreen mode Exit fullscreen mode

基准测试为 2249228637 纳秒——大约两又四分之一秒。

让我们试着快点。

### 写足够的代码让它通过

现在我们终于可以谈论并发性了，出于以下目的，并发性意味着“进行不止一件事情”。这是我们每天自然做的事情。

例如，今天早上我泡了一杯茶。我把水壶放上，然后，当我等着它沸腾的时候，我从冰箱里拿出牛奶，从橱柜里拿出茶，找到我最喜欢的杯子，把茶叶袋放进杯子里，然后，当水壶沸腾的时候，我把水放进杯子里。

我*没有*做的是把水壶放上，然后站在那里茫然地盯着水壶直到它烧开，然后在水壶烧开后做其他事情。

如果你能理解为什么第一种方式泡茶更快，那么你就能理解我们将如何使`CheckWebsites`更快。我们将告诉我们的计算机在等待的时候发出下一个请求，而不是等待一个网站的响应。

通常在 Go 中，当我们调用一个函数`doSomething()`时，我们等待它返回(即使它没有值要返回，我们仍然等待它完成)。我们说这个操作是*阻塞了*——它让我们等待它结束。在 Go 中没有阻塞的操作将在一个单独的*进程*中运行，该进程被称为 *goroutine* 。把一个进程想象成从上到下阅读 Go 代码的页面，当它被调用来阅读它所做的事情时，进入每个函数的内部。当一个单独的进程开始时，就像另一个读者开始在函数内部阅读，让原来的读者继续往下看。

为了告诉 Go 开始一个新的 goroutine，我们通过在函数调用前面加上关键字`go`:`go doSomething()`，将函数调用变成一个`go`语句。

```
package concurrency

type WebsiteChecker func(string) bool

func CheckWebsites(wc WebsiteChecker, urls []string) map[string]bool {
    results := make(map[string]bool)

    for _, url := range urls {
        go func() {
            results[url] = wc(url)
        }()
    }

    return results
} 
```

Enter fullscreen mode Exit fullscreen mode

因为启动一个 goroutine 的唯一方法是将`go`放在函数调用的前面，所以当我们想要启动一个 goroutine 时，我们经常使用*匿名函数*。匿名函数文字看起来和普通的函数声明一样，只是没有名字(这并不奇怪)。你可以在上面的`for`循环体中看到一个。

匿名函数有许多有用的特性，其中两个我们已经在上面使用过了。首先，它们可以在声明的同时执行——这就是匿名函数末尾的`()`正在做的事情。其次，它们维护对定义它们的词法范围的访问——在声明匿名函数时可用的所有变量在函数体中也是可用的。

上面的匿名函数的体和之前的循环体是一样的。唯一的区别是，循环的每次迭代都将启动一个新的 goroutine，与当前进程并发(`WebsiteChecker`函数),每个进程都会将其结果添加到结果映射中。

但是当我们运行`go test` :

```
-------- FAIL: TestCheckWebsites (0.00s)
        CheckWebsites_test.go:31: Wanted map[http://google.com:true http://blog.gypsydave5.com:true waat://furhurterwe.geds:false], got map[]
FAIL
exit status 1
FAIL    github.com/gypsydave5/learn-go-with-tests/concurrency/v1        0.010s 
```

Enter fullscreen mode Exit fullscreen mode

### 快速闪开进入一个平行宇宙(ism)...

你可能得不到这个结果。你可能会收到一条恐慌信息，我们一会儿会讲到。如果你得到了，不要担心，只要继续运行测试，直到你得到上面的结果。或者假装你知道。由你决定。欢迎来到并发:如果处理不当，很难预测会发生什么。不要担心——这就是我们编写测试的原因，以帮助我们知道什么时候我们可以预见地处理并发性。

### ...我们回来了。

我们被原来的测试抓住了`WebsiteChecker`现在正在返回一个空地图。哪里出了问题？

我们的`for`循环启动的 goroutines 没有足够的时间将它们的结果添加到`results`图中；`WebsiteChecker`函数对他们来说太快了，它返回的仍然是空的地图。

为了解决这个问题，我们可以等待所有的 goroutines 完成他们的工作，然后返回。两秒钟应该够了，对吧？

```
package concurrency

import "time"

type WebsiteChecker func(string) bool

func CheckWebsites(wc WebsiteChecker, urls []string) map[string]bool {
    results := make(map[string]bool)

    for _, url := range urls {
        go func() {
            results[url] = wc(url)
        }()
    }

    time.Sleep(2 * time.Second)

    return results
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行测试时，你会得到(或者没有得到——见上文):

```
-------- FAIL: TestCheckWebsites (0.00s)
        CheckWebsites_test.go:31: Wanted map[http://google.com:true http://blog.gypsydave5.com:true waat://furhurterwe.geds:false], got map[waat://furhurterwe.geds:false]
FAIL
exit status 1
FAIL    github.com/gypsydave5/learn-go-with-tests/concurrency/v1        0.010s 
```

Enter fullscreen mode Exit fullscreen mode

这不是很好吗——为什么只有一个结果？我们可能会尝试通过增加等待时间来解决这个问题——如果您愿意，可以尝试一下。没用的。这里的问题是变量`url`在`for`循环的每次迭代中都被重用——它每次都从`urls`获取一个新值。但是我们的每个 goroutines 都有一个对`url`变量的引用——它们没有自己独立的副本。所以他们都在*写`url`在迭代结束时的值——最后一个 url。这就是为什么我们得到的结果是最后一个 url。*

要解决这个问题:

```
package concurrency

import (
    "time"
)

type WebsiteChecker func(string) bool

func CheckWebsites(wc WebsiteChecker, urls []string) map[string]bool {
    results := make(map[string]bool)

    for _, url := range urls {
        go func(u string) {
            results[u] = wc(u)
        }(url)
    }

    time.Sleep(2 * time.Second)

    return results
} 
```

Enter fullscreen mode Exit fullscreen mode

通过给每个匿名函数一个 url 参数- `u` -然后用`url`作为参数调用匿名函数，我们确保`u`的值固定为循环的`url`的值，我们在循环中启动 goroutine。`u`是`url`值的副本，因此不能更改。

如果你幸运的话，你会得到:

```
PASS
ok      github.com/gypsydave5/learn-go-with-tests/concurrency/v1        2.012s 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你运气不好(如果你用`go test -bench=.` )
运行它们，这种可能性更大)

```
fatal error: concurrent map writes

goroutine 8 [running]:
runtime.throw(0x12c5895, 0x15)
        /usr/local/Cellar/go/1.9.3/libexec/src/runtime/panic.go:605 +0x95 fp=0xc420037700 sp=0xc4200376e0 pc=0x102d395
runtime.mapassign_faststr(0x1271d80, 0xc42007acf0, 0x12c6634, 0x17, 0x0)
        /usr/local/Cellar/go/1.9.3/libexec/src/runtime/hashmap_fast.go:783 +0x4f5 fp=0xc420037780 sp=0xc420037700 pc=0x100eb65
github.com/gypsydave5/learn-go-with-tests/concurrency/v3.WebsiteChecker.func1(0xc42007acf0, 0x12d3938, 0x12c6634, 0x17)
        /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:12 +0x71 fp=0xc4200377c0 sp=0xc420037780 pc=0x12308f1
runtime.goexit()
        /usr/local/Cellar/go/1.9.3/libexec/src/runtime/asm_amd64.s:2337 +0x1 fp=0xc4200377c8 sp=0xc4200377c0 pc=0x105cf01
created by github.com/gypsydave5/learn-go-with-tests/concurrency/v3.WebsiteChecker
        /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:11 +0xa1

        ... many more scary lines of text ... 
```

Enter fullscreen mode Exit fullscreen mode

这又长又吓人，但是我们需要做的就是吸一口气，读错误:`fatal error: concurrent map writes`。有时，当我们运行我们的测试时，两个 goroutines 在完全相同的时间写入结果映射。Go 中的地图不喜欢多个东西试图同时写入它们，所以`fatal error`。

这是一个*竞争条件*，当我们软件的输出依赖于我们无法控制的事件的时间和顺序
时，就会出现这种错误。因为我们无法精确控制每个 goroutine 何时写入结果映射，所以我们很容易受到两个 go routine 同时写入的影响。

Go 可以通过其内置的 [*竞态检测器*](https://blog.golang.org/race-detector) 帮助我们发现竞态条件。要启用这个特性，运行带有`race`标志的测试:`go test -race`。

您应该会得到如下所示的输出:

```
==================
WARNING: DATA RACE
Write at 0x00c420084d20 by goroutine 8:
  runtime.mapassign_faststr()
      /usr/local/Cellar/go/1.9.3/libexec/src/runtime/hashmap_fast.go:774 +0x0
  github.com/gypsydave5/learn-go-with-tests/concurrency/v3.WebsiteChecker.func1()
      /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:12 +0x82

Previous write at 0x00c420084d20 by goroutine 7:
  runtime.mapassign_faststr()
      /usr/local/Cellar/go/1.9.3/libexec/src/runtime/hashmap_fast.go:774 +0x0
  github.com/gypsydave5/learn-go-with-tests/concurrency/v3.WebsiteChecker.func1()
      /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:12 +0x82

Goroutine 8 (running) created at:
  github.com/gypsydave5/learn-go-with-tests/concurrency/v3.WebsiteChecker()
      /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:11 +0xc4
  github.com/gypsydave5/learn-go-with-tests/concurrency/v3.TestWebsiteChecker()
      /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker_test.go:27 +0xad
  testing.tRunner()
      /usr/local/Cellar/go/1.9.3/libexec/src/testing/testing.go:746 +0x16c

Goroutine 7 (finished) created at:
  github.com/gypsydave5/learn-go-with-tests/concurrency/v3.WebsiteChecker()
      /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:11 +0xc4
  github.com/gypsydave5/learn-go-with-tests/concurrency/v3.TestWebsiteChecker()
      /Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker_test.go:27 +0xad
  testing.tRunner()
      /usr/local/Cellar/go/1.9.3/libexec/src/testing/testing.go:746 +0x16c
================== 
```

Enter fullscreen mode Exit fullscreen mode

细节还是很难读懂——但是`WARNING: DATA RACE`非常明确。阅读错误正文，我们可以看到两个不同的 goroutines 在 map 上执行写操作:

`Write at 0x00c420084d20 by goroutine 8:`

正在写入同一块内存

`Previous write at 0x00c420084d20 by goroutine 7:`

最重要的是，我们可以看到发生写入的代码行:

`/Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:12`

以及 goroutines 7 和 8 开始的代码行:

`/Users/gypsydave5/go/src/github.com/gypsydave5/learn-go-with-tests/concurrency/v3/websiteChecker.go:11`

你需要知道的一切都打印在你的终端上——你所要做的就是有足够的耐心去阅读它。

### 通道

我们可以通过使用*通道*协调我们的 goroutines 来解决这个数据竞争。通道是一种 Go 数据结构，既可以接收值，也可以发送值。这些操作及其细节允许不同进程之间的通信。

在这种情况下，我们希望考虑父进程和每个 goroutines 之间的通信，它使用 url 来执行`WebsiteChecker`函数。

```
package concurrency

type WebsiteChecker func(string) bool
type result struct {
    string
    bool
}

func CheckWebsites(wc WebsiteChecker, urls []string) map[string]bool {
    results := make(map[string]bool)
    resultChannel := make(chan result)

    for _, url := range urls {
        go func(u string) {
            resultChannel <- result{u, wc(u)}
        }(url)
    }

    for i := 0; i < len(urls); i++ {
        result := <-resultChannel
        results[result.string] = result.bool
    }

    return results
} 
```

Enter fullscreen mode Exit fullscreen mode

在`results`地图旁边，我们现在有了一个`resultChannel`，我们用同样的方式对其进行`make`。`chan result`是通道的类型——一个`result`的通道。新的类型`result`将`WebsiteChecker`的返回值与被检查的 url 相关联——它是一个由`string`和`bool`组成的结构。因为我们不需要命名任何一个值，所以它们在结构中都是匿名的；当很难知道如何命名一个值时，这很有用。

现在，当我们迭代 URL 时，我们不是直接写入到`map`中，而是通过*发送语句*将对`wc`的每个调用的`result`结构发送到`resultChannel`。这使用了`<-`操作符，在左边取一个通道，在右边取一个值:

```
// Send statement
resultChannel <- result{u, wc(u)} 
```

Enter fullscreen mode Exit fullscreen mode

下一个`for`循环对每个 URL 迭代一次。在内部，我们使用了一个*接收表达式*，它将从通道接收的值赋给一个变量。这也使用了`<-`操作符，但是现在两个操作数颠倒了:通道现在在右边，我们要赋值的变量在左边:

```
// Receive expression
result := <-resultChannel 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们使用收到的`result`来更新地图。

通过将结果发送到通道中，我们可以控制每次写入结果映射的时间，确保一次只发生一次。虽然`wc`的每个调用和每个发送到结果通道的调用都是在它自己的进程中并行发生的，但是当我们用接收表达式从结果通道中取出值时，每个结果都是一次处理一个。

我们已经并行化了想要更快完成的代码部分，同时确保不能并行发生的部分仍然线性发生。我们通过使用渠道在多个相关流程中进行沟通。

当我们运行基准测试时:

```
pkg: github.com/gypsydave5/learn-go-with-tests/concurrency/v2
BenchmarkCheckWebsites-8             100          23406615 ns/op
PASS
ok      github.com/gypsydave5/learn-go-with-tests/concurrency/v2        2.377s 
```

Enter fullscreen mode Exit fullscreen mode

23406615 纳秒- 0.023 秒，比
原函数快一百倍左右。巨大的成功。

## 包装完毕

这个练习在 TDD 上比平时要轻松一些。在某种程度上，我们一直在参与对`CheckWebsites`函数的长期重构；输入和输出从未改变，只是变得更快了。但是我们已经进行的测试，以及我们编写的基准，允许我们以一种保持软件仍然工作的信心的方式重构`CheckWebsites`，同时证明它实际上已经变得更快了。

在加快速度的过程中，我们了解到

*   goroutines ，Go 中并发的基本单位，它让我们可以同时查看多个网站。
*   *匿名函数*，我们用它来启动每个检查网站的并发进程。
*   *通道*，帮助组织和控制不同进程之间的通信，使我们能够避免一个*竞争条件*的 bug。
*   竞争检测器帮助我们调试并发代码的问题

### 让它快

经常被误认为是 Kent Beck 提出的一种构建软件的敏捷方法是:

> [让它工作，让它正确，让它快速](http://wiki.c2.com/?MakeItWorkMakeItRightMakeItFast)

“工作”是让测试通过，“正确”是重构代码，“快速”是优化代码，例如，让代码快速运行。一旦我们让它工作起来，并把它做对了，我们就只能‘让它快点’了。幸运的是，我们得到的代码已经被证明是有效的，不需要重构。在完成另外两个步骤之前，我们不应该试图“快速完成”,因为

> [过早优化是万恶之源](http://wiki.c2.com/?PrematureOptimization)
> ——唐纳德·克纳特