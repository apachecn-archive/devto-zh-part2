# 通过编写测试来学习围棋:反思

> 原文：<https://dev.to/quii/learn-go-by-writing-tests-reflection-103g>

这是 WIP 项目[的第 9 篇文章，通过编写测试](https://github.com/quii/learn-go-with-tests)来学习 Go，其目的是熟悉 Go 并学习 TDD 技术

*   [第一篇文章让你了解了 TDD](https://dev.to/quii/learn-go-by-writing-tests--m63)
*   [第二篇文章讨论了数组和切片](https://dev.to/quii/learn-go-by-writing-tests-arrays-and-slices-ahm)
*   [第三篇文章讲授结构、方法、接口&表驱动测试](https://dev.to/quii/learn-go-by-writing-tests-structs-methods-interfaces--table-driven-tests-1p01)
*   [第四篇文章展示了如何出错以及为什么指针有用](https://dev.to/quii/learn-go-by-writing-tests-pointers-and-errors-2kp6)
*   [第五篇文章向您展示了如何以及为什么要进行依赖注入](https://dev.to/quii/learn-go-by-writing-tests-dependency-injection-n7j)
*   [第六篇文章介绍了并发性](https://dev.to/gypsydave5/learn-go-by-writing-tests-concurrency--2ebk)
*   [第七篇文章展示了如何以及为什么嘲笑](https://dev.to/quii/learn-go-by-writing-tests-mocking-fl4)
*   [第八篇文章展示了如何同步异步进程](https://dev.to/quii/learn-go-by-writing-tests-synchronising-asynchronous-processes-1bo7)

# 倒影

[来自推特](https://twitter.com/peterbourgon/status/1011403901419937792?s=09)

> # Golang Challenge: Write a function `walk(x interface{}, fn func(string))` which takes a structure `x` and calls `fn` for all the string fields found in it.

为此，我们需要使用*反射*

> 计算中的反射是程序检查自身结构的能力，特别是通过类型；这是元编程的一种形式。这也是一个很大的困惑来源。

来自[Go 博客:反思](https://blog.golang.org/laws-of-reflection)

## 什么是`interface`？

我们已经享受到了 Go 提供给我们的类型安全功能，这些功能支持已知的类型，比如`string`、`int`和我们自己的类型，比如`BankAccount`。

这意味着我们可以免费获得一些文档，如果你试图将错误的类型传递给一个函数，编译器就会抱怨。

你可能会遇到这样的情况，你想写一个在编译时不知道类型的函数。

Go 让我们用类型`interface{}`来解决这个问题，你可以认为它只是*任何*类型。

所以`walk(x interface{}, fn func(string))`会接受`x`的任何值。

### 那么为什么不使用`interface`来做任何事情并拥有真正灵活的功能呢？

*   作为一个使用`interface`的函数的用户，你失去了类型安全。如果您打算将类型为`string`的`Foo.bar`传递给一个函数，但却传递了类型为`int`的`Foo.baz`，那该怎么办？编译器不会通知你你的错误。你也不知道允许传递给函数什么。知道一个函数以一个`UserService`为例非常有用。
*   作为这样一个函数的作者，你必须能够检查*传递给你的任何东西*,并试图弄清楚它是什么类型，以及你能用它做什么。这是使用*反射*完成的。这可能非常笨拙，难以阅读，并且通常性能较差(因为您必须在运行时进行检查)。

简而言之，只有在真正需要的时候才使用反射。

如果你想要多态函数，考虑一下你是否可以围绕一个接口来设计它(不是令人困惑的`interface`)，这样如果用户实现了你的函数工作所需的任何方法，他们就可以用多种类型来使用你的函数。

我们的功能将需要能够与许多不同的东西。像往常一样，我们将采取迭代的方法，为我们想要支持的每一个新事物编写测试，并在此过程中进行重构，直到我们完成为止。

## 先写测试

我们希望用一个包含字符串字段的结构(`x`)来调用我们的函数。然后我们就可以窥探传入的函数(`fn`)看它是否被调用。

```
func TestWalk(t *testing.T) {

    expected := "Chris"
    var got []string

    x := struct {
        Name string
    }{expected}

    walk(x, func(input string) {
        got = append(got, input)
    })

    if len(got) != 1 {
        t.Errorf("wrong number of function calls, got %d want %d", len(got), 1)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我们想要存储一段字符串(`got`)，它存储了哪些字符串被`walk`传递到了`fn`。通常在前面的章节中，我们已经为这个做了专门的类型来监视函数/方法调用，但是在这种情况下，我们可以为`fn`传入一个匿名函数来关闭`got`
*   我们使用一个匿名的`struct`和一个字符串类型的`Name`字段来寻找最简单的“快乐”路径。
*   最后，用`x`和间谍调用`walk`，现在只需检查`got`的长度，一旦我们有了一些非常基本的工作，我们将更具体地使用我们的断言。

## 试运行测试

```
./reflection_test.go:21:2: undefined: walk 
```

Enter fullscreen mode Exit fullscreen mode

## 编写运行测试所需的最少代码，并检查失败的测试输出

我们需要定义`walk`

```
func walk(x interface{}, fn func(input string)) {

} 
```

Enter fullscreen mode Exit fullscreen mode

尝试再次运行测试

```
=== RUN   TestWalk
-------- FAIL: TestWalk (0.00s)
    reflection_test.go:19: wrong number of function calls, got 0 want 1
FAIL 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

我们可以用任何线索联系间谍。

```
func walk(x interface{}, fn func(input string)) {
    fn("I still can't believe South Korea beat Germany 2-0 to put them last in their group")
} 
```

Enter fullscreen mode Exit fullscreen mode

测试现在应该通过了。我们需要做的下一件事是对调用我们的`fn`的内容做一个更具体的断言。

## 先写测试

将以下内容添加到现有测试中，以检查传递给`fn`的字符串是否正确

```
if got[0] != expected {
    t.Errorf("got '%s', want '%s'", got[0], expected)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk
-------- FAIL: TestWalk (0.00s)
    reflection_test.go:23: got 'I still can't believe South Korea beat Germany 2-0 to put them last in their group', want 'Chris'
FAIL 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func walk(x interface{}, fn func(input string)) {
    val := reflect.ValueOf(x)
    field := val.Field(0)
    fn(field.String())
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码*非常不安全，非常幼稚*但是请记住，当我们处于“红色”(测试失败)时，我们的目标是编写尽可能少的代码。然后我们编写更多的测试来解决我们的问题。

我们需要使用反射来查看`x`并尝试查看它的属性。

[反射包](https://godoc.org/reflect)有一个函数`ValueOf`，它返回给我们一个给定变量的`Value`。这为我们提供了检查一个值的方法，包括我们在下一行使用的字段。

然后我们对传入的值做一些非常乐观的假设

*   我们查看第一个也是唯一一个字段，可能根本没有会引起恐慌的字段
*   然后我们调用`String()`,它以字符串的形式返回底层值，但是我们知道如果这个字段不是字符串，那么它就是错误的。

## 重构

我们的代码看起来很简单，但是我们知道我们的代码有很多缺点。

我们将编写一些测试，在这些测试中我们传递不同的值，并检查用来调用`fn`的字符串数组。

我们应该将测试重构为基于表格的测试，以便更容易地继续测试新的场景。

```
func TestWalk(t *testing.T) {

    cases := []struct{
        Name string
        Input interface{}
        ExpectedCalls []string
    } {
        {
            "Struct with one string field",
            struct {
                Name string
            }{ "Chris"},
            []string{"Chris"},
        },
    }

    for _, test := range cases {
        t.Run(test.Name, func(t *testing.T) {
            var got []string
            walk(test.Input, func(input string) {
                got = append(got, input)
            })

            if !reflect.DeepEqual(got, test.ExpectedCalls) {
                t.Errorf("got %v, want %v", got, test.ExpectedCalls)
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以很容易地添加一个场景，看看如果我们有多个字符串字段会发生什么

## 先写测试

将以下场景添加到`cases`。

```
{
    "Struct with two string fields",
    struct {
        Name string
        City string
    }{"Chris", "London"},
    []string{"Chris", "London"},
} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk/Struct_with_two_string_fields
    --- FAIL: TestWalk/Struct_with_two_string_fields (0.00s)
        reflection_test.go:40: got [Chris], want [Chris London] 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func walk(x interface{}, fn func(input string)) {
    val := reflect.ValueOf(x)

    for i:=0; i<val.NumField(); i++ {
        field := val.Field(i)
        fn(field.String())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`value`有一个方法`NumField`，它返回值中字段的数量。这让我们遍历字段并调用通过测试的`fn`。

## 重构

看起来这里没有任何明显的重构可以改进代码，所以让我们继续吧

`walk`的下一个缺点是它假设每个字段都是一个`string`。让我们为这个场景编写一个测试

## 先写测试

添加以下案例

```
{
    "Struct with non string field",
    struct {
        Name string
        Age  int
    }{"Chris", 33},
    []string{"Chris"},
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk/Struct_with_non_string_field
    --- FAIL: TestWalk/Struct_with_non_string_field (0.00s)
        reflection_test.go:46: got [Chris <int Value>], want [Chris] 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

我们需要检查字段的类型是否为`string`。

```
func walk(x interface{}, fn func(input string)) {
    val := reflect.ValueOf(x)

    for i := 0; i < val.NumField(); i++ {
        field := val.Field(i)

        if field.Kind() == reflect.String {
            fn(field.String())
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过检查其 [`Kind`](https://godoc.org/reflect#Kind) 来做到这一点

## 重构

现在看起来代码已经足够合理了。

下一个场景是如果不是“平”呢`struct`？换句话说，如果我们有一个包含一些嵌套字段的`struct`会发生什么？

## 先写测试

我们一直在使用匿名结构语法为我们的测试专门声明类型，所以我们可以继续这样做

```
{
    "Nested fields",
    struct {
        Name string
        Profile struct {
            Age  int
            City string
        }
    }{"Chris", struct {
        Age  int
        City string
    }{33, "London"}},
    []string{"Chris", "London"},
}, 
```

Enter fullscreen mode Exit fullscreen mode

但是我们可以看到，当你得到内部匿名结构时，语法变得有点混乱。有人建议这样做，这样语法会更好。

让我们对此进行重构，为这个场景创建一个已知类型，并在测试中引用它。有一点间接性，我们测试的一些代码在测试之外，但是读者应该能够通过查看初始化来推断出`struct`的结构。

在您的测试文件
中添加以下类型声明

```
type Person struct {
    Name    string
    Profile Profile
}

type Profile struct {
    Age  int
    City string
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以将这一点添加到我们的案例中，这样读起来比以前清楚多了

```
{
    "Nested fields",
    Person{
        "Chris",
        Profile{33, "London"},
    },
    []string{"Chris", "London"},
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk/Nested_fields
    --- FAIL: TestWalk/Nested_fields (0.00s)
        reflection_test.go:54: got [Chris], want [Chris London] 
```

Enter fullscreen mode Exit fullscreen mode

问题是我们只在类型层次结构的第一层迭代字段

## 写足够的代码让它通过

```
func walk(x interface{}, fn func(input string)) {
    val := reflect.ValueOf(x)

    for i := 0; i < val.NumField(); i++ {
        field := val.Field(i)

        if field.Kind() == reflect.String {
            fn(field.String())
        }

        if field.Kind() == reflect.Struct {
            walk(field.Interface(), fn)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

解决方法很简单，我们再次检查它的`Kind`，如果它恰好是一个`struct`，我们就在内部`struct`上再次调用`walk`

## 重构

```
func walk(x interface{}, fn func(input string)) {
    val := reflect.ValueOf(x)

    for i := 0; i < val.NumField(); i++ {
        field := val.Field(i)

        switch field.Kind() {
        case reflect.String:
            fn(field.String())
        case reflect.Struct:
            walk(field.Interface(), fn)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当你不止一次对同一个值进行比较时*通常*重构为`switch`会提高可读性，并使你的代码更容易扩展。

如果传入的结构体的值是指针呢？

## 先写测试

添加此案例

```
{
    "Pointers to things",
    &Person{
        "Chris",
        Profile{33, "London"},
    },
    []string{"Chris", "London"},
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk/Pointers_to_things
panic: reflect: call of reflect.Value.NumField on ptr Value [recovered]
    panic: reflect: call of reflect.Value.NumField on ptr Value 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func walk(x interface{}, fn func(input string)) {
    val := reflect.ValueOf(x)

    if val.Kind() == reflect.Ptr {
        val = val.Elem()
    }

    for i := 0; i < val.NumField(); i++ {
        field := val.Field(i)

        switch field.Kind() {
        case reflect.String:
            fn(field.String())
        case reflect.Struct:
            walk(field.Interface(), fn)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你不能在指针`Value`上使用`NumField`，我们需要在使用`Elem()`之前提取底层值

## 重构

让我们把从给定的`interface{}`中提取`reflect.Value`的责任封装到一个函数
中

```
func walk(x interface{}, fn func(input string)) {
    val := getValue(x)

    for i := 0; i < val.NumField(); i++ {
        field := val.Field(i)

        switch field.Kind() {
        case reflect.String:
            fn(field.String())
        case reflect.Struct:
            walk(field.Interface(), fn)
        }
    }
}

func getValue(x interface{}) reflect.Value {
    val := reflect.ValueOf(x)

    if val.Kind() == reflect.Ptr {
        val = val.Elem()
    }

    return val
} 
```

Enter fullscreen mode Exit fullscreen mode

这实际上增加了更多的代码，但是我觉得抽象层次是正确的

*   得到`x`的`reflect.Value`，这样我就可以检查它，我不在乎怎么检查。
*   遍历字段，根据类型做任何需要做的事情

接下来，我们需要覆盖切片

## 先写测试

```
{
    "Slices",
    []Profile {
        {33, "London"},
        {34, "Reykjavík"},
    },
    []string{"London", "Reykjavík"},
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk/Slices
panic: reflect: call of reflect.Value.NumField on slice Value [recovered]
    panic: reflect: call of reflect.Value.NumField on slice Value 
```

Enter fullscreen mode Exit fullscreen mode

## 编写运行测试所需的最少代码，并检查失败的测试输出

这类似于之前的指针场景，我们试图在我们的`reflect.Value`上调用`NumField`，但是它没有指针，因为它不是一个结构。

## 写足够的代码让它通过

```
func walk(x interface{}, fn func(input string)) {
    val := getValue(x)

    if val.Kind() == reflect.Slice {
        for i:=0; i< val.Len(); i++ {
            walk(val.Index(i).Interface(), fn)
        }
        return
    }

    for i := 0; i < val.NumField(); i++ {
        field := val.Field(i)

        switch field.Kind() {
        case reflect.String:
            fn(field.String())
        case reflect.Struct:
            walk(field.Interface(), fn)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

这工作，但它令人作呕。别担心，我们有测试支持的工作代码，所以我们可以随意修改。

如果你稍微抽象地想一想，我们想在两者中的任何一个上调用`walk`

*   结构中的每个字段
*   一片中的每一个*事物*

我们目前的代码做到了这一点，但没有很好地反映出来。我们只是在开始时检查它是否是一个片(用一个`return`来停止其余代码的执行),如果不是，我们就假设它是一个结构。

让我们重新编写代码，所以我们先检查类型*然后再做我们的工作。* 

```
func walk(x interface{}, fn func(input string)) {
    val := getValue(x)

    switch val.Kind() {
    case reflect.Struct:
        for i:=0; i<val.NumField(); i++ {
            walk(val.Field(i).Interface(), fn)
        }
    case reflect.Slice:
        for i:=0; i<val.Len(); i++ {
            walk(val.Index(i).Interface(), fn)
        }
    case reflect.String:
        fn(val.String())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来好多了！如果它是一个结构或者一个片，我们迭代它的值，在每个值上调用`walk`。否则，如果它是一个`reflect.String`，我们可以调用`fn`。

尽管如此，对我来说，感觉还可以更好。存在重复，即对字段/值进行迭代，然后调用`walk`的操作，但概念上它们是相同的。

```
func walk(x interface{}, fn func(input string)) {
    val := getValue(x)

    numberOfValues := 0
    var getField func(int) reflect.Value

    switch val.Kind() {
    case reflect.String:
        fn(val.String())
    case reflect.Struct:
        numberOfValues = val.NumField()
        getField = val.Field
    case reflect.Slice:
        numberOfValues = val.Len()
        getField = val.Index
    }

    for i:=0; i< numberOfValues; i++ {
        walk(getField(i).Interface(), fn)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果`value`是一个`reflect.String`，那么我们就像平常一样调用`fn`。

否则我们的`switch`将根据类型提取出两个东西

*   有多少个字段
*   如何提取`Value` ( `Field`或`Index`)

一旦我们确定了这些事情，我们就可以用`getField`函数的结果通过`numberOfValues`调用`walk`进行迭代。

现在我们已经做到了这一点，处理数组应该是微不足道的。

## 先写测试

添加到案例中

```
{
    "Arrays",
    [2]Profile {
        {33, "London"},
        {34, "Reykjavík"},
    },
    []string{"London", "Reykjavík"},
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk/Arrays
    --- FAIL: TestWalk/Arrays (0.00s)
        reflection_test.go:78: got [], want [London Reykjavík] 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

数组可以像切片一样处理，所以只需用逗号
将其添加到 case 中

```
func walk(x interface{}, fn func(input string)) {
    val := getValue(x)

    numberOfValues := 0
    var getField func(int) reflect.Value

    switch val.Kind() {
    case reflect.String:
        fn(val.String())
    case reflect.Struct:
        numberOfValues = val.NumField()
        getField = val.Field
    case reflect.Slice, reflect.Array:
        numberOfValues = val.Len()
        getField = val.Index
    }

    for i:=0; i< numberOfValues; i++ {
        walk(getField(i).Interface(), fn)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们想要处理的最后一种类型是`map`

## 先写测试

```
{
    "Maps",
    map[string]string{
        "Foo": "Bar",
        "Baz": "Boz",
    },
    []string{"Bar", "Boz"},
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestWalk/Maps
    --- FAIL: TestWalk/Maps (0.00s)
        reflection_test.go:86: got [], want [Bar Boz] 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

如果你稍微抽象地思考一下，你会发现`map`和`struct`非常相似，只是在编译时密钥是未知的。

```
func walk(x interface{}, fn func(input string)) {
    val := getValue(x)

    numberOfValues := 0
    var getField func(int) reflect.Value

    switch val.Kind() {
    case reflect.String:
        fn(val.String())
    case reflect.Struct:
        numberOfValues = val.NumField()
        getField = val.Field
    case reflect.Slice, reflect.Array:
        numberOfValues = val.Len()
        getField = val.Index
    case reflect.Map:
        for _, key := range val.MapKeys() {
            walk(val.MapIndex(key).Interface(), fn)
        }
    }

    for i:=0; i< numberOfValues; i++ {
        walk(getField(i).Interface(), fn)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，根据设计，您不能通过索引从地图中获取值。它只能通过*键*来完成，所以这打破了我们的抽象，该死。

## 重构

你现在感觉如何？这在当时感觉可能是一个不错的抽象，但现在代码感觉有点不可靠。

*这样就可以了！重构是一个旅程，有时我们会犯错误。TDD 的一个要点是它给了我们尝试这些东西的自由。*

通过循序渐进，这绝不是一个不可逆转的局面。让我们把它放回重构之前的样子。

```
func walk(x interface{}, fn func(input string)) {
    val := getValue(x)

    walkValue := func(value reflect.Value) {
        walk(value.Interface(), fn)
    }

    switch val.Kind() {
    case reflect.String:
        fn(val.String())
    case reflect.Struct:
        for i := 0; i< val.NumField(); i++ {
            walkValue(val.Field(i))
        }
    case reflect.Slice, reflect.Array:
        for i:= 0; i<val.Len(); i++ {
            walkValue(val.Index(i))
        }
    case reflect.Map:
        for _, key := range val.MapKeys() {
            walkValue(val.MapIndex(key))
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们引入了`walkValue`,它在我们的`switch`中干燥对`walk`的调用，这样它们只需要从`val`中提取出`reflect.Value`。

## 包装完毕

*   介绍了来自`reflect`包的一些概念。
*   使用递归来遍历任意数据结构。
*   回想起来，做了一次糟糕的重构，但并没有为此感到太沮丧。通过测试的迭代工作，这不是什么大不了的事情。
*   这只是反射的一个小方面。[Go 博客有一篇精彩的文章，介绍了更多细节](https://blog.golang.org/laws-of-reflection)
*   现在你已经知道了反射，尽你所能避免使用它