# 通过编写测试学习 Go:依赖注入

> 原文：<https://dev.to/quii/learn-go-by-writing-tests-dependency-injection-n7j>

这是 WIP 项目[的第 5 篇文章，通过编写测试](https://github.com/quii/learn-go-with-tests)来学习 Go，其目的是熟悉 Go 并学习 TDD 的相关技术

*   [第一篇文章让你了解了 TDD](https://dev.to/quii/learn-go-by-writing-tests--m63)
*   [第二篇文章讨论了数组和切片](https://dev.to/quii/learn-go-by-writing-tests-arrays-and-slices-ahm)
*   [第三篇文章讲授结构、方法、接口&表驱动测试](https://dev.to/quii/learn-go-by-writing-tests-structs-methods-interfaces--table-driven-tests-1p01)
*   [第四篇文章展示了如何出错以及为什么指针有用](https://dev.to/quii/learn-go-by-writing-tests-pointers-and-errors-2kp6)

# 依赖注入

假设您之前已经阅读过 structs 部分，因为这需要对接口有所了解。

在编程社区中有很多关于依赖注入的误解。希望这份指南能告诉你怎么做

*   你不需要框架
*   它不会使你的设计过于复杂
*   它有助于测试
*   它允许您编写优秀的通用函数。

我们想写一个问候某人的函数，就像我们在 hello-world 章节中做的那样，但是这次我们将测试*实际打印*。

简单回顾一下，这个函数可能是这样的

```
func Greet(name string) {
    fmt.Printf("Hello, %s", name)
} 
```

Enter fullscreen mode Exit fullscreen mode

但是我们如何测试这个呢？调用`fmt.Printf`打印到标准输出，这对于我们使用测试框架来说很难捕捉。

我们需要做的是能够**注入**(这只是传入的一个花哨的词)打印的依赖性。

我们的函数不需要关心*在哪里*或*如何*打印发生，所以我们应该接受一个*接口*而不是一个具体类型。

如果我们这样做了，我们就可以将实现改为打印我们控制的内容，这样我们就可以测试它。在“现实生活”中，你可以注入一些写入标准输出的东西。

如果你看一下`fmt.Printf`的源代码，你可以看到一个让我们挂接
的方法

```
// It returns the number of bytes written and any write error encountered.
func Printf(format string, a ...interface{}) (n int, err error) {
    return Fprintf(os.Stdout, format, a...)
} 
```

Enter fullscreen mode Exit fullscreen mode

有意思！引擎盖下的`Printf`只是在`os.Stdout`中调用`Fprintf`。

到底什么是*是`os.Stdout`？对于第一个参数，`Fprintf`期望传递给它什么？* 

```
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error) {
    p := newPrinter()
    p.doPrintf(format, a)
    n, err = w.Write(p.buf)
    p.free()
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

一个`io.Writer`

```
type Writer interface {
    Write(p []byte) (n int, err error)
} 
```

Enter fullscreen mode Exit fullscreen mode

当你写更多的 Go 代码时，你会发现这个接口经常出现，因为它是一个很好的通用接口，可以“把数据放在某个地方”。

所以我们知道，在被子下面，我们最终会用`Writer`向某个地方发送我们的问候。让我们使用这个现有的抽象来使我们的代码可测试和更可重用。

## 先写测试

```
func TestGreet(t *testing.T) {
    buffer := bytes.Buffer{}
    Greet(&buffer,"Chris")

    got := buffer.String()
    want := "Hello, Chris"

    if got != want {
        t.Errorf("got '%s' want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

来自`bytes`包的`buffer`类型实现了`Writer`接口。

因此，我们将在测试中使用它作为我们的`Writer`发送，然后我们可以在调用`Greet`后检查写入了什么

## 试运行测试

测试不会编译

```
./di_test.go:10:7: too many arguments in call to Greet
    have (*bytes.Buffer, string)
    want (string) 
```

Enter fullscreen mode Exit fullscreen mode

## 编写运行测试所需的最少代码，并检查失败的测试输出

*听编译器*修复问题。

```
func Greet(writer *bytes.Buffer, name string) {
    fmt.Printf("Hello, %s", name)
} 
```

Enter fullscreen mode Exit fullscreen mode

`Hello, Chris di_test.go:16: got '' want 'Hello, Chris'`

测试失败。请注意，名称将被打印出来，但它将被输出到 stdout。

## 写足够的代码让它通过

在我们的测试中，使用 writer 将问候发送到缓冲区。记住`fmt.Fprintf`类似于`fmt.Printf`，但是取而代之的是用一个`Writer`来发送字符串，而`fmt.Printf`默认为 stdout。

```
func Greet(writer *bytes.Buffer, name string) {
    fmt.Fprintf(writer, "Hello, %s", name)
} 
```

Enter fullscreen mode Exit fullscreen mode

测试现在通过了

## 重构

早先编译器告诉我们传入一个指向`bytes.Buffer`的指针。这在技术上是正确的，但不是很有用。

为了演示这一点，尝试将`Greet`函数连接到一个 Go 应用程序中，我们希望它打印到标准输出。

```
func main() {
    Greet(os.Stdout, "Elodie")
} 
```

Enter fullscreen mode Exit fullscreen mode

`./di.go:14:7: cannot use os.Stdout (type *os.File) as type *bytes.Buffer in argument to Greet`

如前所述，`fmt.Fprintf`允许你传入一个我们既知道`os.Stdout`又知道`bytes.Buffer`实现的`io.Writer`。

如果我们改变代码来使用更通用的接口，我们现在可以在测试和应用程序中使用它。

```
package main

import (
    "fmt"
    "os"
    "io"
)

func Greet(writer io.Writer, name string) {
    fmt.Fprintf(writer, "Hello, %s", name)
}

func main() {
    Greet(os.Stdout, "Elodie")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更上木卫一。作者

我们还可以使用`io.Writer`向哪些地方写入数据？我们的`Greet`函数到底有多通用？

### 互联网

运行以下

```
package main

import (
    "fmt"
    "io"
    "net/http"
)

func Greet(writer io.Writer, name string) {
    fmt.Fprintf(writer, "Hello, %s", name)
}

func MyGreeterHandler(w http.ResponseWriter, r *http.Request) {
    Greet(w, "world")
}

func main() {
    http.ListenAndServe(":5000", http.HandlerFunc(MyGreeterHandler))
} 
```

Enter fullscreen mode Exit fullscreen mode

转到 [http://localhost:5000](http://localhost:5000) 。您将看到您的问候功能正在使用。

HTTP 服务器将在后面的章节中介绍，所以不要太担心细节。

当您编写 HTTP 处理程序时，您会得到一个`http.ResponseWriter`和用于发出请求的`http.Request`。当你实现你的服务器时，你*使用编写器编写*你的响应。

你可能会猜到`http.ResponseWriter`也实现了`io.Writer`，所以这就是为什么我们可以在处理程序中重用我们的`Greet`函数。

## 包装完毕

我们的第一轮代码不容易测试，因为它将数据写到我们无法控制的地方。

*受我们的测试*的激励，我们重构了代码，这样我们就可以控制*，其中*数据是由**注入一个依赖项**写入的，这使我们能够:

*   **测试我们的代码**如果你不能轻松地测试一个函数*，通常是因为依赖硬连接到一个函数*或*全局状态。例如，如果你有一个全局数据库连接池，它被某种服务层使用，那么很可能很难测试，并且运行起来会很慢。DI 将促使你注入一个数据库依赖(通过一个接口),然后你可以用一些你可以在测试中控制的东西模拟出来。*
**   **分离我们的关注点**，将*数据去向*与*如何生成数据*解耦。如果你觉得一个方法/函数有太多的责任(生成数据*和*写入数据库？处理 HTTP 请求*和*做域级逻辑？)DI 大概就要成为你需要的工具了。*   **允许我们的代码在不同的环境中重用**我们的代码可以使用的第一个“新”环境是内部测试。但是如果有人想用你的函数尝试新的东西，他们可以注入他们自己的依赖。*

 *### 嘲讽呢？我听说你需要它作为 DI，而且它是邪恶的

嘲讽后面会详细介绍(而且不是恶)。您使用嘲讽来用一个您可以在测试中控制和检查的虚构版本替换您注入的真实内容。但是在我们的例子中，标准库已经为我们准备好了一些东西。

### 围棋标准库确实不错，抓紧时间研究一下吧

通过熟悉`io.Writer`接口，我们能够在测试中使用`bytes.Buffer`作为我们的`Writer`，然后我们可以使用标准库中的其他`Writer`在命令行应用程序或 web 服务器中使用我们的功能。

你对标准库越熟悉，你就会越多地看到这些通用接口，然后你可以在你自己的代码中重用它们，使你的软件在许多环境中可重用。

这个例子很大程度上受 Go 编程语言的一章的影响，所以如果你喜欢这个，就去买吧！**