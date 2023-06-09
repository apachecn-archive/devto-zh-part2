# PHP 开发人员入门指南

> 原文：<https://dev.to/restoreddev/introduction-to-go-for-php-developers-33ko>

最近，我开始为我的团队开发一个内部 CLI 应用程序。我选择的主要编程语言是 PHP，但我想用一种可以在任何平台上运行的语言来创建这个程序，而不必安装解释器。我还希望该应用程序是一个独立的二进制文件，便于分发和安装。我下载了 Go，惊喜地发现学习这门语言是如此简单，而且我在很短的时间内就学会了。Go 的过程化编程模型真的很合我的 PHP 大脑，我能够快速启动并运行这个应用程序。尽管与 PHP 有一些显著的区别，但我想把它们分享给将来想学习 Go 的任何其他 PHP 开发人员。

## 安装

#### MacOS

我用的是 Mac，所以我用自制软件安装了 Go:`brew install go`。如果你的 Mac 上没有[自制软件](https://brew.sh/)，我强烈推荐你使用它。

#### Windows & Linux

Go 网站上有各种操作系统的下载，包括一个用于 Windows 的安装程序，使安装变得简单。

## 入门

在 Go 中，每个 Go 项目的所有源代码都存储在一个名为 GOPATH 的目录下。默认情况下，GOPATH 在您的个人文件夹中设置为`go`，例如`/Users/andrewdavis/go`。在 GOPATH 中，有一个`bin`目录和一个`src`目录。`bin`目录包含你下载的任何二进制文件作为依赖项。您需要将`bin`文件夹添加到 PATH 环境变量中。您可以在终端的中这样做。bashrc/。带`export PATH=$PATH:$(go env GOPATH)/bin`的 zshrc 文件。要开始学习围棋，您可以通过在您的终端`go get golang.org/x/tour/gotour`中运行以下命令来下载围棋程序。`go get`使用提供的路径下载第三方依赖项的源代码和二进制文件。现在，你可以在你的终端上运行`gotour`，它将启动一个网络服务器，并把你的浏览器指向它。

要创建一个项目，在 GOPATH: `mkdir -p $GOPATH/src/helloworld`中的`src`下创建一个目录。打开文件夹`cd $GOPATH/src/helloworld`并创建一个名为 main 的文件`touch main.go`。在该文件中，放入以下内容:

```
package main

import "fmt"

func main() {
  fmt.Println("Hello world!")
} 
```

Enter fullscreen mode Exit fullscreen mode

所有 Go 程序的起点都是主包中的 main 函数。接下来可以运行`go run main.go`来运行程序。你也可以运行`go install`，程序将被编译并放在`bin`目录中，这样你就可以在你的终端中执行`helloworld`，它将运行你的代码。

## 与 PHP 的主要区别

现在您已经建立了一个项目，您可以开始探索不同的 Go 功能。您首先会注意到的一件事是，在 Go 中不需要分号。语句的结尾由新的一行来检测。以下是我花了一些时间才理解的更多差异:

#### 变量

Go 是一种静态的强类型语言，所以每个变量都有一个指定的类型。函数中的变量使用`:=`操作符赋值，该操作符将自动为您设置变量类型:`name := "Andrew" // name is now a string`。要创建一个变量而不在其中设置任何数据，或者在函数之外创建一个变量，你必须使用 var 关键字:`var name string`。

#### If 语句

If 语句的工作方式和 PHP 中的一样，但是它们不需要在布尔检查中使用括号。在阅读 Go 代码时，这种差异最初让我感到困惑。然而，我认为它使代码更容易阅读。

```
package main

import "fmt"

func main() {
  value := false
  if value {
    fmt.Println("Value is true")
  } else {
    fmt.Println("Value is false")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 包与名称空间

Go 使用术语`package`来命名它的内容。如果您的 Go 代码中有一个名为`controllers`的文件夹，那么该文件夹中的每个文件都将以`package controllers`开头。要从另一个包中导入控制器，您应该编写`import “helloworld/controllers”`。您的包中任何名称以大写字母开头的内容都可以在另一个包中使用。如果您在`controllers`中有一个名为`func HelloWorld()`的函数，那么一旦`controllers`被导入，您就可以调用`controllers.HelloWorld()`来运行这个函数。任何不以大写字母开头的内容都只能在同一个包中使用。不需要公私分明！

#### 字符串

在 Go 中，所有字符串都必须用双引号括起来。Go 中的单引号表示一个符文(单个 Unicode 码位)。习惯上，我用单引号键入字符串，因为这在 PHP 中是常见的做法。适应总是使用双引号需要一点时间。

```
var name = "Andrew"
var copy = '©' 
```

Enter fullscreen mode Exit fullscreen mode

#### 结构 vs 类

Go 没有 PHP 那样的`class`系统。相反，它使用结构来模拟定制的数据结构。你可以像这样写一个结构:

```
package main

type Cup struct {
  name string
  color string
  volume int
} 
```

Enter fullscreen mode Exit fullscreen mode

通过创建引用结构的函数，可以将方法添加到结构中。

```
func (c Cup) nameAndColor() string {
  return c.name + ": " + c.color
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过写入结构名称并在花括号中传递其初始值来创建结构的实例。方法执行使用点符号。

```
func main() {
  c := Cup{name: "Solo", color: "Red", volume: 12}
  c.nameAndColor() // returns "Solo: Red"
} 
```

Enter fullscreen mode Exit fullscreen mode

要创建修改 struct 实例的方法，该方法必须引用指向 struct 的指针:

```
func (c *Cup) crush() {
  c.volume = 0
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 错误

在 Go 中，错误不会被视为异常。没有抛出或捕捉机制。相反，如果发生错误，函数会返回错误。Go 支持从一个函数返回多个值。如果您调用一个可能返回错误的函数，您必须检查错误是否不为零，以处理错误情况。

```
package main

import "fmt"

func GetName(name string) (string, error) {
  if name == "Bob" {
    return "", fmt.Errorf("Name cannot be Bob")
  }

  return name, nil
}

func main() {
  name, err := GetName("Bob")
  if err != nil {
    fmt.Println("Uh-oh an error has occurred")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 鳍

当然，关于围棋还有很多东西要学，但希望这能帮助你入门。有很多学习围棋的好资源。对我最有帮助的是 [Go docs](https://golang.org/doc/) 和 [Go By Example](https://gobyexample.com/) 。如果你有任何想法或问题，请留下评论。感谢阅读！

PHP 的 logo 来自于 [PHP 网站](http://php.net/download-logos.php)。
Go[地鼠形象](https://github.com/egonelbre/gophers)来自 [@egonelbre](https://twitter.com/@egonelbre) 。