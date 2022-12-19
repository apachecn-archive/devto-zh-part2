# Go 中的测试

> 原文：<https://dev.to/dannypsnl/testing-in-go-35ei>

只需列出一些 Go 中的测试方法。

基本上，我们使用`testing`这个内置库来测试

用 Go 开始你的第一个测试是一件简单的事情。

示例(我们将测试目录`add`下的以下函数)

```
package add

func Add(x, y int) int { return x + y } 
```

1.  创建一个包含后缀`_test`的文件，它将是一个测试文件，例如`add_test.go`
2.  在测试文件中创建一个前缀为`Test`的函数，使用`t *testing.T`作为它的参数

```
package add

import "testing"
 func TestAdd(t *testing.T) {
     if Add(1, 2) != 3 {
         t.Errorf("Add(1, 2) should be 3 but: %d", Add(1, 2))
     }
 } 
```

1.  键入`go test` &在终端执行

好了，现在我们得到一个测试，如果你看到错误消息，那么你的`Add`实现一定有问题

> 通常，我们不会使用`go test`而是`go test ./...`，因为我们在一个项目下会有很多包，`./...`会找出每个子目录(那些可以是 go 包)&运行测试

我们有`func (*testing.T) Run(subTestName string, subTest func(t *testing.T))`这个函数，我们可以用它来创建一个新的子测试。

```
func TestCarFactory(t *testing.T) {
    factory := car.NewFactory()
    t.Run("Toyota", func(t *testing.T) {
        toyota := factory.Build(car.Toyota)
        // test toyota
    })
    t.Run("Mazda", func(t *testing.T) {
        mazda := factory.Build(car.Mazda)
        // test mazda
    })
} 
```

基本上，你可以看到子测试意味着我们想要为不同的测试重用相同的上下文，或者像我一样，只是用它来表示测试结构。

一个实际的问题是有时我们从测试函数中提取一个测试助手。

例如:

```
func assertNoError(t *testing.T, err error) {
    if err != nil {
        t.Errorf("assert no error but: %s", err)
    }
} 
```

你会发现所有错误都说是发生在`t.Errorf`那一行，而不是错误实际发生的地方！

为了解决这个问题，你必须添加`t.Helper()`这个函数调用，根据文档:

> Helper 将调用函数标记为测试帮助函数。当打印文件和行信息时，该功能将被跳过。可以从多个 goroutines 中同时调用。

我推荐[https://github.com/stretchr/testify](https://github.com/stretchr/testify)为断言，**不要多此一举！**(我经常违反的东西)

并且[https://github.com/gavv/httpexpect](https://github.com/gavv/httpexpect)是一个非常棒的 web API 测试库。

一个很好的事实是，Go 还能帮助你轻松创建基准。

仍然在测试文件中，但是使用`Benchmark`作为一个测试的前缀。

```
package add

func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(1, 2)
    }
} 
```

运行基准测试需要参数`-bench`，它会像`go test -bench .`

输出:

```
goos: darwin
goarch: amd64
pkg: test
BenchmarkAdd-4 2000000000 0.61 ns/op
PASS
ok test 1.285s 
```

作为`t.Run`，基准中可以有`b.Run`。

为了更好地分析程序，您可以使用`go test -bench . -cpuprofile cpu.out -memprofile mem.out`来生成一些概要文件

然后使用`go tool pprof -http=127.0.0.1:5000 cpu.out`在浏览器上查看结果(如果您熟悉 CLI 模式，可以移除`-http`标志)

您可以看到这样的内容:

```
----------------------------------------------------------+-------------
                                            1130ms 100% | testing.(*B).launch /usr/local/Cellar/go/1.11.2/libexec/src/testing/benchmark.go:290
         0 0% 100% 1130ms 98.26% | testing.(*B).runN /usr/local/Cellar/go/1.11.2/libexec/src/testing/benchmark.go:141
                                            1130ms 100% | test.BenchmarkAdd /Users/dannypsnl/code/go/src/test/add_test.go:8
----------------------------------------------------------+------------- 
```

这里的例子太简单了，所以没什么可展示的，在真实世界的代码中，知道程序的热点是非常有用的。

> 另外，在 profile 示例中，`-bench`不能省略，因为我们希望某个东西运行很多时间来检测它的实际性能。

如果想得到真实使用下的性能，可以将 pprof 导入程序:

```
import (
    _ "net/http/pprof"
) 
```

如果你的程序不是一个 HTTP 服务器，那么你必须启动一个像:

```
go func() {
    log.Println(http.ListenAndServe("0.0.0.0:6060", nil))
}() 
```

`0.0.0.0`的理由可以参考[https://stack overflow . com/questions/20778771/0-0-0-0-127-0-0-1-and-localhost](https://stackoverflow.com/questions/20778771/what-is-the-difference-between-0-0-0-0-127-0-0-1-and-localhost)的说法

之后，你可以运行你的程序，然后像`go tool pprof http://127.0.0.1:6060/debug/pprof/profile`一样查看你的个人资料

要了解更多信息，您可以参考:

*   [https://golang.org/pkg/testing](https://golang.org/pkg/testing)
*   [https://golang.org/pkg/net/http/pprof](https://golang.org/pkg/net/http/pprof)

感谢阅读