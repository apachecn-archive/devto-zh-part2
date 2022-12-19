# 如果出现以下情况，我应该何时使用一个衬垫...Go 中的 else 语句？

> 原文：<https://dev.to/joncalhoun/when-should-i-use-one-liner-ifelse-statements-in-go--2122>

> *本文原帖[calhoun . io](https://www.calhoun.io/one-liner-if-statements-with-errors/)T3】*

在使用 Go 几个星期后，你很可能会遇到单行的`if...else`语句。大多数情况下，您看到的第一个将用于错误处理，看起来像这样:

```
if err := doStuff(); err != nil {
  // handle the error here
} 
```

Enter fullscreen mode Exit fullscreen mode

`if`的第一位被称为[初始化语句](https://golang.org/doc/effective_go.html#if)，它可以用来设置局部变量。对于只返回错误的函数，初始化语句是一个非常有用的工具。错误仅限于处理它的`if`块，我们的代码最终变得更容易阅读。

但是什么时候使用这种方法比将代码分成多行更合适呢？对于什么时候应该或者不应该使用一行语句，有什么规则或者指导方针吗？

在这篇文章中，我们将探索使用初始化语句合适的情况，以及不合适的情况，并解释两者之间的一些细微差别。

## 分手长表情

第一部分可能有点争议。每个人对我们是否应该将长行分成多行，最大字符数是多少，以及这之间的其他事情都有不同的看法。 [Go Wiki](https://github.com/golang/go/wiki/CodeReviewComments#line-length) 有一节讨论了这个话题，我大体同意，建议你去看看。我会等的...

好吧，读书回来了？太好了！现在我们来看一些例子。

首先是长函数名的情况。最好的解决方法是重命名函数，但是您可能使用了另一个库，而这是不可能的。让我们面对现实吧，每隔一段时间，一个 Java 开发人员就会得到一些 Go 代码，我们不能把所有的时间都花在清理这些代码上。

```
// rather ugly
if err := thisHasAReallyLongSuperDescriptiveNameForSomeCrazyReason(); err != nil {
    // handle the error
}

// better
err := thisHasAReallyLongSuperDescriptiveNameForSomeCrazyReason()
if err != nil {
    // handle the error
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/gxLEZU_Uhmw](https://play.golang.org/p/gxLEZU_Uhmw)T3】*

假设我们不能重命名函数，我倾向于将这些调用放在一行中。同样，这不是一条一成不变的规则，但是很少发现这样的情况，使用几行代码并不比将函数调用这个庞然大物放在具有其他逻辑的一行中更清晰。

另一种可能遇到超长`if`语句的情况是接受许多参数的函数；如果您正在传递硬编码的字符串，这一点尤其正确。

同样，这可以通过多种方式解决，例如将参数换行，不使用单行语法。下面显示了几个例子，在您看到代码后，我们将对它们进行更多的讨论。

```
// pretty ugly
if err := manyArgs("This is a fairly long message about doing some stuff", "and we might provide even more data"); err != nil {
    panic(err)
}

// Fix 1: better, but the condition of the if statement is easy to miss
if err := manyArgs(
    "This is a fairly long message about doing some stuff",
    "and we might provide even more data"); err != nil {
    panic(err)
}

// Fix 2: probably the best solution if using the "one-liner" format
if err := manyArgs(
    "This is a fairly long message about doing some stuff",
    "and we might provide even more data",
); err != nil {
    panic(err)
}

// Fix 3: my personal preference here is not using the "one-liner" format
err := manyArgs(
    "This is a fairly long message about doing some stuff",
    "and we might provide even more data")
if err != nil {
    panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/bB1lvtn5dpJ](https://play.golang.org/p/bB1lvtn5dpJ)T3】*

在 Fix 1 中，发生了什么变得更加清晰，但是我不喜欢它，因为条件- `err != nil` -很容易在代码的其余部分丢失。

修复 2 是一个很大的改进。很容易忘记第二个参数后面的逗号，但好处是一旦添加了逗号，就可以在函数调用中添加新的参数，而无需更改现有的代码。当调用变量函数并向其添加另一段数据时，这非常方便。这里的另一个额外好处是，条件在它自己的行上，这使得在浏览代码时更容易阅读。

最后我们有我个人最喜欢的，修复 3；这类似于我们之前使用的修复——我们在自己的行上进行函数调用，然后使用该数据创建`if`块。对我来说，这读起来更清晰，因为`if`项更接近于它相关的地方——条件子句。在子句和`if`关键字之间增加一些距离，当它是真正的一行代码时，我不会感到困扰，但是在像这样的多行代码中，它就感觉不那么可读了。YMMV。

## 变量作用域

我们已经看到了分解`if`语句的一些原因，但是我们还需要讨论从`if`语句中分解初始化子句的后果。也就是我们需要讨论变量的取值范围。

当我们在 if 语句的初始化部分声明一个新变量时，该变量是在`if`语句的范围内声明的。这意味着它只在那个`if`语句的生命周期内可用。这方面的一个例子如下所示。

```
func main() {
    if x, err := demo(); err != nil {
        // handle the error
    }
    // this doesn't work because x is scoped to the if block
    fmt.Println(x)
}

func demo(args ...string) (int, error) {
    return 1, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/jj9MDxYINFh](https://play.golang.org/p/jj9MDxYINFh)T3】*

如果我们想访问前面例子中的`x`变量，我们需要在`if`语句之外声明它，否则一旦 If 语句完成，它就会消失。这也是为什么我们可以有一个名为`err`的现有变量，然后在 if 语句中重新声明它。新变量在一个新的作用域中，所以它是编译器允许的。

```
func main() {
    var err error
    if err := demo(); err != nil {
        fmt.Println("Error in if block:", err)
    }
    fmt.Println(err) // this will be nil still!
}

func demo(args ...string) error {
    return errors.New("on no!")
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/tASeTl2tlmK](https://play.golang.org/p/tASeTl2tlmK)T3】*

**警告:** *作用域内的变量与外部作用域内的变量同名有时会导致令人难以置信的错误。将它们与`if`块一起使用通常没问题，但是当您有包全局变量并在同名函数内创建一个新的、有作用域的变量时，它们可能会特别成问题。`:=`是声明新变量的一个有用的简写，但是理解在使用它时作用域是如何工作的很重要。*

作用域变量也可以在 if 语句的`else`块中访问。这可以通过用变量`x`修改我们之前的例子来说明。

```
if x, err := demo(); err != nil {
    // handle the error
} else {
    fmt.Println(x)
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/uRfCxR9bLSP](https://play.golang.org/p/uRfCxR9bLSP)T3】*

虽然这是完全有效的代码，但它很容易导致不太习惯的代码。按[有效走](https://golang.org/doc/effective_go.html#if)...

> 在 Go 库中，你会发现当一个`if`语句没有流入下一个语句时——也就是说，语句体以`break`、`continue`、`goto`或`return`结尾——不必要的`else`会被省略。

作为处理错误的一部分，返回检查错误的`if`语句是很常见的。也就是说，如果我们在前面的代码中添加了错误处理，它可能看起来像这样:

```
if x, err := demo(); err != nil {
    return err
} else {
    fmt.Println(x)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可能会返回错误，引起恐慌，记录下来，然后调用`os.Exit`，或者如果在`for`循环中，我们可能会`continue`，但是在所有这些场景中，我们都不会进入代码中的下一个语句。在这些情况下，声明我们的变量并在`if`范围之外调用`demo`函数，然后在 if 语句中使用它们更有意义。那么我们的代码就变成:

```
x, err := demo()
if err != nil {
    return err
}
fmt.Println(x) 
```

Enter fullscreen mode Exit fullscreen mode

虽然实际上是一样的，但是这段代码通常更容易阅读，并且如果我们在函数中需要它的话，继续使用`x`也更容易。

## 潜在烦恼

既然我们已经了解了作用域是如何工作的，我们需要看看当*而不是*使用一行 if 语句——未定义或已经定义的变量时可能发生的另一个潜在的麻烦。

假设您有以下代码:

```
err := demo1()
if err != nil {
    panic(err)
}
err = demo2()
if err != nil {
    panic(err)
}
err = demo3()
if err != nil {
    panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/UZtbUjmv1e5](https://play.golang.org/p/UZtbUjmv1e5)T3】*

在第一行中，我们声明了`err`变量，然后当我们调用`demo2`和`demo3`时，变量已经被声明了，所以我们使用`=`而不是`:=`，这将重新声明变量。

这段代码完全有效，但是如果我们正在编写代码，并且需要在对`demo1`的调用之前添加另一个函数调用*，会发生什么呢？也许我们需要事先打电话给`demo2`。*

嗯，我们可能会在函数的顶部添加类似下面的代码。

```
err := demo2()
if err != nil {
    panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/tqMQDQ_gmTe](https://play.golang.org/p/tqMQDQ_gmTe)T3】*

我们需要用这个函数调用声明`err`，因为它还没有被声明。记住，我们在原始代码中在调用`demo1`的之前添加了这个*，但是我们现在在同一个作用域中有两个`err`的声明；这将导致我们的编译器抱怨，我们的代码不会编译。* 

```
prog.go:8:6: no new variables on left side of := 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们需要编辑我们调用`demo1`的那一行，并更新代码以使用`=`而不是`:=`。

```
err := demo2()
if err != nil {
    panic(err)
}
err = demo1()
if err != nil {
    panic(err)
}
// ... unchanged 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/3vE1FnA763a](https://play.golang.org/p/3vE1FnA763a)T3】*

当您删除现有代码时，也会出现类似的麻烦。例如，如果我们不再需要第一个函数调用——目前是对`demo2`的调用——并且我们从代码中删除了它，我们将需要手动声明`err`变量，或者更新下一个使用它的函数调用，使用`:=`而不是`=`。

```
var err error
err = demo1()
if err != nil {
    panic(err)
}
err = demo2()
if err != nil {
    panic(err)
}
// ... unchanged 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑起来→[https://play.golang.org/p/MkTkEZoUlql](https://play.golang.org/p/MkTkEZoUlql)T3】*

如果您预计编辑代码会使使用`:=`变得很麻烦，那么可以考虑在函数中提前声明变量(就像我们在上一个例子中做的那样)。这在错误中尤其常见，因为您几乎总是需要一个变量来捕获错误，并且在第一次声明它之前需要添加新代码的情况并不少见。

有时`if`作用域变量也有助于降低这个问题出现的频率，但是正如我们前面看到的，我们不能总是在不牺牲可读性的情况下使用这些变量。不幸的是，没有一套我们可以遵循的规则来决定在任何时候什么是最好的方法；这最终成为一个判断性的决定。但是不要担心——随着你对围棋越来越熟悉，你会开始获得一些直觉，帮助你做出这样的决定。在那之前，继续练习吧！

**寻找练习项目在 Go 中编码？**

我几乎每周都会为我的免费课程“Gophercises”发布新的练习。在每个练习中，你都有一个相对较小的项目，需要你花一个小时到几个小时来完成。一旦你尝试了这个练习，你可以观看截屏视频，在那里我编写了一个解决方案，并解释了我在编写代码时在做什么。

无论你是自己解决问题，还是只是想看我编写解决方案，每一个练习都是精心策划的，教你一些独特的东西。有些旨在向您介绍新的库，有些介绍新的编码技术，还有一些侧重于更广泛的主题，如编写并发代码和进行基准测试。