# 通过编写测试来学习围棋

> 原文：<https://dev.to/quii/learn-go-by-writing-tests--m63>

这篇文章是 WIP 项目[的第一篇，该项目名为](https://github.com/quii/learn-go-with-tests)。

来自自述文件

*   通过编写测试来探索 Go 语言
*   获得 TDD 的基础知识。对于学习 TDD 来说，Go 是一种很好的语言，因为它是一种容易学习的语言，而且测试是内置的
*   相信你将能够在 Go 中开始编写健壮的、经过良好测试的系统

假设您已经安装了[并开始设置](https://golang.org/doc/install),并且您有一些编程的初级知识。

# 您好，世界

传统上，你用一种新语言做的第一个节目是“你好，世界”。创建一个名为`hello.go`的文件，并编写以下代码。要运行它，请键入`go run hello.go`。

```
package main

import "fmt"

func main() {
    fmt.Println("Hello, world")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 工作原理

当你在 Go 中写一个程序时，你会有一个定义了一个`main` func 的`main`包。`func`关键字是你如何定义一个有名字和主体的函数。

用`import "fmt"`我们正在导入一个包，它包含了我们用来打印的`Println`函数。

## 如何测试

你如何测试这个？将您的“领域”代码与外界隔离开来是很好的(副作用)。`fmt.Println`是一个副作用(打印到 stdout ),我们发送的字符串是我们的域。

所以让我们把这些问题分开，这样更容易测试

```
package main

import "fmt"

func Hello() string {
    return "Hello, world"
}

func main() {
    fmt.Println(Hello())
} 
```

Enter fullscreen mode Exit fullscreen mode

我们又用`func`创建了一个新函数，但这次我们在定义中添加了另一个关键字`string`。这意味着这个函数返回一个`string`。

现在创建一个名为`hello_test.go`的新文件，我们将在这里为我们的`Hello`函数
编写一个测试

```
package main

import "testing"

func TestHello(t *testing.T) {
    got := Hello()
    want := "Hello, world"

    if got != want {
        t.Errorf("got '%s' want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在解释之前，让我们先运行代码。在您的终端中运行`go test`。应该已经过去了！只是为了检查一下，试着通过改变`want`字符串来故意破坏测试。

请注意，您不必在多个测试框架之间进行选择，也不必为了编写测试而破译测试 DSL。您需要的一切都内置在语言中，语法与您将要编写的代码的其余部分是相同的。

### 写作测试

编写测试就像编写函数一样，有一些规则

*   它需要在一个文件名类似于`xxx_test.go`的文件中
*   测试功能必须以单词`Test`开始
*   测试函数只接受一个参数`t *testing.T`

现在，知道你的类型`*testing.T`的`t`是你进入测试框架的“钩子”就足够了，所以当你想失败时，你可以做像`t.Fail()`这样的事情。

#### 新事物

##### `if`

Go 中的 If 语句非常像其他编程语言。

##### 声明变量

我们用语法`varName := value`声明了一些变量，这让我们可以在可读性测试中重用一些值

##### `t.Errorf`

我们在我们的`t`上调用`Errorf` *方法*，它将打印出一条消息并且测试失败。`F`代表格式，它允许我们构建一个字符串，将值插入占位符值`%s`。当你让测试失败时，它是如何工作的就很清楚了。

我们将在后面探讨方法和函数之间的区别。

### Go doc

Go 的另一个生活质量特性是文档。您可以通过运行`godoc -http :8000`在本地启动文档。如果你去 [localhost:8000/pkg](http://localhost:8000/pkg) ，你会看到所有的软件包都安装在你的系统上。

绝大多数标准库都有优秀的文档和示例。导航到[http://localhost:8000/pkg/testing/](http://localhost:8000/pkg/testing/)会很有价值，可以看看你能得到什么。

### 你好，你

现在我们有了一个测试，我们可以安全地迭代我们的软件了。

在上一个例子中，我们在之后写了测试*，代码已经写好了，所以你可以得到一个如何写测试和声明函数的例子。从这一点上，我们将是*写测试第一**

我们的下一个要求是让我们指定问候的接收者。

让我们从在测试中捕获这些需求开始。这是基本的测试驱动开发，允许我们确保我们的测试实际上是测试我们想要的东西。当您回顾性地编写测试时，即使代码没有按预期工作，您的测试也有可能继续通过。

```
package main

import "testing"

func TestHello(t *testing.T) {
    got := Hello("Chris")
    want := "Hello, Chris"

    if got != want {
        t.Errorf("got '%s' want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在运行`go test`，你应该有一个编译错误

```
./hello_test.go:6:18: too many arguments in call to Hello
    have (string)
    want () 
```

Enter fullscreen mode Exit fullscreen mode

当使用像 Go 这样的静态类型语言时，*听编译器*是很重要的。编译器知道您的代码应该如何整合和工作，所以您不必这样做。

在这种情况下，编译器会告诉你需要做什么才能继续。我们必须改变函数`Hello`来接受一个论点。

编辑`Hello`函数以接受字符串
类型的参数

```
func Hello(name string) string {
    return "Hello, world"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你再次尝试运行测试，你的`main.go`将无法编译，因为你没有传递参数。发送“世界”使其通过。

```
func main() {
    fmt.Println(Hello("world"))
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您运行测试时，您应该会看到类似于
的内容

```
hello_test.go:10: got 'Hello, world' want 'Hello, Chris'' 
```

Enter fullscreen mode Exit fullscreen mode

我们终于有了一个编译程序，但是根据测试，它不符合我们的要求。

让我们通过使用 name 参数并将其与`Hello,`
连接来完成测试

```
func Hello(name string) string {
    return "Hello, " + name
} 
```

Enter fullscreen mode Exit fullscreen mode

当您运行测试时，它们现在应该通过了。通常，作为 TDD 周期的一部分，我们现在应该*重构*。

这里没有太多要重构的，但是我们可以引入另一个语言特性*常量*

### 常数

常量定义如下

```
const helloPrefix = "Hello, " 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以重构我们的代码

```
const helloPrefix = "Hello, "

func Hello(name string) string {
    return helloPrefix + name
} 
```

Enter fullscreen mode Exit fullscreen mode

重构之后，重新运行您的测试，以确保您没有破坏任何东西。

常量应该可以提高应用程序的性能，因为它可以省去每次调用`Hello`时创建`"Hello, "`字符串实例的麻烦。

明确地说，对于这个例子来说，性能提升几乎可以忽略不计！但是有必要考虑创建常数来捕捉值的含义，有时还可以帮助提高性能。

## 您好，世界...又

下一个要求是，当我们的函数用空字符串调用时，它默认打印“Hello，World”，而不是“Hello”

开始写一个新的失败测试

```
func TestHello(t *testing.T) {

    t.Run("saying hello to people", func(t *testing.T) {
        got := Hello("Chris")
        want := "Hello, Chris"

        if got != want {
            t.Errorf("got '%s' want '%s'", got, want)
        }
    })

    t.Run("say hello world when an empty string is supplied", func(t *testing.T) {
        got := Hello("")
        want := "Hello, World"

        if got != want {
            t.Errorf("got '%s' want '%s'", got, want)
        }
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们介绍另一个测试工具，子测试。有时候，围绕一个“事物”进行分组测试，然后用子测试描述不同的场景是很有用的。

这种方法的一个好处是您可以设置共享代码，这些代码可以在其他测试中使用。

当我们检查消息是否是我们所期望的时，会出现重复的代码。

重构不是*仅仅是*对于生产代码！

重要的是，你的测试*是代码需要做什么的清晰规范*。

我们可以也应该重构我们的测试。

```
func TestHello(t *testing.T) {

    assertCorrectMessage := func(t *testing.T, got, want string) {
        t.Helper()
        if got != want {
            t.Errorf("got '%s' want '%s'", got, want)
        }
    }

    t.Run("saying hello to people", func(t *testing.T) {
        got := Hello("Chris")
        want := "Hello, Chris"
        assertCorrectMessage(t, got, want)
    })

    t.Run("empty string defaults to 'world'", func(t *testing.T) {
        got := Hello("")
        want := "Hello, World"
        assertCorrectMessage(t, got, want)
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做了什么？

我们已经将断言重构为一个函数。这减少了重复，提高了测试的可读性。在 go 中，你可以在其他函数中声明函数，并将它们赋给变量。然后你可以调用它们，就像普通的函数一样。我们需要传入`t *testing.T`，这样我们就可以在需要的时候告诉测试代码失败。

需要`t.Helper()`来告诉测试套件这个方法是一个助手。通过这样做，当失败时，报告的行号将在我们的*函数调用*中，而不是在我们的测试助手中。这将帮助其他开发人员更容易地跟踪问题。如果还是不明白，就注释掉，做一个测试失败，观察测试输出。

现在我们有了一个写得很好的失败测试，让我们修复代码。

```
const helloPrefix = "Hello, "

func Hello(name string) string {
    if name == "" {
        name = "World"
    }
    return helloPrefix + name
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行我们的测试，我们应该看到它满足新的需求，并且我们没有意外地破坏其他功能

### 学科

让我们再复习一遍这个循环

*   写一个测试
*   让编译器通过
*   运行测试，查看它是否失败，并检查错误消息是否有意义
*   编写足够的代码来通过测试
*   重构

从表面上看，这似乎很乏味，但坚持反馈循环很重要。

它不仅确保你有*相关的测试*，它还有助于确保*你通过重构测试的安全性来设计好的软件*。

看到测试失败是一个重要的检查，因为它还让您看到错误消息是什么样子的。作为一名开发人员，当失败的测试没有给出一个关于问题是什么的清晰想法时，与代码库一起工作是非常困难的。

通过确保你的测试是*快速的*并且设置你的工具以便运行测试是简单的，当你写你的代码的时候你可以进入一种流动的状态。

通过不写测试，你就承诺通过运行你的软件来手动检查你的代码，这打破了你的流程状态，你不会节省任何时间，特别是从长远来看。

## 继续走！更多要求

天哪，我们有更多的要求。我们现在需要支持第二个参数，指定问候语的语言。如果传入了我们不认识的语言，就默认为英语。

我们应该相信，我们可以使用 TDD 轻松地充实这一功能！

为通过西班牙语考试的用户编写一个测试。将其添加到现有套件中。

```
 t.Run("in Spanish", func(t *testing.T) {
        got := Hello("Elodie", "Spanish")
        want := "Hola, Elodie"
        assertCorrectMessage(t, got, want)
    }) 
```

Enter fullscreen mode Exit fullscreen mode

切记不要作弊！*先测试一下*。当你尝试运行测试时，编译器*应该*抱怨，因为你用两个参数而不是一个来调用`Hello`。

```
./hello_test.go:27:19: too many arguments in call to Hello
    have (string, string)
    want (string) 
```

Enter fullscreen mode Exit fullscreen mode

通过向`Hello`
添加另一个字符串参数来修复编译问题

```
func Hello(name string, language string) string {
    if name == "" {
        name = "World"
    }
    return helloPrefix + name
} 
```

Enter fullscreen mode Exit fullscreen mode

当你再次尝试运行测试时，它会抱怨没有传递足够的参数给其他测试中的`Hello`和`main.go`

```
./hello.go:15:19: not enough arguments in call to Hello
    have (string)
    want (string, string) 
```

Enter fullscreen mode Exit fullscreen mode

通过传递空字符串来修复它们。现在，除了我们的新场景
，你所有的测试都应该编译通过*和*

```
hello_test.go:29: got 'Hola, Elodie' want 'Hello, Elodie' 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在这里使用`if`来检查语言是否等于“西班牙语”,如果是，则更改消息

```
func Hello(name string, language string) string {
    if name == "" {
        name = "World"
    }

    if language == "Spanish" {
        return "Hola, " + name
    }

    return helloPrefix + name
} 
```

Enter fullscreen mode Exit fullscreen mode

测试现在应该通过了。

现在是时候对进行重构了。您应该在代码中看到一些问题，“神奇”的字符串，其中一些是重复的。试着自己重构它，每次修改都要确保重新运行测试，以确保你的重构没有破坏任何东西。

```
const spanish = "Spanish"
const helloPrefix = "Hello, "
const spanishHelloPrefix = "Hola, "

func Hello(name string, language string) string {
    if name == "" {
        name = "World"
    }

    if language == spanish {
        return spanishHelloPrefix + name
    }

    return helloPrefix + name
} 
```

Enter fullscreen mode Exit fullscreen mode

### 法语

*   编写一个测试，断言如果你传入`"French"`，你将得到`"Bonjour, "`
*   看到它失败，检查错误消息是否易读
*   对代码进行最小的合理修改

你可能已经写了类似这样的东西

```
func Hello(name string, language string) string {
    if name == "" {
        name = "World"
    }

    if language == spanish {
        return spanishHelloPrefix + name
    }

    if language == french {
        return frenchHelloPrefix + name
    }

    return helloPrefix + name
} 
```

Enter fullscreen mode Exit fullscreen mode

## `switch`

当你有很多`if`语句来检查一个特定的值时，通常使用一个`switch`语句来代替。如果我们希望以后添加更多的语言支持，我们可以使用`switch`来重构代码，使其更易于阅读和扩展

```
func Hello(name string, language string) string {
    if name == "" {
        name = "World"
    }

    prefix := helloPrefix

    switch language {
    case french:
        prefix = frenchHelloPrefix
    case spanish:
        prefix = spanishHelloPrefix
    }

    return prefix + name
} 
```

Enter fullscreen mode Exit fullscreen mode

编写一个测试，现在包括你选择的语言的问候，你应该看到扩展我们的*惊人的*函数是多么简单。

### 一...最后的...重构？

你可能会说，也许我们的函数变得有点大了。对此最简单的重构是将一些功能提取到另一个函数中，并且您已经知道如何声明函数。

```
func Hello(name string, language string) string {
    if name == "" {
        name = "World"
    }

    return greetingPrefix(language) + name
}

func greetingPrefix(language string) (prefix string) {
    switch language {
    case french:
        prefix = frenchHelloPrefix
    case spanish:
        prefix = spanishHelloPrefix
    default:
        prefix = englishPrefix
    }
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

几个新概念:

*   在我们的函数签名中，我们做了一个名为*的返回值* `(prefix string)`。
*   这将在函数中创建一个名为`prefix`的变量
    *   它将被赋予“零”值。这取决于类型，例如`int` s 是 0，对于字符串是`""`
        *   你可以通过调用`return`而不是`return prefix`来返回它所设置的值。
    *   这将显示在函数的 Go 文档中，这样可以使代码的意图更加清晰。
*   如果其他`case`语句都不匹配，switch case 中的`default`将被分支到
*   函数名以小写字母开头。在 Go 中，公共函数以大写字母开始，私有函数以小写字母开始。我们不希望我们算法的内部公开，所以我们把这个函数设为私有。

## 包装完毕

谁知道你能从`Hello, world`中得到这么多？

到目前为止，您应该对有所了解

### 围棋的一些语法

*   写作测试
*   用参数和返回类型声明函数
*   `if`、`else`、`switch`
*   声明变量和常数

### 理解 TDD 过程和*为什么*这些步骤很重要

*   *编写一个失败的测试，并看到它失败*，所以我们知道我们已经为我们的需求编写了一个*相关的*测试，并看到它产生了一个*易于理解的失败描述*
*   编写最少的代码使它通过，这样我们知道我们有工作的软件
*   *然后*重构，以我们测试的安全性为后盾，确保我们拥有易于使用的精心制作的代码

在我们的例子中，我们已经从`Hello()`到`Hello("name")`，再到`Hello("name", "french")`，这是一小步，很容易理解。

与“真实世界”的软件相比，这当然是微不足道的，但是原则仍然有效。TDD 是一项需要实践来发展的技能，但是通过将问题分解成可以测试的更小的部分，你将会更容易地编写软件。