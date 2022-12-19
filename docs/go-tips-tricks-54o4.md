# Go 提示和技巧

> 原文：<https://dev.to/farisj/go-tips-tricks-54o4>

在几年几乎完全用 Ruby on Rails 和 Javascript 编程之后，我很幸运地扩展了我的技能，并参与了一些用 Golang 编写的项目。Golang，也被称为 [Go](https://golang.org/) ，是谷歌的开源编程语言，作为一种有弹性、有主见的后端语言，越来越受欢迎。在我看来，一些更有趣的特性是:

*   编辑
*   静态类型化
*   强并发支持
*   内置文档和测试工具

关于 Go 有很多很棒的东西，在用 Go 开发项目一段时间后，我发现了一些关于这种语言的好东西，我想和大家分享一下。我已经有一段时间没有写博客了，所以希望这篇文章能点燃我的热情，鼓励我再次把写技术博客作为一种爱好。至于这篇文章，这不是一个令人敬畏的特性和库的详尽列表(太多了！)，但这是一个开始。所以，让我们开始吧！

[![Starting Party](img/050ec7ac268062d44d7e4a54f42ec88c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fe48UIwA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://farisj.github.io/asseimg/go-tips-tricks/party.gif)

**注:**这篇博文假设了一些围棋的基础知识。

# 在地图上迭代时强制随机化

假设您的代码中有一个`map`，并且您想要显示该地图的内容。一种常见的方法是迭代映射的`range`和`fmt.Println()`键/值。

```
func main() {
  m := map[int]bool{
    1: true,
    5: true,
    99: true,
    101: false,
    14: true,
  }

  for k, v := range m {
    fmt.Printf("%d: %t\n", k, v)
  }
} 
```

人们或多或少普遍认为依赖有序地图是个坏主意，因为您依赖的功能不是该数据结构的主要目的。Golang 同意这一点，并积极执行。事实上，*根据运行时生成的随机种子，主动*随机化地图的排序。

在这个 [Go 游乐场](https://play.golang.org/p/8IdNVrPsleU)亲自尝试一下(顺便说一下，这是一个很棒的工具！).在那个操场上，我们正在手动设置`rand.Seed()`，这会影响语言使用的伪随机算法。当您使用相同的种子重新运行函数时，地图每次都会以相同的方式打印出来。但是，当种子发生变化时，排序也会发生变化。

种子随着每一个`go run`而改变(除了在 Go Playground 上，默认情况下它是一个固定的种子，这就是为什么上面的代码手动改变种子)，所以你永远不会在生产中得到相同的订单。这是 Go 执行这一良好行为的方式！

# 嵌入和组合结构

看到 Go 的组合方法以及如何通过“嵌入”结构来共享方法，我印象深刻。

考虑我们有两种类型，`User`和`Admin` :

```
type User struct {
  Name string
}

type Admin struct {
  User User
  Permissions map[string]string
} 
```

在上面的例子中，有一个`User`和一个`Admin`，其中`Admin`有一个`User`属性以及一些额外的`Permissions.`

在这里，人们可以通过`admin.User.Name`访问`Admin`的`Name`(假设有一个名为`admin`的实例化`Admin`)。

然而，考虑一下在前面的例子中删除了*的一个*单词，这完全改变了`Admin`的方法表面:

```
type User struct {
  Name string
}

type Admin struct {
  User
  Permissions map[string]string
} 
```

你注意到了吗？`Admin`结构中的第二个`User`被移除。这意味着`User`被“嵌入”在`Admin`中，并且它的所有方法也被“提升”到`Admin`。这意味着可以通过`admin.Name`引用用户的名字——不需要中间调用`User`！

我发现这非常简洁——它绝对可以用来共享方法，并有助于保持代码的整洁。

这种技术的另一个额外用途是“组合”类型，它包括嵌入各种类型来创建其他类型/接口。一个很好的例子是在`io`库中的`ReadWriter`，我直接从 [Go 文档](https://godoc.org/io#ReadWriter) :
中提取了它的代码

```
type ReadWriter interface {
    Reader
    Writer
} 
```

从上面的定义中我可以看出，`ReadWriter`是一个接口，它必须包含在`Reader`和`Writer`上定义的所有函数，这些函数是在别处定义的。知道了围棋的构图方式，那就很有道理了！它还允许`Reader`和`Writer`彼此独立使用，这是一个优点。

# 测试

## 将测试标志用于您的优势

`testing`包提供了一些方法，让测试套件自己知道它是如何运行的。可以与`go test`一起使用的`-v`和`-short`标志被绑定到包本身，用户可以根据这些标志是否存在来编写不同行为的测试。

考虑一个如下所示的包并进行测试:

```
import "fmt"

func main() {
  return fmt.Println(helloWorld())
}

func helloWorld() string{
  return fmt.Sprintf("Hello world")
}

import (
  "testing"
  "fmt"
)

func TestHelloWorld(t *testing.T) {
  if testing.Short() {
    t.Skip("Short test suite, skipping test.")
  }

  if testing.Verbose() {
    fmt.Println("About to test helloWorld()...")
  }

  exp := "Hello world"
  got := helloWorld()
  if exp != got {
    t.Errorf("Expected %s, got %s", exp, got)
  }
} 
```

在上面的例子中，你会看到`testing`包公开了一个`testing.Short()`和`testing.Verbose()`方法，它们可以被用来指定测试逻辑。这些直接对应`go test -short`和`go test -v`。尽管这只是一个小特性，但它允许程序员对他们的测试套件进行更有表现力的控制，我很欣赏这一点！

## 存根测试&保持代码松散耦合

Go 程序面临的一个常见问题是，由于 Go 在编译时严格的类型检查，在测试中缺乏灵活性。当 Go 抱怨测试中的类型问题时，它所做的实际上是暴露了一个被称为“紧耦合”代码的常见问题，其中函数和结构显式地依赖于内部的其他构造，这阻止了它们被孤立地测试。

这里有一个例子:假设你正在构建一个 Go 应用程序，它点击一个 API，然后做一些工作。让我们称这个结构为`Worker` :

```
type Worker struct {
  APIClient *Client
}

func (w Worker) Work(input string) {
  // Some work here...
  result, err := w.APIClient.Update(input)
  if err != nil {
    return nil, result
  }
  return result, nil
} 
```

这看起来很棒，但是考虑一下这个结构的测试会是什么样子:

```
import (
  "testing"
)

func TestWork(t *Testing.T) {
  // Setup test...
  worker := Worker(&Client{})
  result, err := worker.Work(input)

  if err != nil {
    t.Fatalf("Error: %s", err)
  }
  // Other assertions...
} 
```

实际调用上面的`Work()`函数的问题是，它在内部使用了`Client.Update()`方法，这不是我们在这里要测试的东西！我们只是想确保客户正在与`Update(input)`通话。在 Rails 的`rspec`测试中，清除这一点非常容易，因为我们可以说类似于

```
expect_any_instance_of(Client).to receive(:update).with(input).and_return(result, nil) 
```

但是在 Go 中我们不能这么容易地 stub，因为如果我们试图用真实的`Client` struct 之外的任何东西来实例化一个`Worker`，Go 编译器会很不高兴。

因为`Worker`和`Client`是紧密耦合的，我们不能在不构建另一个的情况下测试一个。让我们试着编辑一下`Worker`结构，使它不那么直接依赖于`Client`结构。相反，我们可以给`Worker`一个`interface`，它看起来像`Client`并且有一个`Update`方法，就像这样:

```
type Worker struct {
  APIClient DataLayer
}

type DataLayer interface {
 Update(string) (string, err)
} 
```

这样，我们可以在测试中制作一个*假的*客户端，绕过真实的`Client`结构中发生的所有逻辑。(显然，我们必须单独测试😉)我们更新后的测试可能是这样的:

```
import (
  "testing"
)

type FakeClient struct {
  updateCalled bool
}

func (f *FakeClient) Update (string, error) {
  f.updateCalled = true
  return "", nil
}

func TestWork(t *testing.T) {
  // Setup test...
  client := &FakeClient{}
  worker := Worker(client)
  _, err := worker.Work(input) // Goes out an hits an API

  if client.updateCalled != true {
    t.Fatal("Expected Client.Update() to be called")
  }
  // Other assertions...
} 
```

现在，我们可以使用我们的存根`Client`类来验证`Worker`的内部行为是否正确，而不必担心`Client`的细节！

与 Ruby 或 Javascript 不同，在 Ruby 或 Javascript 中，测试框架是由外部各方构建的，旨在为他们的语言提供丰富的测试文化，Go 的测试功能内置在默认库中。Go 测试最终通常会产生一些需要额外设置的套件，但是好处是程序员不需要学习额外的测试 DSL 这只是 Go 代码！编写与执行代码如此接近的测试有助于使您正在处理的内容更加清晰。

# 标杆管理

在 Go 和所有其他语言中，基准测试是评估代码性能的过程。

在 Go 中，基准测试的构造类似于传统测试。基准测试遵循一些严格的准则，看起来是这样的:

```
func Fib(n int) int {
  if n < 2 {
    return n
  }
  return Fib(n-1) + Fib(n-2)
}

import "testing"

func BenchmarkFib(b *testing.B) {
  for n := 0; n < b.N; n++ {
    Fib(20) // run the Fib function b.N times
  }
} 
```

在基准测试中，函数以前缀`Benchmark`开始，并带有一个指向`testing.B`的指针，而不是像逻辑测试那样的`testing.T`。测试通常包括一个到`b.N`的循环，其中基准测试控制什么是`N`以及测试运行的次数。测试可以使用`-bench`标志运行，即:

```
go test -bench=. 
```

这里的`.`是一个正则表达式，告诉去板凳一切！如果您有一个复杂的套件，您可以运行一部分测试，并根据您的喜好进行测试！结果看起来会像这样:

```
pkg: farisj/mypackage/fib
BenchmarkFib-4 30000 49477 ns/op
PASS
ok farisj/mypackage/fib 1.996s 
```

例如，这种类型的工作在重构时非常有用。对两种不同的算法进行基准测试可以帮助程序员做出工程决策，这些决策不仅由他们自己的直觉支持，也由数字支持！

我个人在基准测试方面遇到的一个问题是，我已经习惯了这样一种观点，即给出的数字是相对于我的机器而言的，代码运行没有客观的“速度”。不仅有许多运行 Go 代码的不同平台/处理器，它们的性能各不相同，而且通常还有许多其他进程在运行，它们会耗尽资源。如果按照我的方式，代码会有一些客观的“分数”,但事实就是如此！我不得不相信不同功能的相对性能，并希望我的计算机处理器能够处理它。

[![Fox Meditating](img/e1ed840acc6127731a906f7db8188085.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e6cCtE_2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://farisj.github.io/asseimg/go-tips-tricks/zen.gif)

(我试图接受我无法改变的事情的动作镜头。)

# 使用 Delve 进行调试

[Delve](https://github.com/derekparker/delve) 是一个 Go 包，启动一个交互式会话，用户可以在函数中设置断点，评估变量，并实时单步调试代码。它类似于 Ruby 中的 [binding.pry](https://pryrepl.org/) ，尽管有一些不同。从我目前看到的来看，`delve`比`binding.pry`更复杂一点，但是嘿，那只是对你来说的 Go vs Ruby！这肯定是一种权衡。

给定下面的示例程序，我们可以使用`delve`来检查我们的变量:

```
func (user User) String() string {
  return fmt.Sprintf("%s %s", user.First, user.Last)
}

func main() {
  names := []string{
    "Steven",
    "Connie",
    "Onion",
  }
  for index := 0; index < 3; index++ {
    name := names[index]
    message := Hello(name, index == 0)
    fmt.Printf(message)
  }
}

func Hello(name string, firstTime bool) string {
  if firstTime {
    return fmt.Sprintf("Hello %s. You're first!\n", name)
  }
  return fmt.Sprintf("Hello %s. Happy to see you!\n", name)
} 
```

一旦你安装好了它(查看[文档](https://github.com/derekparker/delve/tree/master/Documentation/installation))，Delve 就会和
一起运行

```
dlv debug ./main.go 
```

其中`./main.go`是应用程序入口点。从那里，会话开始，在那里您可以设置断点，评估变量等。以下是一些基本要素:

*   用`breakpoint`命令设置一个断点，并指定函数名或行号，即:

```
break main.go:25 
```

或者

```
break main.Hello 
```

一旦设置了断点，运行`continue`启动程序。从那里，它将开始正常执行代码，并在到达设置的断点时停止。假设我们在`Hello()`上设置了一个断点，我们会看到调试器在那里等待输入:

```
(dlv) continue
> main.Hello() ./main.go:25 (hits goroutine(1):1 total:1) (PC: 0x1088ffb)
    20: message := Hello(name, index == 0)
    21: fmt.Printf(message)
    22: }
    23: }
    24:
=> 25: func Hello(name string, firstTime bool) string {
    26: if firstTime {
    27: return fmt.Sprintf("Hello %s. You're first!\n", name)
    28: }
    29: return fmt.Sprintf("Hello %s. Happy to see you!\n", name)
    30: } 
```

然后，可以输入`firstTime`来查看那个布尔值！点击`continue`并返回到该断点后，该值将从`true`变为`false`。

Delve 似乎是调试应用程序的一个非常有效和有用的工具，它比简单的`fmt.Println()`调试提供了更多的灵活性和控制。也就是说，到目前为止我已经完成了`fmt.Println()`调试，所以我还没有直接发现它的有用性。我在等着有一天我会开发一个非常复杂的应用程序，而`fmt.Println`就是不能胜任！

# 总结

Golang 是一种强大的语言，有很多约定，但也有很多内置的开发工具。有了富于表现力和高度固执己见的语法和结构，就可以很容易地投入到一个项目中并开始做出贡献，而无需花费太多时间来解开任何复杂的元编程约定、猴子补丁或存根方法。最近在 Rails 和 Go 项目之间来回切换，我可以看到 Go 的底层语法和静态类型是多么具有挑战性，但事实是这些语言选择意味着 bug 隐藏的地方更少了！

尝试一门新的语言可能会令人望而生畏，但是 Go 充满活力的社区和一流的文档工具让入门变得容易多了。我肯定会推荐任何对后端编程感兴趣的人尝试一下 Go，看看它到底是怎么回事！

感谢大家花时间阅读我的博客文章，强调了我对围棋的一些了解。希望我很快会带着另一个信息丰富的帖子回来！