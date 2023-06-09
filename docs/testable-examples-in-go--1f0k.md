# 围棋中可测试的例子

> 原文：<https://dev.to/slomek/testable-examples-in-go--1f0k>

这篇文章最初发表在我的博客[MyCodeSmells.com](https://mycodesmells.com/post/testable-examples-in-go)上。查看更多内容！

为应用程序编写技术文档通常比编写代码更困难。我们，开发人员，非常了解我们的工艺，但是当涉及到留下一些事物如何工作，如何使用它们等的痕迹时。，我们出奇的无效。另一方面，当我们要使用一些外部依赖时，我们希望看到如何将它插入到我们的代码中的例子。我们如何在围棋中做到这一点？

当我第一次听说 Go 中可测试的例子时，我并不认为这是一个好主意。

## 增强文档

在用 Go 写应用的时候，我们经常会做两件事:写生产代码和测试。这些对任何人来说都是自然的，并不特定于这种编程语言。有时，我们会添加另一个部分，即基准，因为制作基准不费吹灰之力。还有一件事你可以做，但这是最不受欢迎的，因为它们对软件包的用户来说很棒，但对作者来说不是。

您可以将示例想象为检查输出的测试，该输出将被打印到标准输出。显然，您也可以通过对某个函数的结果显式调用`fmt.Print(..)`来创建示例。这是因为示例函数的主要目的是呈现一段代码，并显示预期的输出。

## 举例举例举例

假设我们有一个导出的函数，它对两个整数求和:

```
// sum.go
func Sum(a, b int) int {
    return a + b
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能很难想象，但可能会有一些用户不知道如何使用它。为此，我们需要为`Sum`创建示例函数，以便它出现在文档中(通过`godoc`):

```
// sum_test.go
func ExampleSum() {
    res := Sum(1, 5)
    fmt.Printf(res)
    // Output:
    // 6
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以对我们的示例做一些小的修改，并通过示例函数名中的一个单词后缀来标识它们:

```
// sum_test.go
func ExampleSum_negative() {
    Sum(-3, -9)
    //Output:
    // -12
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？

我们还可以为结构的函数创建示例，以便将它们放在`godoc`输出中的正确位置:

```
// user.go
type User struct {
    Name string
}

func (u User) Hi() {
    fmt.Printf("Hi, my name is %s!", u.Name)
} 
```

Enter fullscreen mode Exit fullscreen mode

使用结构名和我们想要展示的函数来命名示例函数是很重要的:

```
// user_test.go
func ExampleUser_Hi() {
    u := User{"Timmy"}
    u.Hi()
    // Output:
    // Hi, my name is Timmy!
} 
```

Enter fullscreen mode Exit fullscreen mode

## 运行实例

一旦创建了示例函数，就必须确保`// Output:`注释部分实际显示了上面编写的代码返回的输出。

运行示例非常简单，因为它们是通过`go test`执行的，就像任何*普通的*测试一样:

```
$ go test
--- FAIL: ExampleUser_Hi (0.00s)
got:
Hi, my name is not Timmy!
want:
Hi, my name is Timmy!
FAIL
exit status 1
FAIL    github.com/mycodesmells/golang-examples/misc/examples 0.005s 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，编写示例需要很少的努力，但是它们可以使您的文档更加完整，并且使其他人更加渴望使用您的包，因为他们可以看到您的代码是如何工作的。

这些例子的源代码可以从 Github 上的[获得，文档可以从 GoDoc](https://github.com/mycodesmells/golang-examples/tree/master/misc/examples) 上的[获得。](https://godoc.org/github.com/mycodesmells/golang-examples/misc/examples)