# Go 2 草案:泛型

> 原文：<https://dev.to/dean/go-2-draft-generics-3333>

# Go 2 正在起草中。

如果你还没有听说过，那么`Go 2`将会有一些重大的语言变化。Go 团队最近提交了这些变化的[草案](https://go.googlesource.com/proposal/+/master/design/go2draft.md)，作为 3 部分系列的一部分，我已经仔细阅读了每个部分，解释了它，并陈述了我对它们的看法！这是该系列的第二部分。上次我做了[错误值](https://dev.to/deanveloper/go-2-draft-error-values-lid)，这次我将讨论泛型！

请记住，这些只是草稿。它们很可能会被添加到语言中，但也有可能不会。当然，请记住，这些草稿并不是最终版本。语法可能会和现在有点不同。

这些可能不是语言的唯一变化，但可能是唯一的主要变化。

## 这么说，Go 是拿到仿制药了？

嗯，也许吧...记住这些只是*草稿*，可能不会这样实现，甚至根本不会！

但是在我们讨论为什么我们想要泛型之前，我们需要弄清楚*没有*有什么问题。

# 界面

所以，Go 有一种独特的使用接口的方式。这使得我们在许多情况下并不真正需要泛型，因为传入的值不需要显式实现接口，它们只需要有所需的方法！

但是有些事情是接口无法解决的。尤其是当涉及到通道、切片、地图和其他原始类型时。

# 为什么我们需要泛型

没有泛型，就不能编写像下面这样的通用函数*:* 

```
// Returns the keys from a map
func Keys(m map[K]V) []K

// Merges multiple channels into a single channel
func Merge(chans ...<-chan T) <-chan T 
```

Enter fullscreen mode Exit fullscreen mode

这些只是几个例子。实现这些的唯一类型安全的方法是为你想要实现的每种类型编写一个新函数，这与 Go 减少代码重复的目标相反。

# Go 中泛型的目标

那么，Go 想从泛型中得到什么呢？

*   Go 希望增加一种方式来“抽象掉不必要的类型细节”，比如上一节中的键和 Merge 函数。

*   他们不想针对特殊的实现，比如他们的例子，有一个通用的`Vector<T>`和一个特殊的`Vector<bool>`，它有位打包。

*   他们不想要图灵完全模板(如 C++)或类型擦除(如 Java)，或者揭示泛型系统内部工作方式的怪异案例。泛型实现应该能够很好地融入到语言中(再看看 Java 中的泛型数组类型...).

*   泛型类型信息在编译时和运行时都应该是可访问的。

# 提议的语法

好吧，Go 中的类型总是被声明的，泛型类型也不例外。虽然它们不能在 package 关键字中声明，但它们应该以`type`为前缀。

```
// A list implementation based on an internal slice.
type List(type T) []T

// A keys function which returns all keys of a map in a type-safe way.
func Keys(type K, V)(m map[K]V) []K 
```

Enter fullscreen mode Exit fullscreen mode

嘿，这是个不错的进步！

但是假设我们想要一个`IndexedSet(type T)`，并不是 Go 中的所有东西都是可比较的(例如，函数和地图目前在 Go 中是不可比较的)，并且值必须是可比较的，以便检查它是否已经在集合中！

这就是我们得到`contract`的地方。契约看起来很像函数，但是它们不是执行代码，而是指定对类型的限制。

让我们为我们的`Set(type T)`做一个名为`Equal`的契约。

```
contract Equal(t T) {
    t == t // assert that t must be able to use the `==` operator.
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以实现我们的`IndexedSet`！

```
// T must satisfy the `Equal` contract. This is shorthand for (type T Equal(T))
type IndexedSet(type T Equal) []T

// Returns the index of x in our IndexedSet, or -1 if
// x is not in the set.
func (s IndexedSet(T)) Find(x T) int {
    for i, v := range s {
        // both `v` and `x` are type T, which satisfies our
        // `Equal` contract
        if v == x {
            return i
        }
    }
    return -1
} 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在我们可以表示一组值，并通过索引访问每个值！

因此，我们已经表明，我们可以在数据结构上使用泛型，这样我们就可以将其推广到几乎任何类型。但是数据结构不是唯一可以有泛型的东西，记住我们也有函数！

让我们创建一个 Sum 函数，它对任何可求和类型的切片中的所有值求和。

```
contract Add(t T) {
    t + t
}

// (type T Add) is shorthand for (type T Add(T))
func Sum(type T Add)(x []T) T {
    var total T
    for _, v := range x {
        total += v
    }
    return total
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有一个函数，它将一个切片中的所有值相加，并返回结果。我们来看看怎么叫吧！

```
func main() {

        // =================================
        // Running `Sum` with ints
        // =================================

    var w []int
    wSum := Sum(int)(w)

        // =================================
    // Same as above, but with type inference
        // =================================

    var y []complex128
    ySum := Sum(y) // very familiar syntax!

        // =================================
    // Super clear as to how this works
        // =================================

        var foo func([]float32) float32
    foo = Sum(float32)

    var z []float32
    zSum := foo(z)

        // =================================
        // Compile error!
        // `Sum` may not be referenced without
        // defining its generic types.
        // =================================

    bar := Sum
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经介绍了他们非常确定自己想要的东西，那就让我们来谈谈他们不确定的东西。

# 开放式问题

因此，Go 团队仍然不确定关于泛型的一些问题。

### 1。隐含的约束

```
// Maps must have the `==` operator defined on their keys.
// Then the question begs, should the following function compile properly?
// Or should we be required to make a contract for K?
func Keys(type K, V)(m map[K]V) []K

// alternatively...

contract Equal(t T) {
    t == t
}

// Now, we apply the Equal contract to K
func Keys(type K, V Equal(K))(m map[K]V) []K 
```

Enter fullscreen mode Exit fullscreen mode

### 2。双重实施

由于泛型类型的真实类型在编译时和运行时都是可用的，所以当我们希望在一个类型上有一个泛型方法时，就会出现一个问题。这个问题有点难以解释，所以让我们举个例子:

```
// A set of values
type Set(type T Equal) ...

// Calls the function `f` on each value in Set(T),
// and then returns the results as a Set(U)
func (s Set(T)) Apply(type U)(f func(T) U) Set(U) 
```

Enter fullscreen mode Exit fullscreen mode

这是非常有用的，但也有后果...

考虑`Set(int).Apply(int)`评估为来自`Set(int).Apply(string)`的独立函数。

下面的代码会输出什么？

```
func main() {
    v := reflect.ValueOf(Set(int){})

    fmt.Println(v.NumMethod())
} 
```

Enter fullscreen mode Exit fullscreen mode

因为`Set(int).Apply(int)`和`Set(int).Apply(string)`是分开的，那么`v.NumMethod()`必须等于已定义类型的数量(理论上是无限的)。

另一方面，`Apply`方法仍然可以写成顶级函数，但是因为限制，不能是`Set(T)`上的方法。

```
func Apply(type T, U Equal(T))(s Set(T), f func(T) U) Set(U) 
```

Enter fullscreen mode Exit fullscreen mode

### 3。合同机构

本质上，他们想知道是否应该允许合同主体拥有任何代码，或者他们是否应该被限制为 go 代码的 T2 子集。

例如，我们是否应该允许以下行为？

```
contract C(t T) {
    if t == t {
        for i := 0; i < t; i++ {
            t = t + t
            t = t * t
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*(旁注:合同中的代码永远不会被执行)*

实际上，如果采用下面的形式，合同的可读性会更好:

```
contract C(t T) {
    t == t       // T is comparable
    0 < t        // T is relational to integers
    t = t + t    // T is assignable and has a + operator
    t = t * t    // T is assignable and has a * operator
} 
```

Enter fullscreen mode Exit fullscreen mode

# 我的看法

### 语义

我喜欢这个语义。我真的很喜欢对一个使用 int 作为泛型类型的函数求值的想法。看起来像是一个函数调用返回另一个函数，真的很不错。

### 语法

虽然`f(int)`背后的语义非常好，但它有一个巨大的缺点。

所以。很多。括号。

当前的函数声明语法类似于下面的内容:

```
func (Receiver) FuncName(Generic Type List)(Argument List) (Return Value List) 
```

Enter fullscreen mode Exit fullscreen mode

### 合同

我不喜欢合同。我觉得他们本可以做得更好，我已经[向他们的](https://gist.github.com/deanveloper/c495da6b9263b35f98b773e34bd41104)[维基](https://github.com/golang/go/wiki/Go2GenericsFeedback)提交了关于此事的反馈。我得到了回应，这很酷。可能需要调整我的反馈，因为

### 整体

我真的很喜欢仿制药。就我个人而言，我不认为围棋真的*需要*他们，但我会*喜欢*他们。我唯一的问题是，我觉得围棋是*解决*了很多，这真的不好。

直接来自仿制药草案概述:

> Go 中的多态性必须能顺利融入周围的语言，不能有尴尬的特例，也不能暴露实现细节。

如果这是你想要的，那么为什么我们要处理没有接收器的通用函数的尴尬的特殊情况呢？

从合同草案中:

> 为什么不用接口代替契约呢？
> *接口方法语法比较熟悉。用 x + x 编写契约体是普通的 Go 语法，但是它是程式化的、重复的，并且看起来很怪异。*
> 
> 不清楚如何使用接口方法表示运算符。我们考虑过像+(T，T) T 这样的语法，但是这是令人困惑和重复的。还有一个小点，but ==(T，T) bool 不对应==运算符，返回的是无类型布尔值，不是 bool。我们也考虑过简单地写+或者==。这看起来可行，但是不幸的是分号插入规则要求在行尾的每个操作符后面写一个分号。使用看起来像函数的契约以一些重复为代价给了我们一个熟悉的语法。这些都不是致命的问题，但都是难点。

TL；DR:他们找不出一个好的语法来定义接口的操作符...这真的是个好理由吗？

最后，我觉得他们为了让这个提议通过，已经做了相当多的决定，但这看起来不太可能。不是说这是一个糟糕的提议，也不是说我会对这种语言感到难过，但我觉得它可以做得更好。