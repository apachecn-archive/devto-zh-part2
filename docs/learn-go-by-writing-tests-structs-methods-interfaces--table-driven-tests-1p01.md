# 通过编写测试学习 Go:结构、方法、接口和表驱动测试

> 原文：<https://dev.to/quii/learn-go-by-writing-tests-structs-methods-interfaces--table-driven-tests-1p01>

这是 WIP 项目[的第三篇文章，通过编写测试](https://github.com/quii/learn-go-with-tests)来学习 Go，其目的是熟悉 Go 并学习 TDD 技术

*   [第一篇文章让你了解了 TDD](https://dev.to/quii/learn-go-by-writing-tests--m63)
*   [第二篇文章讨论了数组和切片](https://dev.to/quii/learn-go-by-writing-tests-arrays-and-slices-ahm)

# 结构、方法和接口

假设我们需要一些几何代码来计算一个给定高度和宽度的矩形的周长。我们可以写一个`Perimeter(width float64, height float64)`函数，其中`float64`代表浮点数，比如`123.45`

到现在为止，TDD 周期对您来说应该非常熟悉了。

## 先写测试

```
func TestPerimeter(t *testing.T) {
    got := Perimeter(10.0, 10.0)
    want := 40.0

    if got != want {
        t.Errorf("got %.2f want %.2f", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意新的格式字符串了吗？`f`是我们的`float64`，`.2`表示打印 2 位小数。

## 试运行测试

`./shapes_test.go:6:9: undefined: Perimeter`

## 编写运行测试所需的最少代码，并检查失败的测试输出

```
func Perimeter(width float64, height float64) float64 {
    return 0
} 
```

Enter fullscreen mode Exit fullscreen mode

结果在`shapes_test.go:10: got 0 want 40`

## 写足够的代码让它通过

```
func Perimeter(width float64, height float64) float64 {
    return 2 * (width + height)
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，很容易。现在让我们创建一个名为`Area(width, height float64)`的函数，它返回一个矩形的面积。

尝试自己做，遵循 TDD 循环。

您应该以这样的测试结束

```
func TestPerimeter(t *testing.T) {
    got := Perimeter(10.0, 10.0)
    want := 40.0

    if got != want {
        t.Errorf("got %.2f want %.2f", got, want)
    }
}

func TestArea(t *testing.T) {
    got := Area(12.0, 6.0)
    want := 72.0

    if got != want {
        t.Errorf("got %.2f want %.2f", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

还有这样的代码

```
func Perimeter(width float64, height float64) float64 {
    return 2 * (width + height)
}

func Area(width float64, height float64) float64 {
    return width * height
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

我们的代码完成了这项工作，但是它不包含任何关于矩形的显式内容。粗心的开发人员可能会试图向这些函数提供三角形的宽度和高度，而没有意识到它们将返回错误的答案。

我们可以给函数起更具体的名字，比如`RectangleArea`。一个更简洁的解决方案是定义我们自己的*类型*，称为`Rectangle`，它为我们封装了这个概念。

我们可以使用一个**结构**创建一个简单的类型。结构只是一个命名的字段集合，您可以在其中存储数据。

像这样声明一个结构

```
type Rectangle struct {
    Width float64
    Height float64
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们重构测试，使用`Rectangle`而不是普通的`float64` s.

```
func TestPerimeter(t *testing.T) {
    rectangle := Rectangle{10.0, 10.0}
    got := Perimeter(rectangle)
    want := 40.0

    if got != want {
        t.Errorf("got %.2f want %.2f", got, want)
    }
}

func TestArea(t *testing.T) {
    rectangle := Rectangle{12.0, 6.0}
    got := Area(rectangle)
    want := 72.0

    if got != want {
        t.Errorf("got %.2f want %.2f", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，在尝试修复之前运行您的测试，您应该会得到类似
的有用错误

```
./shapes_test.go:7:18: not enough arguments in call to Perimeter
    have (Rectangle)
    want (float64, float64) 
```

Enter fullscreen mode Exit fullscreen mode

你可以用`myStruct.field`的语法访问一个结构的字段。

更改这两个函数来修复测试。

```
func Perimeter(rectangle Rectangle) float64 {
    return 2 * (rectangle.Width + rectangle.Height)
}

func Area(rectangle Rectangle) float64 {
    return rectangle.Width * rectangle.Height
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望你会同意，传递一个`Rectangle`给一个函数更清楚地表达了我们的意图，但是使用结构体有更多的好处，我们将会谈到。

我们的下一个需求是为圆写一个`Area`函数。

## 先写测试

```
func TestArea(t *testing.T) {

    t.Run("rectangles", func(t *testing.T) {
        rectangle := Rectangle{12, 6}
        got := Area(rectangle)
        want := 72.0

        if got != want {
            t.Errorf("got %.2f want %.2f", got, want)
        }
    })

    t.Run("circles", func(t *testing.T) {
        circle := Circle{10}
        got := Area(circle)
        want := 314.16

        if got != want {
            t.Errorf("got %.2f want %.2f", got, want)
        }
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

`./shapes_test.go:28:13: undefined: Circle`

## 编写运行测试所需的最少代码，并检查失败的测试输出

我们需要定义我们的`Circle`类型。

```
type Circle struct {
    Radius float64
} 
```

Enter fullscreen mode Exit fullscreen mode

现在试着再做一次测试

`./shapes_test.go:29:14: cannot use circle (type Circle) as type Rectangle in argument to Area`

一些编程语言允许你这样做:

```
func Area(circle Circle) float64 { ... }
func Area(rectangle Rectangle) float64 { ... } 
```

Enter fullscreen mode Exit fullscreen mode

但是你不能进去

`./shapes.go:20:32: Area redeclared in this block`

我们有两个选择

*   你可以在不同的*包*中声明相同名称的函数。所以我们可以在一个新的包中创建我们的`Area(Circle)`,但是这里感觉有点过头了
*   我们可以在新定义的类型上定义*方法*。

### 有哪些方法？

到目前为止，我们只编写了*函数*，但是我们一直在使用一些方法。当我们调用`t.Errorf`时，我们是在我们的`t` ( `testing.T`)的实例上调用方法`ErrorF`。

方法与函数非常相似，但它们是通过在特定类型的实例上调用它们来调用的。你可以在任何你喜欢的地方调用函数，比如`Area(rectangle)`你只能在“事物”上调用方法。

举个例子会有所帮助，所以让我们先把测试改为调用方法，然后修改代码。

```
func TestArea(t *testing.T) {

    t.Run("rectangles", func(t *testing.T) {
        rectangle := Rectangle{12, 6}
        got := rectangle.Area()
        want := 72.0

        if got != want {
            t.Errorf("got %.2f want %.2f", got, want)
        }
    })

    t.Run("circles", func(t *testing.T) {
        circle := Circle{10}
        got := circle.Area()
        want := 314.1592653589793

        if got != want {
            t.Errorf("got %f want %f", got, want)
        }
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们尝试运行测试，我们会得到

```
./shapes_test.go:19:19: rectangle.Area undefined (type Rectangle has no field or method Area)
./shapes_test.go:29:16: circle.Area undefined (type Circle has no field or method Area) 
```

Enter fullscreen mode Exit fullscreen mode

> 类型 Circle 没有字段或方法区域

我想在这里重申一下编译器有多棒。花时间慢慢阅读你得到的错误信息是非常重要的，从长远来看，这将对你有所帮助。

## 编写运行测试所需的最少代码，并检查失败的测试输出

让我们给我们的类型添加一些方法

```
type Rectangle struct {
    Width  float64
    Height float64
}

func (r Rectangle) Area() float64  {
    return 0
}

type Circle struct {
    Radius float64
}

func (c Circle) Area() float64  {
    return 0
} 
```

Enter fullscreen mode Exit fullscreen mode

声明方法的语法几乎与函数相同，这是因为它们非常相似。唯一的区别是方法接收者`func (receiverName RecieverType) MethodName(args)`的语法。

当您的方法在该类型的变量上被调用时，您通过`receiverName`变量获得对其数据的引用。在许多其他编程语言中，这是隐式完成的，您可以通过`this`访问接收器。

Go 中的一个惯例是让 receiver 变量成为该类型的第一个字母。

如果您尝试重新运行测试，它们现在应该会编译并给出一些失败的输出

## 写足够的代码让它通过

现在让我们通过修正我们的新方法
来使我们的矩形测试通过

```
func (r Rectangle) Area() float64  {
    return r.Width * r.Height
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您重新运行测试，矩形测试应该会通过，但圆形测试仍然会失败。

为了让 circle 的`Area`函数通过，我们将从`math`包中借用`Pi`常量(记得导入它)。

```
func (c Circle) Area() float64  {
    return math.Pi * c.Radius * c.Radius
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

我们的测试有些重复。

我们想要做的就是获取一组*形状*，对它们调用`Area()`方法，然后检查结果。

我们希望能够编写某种类型的`checkArea`函数，我们可以将`Rectangle`和`Circle`都传递给它，但是如果我们试图传递不是形状的东西，就会编译失败。

使用 Go，我们可以用**接口**将这种意图编码。

在像 Go 这样的静态类型语言中，接口是一个非常强大的概念，因为它们允许您创建可以用于不同类型的函数，创建高度解耦的代码，同时仍然保持类型安全。

让我们通过重构测试来介绍这一点。

```
func TestArea(t *testing.T) {

    checkArea := func(t *testing.T, shape Shape, want float64) {
        t.Helper()
        got := shape.Area()
        if got != want {
            t.Errorf("got %.2f want %.2f", got, want)
        }
    }

    t.Run("rectangles", func(t *testing.T) {
        rectangle := Rectangle{12, 6}
        checkArea(t, rectangle, 72.0)
    })

    t.Run("circles", func(t *testing.T) {
        circle := Circle{10}
        checkArea(t, circle, 314.1592653589793)
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在创建一个助手函数，就像在其他练习中一样，但是这次我们要求传入一个`Shape`。如果我们试图用一个不是形状的东西来调用它，那么它就不会编译。

东西是怎么变成形状的？我们只是使用接口声明
告诉 Go 什么是`Shape`

```
type Shape interface {
    Area() float64
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在创建一个新的`type`，就像我们创建`Rectangle`和`Circle`一样，但是这次是一个`interface`而不是一个`struct`。

一旦您将此添加到代码中，测试就会通过。

### 等等，什么？

这与大多数其他编程语言中的接口非常不同。通常你必须写代码来说`My type Foo implements interface Bar`。

但是在我们的情况下

*   `Rectangle`有一个名为`Area`的方法，它返回一个`float64`，所以它满足了`Shape`接口
*   `Circle`有一个名为`Area`的方法，它返回一个`float64`，所以它满足了`Shape`接口
*   `string`没有这样的方法，所以不满足接口
*   等等。

在 Go **中，接口解析是隐式的**。如果您传入的类型与接口要求的匹配，它将会编译。

### 脱钩

请注意，我们的助手不需要关心形状是`Rectangle`还是`Circle`还是`Triangle`。通过声明一个接口，助手*将*从具体类型中分离出来，并拥有完成工作所需的方法。

这种使用接口来声明**你所需要的**的方法在软件设计中非常重要，将在后面的章节中详细介绍。

## 进一步重构

既然你对结构有了一些了解，我们现在可以引入“表驱动测试”

当您想要构建一个可以用相同方式测试的测试用例列表时，表驱动测试非常有用。

```
func TestArea(t *testing.T) {

    areaTests := []struct {
        shape Shape
        want  float64
    }{
        {Rectangle{12, 6}, 72.0},
        {Circle{10}, 314.1592653589793},
    }

    for _, tt := range areaTests {
        got := tt.shape.Area()
        if got != tt.want {
            t.Errorf("got %.2f want %.2f", got, tt.want)
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一的新语法是创建一个“匿名结构”。我们通过使用带有两个字段的`[]struct`来声明一部分结构，这两个字段是`shape`和`want`。然后我们用案例填充数组。

然后，我们像处理任何其他切片一样对它们进行迭代，使用 struct 字段来运行我们的测试。

你可以看到开发者引入一个新的形状，实现`Area`，然后将它添加到测试用例中是多么容易。此外，如果用`Area`发现了一个 bug，在修复它之前添加一个新的测试用例来测试它是非常容易的。

基于表格的测试可能是你工具箱中的一个很好的项目，但是要确定你需要测试中的额外噪音。如果您希望测试接口的各种实现，或者如果传递给函数的数据有许多不同的需求需要测试，那么它们是非常合适的。

让我们通过添加另一个形状并测试它来演示这一切；三角形。

## 先写测试

为我们的新形状添加一个新的测试非常容易。将`{Triangle{12, 6}, 36.0},`添加到我们的列表中。

```
func TestArea(t *testing.T) {

    areaTests := []struct {
        shape Shape
        want  float64
    }{
        {Rectangle{12, 6}, 72.0},
        {Circle{10}, 314.1592653589793},
        {Triangle{12, 6}, 36.0},
    }

    for _, tt := range areaTests {
        got := tt.shape.Area()
        if got != tt.want {
            t.Errorf("got %.2f want %.2f", got, tt.want)
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

请记住，继续尝试运行测试，让编译器引导您找到解决方案。

## 编写运行测试所需的最少代码，并检查失败的测试输出

`./shapes_test.go:25:4: undefined: Triangle`

我们还没有定义三角形

```
type Triangle struct {
    Base   float64
    Height float64
} 
```

Enter fullscreen mode Exit fullscreen mode

再试一次

```
./shapes_test.go:25:8: cannot use Triangle literal (type Triangle) as type Shape in field value:
    Triangle does not implement Shape (missing Area method) 
```

Enter fullscreen mode Exit fullscreen mode

它告诉我们不能使用三角形作为形状，因为它没有`Area()`方法，所以添加一个空的实现来让测试工作

```
func (c Triangle) Area() float64 {
    return 0
} 
```

Enter fullscreen mode Exit fullscreen mode

最后代码编译器和我们得到了我们的错误

`shapes_test.go:31: got 0.00 want 36.00`

## 写足够的代码让它通过

```
func (c Triangle) Area() float64 {
    return (c.Base * c.Height) * 0.5
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的测试通过了！

## 重构

同样，实现很好，但是我们的测试需要一些改进

当你扫描这个

```
{Rectangle{12, 6}, 72.0},
{Circle{10}, 314.1592653589793},
{Triangle{12, 6}, 36.0}, 
```

Enter fullscreen mode Exit fullscreen mode

现在还不清楚所有的数字代表什么，你应该让你的测试容易理解。

到目前为止，只向您展示了一种创建 struct`MyStruct{val1, val2}`实例的语法，但是您可以随意命名字段。

让我们看看什么样子的

```
{shape: Rectangle{Width: 12, Height: 6}, hasArea: 72.0},
{shape: Circle{Radius: 10}, hasArea: 314.1592653589793},
{shape: Triangle{Base: 12, Height: 6}, hasArea: 36.0, 
```

Enter fullscreen mode Exit fullscreen mode

在[测试驱动开发示例](https://g.co/kgs/yCzDLF)中，Kent Beck 对一些测试进行了一定程度的重构，并断言

> 这个测试更清楚地告诉我们，好像它是对真理的断言，而不是一系列的运算

(强调我的)

现在，我们的测试(至少是案例列表)对形状及其面积的真实性做出了断言。

#### 确保你的测试输出是有帮助的

还记得之前我们实现`Triangle`时失败的测试吗？上面印着`shapes_test.go:31: got 0.00 want 36.00`

我们知道这与`Triangle`有关，因为我们只是在处理它，但是如果在表格中的 20 种情况中的一种情况下，一个 bug 溜进了系统，该怎么办呢？开发人员如何知道哪个案例失败了？这对于开发人员来说不是一个很好的体验，他们将不得不手动查看案例来找出哪个案例实际上失败了。

我们可以改变我们的错误信息`%#v got %.2f want %.2f`。`%#v` format string 将打印出我们的 struct 及其字段中的值，因此开发人员可以一目了然地看到正在测试的属性。

表格驱动测试的最后一个技巧是使用`t.Run`。

通过将每个案例包装在一个`t.Run`中，你将有更清晰的失败测试输出，因为它将打印案例的名称

```
-------- FAIL: TestArea (0.00s)
    --- FAIL: TestArea/Rectangle (0.00s)
        shapes_test.go:33: main.Rectangle{Width:12, Height:6} got 72.00 want 72.10 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`go test -run TestArea/Rectangle`在您的表中运行特定的测试

这是我们捕获这个
的最终测试代码

```
func TestArea(t *testing.T) {

    areaTests := []struct {
        name    string
        shape   Shape
        hasArea float64
    }{
        {name: "Rectangle", shape: Rectangle{Width: 12, Height: 6}, hasArea: 72.0},
        {name: "Circle", shape: Circle{Radius: 10}, hasArea: 314.1592653589793},
        {name: "Triangle", shape: Triangle{Base: 12, Height: 6}, hasArea: 36.0},
    }

    for _, tt := range areaTests {
        // using tt.name from the case to use it as the `t.Run` test name
        t.Run(tt.name, func(t *testing.T) {
            got := tt.shape.Area()
            if got != tt.hasArea {
                t.Errorf("%#v got %.2f want %.2f", tt.shape, got, tt.hasArea)
            }
        })

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

这是更多的 TDD 实践，反复讨论我们对基本数学问题的解决方案，并学习由我们的测试激发的新的语言特性。

*   声明结构来创建您自己的数据类型，这使您可以将相关数据捆绑在一起，并使代码的意图更加清晰
*   声明接口，这样就可以定义不同类型可以使用的函数([参数多态性](https://en.wikipedia.org/wiki/Parametric_polymorphism))
*   添加方法，以便可以向数据类型添加功能，从而可以实现接口
*   基于表格的测试使你的断言更清晰，你的套件更容易扩展和维护

这是一个重要的章节，因为我们现在开始定义我们自己的类型。在像 Go 这样的静态类型语言中，能够设计自己的类型对于构建易于理解、拼凑和测试的软件至关重要。

接口是一个很好的工具，可以隐藏系统其他部分的复杂性。在我们的例子中，我们的测试助手*代码*不需要知道它断言的确切形状，只需要知道如何“请求”它的面积。

随着你对 Go 越来越熟悉，你开始看到接口和标准库的真正力量。您将了解在标准库中定义的接口，这些接口在任何地方都可以使用*，通过针对您自己的类型实现它们，您可以非常快速地重用许多优秀的功能。*