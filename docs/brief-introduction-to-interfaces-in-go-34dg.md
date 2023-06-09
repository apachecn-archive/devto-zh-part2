# Go 中的接口简介

> 原文：<https://dev.to/shearytan/brief-introduction-to-interfaces-in-go-34dg>

作为一个新的 Gopher 和一个以前从未键入过 C++或任何强类型语言的 CodeNewbie，我很难找到一些初学者友好的关于 Go 接口的资源。

所以我在这里，决定写一个，并尽我所能帮助任何初学者。

* * *

# 什么是接口？

> Go 中的接口提供了一种指定对象行为的方式:如果某样东西可以做到这一点，那么它就可以用在这里。-有效 Go

这基本上意味着接口是一种有很多方法的类型。

好了，话不多说，今天就把事情说清楚。

## 写一些代码

**目的**:实现两个方法`sum()`和`substr()`，然后打印出两个数的和与减。

1)让我们从设置
开始

```
package main

import "fmt"

func main() {
} 
```

Enter fullscreen mode Exit fullscreen mode

2)然后是类型，这里我们有`item1`和`item2`两个数字分别输入`float64`:

```
package main

import "fmt" 
```

Enter fullscreen mode Exit fullscreen mode

```
type item1 struct {
    num1, num2 float64
}

type item2 struct {
    num1, num2 float64
}

func main() {
} 
```

Enter fullscreen mode Exit fullscreen mode

3)现在我们需要一些指令(方法)让代码工作:

```
package main

import "fmt"

type item1 struct {
    num1, num2 float64
}

type item2 struct {
    num1, num2 float64
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Method
func (a item1) sum() float64 {
    return a.num1 + a.num2
}

// Method
func (a item1) substr() float64 {
    return a.num1 - a.num2
}

// Method
func (b item2) sum() float64 {
    return b.num1 + b.num2
}

// Method
func (b item2) substr() float64 {
    return b.num1 - b.num2
} 
```

Enter fullscreen mode Exit fullscreen mode

```
func main() {
} 
```

Enter fullscreen mode Exit fullscreen mode

在继续之前，让我们更好地理解这个方法。

```
// Method
func (b item2) substr() float64 {
    return b.num1 - b.num2
} 
```

Enter fullscreen mode Exit fullscreen mode

`b`是对`item2`的引用，是我们在`item2`内部访问`num1`和`num2`的方式。然后我们有了名字`substr()`，返回类型`float64`和返回方法。

4)执行`result1`和`result2`，并打印出结果:

```
package main

import "fmt"

type item1 struct {
    num1, num2 float64
}

type item2 struct {
    num1, num2 float64
}

// Method
func (a item1) sum() float64 {
    return a.num1 + a.num2
}

// Method
func (a item1) substr() float64 {
    return a.num1 - a.num2
}

// Method
func (b item2) sum() float64 {
    return b.num1 + b.num2
}

// Method
func (b item2) substr() float64 {
    return b.num1 - b.num2
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Result 1 & Result 2
func result1(i item1) {
    fmt.Println("Sum: ", i.sum())
    fmt.Println("Substr: ", i.substr())
}

func result2(i item2) {
    fmt.Println("Sum: ", i.sum())
    fmt.Println("Substr: ", i.substr())
}

func main() {
    a := item1{num1: 10, num2: 5}
    b := item2{num1: 20, num2: 10}

    result1(a) 
    result2(b)
}

// Sum:  15
// Substr:  5
// Sum:  30
// Substr:  10 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将两个变量:`a`和`b`传递给两个函数:`result1`和`result2`，得到结果。

但问题是:

> *为什么我们需要编写两个基本上做同样事情的函数(`result1`和`result2`)。*

* * *

### 所以这就是你一直在等待的东西:接口

5)实现接口。我之前提到过，接口是一个有方法(函数)集合的类型。它既有`sum()`又有`substr()`，这是下面这些方法的名字:

```
package main

import "fmt" 
```

Enter fullscreen mode Exit fullscreen mode

```
type math interface {
    sum() float64
    substr() float64
} 
```

Enter fullscreen mode Exit fullscreen mode

```
type item1 struct {
    num1, num2 float64
}

type item2 struct {
    num1, num2 float64
}

// Method
func (a item1) sum() float64 {
    return a.num1 + a.num2
}

// Method
func (a item1) substr() float64 {
    return a.num1 - a.num2
}

// Method
func (b item2) sum() float64 {
    return b.num1 + b.num2
}

// Method
func (b item2) substr() float64 {
    return b.num1 - b.num2
}

func main() {

} 
```

Enter fullscreen mode Exit fullscreen mode

6)打印出结果:

```
package main

import "fmt"

type math interface {
    sum() float64
    substr() float64
}

type item1 struct {
    num1, num2 float64
}

type item2 struct {
    num1, num2 float64
}

// Method
func (a item1) sum() float64 {
    return a.num1 + a.num2
}

// Method
func (a item1) substr() float64 {
    return a.num1 - a.num2
}

// Method
func (b item2) sum() float64 {
    return b.num1 + b.num2
}

// Method
func (b item2) substr() float64 {
    return b.num1 - b.num2
} 
```

Enter fullscreen mode Exit fullscreen mode

```
func result(m math) {
    fmt.Println("Sum: ", m.sum())
    fmt.Println("Substr: ", m.substr())
}

func main() {
    a := item1{num1: 10, num2: 5}
    b := item2{num1: 20, num2: 10}

    result(a)
    result(b)
}

// Sum:  15
// Substr:  5
// Sum:  30
// Substr:  10 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们只需要包含接口`math`和`result`，然后我们就可以通过在`main()`部分调用一次来访问`sum()`和`substr()`。
这里我们对两个变量调用一次`result`，得到了相同的结果！

继续-> [清空界面](https://dev.to/shearywtan/continue---empty-interfaces-in-go-219e)

> 感谢你阅读我第一篇发表的文章！