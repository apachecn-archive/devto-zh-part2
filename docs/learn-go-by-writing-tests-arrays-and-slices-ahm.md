# 通过编写测试学习围棋:数组和切片

> 原文：<https://dev.to/quii/learn-go-by-writing-tests-arrays-and-slices-ahm>

这是 WIP 项目[的第二篇文章，通过编写测试](https://github.com/quii/learn-go-with-tests)来学习 Go，其目的是熟悉 Go 并学习 TDD 技术

[第一篇文章让你了解了 TDD](https://dev.to/quii/learn-go-by-writing-tests--m63)

# 数组和切片

数组允许您以特定的顺序在一个变量中存储多个相同类型的元素。

当你有一个数组时，迭代它们是很常见的。所以让我们用[我们新发现的`for`](https://github.com/quii/learn-go-with-tests/blob/master/for/readme.md) 知识来做一个`Sum`函数。`Sum`将接受一个数字数组并返回总数。

让我们使用我们的 TDD 技能

## 先写测试

在`sum_test.go`中

```
func TestSum(t *testing.T) {

    numbers := [5]int{1, 2, 3, 4, 5}

    got := Sum(numbers)
    want := 15

    if want != got {
        t.Errorf("got %d want %d given, %v", got, want, numbers)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

数组有一个*固定容量*，你可以在声明变量时定义它。有时在错误消息中打印函数的输入也很有用，我们使用的是“默认”格式的`%v`占位符，它对数组很有效。

[阅读更多关于格式字符串的信息](https://golang.org/pkg/fmt/)

## 试运行测试

通过运行`go test`，编译器将因`./sum_test.go:10:15: undefined: Sum`而失败

## 编写运行测试所需的最少代码，并检查失败的测试输出

在`sum.go`中

```
func Sum(numbers [5]int) (sum int) {
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

您的测试现在应该失败，并显示一条清晰的错误消息

`sum_test.go:13: got 0 want 15 given, [1 2 3 4 5]`

## 写足够的代码让它通过

```
func Sum(numbers [5]int) (sum int) {
    for i := 0; i < 5; i++ {
        sum += numbers[i]
    }
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

要从数组的特定索引处获取值，只需使用`array[index]`语法。在这种情况下，我们使用`for`迭代 5 次来遍历数组，并将每个条目添加到`sum`中

#### 关于源码控制的一个说明

此时，如果您正在使用源代码管理(您应该这样做！)我会照原样保留代码。我们有测试支持的工作软件。

不过，我*不会*推到 master，因为我计划下一步进行重构。在这一点上提交是很好的，以防你不知何故陷入重构的混乱——你总是可以回到工作版本。

## 重构

让我们引入`range`来帮助清理我们的代码

```
func Sum(numbers [5]int) (sum int) {
    for _, number := range numbers {
        sum += number
    }
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

让你迭代一个数组。每次调用它都会返回两个值，索引和值。我们选择使用`_`来忽略索引值

#### 回到源代码控制

现在，我们对代码感到满意，我将修改之前的提交，因此我们只签入代码的可爱版本及其测试。

### 数组及其类型

数组的一个有趣的特性是大小是以它的类型编码的。如果你试图将一个`[4]int`传递给一个需要`[5]int`的函数，它不会编译。它们是不同的类型，所以这就像试图将一个`string`传递给一个需要`int`的函数一样。

你可能认为数组是固定长度的很麻烦，大多数时候你可能不会用到它们！

Go 有*个切片*，这些切片不编码集合的大小，而是可以有任何大小。

下一个要求是对不同大小的集合求和。

## 先写测试

我们现在将使用切片类型，它允许我们拥有任意大小的集合。语法与数组非常相似，只是在声明数组时省略了大小

`mySlice := []int{1,2,3}`而不是`mySlice := [3]int{1,2,3}`

```
func TestSum(t *testing.T) {

    t.Run("collection of 5 numbers", func(t *testing.T) {
        numbers := [5]int{1, 2, 3, 4, 5}

        got := Sum(numbers)
        want := 15

        if got != want {
            t.Errorf("got %d want %d given, %v", got, want, numbers)
        }
    })

    t.Run("collection of any size", func(t *testing.T) {
        numbers := []int{1, 2, 3}

        got := Sum(numbers)
        want := 6

        if got != want {
            t.Errorf("got %d want %d given, %v", got, want, numbers)
        }
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

这不编译

`./sum_test.go:22:13: cannot use numbers (type []int) as type [5]int in argument to Sum`

## 编写运行测试所需的最少代码，并检查失败的测试输出

这里的问题是我们可以

*   通过将参数`Sum`改为切片而不是数组来打破现有的 API。当我们这样做的时候，我们会知道我们潜在地毁了某人的一天，因为我们的 *other* 测试无法编译！
*   创建新功能

在我们的例子中，没有其他人使用我们的函数，所以与其维护两个函数，不如只维护一个。

```
func Sum(numbers []int) (sum int) {
    for _, number := range numbers {
        sum += number
    }
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试运行测试，它们仍然无法编译，您将不得不更改第一个测试，以传入一个片段而不是一个数组。

## 写足够的代码让它通过

事实证明，修复编译器问题是我们在这里需要做的所有事情，并且测试通过了！

## 重构

我们已经重构了`Sum`,我们所做的只是从数组改变为切片，所以这里没有太多事情要做。请记住，在重构阶段，我们不能忽视我们的测试代码，我们在这里有一些事情要做。

```
func TestSum(t *testing.T) {

    t.Run("collection of 5 numbers", func(t *testing.T) {
        numbers := []int{1, 2, 3, 4, 5}

        got := Sum(numbers)
        want := 15

        if got != want {
            t.Errorf("got %d want %d given, %v", got, want, numbers)
        }
    })

    t.Run("collection of any size", func(t *testing.T) {
        numbers := []int{1, 2, 3}

        got := Sum(numbers)
        want := 6

        if got != want {
            t.Errorf("got %d want %d given, %v", got, want, numbers)
        }
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

质疑测试的价值是很重要的。目标不应该是进行尽可能多的测试，而是对你的代码库有尽可能多的信心。测试太多会导致真正的问题，只会增加维护的开销。**每次测试都有成本**。

在我们的例子中，您可以看到为这个函数进行两次测试是多余的。如果它适用于一种尺寸的切片，那么它很可能适用于任何尺寸的切片(在合理的范围内)。

Go 内置的测试工具包中有一个覆盖工具，它可以帮助你识别你没有覆盖的代码区域。我想强调的是，拥有 100%的覆盖率不应该是你的目标，它只是给你一个关于覆盖率的概念的工具。如果你对 TDD 要求严格，那么无论如何你都很有可能拥有接近 100%的覆盖率。

试试跑步

`go test -cover`

你应该看到

```
PASS
coverage: 100.0% of statements 
```

Enter fullscreen mode Exit fullscreen mode

现在删除其中一个测试，并再次检查覆盖率。

现在我们很高兴我们有一个测试良好的功能，你应该在接受下一个挑战之前提交你的伟大工作。

我们需要一个名为`SumAll`的函数，它接受不同数量的切片，返回一个包含每个切片总数的新切片。

例如

`SumAll([]int{1,2}, []int{0,9})`将返回`[]int{3, 9}`

或者

`SumAll([]int{1,1,1})`将返回`[]int{3}`

## 先写测试

```
func TestSumAll(t *testing.T)  {

    got := SumAll([]int{1,2}, []int{0,9})
    want := []int{3, 9}

    if got != want {
        t.Errorf("got %v want %v", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

`./sum_test.go:23:9: undefined: SumAll`

## 编写运行测试所需的最少代码，并检查失败的测试输出

我们需要根据测试的需要来定义 SumAll。

Go 可以让你编写变量函数，它可以接受可变数量的参数。

```
func SumAll(numbersToSum ...[]int) (sums []int) {
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

尝试编译，但我们的测试仍然无法编译！

`./sum_test.go:26:9: invalid operation: got != want (slice can only be compared to nil)`

Go 不允许对切片使用等式运算符。你*可以*写一个函数来迭代每个`got`和`want`切片并检查它们的值，但是为了方便起见，我们可以使用`reflect.DeepEqual`，这对于查看*是否有任何*两个变量相同很有用。

```
func TestSumAll(t *testing.T)  {

    got := SumAll([]int{1,2}, []int{0,9})
    want := []int{3, 9}

    if !reflect.DeepEqual(got, want) {
        t.Errorf("got %v want %v", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

(确保您的文件顶部的`import reflect`可以访问`DeepEqual`)

需要注意的是`reflect.DeepEqual`不是“类型安全”的，即使你做了一些傻事，代码也会编译。为了看到这一点，暂时将测试改为:

```
func TestSumAll(t *testing.T)  {

    got := SumAll([]int{1,2}, []int{0,9})
    want := "bob"

    if !reflect.DeepEqual(got, want) {
        t.Errorf("got %v want %v", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的是尝试比较一个`slice`和一个`string`。这是没有意义的，但测试编译！因此，虽然使用`reflect.DeepEqual`是一种比较切片(和其他东西)的便捷方式，但你在使用时必须小心。

再次改回测试并运行它，您应该得到如下所示的测试输出

`sum_test.go:30: got [] want [3 9]`

## 写足够的代码让它通过

我们需要做的是迭代 varargs，使用之前的`Sum`函数计算总和，然后将其添加到我们将返回的切片

```
func SumAll(numbersToSum ...[]int) (sums []int) {
    lengthOfNumbers := len(numbersToSum)
    sums = make([]int, lengthOfNumbers)

    for i, numbers := range numbersToSum {
        sums[i] = Sum(numbers)
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

很多新东西要学！

有一种创建切片的新方法。`make`允许您创建一个起始容量为我们需要处理的`numbersToSum`的`len`的切片。

你可以用`mySlice[N]`索引像数组一样的片来获取值，或者用`=`给它分配一个新值

测试现在应该通过了

## 重构

如前所述，切片具有容量。如果你有一个容量为 2 的片，试着做`mySlice[10] = 1`，你会得到一个*运行时*错误。

然而，您可以使用`append`函数，它接受一个切片和一个新值，返回一个包含所有项目的新切片。

```
func SumAll(numbersToSum ...[]int) (sums []int) {
    for _, numbers := range numbersToSum {
        sums = append(sums, Sum(numbers))
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个实现中，我们不太担心容量。我们从一个空的切片(在函数签名中定义)开始，并在遍历 varargs 时将`Sum`的结果附加到它上面。

我们的下一个需求是将`SumAll`改为`SumAllTails`，现在它计算每个切片的“尾部”总数。集合的尾部是除第一个项目(“头部”)之外的所有项目

## 先写测试

```
func TestSumAllTails(t *testing.T)  {
    got := SumAllTails([]int{1,2}, []int{0,9})
    want := []int{2, 9}

    if !reflect.DeepEqual(got, want) {
        t.Errorf("got %v want %v", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

`./sum_test.go:26:9: undefined: SumAllTails`

## 编写运行测试所需的最少代码，并检查失败的测试输出

将该功能重命名为`SumAllTails`，并重新运行测试

`sum_test.go:30: got [3 9] want [2 9]`

## 写足够的代码让它通过

```
func SumAllTails(numbersToSum ...[]int) (sums []int) {
    for _, numbers := range numbersToSum {
        tail := numbers[1:]
        sums = append(sums, Sum(tail))
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

切片可以切片！语法是`slice[low:high]`,如果你省略了`:`一边的值，它会捕捉它旁边的所有内容。在我们的例子中，我们说用`numbers[1:]`从 1 到结尾。您可能想花一些时间围绕切片编写其他测试，并尝试使用切片操作符，以便熟悉它。

## 重构

这次没什么可重构的。

如果你把一个空数组传入我们的函数，你认为会发生什么？空数组的“尾”是什么？当您告诉 go 从`myEmptySlice[1:]`中捕获所有元素时会发生什么。？

## 先写测试

```
func TestSumAllTails(t *testing.T)  {

    t.Run("make the sums of some slices", func(t *testing.T) {
        got := SumAllTails([]int{1,2}, []int{0,9})
        want := []int{2, 9}

        if !reflect.DeepEqual(got, want) {
            t.Errorf("got %v want %v", got, want)
        }
    })

    t.Run("safely sum empty slices", func(t *testing.T) {
        got := SumAllTails([]int{}, []int{3, 4, 5})
        want :=[]int{0, 9}

        if !reflect.DeepEqual(got, want) {
            t.Errorf("got %v want %v", got, want)
        }
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
panic: runtime error: slice bounds out of range [recovered]
    panic: runtime error: slice bounds out of range 
```

Enter fullscreen mode Exit fullscreen mode

哦不！需要注意的是测试*已经编译了*，这是一个运行时错误。编译时错误是我们的朋友，因为它们帮助我们写出有用的软件，运行时错误是我们的敌人，因为它们影响我们的用户。

## 写足够的代码让它通过

```
func SumAllTails(numbersToSum ...[]int) (sums []int) {
    for _, numbers := range numbersToSum {
        if len(numbers) == 0 {
            sums = append(sums, 0)
        } else {
            tail := numbers[1:]
            sums = append(sums, Sum(tail))
        }
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

我们的测试又有一些关于断言的重复代码，让我们把它提取到一个函数

```
func TestSumAllTails(t *testing.T) {

    checkSums := func(t *testing.T, got, want []int) {
        if !reflect.DeepEqual(got, want) {
            t.Errorf("got %v want %v", got, want)
        }
    }

    t.Run("make the sums of tails of", func(t *testing.T) {
        got := SumAllTails([]int{1, 2}, []int{0, 9})
        want := []int{2, 9}
        checkSums(t, got, want)
    })

    t.Run("safely sum empty slices", func(t *testing.T) {
        got := SumAllTails([]int{}, []int{3, 4, 5})
        want := []int{0, 9}
        checkSums(t, got, want)
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

一个方便的副作用是这给我们的代码增加了一点类型安全。如果一个愚蠢的开发者用`checkSums(t, got, "dave")`添加了一个新的测试，编译器会阻止他们。

## 包装完毕

我们已经覆盖了

*   数组
*   部分
    *   制造它们的各种方法
    *   它们如何具有固定的 T2 容量，但是您可以使用 T0 从旧的存储片创建新的存储片
    *   怎么切片，切片！
*   `len`获取一个数组或切片的长度
*   测试覆盖工具
*   以及为什么它很有用，但会降低代码的类型安全性

我们已经对整数使用了片和数组，但是它们也适用于任何其他类型，包括数组/片本身。所以如果需要的话可以声明一个变量`[][]string`。

[查看 Go 关于切片的博客文章](https://blog.golang.org/go-slices-usage-and-internals)深入了解切片。试着写更多的测试来证明你从阅读中学到了什么。

除了编写测试之外，另一种简便的 Go 实验方式是 Go playground。您可以尝试大部分事情，如果您需要提问，您可以轻松地共享您的代码。[我做了一个围棋场地，里面有一个切片，你可以用它来做实验](https://play.golang.org/p/ICCWcRGIO68)