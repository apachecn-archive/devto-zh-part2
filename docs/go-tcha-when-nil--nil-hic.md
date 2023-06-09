# 当零的时候！=零

> 原文：<https://dev.to/pauljlucas/go-tcha-when-nil--nil-hic>

在我在 Go 中的第一个项目中，我实现了自己的`Error` `struct`来传达详细的错误信息，而不仅仅是 Go 内置的`error` `interface`提供的简单错误消息。

刚实现的时候是作为一个`struct`来做的。从那以后，我知道了最好还是做一个`interface` :

```
package myerr

type Code string

type Error interface {
    error            // implementations must satisfy error interface
    Code() Code      // project-specific error code
    Cause() Error    // the Error that caused this Error, if any
    OrigErr() error  // the original error
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

> **抛开错误代码**
> 
> 我选择一个`string`而不是一个整数作为错误代码的原因是因为整数的唯一优点是它们使得类似 C 的代码很容易对它们使用`switch`。然而，整数代码也有一些缺点，包括:
> 
> 1.  它们不适合人类读者:您必须查找代码，或者错误提供程序必须包含字符串描述(在这种情况下，提供程序还不如一开始就将代码变成字符串)。
> 2.  您必须确保来自不同子系统的代码不会冲突。
> 
> 是的，比较字符串和整数会有一点点性能损失，但是:
> 
> 1.  据推测，处理错误是例外的，所以代码稍微慢一点也没关系，因为它不是关键路径。
> 2.  如果您正在开发一个 REST 应用程序，其中错误代码作为 JSON 响应的一部分通过 HTTP 发送，那么您已经受到了整数代码的`itoa` ing 和`atoi` ing 的性能影响(更不用说解析整个 JSON 文档了),因为它是作为文本通过网络发送的。因此，一些额外的字符串比较是噪声。

尽管现在是一个`interface`，`Error`仍然需要由一个`struct` :
来实现

```
type MyError struct {
    code    Code
    origErr error
    cause   *MyError
}

func (e *MyError) Cause() Error {
    return e.cause
}

func (e *MyError) Code() Code {
    return e.code
}

func (e *MyError) OrigErr() error {
    return e.origErr
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来相当简单。(注意:这里省略了创建错误的其他函数，因为它们与本文无关；但它们同样简单。)

### 问题

当时我正在编写一些负面的单元测试(那些确保错误被正确检测和报告的测试)，所以我需要一种方法来比较两个`Error`s。Go 在其标准库中有 [`DeepEqual()`](https://golang.org/pkg/reflect/#DeepEqual) ，但是，尽管它工作，它只告诉你两个对象是否相等。对于测试来说，如果两个对象不相等，知道什么是*特别是*不相等是有帮助的，所以我写了`ErrorDeepEqual()`，它返回一个`error`来描述这个:

```
func ErrorDeepEqual(e1, e2 myerror.Error) error {
    if e1 == nil {
        if e2 != nil {
            return errors.New("e1(nil) != e2(!nil)")
        }
        return nil
    }
    if e2 == nil {
        return errors.New("e1(!nil) != e2(nil)")
    }
    if e1.Code() != e2.Code() {
        return fmt.Errorf("e1.Code(%v) != e2.Code(%v)", e1.Code(), e2.Code())
    }
    // ...
    return ErrorDeepEqual(e1.Cause(), e2.Cause())
} 
```

Enter fullscreen mode Exit fullscreen mode

因为一个`Error`可以有一个原因，所以`ErrorDeepEqual()`以一个递归调用结束，以检查原因是否相等。问题是一次测试结束时，一个`nil`指针出现在这条线上:

```
 if e1.Code() != e2.Code() {  // panics here because e1 is nil 
```

Enter fullscreen mode Exit fullscreen mode

但是这一行之前的`if`行检查的是`nil`，所以`e1`和`e2`都不可能是这里的`nil`吧？怎么可能`e1 == nil`是`false`而`e1`是`nil`？

### 原因

事实证明，在围棋中，这种情况可能会发生，并让足够多的人感到困惑，以至于出现了关于它的 [FAQ](https://golang.org/doc/faq#nil_error) 。一个简单的总结就是一个`interface`有两部分:一个*类型*和一个该类型的*值*。只有当*类型*和*值都为`nil` :
时，一个`interface`才是`nil`*

```
var i interface{}                // i = (nil,nil)
fmt.Println(i == nil)            // true
var p *int                       // p = nil
i = p                            // i = (*int,nil)
fmt.Println(i == nil)            // false: (*int,nil) != (nil,nil)
fmt.Println(i == (*int)(nil))    // true : (*int,nil) == (*int,nil)
fmt.Println(i.(*int) == nil)     // true : nil == nil 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将把 Go 为什么这样工作的基本原理放在一边(以及这个基本原理是否是一个好的基本原理)。

### 修罗

同时，我还必须弄清楚*如何*得到一个非`nil` `Error`和一个`nil`值。原来这就是罪魁祸首:

```
func (e *MyError) Cause() Error {
    return e.cause               // WRONG!
} 
```

Enter fullscreen mode Exit fullscreen mode

问题是，每当你给一个`interface`赋值时(要么通过赋值*显式地赋值，要么通过`return`值隐式地赋值*，它就同时接受*值*和*具体类型。一旦它有了具体的类型，它将永远无法与(无类型的)`nil`相提并论。修复方法是:* 

```
func (e *MyError) Cause() Error {
    if e.cause == nil {          // if typed pointer is nil ...
        return nil               //     return typeless nil explicitly
    }
    return e.cause               // else return typed non-nil
} 
```

Enter fullscreen mode Exit fullscreen mode

也就是:当一个函数的返回类型是一个`interface`并且你可以返回一个`nil`指针时，你*必须*检查`nil`并且如果指针是`nil`则返回文字`nil`。

### 理

有几个原因*为什么* Go 这样工作。在 [go-nuts 邮件列表](https://groups.google.com/forum/#!forum/golang-nuts)上有一个长长的[讨论帖子](https://groups.google.com/d/topic/golang-nuts/wnH302gBa4I/discussion)。我仔细检查了一下，提炼出了主要原因。

首先是因为 Go 允许*任何*用户定义的类型都有为其定义的方法，而不仅仅是`struct`类型(或者其他语言中的类)。例如，我们可以定义自己类型的`int`，并为它实现[`Stringer``interface`](https://golang.org/pkg/fmt/#Stringer):

```
type Aint int                    // accounting int: print negatives in ()

func (n Aint) String() string {
    if n < 0 {
        return fmt.Sprintf("(%d)", -n)
    }
    return fmt.Sprintf("%d", n)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们用一个`Stringer` `interface`变量:

```
var n Aint                       // n = (Aint,0)
var s fmt.Stringer = n           // s = (Aint,0)
fmt.Println(s == nil)            // false: (Aint,0) != (nil,nil) 
```

Enter fullscreen mode Exit fullscreen mode

这里，`s`是非`nil`的，因为它引用了一个`Aint`——而值恰好是 0。0 值没有什么值得注意的，因为 0 对于`int`来说是一个非常好的值。

第二个原因是 Go 有*非常*强的打字。比如:

```
fmt.Println(s == 0)              // error: can't compare (Aint,0) to (int,0) 
```

Enter fullscreen mode Exit fullscreen mode

是一个编译时错误，因为您不能将`s`(指的是`Aint`)与 0 进行比较(因为普通的 0 属于`int`类型)。但是，您可以使用强制转换或类型断言:

```
fmt.Println(s == Aint(0))        // true: (Aint,0) == (Aint,0)
fmt.Println(s.(Aint) == 0)       // true: 0 == 0 
```

Enter fullscreen mode Exit fullscreen mode

第三个原因是 [Go 显式地允许`nil`用于有指针接收器的方法](https://tour.golang.org/methods/12) :

```
type T struct {
    // ...
}

func (t *T) String() string {
    if t == nil {
        return "<nil>"
    }
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

正如 0 对于`Aint`来说是一个非常好的值一样，`nil`对于指针来说也是一个非常好的值。由于`nil`没有指向任何`T`，[可以用来实现`T`](https://groups.google.com/d/msg/golang-nuts/wnH302gBa4I/DJjKtkRYBwAJ) 的默认行为。对`Aint`重复前面的例子，但现在对`*T` :

```
var p *T                         // p = (*T,nil)
var s fmt.Stringer = p           // s = (*T,nil)
fmt.Println(s == nil)            // false: (*T,nil) != (nil,nil)
fmt.Println(s == (*T)(nil))      // true : (*T,nil) == (*T,nil)
fmt.Println(s.(*T) == nil)       // true : nil == nil 
```

Enter fullscreen mode Exit fullscreen mode

我们对指针得到类似的结果— *几乎是*。(你能发现不一致的地方吗？)

### 哪里出错了

不像前面的`Aint`的例子，比较`s`(类型`Aint`)和 0(类型`int`)是一个错误:

```
fmt.Println(s == 0)              // error: can't compare (Aint,0) to (int,0) 
```

Enter fullscreen mode Exit fullscreen mode

而比较`s`(类型`*T`)和`nil`(类型`nil`)就可以:

```
fmt.Println(s == nil)            // OK to compare 
```

Enter fullscreen mode Exit fullscreen mode

问题是，在 Go 中，`nil`根据上下文有两种不同的含义:

1.  `nil`指值为 0 的指针。
2.  `nil`是指一个没有类型，值为 0 的`interface`。

[假设 Go 有另一个关键字`nilinterface`用于案例 2，而`nil`专门用于案例 1](https://groups.google.com/d/msg/golang-nuts/wnH302gBa4I/zqcfBFPIBgAJ) 。那么上面那行*应该*写成:

```
fmt.Println(s == nilinterface)   // false: (*T,nil) != nilinterface 
```

Enter fullscreen mode Exit fullscreen mode

很明显，你是在检查`interface`本身的 no 类型和 0 值，而*不是*在检查`interface`的*值*。

此外，使用原始的`nil`会导致错误(出于同样的原因，在没有强制转换或类型断言的情况下，不能将`Aint`与 0 进行比较):

```
fmt.Println(s == nil)            // what-if error: can't compare (*T,nil) to (nil,nil)
fmt.Println(s == (*T)(nil))      // still true: (*T,nil) == (*T,nil)
fmt.Println(s.(*T) == nil)       // still true: nil == nil 
```

Enter fullscreen mode Exit fullscreen mode

Go 中的困惑在于，新程序员写:

```
fmt.Println(s == nil)            // checks interface for empty, not the value 
```

Enter fullscreen mode Exit fullscreen mode

他们*认为*他们在检查接口的值，但他们实际上是在检查接口本身是否没有类型和 0 值。如果 Go 有一个像`nilinterface`这样独特的关键词，所有这些困惑都会烟消云散。

### 黑客攻击

如果你觉得这些都很麻烦，你可以写一个函数来检查一个`interface`的值，忽略它的类型(如果有的话):

```
func isNilValue(i interface{}) bool {
    return i == nil || reflect.ValueOf(i).IsNil()
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这种方法可行，但速度很慢，因为反射很慢。一个更快的实现是:

```
func isNilValue(i interface{}) bool {
    return (*[2]uintptr)(unsafe.Pointer(&i))[1] == 0
} 
```

Enter fullscreen mode Exit fullscreen mode

这将忽略类型部分，直接检查`interface`的值部分是否为零。*