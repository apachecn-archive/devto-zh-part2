# 当零不等于零时

> 原文：<https://dev.to/joncalhoun/when-nil-isnt-equal-to-nil-5c7b>

> *这篇文章最初发表在我的网站 [calhoun.io](https://www.calhoun.io/when-nil-isnt-equal-to-nil/) 上，源于[在围棋论坛](https://forum.golangbridge.org/t/a-nil-a-b-b-nil-with-pointers-and-interface/10593)上提出的一个问题。*

在本文中，我们将探讨一些情况，当我们使用 Go 的`==`操作比较看起来与开发者相等的变量时，它们将被评估为假。我们还将讨论为什么会发生这种情况，这有望使您在自己的代码中更容易避免遇到这种问题。

首先让我们看一个例子来说明我的意思。假设我们有两个变量，每个变量都有自己的类型，但是每个变量都被赋予了硬编码的值`nil`。

```
var a *int = nil
var b interface{} = nil 
```

Enter fullscreen mode Exit fullscreen mode

你期望下面的评价是什么？

```
fmt.Println("a == nil:", a == nil)
fmt.Println("b == nil:", b == nil)
fmt.Println("a == b:", a == b) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/2I6FQ_j5gFc](https://play.golang.org/p/2I6FQ_j5gFc)T3】*

如果你想在 Go playground 上运行代码来验证你可以，但是这里是实际的输出:

```
a == nil: true
b == nil: true
a == b: false 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们快速看看另一个非常相似但略有不同的例子。我们要改变`b`的初始值。

```
var a *int = nil
// This is the only change - we assign b to a instead
// of the hard-coded nil value.
var b interface{} = a

fmt.Println("a == nil:", a == nil)
fmt.Println("b == nil:", b == nil)
fmt.Println("a == b:", a == b) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/Tj-ImVLqMtx](https://play.golang.org/p/Tj-ImVLqMtx)T3】*

同样，您期望的输出是什么？下面是更新后的输出，用于检查您的答案。

```
a == nil: true
b == nil: false
a == b: true 
```

Enter fullscreen mode Exit fullscreen mode

## 这到底是怎么回事？

这种现象有点难以解释，但是不，这不是语言中的一个错误，也不是随机的/不可思议的/或者别的什么。有一些明确的规则被应用，我们只是需要一些时间来理解它们。之后，这一切都有意义了，你会明白为什么你偶尔会看到人们这样写代码:

```
if a == nil {
  b = nil
} 
```

Enter fullscreen mode Exit fullscreen mode

而不是只把`b`赋给`a`。

我们首先需要明白的是，Go 中的每个指针都有两条基本信息；指针的类型，以及它所指向的值。我们将把它们表示为向前移动的一对`(type, value)`。

事实上，每个指针变量都需要一个类型，这就是为什么我们不能在没有声明类型的情况下将一个零值赋给一个变量。也就是说，下面的代码不会编译。

```
// This does not work because we do not know the type
n := nil 
```

Enter fullscreen mode Exit fullscreen mode

为了编译这段代码，我们必须使用一个类型化的指针，并将其赋值为 nil:

```
var a *int = nil
var b interface{} = nil 
```

Enter fullscreen mode Exit fullscreen mode

这两个变量都有类型。我们甚至可以使用`fmt.Printf`函数打印出这些类型，看看它们是什么。

```
var a *int = nil
var b interface{} = nil

fmt.Printf("a=(%T, ...)\n", a)
fmt.Printf("b=(%T, ...)\n", b) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/wCdObTPinJj](https://play.golang.org/p/wCdObTPinJj)T3】*

**注** : *`%T`用于打印出带有`fmt.Printf`的值的类型。你可以在文档中读到更多关于这些特殊字符[的信息。](https://golang.org/pkg/fmt/#hdr-Printing)*

这段代码的输出如下所示。

```
a=(*int, ...)
b=(<nil>, ...) 
```

Enter fullscreen mode Exit fullscreen mode

似乎当我们将硬编码值`nil`赋给`*int`变量`a`时，该类型将被设置为定义`a`变量- `*int`时使用的相同类型。有道理。

第二个变量`b`，有点令人困惑。它的类型是`interface{}`(即[空接口](https://www.calhoun.io/how-do-interfaces-work-in-go/)，但是我们打印出 nil 值时给出的类型是`<nil>`。这是怎么回事？

简而言之，因为我们使用空接口，所以任何类型都可以满足。`<nil>`类型在技术上是一个类型，它满足空接口，所以在编译器无法确定其他类型信息时使用。

好了，我们知道所有的指针都有一个关联的`(type, value)`，我们也看到了当我们给每个变量赋值`nil`时会发生什么。现在让我们看看当我们使用`a`变量而不是硬编码的`nil`将`b`赋值为零时，这些类型是什么。

```
var a *int = nil
var b interface{} = a

fmt.Printf("a=(%T, ...)\n", a)
fmt.Printf("b=(%T, ...)\n", b) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/j86pv3lnd58](https://play.golang.org/p/j86pv3lnd58)T3】*

啊...看来`b`又有新类型了。

之前，当我们将`b`赋给硬编码的 nil 值时，我们没有类型信息。现在我们将`b`赋值给值`a`，这不再是真的。我们确切地知道该使用什么类型的信息——不管是什么被`a`变量所使用！

> **到目前为止的快速总结...**
> 
> *   所有指针都有类型和它们所指向的值。
> *   当我们将一个变量赋给硬编码的`nil`值时，编译器必须确定要使用的正确类型。
> *   当我们将一个变量赋给另一个 nil 变量时，可以根据另一个变量的类型来确定它的类型。

## 当我们检查相等时会发生什么？

既然我们已经了解了这些类型是如何被确定的，那么让我们看看当我们在代码中检查相等性时会发生什么。

我们将从给`a`和`b`分配硬编码的`nil`值开始。之后，我们将看到一个类似的代码片段，其中`b`被赋给了变量`a`。

```
var a *int = nil
var b interface{} = nil

// We will print out both type and value here
fmt.Printf("a=(%T, %v)\n", a, a)
fmt.Printf("b=(%T, %v)\n", b, b)
fmt.Println()
fmt.Println("a == nil:", a == nil)
fmt.Println("b == nil:", b == nil)
fmt.Println("a == b:", a == b) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/i8kaKz8qGEh](https://play.golang.org/p/i8kaKz8qGEh)T3】*

这个程序的输出如下所示。

```
a=(*int, <nil>)
b=(<nil>, <nil>)

a == nil: true
b == nil: true
a == b: false 
```

Enter fullscreen mode Exit fullscreen mode

这里明显怪异的地方是`a`不等于`b`。这看起来不可思议，因为乍一看，我们说的是`a == nil`和`b == nil`而不是`a != b`，这在逻辑上是不可能的。

现实情况是，我们所写的东西——例如`a == nil`——并不是实际被比较的真实表现。我们真正比较的是值和类型。也就是说，我们不仅仅是将存储在`a`中的值与`nil`值进行比较；我们也在比较他们的类型。这将在下面更清楚地显示。

```
a == nil: (*int, <nil>) == (*int*, <nil>)
b == nil: (<nil>, <nil>) == (<nil>, <nil>)
# Notice that these two are clearly not equal
# once we add in the type information.
a == b: (*int, <nil>) == (<nil>, <nil>) 
```

Enter fullscreen mode Exit fullscreen mode

当以这种方式编写比较时，很明显这两者不相等——它们有不同的类型——但这是所有在我们的代码中没有明确说明的信息，不幸的是这导致了这种常见的误解。

> **另一种方法**
> 如果你真的想在你的代码中比较`a`和`b`，你可能会写类似这样的代码:
> 
> ```
> if a == nil && b == nil {
>   // both are nil!
> } 
> ```
> 
> 这是更多的代码，但更经常地会符合您的意图。也就是说，将`b`赋给另一个 nil 变量(而不是硬编码的`nil`)会搞糟这种方法，我们将在下一个例子中看到。

现在让我们看看当我们将`b`赋给`a`变量并执行相同的比较时会发生什么。

```
var a *int = nil
var b interface{} = a // <- the change

fmt.Printf("a=(%T, %v)\n", a, a)
fmt.Printf("b=(%T, %v)\n", b, b)
fmt.Println()
fmt.Println("a == nil:", a == nil)
fmt.Println("b == nil:", b == nil)
fmt.Println("a == b:", a == b) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/dwkTU54PZRr](https://play.golang.org/p/dwkTU54PZRr)T3】*

这个程序的输出是...

```
a=(*int, <nil>)
b=(*int, <nil>)

a == nil: true
b == nil: false
a == b: true 
```

Enter fullscreen mode Exit fullscreen mode

现在被淘汰的是第二行，`b == nil`。

这一点不太明显，但是当我们将变量`b`与硬编码的`nil`进行比较时，我们的编译器再次需要确定用什么类型来赋予那个`nil`值。当这种情况发生时，编译器会做出相同的决定，如果将`nil`赋给`b`变量——也就是说，它将我们等式的右边设置为`(<nil>, <nil>)`——如果我们查看`b`的输出，它显然有不同的类型:`(*int, <nil>)`。

在这一点上，一个普遍的想法是，这是令人困惑的，语言应该只是为我们处理这个细节。不幸的是，这在编译时是不可能的，因为`b`变量的实际类型会随着程序的运行而改变。

```
var a *int = nil
var b interface{} = a
var c *string = nil

fmt.Printf("b=(%T, %v)\n", b, b)
fmt.Println("b == nil:", b == nil)

b = c
fmt.Printf("b=(%T, %v)\n", b, b)
fmt.Println("b == nil:", b == nil)

b = nil
fmt.Printf("b=(%T, %v)\n", b, b)
fmt.Println("b == nil:", b == nil) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/_or0_qmZ7iv](https://play.golang.org/p/_or0_qmZ7iv)T3】*

在这个程序中，我们的`b`变量的类型改变了三次。它以`(*int, <nil>)`开始，然后变成`(*string, <nil>)`，最后以`(<nil>, <nil>)`结束。

编译器在编译时无法处理每一种类型，这意味着如果它成为运行时决策，这只能在 Go 中自动处理，这将有其独特的复杂性，可能不值得引入。

## 编译器的类型决定也可以用数字来演示

我们看到了`nil`如何被编译器强制转换成正确的类型，但这实际上并不是编译器做出这种类型决定的唯一情况。例如，当你把变量赋给一个硬编码的数字时，编译器会根据程序的上下文决定使用哪种类型。

显而易见的情况是当类型和变量一起声明时(例如`var a int = 12`)，但是当我们将硬编码的值传递给一个函数或者当我们只是将一个变量赋给一个数字时，也会发生这种情况。所有这些情况如下所示。

```
package main

import "fmt"

func main() {
    var a int = 12
    var b float64 = 12
    var c interface{} = a
    d := 12 // will be an int

    fmt.Printf("a=(%T,%v)\n", a, a)
    fmt.Printf("b=(%T,%v)\n", b, b)
    fmt.Printf("c=(%T,%v)\n", c, c)
    fmt.Printf("d=(%T,%v)\n", d, d)
    useInt(12)
    useFloat(12)
}

func useInt(n int) {
    fmt.Printf("useInt=(%T,%v)\n", n, n)
}

func useFloat(n float64) {
    fmt.Printf("useFloat=(%T,%v)\n", n, n)
} 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/tSoxf4ohY7C](https://play.golang.org/p/tSoxf4ohY7C)T3】*

我们甚至可以用数字来证明一些比较混乱。

```
var a int = 12
var b float64 = 12
var c interface{} = a

fmt.Println("a==12:", a == 12) // true
fmt.Println("b==12:", b == 12) // true
fmt.Println("c==12:", c == 12) // true
fmt.Println("a==c:", a == c) // true
fmt.Println("b==c:", b == c) // false
// We cannot compare a and b because their types
// are clearly never going to match. 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/yNV0c6hAbtX](https://play.golang.org/p/yNV0c6hAbtX)T3】*

所以`a == 12`、`b == 12`和`c == 12`，但是如果我们比较`b == c`，我们会返回 false。什么？！？！

同样，它又回到了底层类型:

```
a=(int,12)
b=(float64,12)
c=(int,12) 
```

Enter fullscreen mode Exit fullscreen mode

`a`和`c`具有`int`类型。`b`有一个`float64`类型，所以当我们将它们与像`12`这样的硬编码值进行比较时，在比较发生之前，它们需要被强制转换成一个类型。

另一个与数字相关的有趣注意事项是，当将`12`与一个接口进行比较时，编译器总是将它强制转换为`int`。与接口相比，这类似于`nil`如何被强制转换为`(<nil>, <nil>)`，我们可以通过将最后一段代码改为
来演示这一点

```
var b float64 = 12
var c interface{} = b

fmt.Println("c==12:", c == 12)
fmt.Printf("c=(%T,%v)\n", c, c)
fmt.Printf("hard-coded=(%T,%v)\n", 12, 12) 
```

Enter fullscreen mode Exit fullscreen mode

*在围棋场上跑:[https://play.golang.org/p/9gEsrSO9zTS](https://play.golang.org/p/9gEsrSO9zTS)T3】*

其输出如下:

```
c==12: false
c=(float64,12)
hard-coded=(int,12) 
```

Enter fullscreen mode Exit fullscreen mode

现在`c == 12`返回 false，因为`(float64, 12)`与硬编码的`(int, 12)`不同，因为它有不同的类型。

## 总结...

当我们将硬编码的值与变量进行比较时，编译器必须假设它们具有某种特定的类型，并遵循某种规则来实现这一点。有时这可能会令人困惑，但随着时间的推移，你会习惯的。

如果您发现自己处理的各种类型都可以赋给 nil，一种避免问题的常用技术是显式地将事物赋给`nil`。也就是说，不要写`a = b`，而是写

```
var a *int = nil
var b interface{}

if a == nil {
  b = nil
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们稍后将`b`与硬编码的`nil`进行比较时，我们将得到我们预期的结果。这需要多做一点工作，但是在几乎所有的情况下都会产生更好的整体效果。

> **免责声明**
> 我没有真正研究过编译器或 Go 的内部工作原理，所以如果有任何不准确的地方，请告诉我，我会解决的。这都是基于观察和我看过的其他文章。