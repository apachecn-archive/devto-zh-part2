# 通过编写测试来学习围棋:模仿

> 原文：<https://dev.to/quii/learn-go-by-writing-tests-mocking-fl4>

这是 WIP 项目[的第 7 篇文章，通过编写测试](https://github.com/quii/learn-go-with-tests)来学习 Go，其目的是熟悉 Go 并学习 TDD 的相关技术

*   [第一篇文章让你了解了 TDD](https://dev.to/quii/learn-go-by-writing-tests--m63)
*   [第二篇文章讨论了数组和切片](https://dev.to/quii/learn-go-by-writing-tests-arrays-and-slices-ahm)
*   [第三篇文章讲授结构、方法、接口&表驱动测试](https://dev.to/quii/learn-go-by-writing-tests-structs-methods-interfaces--table-driven-tests-1p01)
*   [第四篇文章展示了如何出错以及为什么指针有用](https://dev.to/quii/learn-go-by-writing-tests-pointers-and-errors-2kp6)
*   [第五篇文章向您展示了如何以及为什么要进行依赖注入](https://dev.to/quii/learn-go-by-writing-tests-dependency-injection-n7j)
*   [第六篇文章介绍了并发性](https://dev.to/gypsydave5/learn-go-by-writing-tests-concurrency--2ebk)

这一章是关于嘲讽的。即使你对围棋不是很熟悉，如果你想了解嘲讽，这篇文章应该是有帮助的。

# 嘲讽

你被要求编写一个程序，从 3 开始计数，在新的一行上打印每个数字(有 1 秒钟的停顿)，当它到达零时，它将打印“Go！”然后退出。

```
3
2
1
Go! 
```

Enter fullscreen mode Exit fullscreen mode

我们将通过编写一个名为`Countdown`的函数来解决这个问题，然后我们将它放入一个`main`程序中，这样它看起来就像这样:

```
package main

func main() {
    Countdown()
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是一个非常琐碎的程序，但是为了全面测试它，我们将需要一如既往地采用*迭代*、*测试驱动*的方法。

我说的迭代是什么意思？我们确保尽可能采取最小的步骤来拥有*有用的软件*。

我们不想花太多时间在理论上经过一些黑客攻击就能工作的代码上，因为这通常是开发人员陷入困境的原因。能够将需求分割得越小越好，这是一项重要的技能，这样你就可以拥有能够工作的软件。

以下是我们如何划分工作并对其进行迭代

*   打印 3
*   打印 3 个带走！
*   每行之间等待一秒钟

## 先写测试

我们的软件需要打印到标准输出，我们在 DI 部分看到了如何使用 DI 来方便测试。

```
func TestCountdown(t *testing.T) {
    buffer := &bytes.Buffer{}

    Countdown(buffer)

    got := buffer.String()
    want := "3"

    if got != want {
        t.Errorf("got '%s' want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你对类似`buffer`的东西不熟悉，请重新阅读前面的部分。

我们知道我们希望我们的`Countdown`函数在某个地方写数据，而`io.Writer`实际上是作为 Go 中的一个接口捕获数据的方式。

*   在`main`中，我们将发送到`os.Stdout`,这样我们的用户就可以看到打印在终端上的倒计时
*   在测试中，我们将发送到`bytes.Buffer`,这样我们的测试可以捕获正在生成的数据

## 试运行测试

`./countdown_test.go:11:2: undefined: Countdown`

## 编写运行测试所需的最少代码，并检查失败的测试输出

定义`Countdown`

```
func Countdown() {} 
```

Enter fullscreen mode Exit fullscreen mode

再试一次

```
./countdown_test.go:11:11: too many arguments in call to Countdown
    have (*bytes.Buffer)
    want () 
```

Enter fullscreen mode Exit fullscreen mode

编译器会告诉你你的函数签名可能是什么，所以更新它。

```
func Countdown(out *bytes.Buffer) {} 
```

Enter fullscreen mode Exit fullscreen mode

`countdown_test.go:17: got '' want '3'`

完美！

## 写足够的代码让它通过

```
func Countdown(out *bytes.Buffer) {
    fmt.Fprint(out, "3")
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用的是`fmt.Fprint`，它接受一个`io.Writer`(就像`*bytes.Buffer`)并向它发送一个`string`。测试应该会通过。

## 重构

我们知道虽然`*bytes.Buffer`可以工作，但是最好使用通用接口。

```
func Countdown(out io.Writer) {
    fmt.Fprint(out, "3")
} 
```

Enter fullscreen mode Exit fullscreen mode

重新运行测试，他们应该会通过。

为了完成任务，现在让我们将函数连接到一个`main`中，这样我们就有一些工作软件来保证我们正在取得进展。

```
package main

import (
    "fmt"
    "io"
    "os"
)

func Countdown(out io.Writer) {
    fmt.Fprint(out, "3")
}

func main() {
    Countdown(os.Stdout)
} 
```

Enter fullscreen mode Exit fullscreen mode

试着运行这个程序，你会惊讶于你的手工。

是的，这看起来微不足道，但这种方法是我推荐给任何项目的。取一小块功能，让它端到端地工作，由测试支持。

接下来我们可以让它打印 2，1，然后“走！”。

## 先写测试

通过投资使整个管道正常工作，我们可以安全而容易地迭代我们的解决方案。我们将不再需要停止并重新运行程序来确信它的工作，因为所有的逻辑都被测试了。

```
func TestCountdown(t *testing.T) {
    buffer := &bytes.Buffer{}

    Countdown(buffer)

    got := buffer.String()
    want := `3
2
1
Go!`

    if got != want {
        t.Errorf("got '%s' want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

反勾号语法是创建`string`的另一种方式，但是它允许您放置像换行符这样的东西，这对我们的测试来说是完美的。

## 试运行测试

```
countdown_test.go:21: got '3' want '3
        2
        1
        Go!' 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func Countdown(out io.Writer) {
    for i := 3; i > 0; i-- {
        fmt.Fprintln(out, i)
    }
    fmt.Fprint(out, "Go!")
} 
```

Enter fullscreen mode Exit fullscreen mode

使用一个`for`循环，用`i--`倒计数，用`fmt.Fprintln`打印到`out`，我们的数字后面跟一个换行符。最后用`fmt.Fprint`发“走！”向后

## 重构

除了将一些神奇的值重构为命名的常量之外，没有什么可以重构的。

```
const finalWord = "Go!"
const countdownStart = 3

func Countdown(out io.Writer) {
    for i := countdownStart; i > 0; i-- {
        fmt.Fprintln(out, i)
    }
    fmt.Fprint(out, finalWord)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在运行这个程序，你应该得到想要的输出，但是我们没有一个 1 秒钟暂停的戏剧性倒计时。

让我们用`time.Sleep`来实现它。尝试将它添加到我们的代码中。

```
func Countdown(out io.Writer) {
    for i := countdownStart; i > 0; i-- {
        time.Sleep(1 * time.Second)
        fmt.Fprintln(out, i)
    }

    time.Sleep(1 * time.Second)
    fmt.Fprint(out, finalWord)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行这个程序，它就会按照我们的要求运行。

## 嘲讽

测试仍然通过，软件工作正常，但是我们有一些问题:

*   我们的测试需要 4 秒钟运行。
    *   每一篇关于软件开发的前瞻性思考文章都强调了快速反馈循环的重要性。
    *   缓慢的测试毁了开发人员的生产力。
    *   想象一下，如果需求变得更加复杂，需要更多的测试。我们对在`Countdown`的每个新测试中加入 4s 感到满意吗？
*   我们还没有测试函数的一个重要属性。

我们对需要提取的`Sleep` ing 有依赖性，这样我们就可以在测试中控制它。

如果我们可以*模仿* `time.Sleep`，我们可以使用*依赖注入*来代替“真实的”`time.Sleep`，然后我们可以**窥探调用**来对它们做出断言。

## 先写测试

让我们将依赖关系定义为一个接口。这让我们可以在测试中使用一个*真实*睡眠者`main`和一个*间谍睡眠者*。通过使用一个接口，我们的`Countdown`函数对此很明显，并为调用者增加了一些灵活性。

```
type Sleeper interface {
    Sleep()
} 
```

Enter fullscreen mode Exit fullscreen mode

我做了一个设计决定，我们的`Countdown`函数不负责
睡眠时间的长短。这至少暂时简化了我们的代码
,意味着我们功能的用户可以按照他们喜欢的方式配置睡眠。

现在我们需要制作一个*模拟*供我们的测试使用。

```
type SpySleeper struct {
    Calls int
}

func (s *SpySleeper) Sleep() {
    s.Calls++
} 
```

Enter fullscreen mode Exit fullscreen mode

*间谍*是一种*模仿*，它可以记录一个依赖是如何被使用的。他们可以记录发送的参数、次数等。在我们的例子中，我们跟踪调用了多少次`Sleep()`,这样我们可以在测试中检查它。

更新测试以注入对我们的 Spy 的依赖，并断言 sleep 已经被调用了 4 次。

```
func TestCountdown(t *testing.T) {
    buffer := &bytes.Buffer{}
    spySleeper := &SpySleeper{}

    Countdown(buffer, spySleeper)

    got := buffer.String()
    want := `3
2
1
Go!`

    if got != want {
        t.Errorf("got '%s' want '%s'", got, want)
    }

    if spySleeper.Calls != 4 {
        t.Errorf("not enough calls to sleeper, want 4 got %d", spySleeper.Calls)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
too many arguments in call to Countdown
    have (*bytes.Buffer, Sleeper)
    want (io.Writer) 
```

Enter fullscreen mode Exit fullscreen mode

## 编写运行测试所需的最少代码，并检查失败的测试输出

我们需要更新`Countdown`来接受我们的`Sleeper`

```
func Countdown(out io.Writer, sleeper Sleeper) {
    for i := countdownStart; i > 0; i-- {
        time.Sleep(1 * time.Second)
        fmt.Fprintln(out, i)
    }

    time.Sleep(1 * time.Second)
    fmt.Fprint(out, finalWord)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你再试一次，你的`main`将因为同样的原因
不再编译

```
./main.go:26:11: not enough arguments in call to Countdown
    have (*os.File)
    want (io.Writer, Sleeper) 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建一个*真实的* sleeper，它实现了我们需要的接口

```
type ConfigurableSleeper struct {
    duration time.Duration
}

func (o *ConfigurableSleeper) Sleep() {
    time.Sleep(o.duration)
} 
```

Enter fullscreen mode Exit fullscreen mode

我决定做一点额外的努力，使我们真正的 sleeper 是可配置的，但是你也可以很容易地不去麻烦，硬编码为 1 秒。

然后我们可以在我们的实际应用中使用它，就像这样

```
func main() {
    sleeper := &ConfigurableSleeper{1 * time.Second}
    Countdown(os.Stdout, sleeper)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

测试现在正在编译，但是没有通过，因为我们仍然在调用`time.Sleep`而不是依赖项中的注入。让我们解决这个问题。

```
func Countdown(out io.Writer, sleeper Sleeper) {
    for i := countdownStart; i > 0; i-- {
        sleeper.sleep()
        fmt.Fprintln(out, i)
    }

    sleeper.sleep()
    fmt.Fprint(out, finalWord)
} 
```

Enter fullscreen mode Exit fullscreen mode

测试应该通过，不再需要 4 秒钟。

### 还有些问题

还有一个重要的属性我们没有测试。

`Countdown`应在第一次打印前休眠，然后在每次打印后休眠，直到最后一次，例如:

*   `Sleep`
*   `Print N`
*   `Sleep`
*   `Print N-1`
*   `Sleep`
*   等等

我们的最新变化只是断言它已经休眠了 4 次，但是这些休眠可能会无序发生

在写测试的时候，如果你不确定你的测试是否给了你足够的信心，那就打破它！(但是，首先要确保您已经将您的更改提交到了源代码控制中)。将代码更改为以下内容

```
func Countdown(out io.Writer, sleeper Sleeper) {
    for i := countdownStart; i > 0; i-- {
        sleeper.Sleep()
    }

    for i := countdownStart; i > 0; i-- {
        fmt.Fprintln(out, i)
    }

    sleeper.Sleep()
    fmt.Fprint(out, finalWord)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行您的测试，即使实现是错误的，它们仍然应该通过。

让我们用一个新的测试再次使用刺探来检查操作的顺序是否正确。

我们有两个不同的依赖项，我们希望将它们的所有操作记录到一个列表中。所以我们将为他们两个创建*一个间谍。* 

```
type CountdownOperationsSpy struct {
    Calls []string
}

func (s *CountdownOperationsSpy) Sleep() {
    s.Calls = append(s.Calls, sleep)
}

func (s *CountdownOperationsSpy) Write(p []byte) (n int, err error) {
    s.Calls = append(s.Calls, write)
    return
}

const write = "write"
const sleep = "sleep" 
```

Enter fullscreen mode Exit fullscreen mode

我们的`CountdownOperationsSpy`实现了`io.Writer`和`Sleeper`，将每个调用记录到一个片段中。在这个测试中，我们只关心操作的顺序，所以只将它们记录为命名操作的列表就足够了。

我们现在可以在测试套件中添加一个子测试。

```
t.Run("sleep after every print", func(t *testing.T) {
    spySleepPrinter := &CountdownOperationsSpy{}
    Countdown(spySleepPrinter, spySleepPrinter)

    want := []string{
        sleep,
        write,
        sleep,
        write,
        sleep,
        write,
        sleep,
        write,
    }

    if !reflect.DeepEqual(want, spySleepPrinter.Calls) {
        t.Errorf("wanted calls %v got %v", want, spySleepPrinter.Calls)
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个测试现在应该失败了。将其还原，新的测试应该会通过。

我们现在有两个测试在监视`Sleeper`，所以我们现在可以重构我们的测试，一个测试正在打印什么，另一个确保我们在打印之间睡觉。最后，我们可以删除我们的第一个间谍，因为它不再被使用。

```
func TestCountdown(t *testing.T) {

    t.Run("prints 3 to Go!", func(t *testing.T) {
        buffer := &bytes.Buffer{}
        Countdown(buffer, &CountdownOperationsSpy{})

        got := buffer.String()
        want := `3
2
1
Go!`

        if got != want {
            t.Errorf("got '%s' want '%s'", got, want)
        }
    })

    t.Run("sleep after every print", func(t *testing.T) {
        spySleepPrinter := &CountdownOperationsSpy{}
        Countdown(spySleepPrinter, spySleepPrinter)

        want := []string{
            sleep,
            write,
            sleep,
            write,
            sleep,
            write,
            sleep,
            write,
        }

        if !reflect.DeepEqual(want, spySleepPrinter.Calls) {
            t.Errorf("wanted calls %v got %v", want, spySleepPrinter.Calls)
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经正确地测试了我们的函数和它的两个重要属性。

## 但嘲讽难道不是恶吗？

你可能听说过嘲讽是邪恶的。就像软件开发中的任何东西一样，它可以被用来作恶，就像 DRY 一样。

当人们不听他们的测试并且不尊重重构阶段时，他们通常会陷入糟糕的状态。

如果你的模拟代码变得复杂，或者你不得不模拟出很多东西来测试某样东西，你应该*倾听*这种不好的感觉，并思考你的代码。通常这是一个迹象

*   你正在测试的东西是不得不做太多的事情。
    *   把模块拆开，这样它做得更少
*   它的依赖关系太细粒度了
    *   考虑如何将这些依赖关系整合到一个有意义的模块中。
*   您的测试过于关注实现细节
    *   倾向于测试预期的行为，而不是实现

通常很多嘲笑指向你代码中糟糕的抽象。

**人们在这里看到的是 TDD 的弱点，但它实际上是一个优势**，糟糕的测试代码往往是糟糕设计的结果，或者更好地说，设计良好的代码易于测试。

### 但是模拟和测试仍然让我的生活很艰难！

遇到过这种情况吗？

*   你想做一些重构
*   为了做到这一点，你最终要改变很多测试
*   你质疑 TDD 并在媒体上发表了一篇题为“嘲笑被认为是有害的”的帖子

这通常表明你测试了太多的*实现细节*。试着让你的测试测试*有用的行为*，除非实现对系统如何运行真的很重要。

有时候很难知道到底要测试什么水平，但是这里有一些我试着遵循的思考过程和规则

*   **重构的定义是代码改变，但行为保持不变**。如果你已经决定做一些重构，理论上你应该能够在没有任何测试改变的情况下提交。所以在写测试的时候，问问你自己
    *   我是在测试我想要的行为还是实现细节？
    *   如果我要重构这段代码，我会对测试做很多改动吗？
*   虽然 Go 允许你测试私有函数，但我会避免使用它，因为私有函数与实现有关。
*   我觉得如果一个测试有超过 3 个模拟，那么这就是一个危险信号，是时候重新考虑设计了
*   谨慎使用间谍。Spies 让你看到你正在编写的算法的内部，这可能非常有用，但这意味着你的测试代码和实现之间的耦合更紧密。如果你打算窥探这些细节，请确保你真的关心这些细节

和往常一样，软件开发中的规则并不是真正的规则，也可能有例外。[鲍勃大叔的文章《何时嘲弄》](https://8thlight.com/blog/uncle-bob/2014/05/10/WhenToMock.html)有一些很好的指点。

## 包装完毕

### 更多关于 TDD 的方法

*   当面对不那么琐碎的例子时，把问题分解成“垂直的薄片”。尽可能快地让*的工作软件得到测试*的支持，以避免陷入兔子洞，采取“大爆炸”的方法。
*   一旦你有了一些可用的软件，逐步迭代直到你得到你需要的软件应该会更容易。

### 嘲讽

*   没有嘲讽，你代码的重要部分将不会被测试。在我们的例子中，我们无法测试我们的代码是否在每次打印之间暂停，但是有无数其他的例子。调用一个*可以*失败的服务？想要在特定的状态下测试您的系统？很难在不嘲讽的情况下测试这些场景。
*   没有模拟，你可能不得不建立数据库和其他第三方的东西，只是为了测试简单的业务规则。你可能会有缓慢的测试，导致缓慢的反馈循环。
*   由于不得不启动数据库或网络服务来测试某些东西，由于这些服务的不可靠性，你可能会有**脆弱的测试**。

一旦开发人员学会了模仿，就很容易根据系统的工作方式(T1)而不是工作方式(T3)来过度测试系统的每个方面。时刻关注**你的测试的价值**以及它们对未来重构的影响。

在这篇关于嘲讽的文章中，我们只提到了**间谍**，这是一种嘲讽。有不同种类的模拟。[鲍勃叔叔在一篇非常易读的文章](https://8thlight.com/blog/uncle-bob/2014/05/14/TheLittleMocker.html)中解释了这些类型。在后面的章节中，我们将需要编写依赖其他人获取数据的代码，这也是我们将展示**存根**的地方。