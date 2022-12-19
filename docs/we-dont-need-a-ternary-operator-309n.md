# 我们不需要三元运算符

> 原文：<https://dev.to/mortoray/we-dont-need-a-ternary-operator-309n>

作为紧凑代码的主要部分，三元运算符`?:`似乎已经在任何编程语言中赢得了一席之地。我经常用；你应该经常使用它。但是，我不喜欢运营商本身。感觉很复杂，不完整。虽然我已经停止了对 Leaf 的开发，但我确实找到了一个更好的选择。

让我们更深入地看看这个三元运算符是什么，然后展示一门语言如何避免它。

## 这是什么三元运算符？

这个操作者的名字引起了疑问。从典型的数学意义上，或者从解析器的角度来看，三元运算符是一个带有三个参数的运算符。操作符是一个具有特殊语法的函数，而不是通用的调用语法`foo(a,b,c)`。最常见的是二元运算符，我们随处可见。

```
//binary operators
x + y
x / y
x * y 
```

Enter fullscreen mode Exit fullscreen mode

还有一些我们常见的一元运算符。一元意味着他们只有一个论点。

```
//unary operators
-a
~bits
!cond 
```

Enter fullscreen mode Exit fullscreen mode

不缺少一元和二元运算符。但是我们有什么三元运算符的例子呢？

```
//ternary operators
cond ? true_value : false_value 
```

Enter fullscreen mode Exit fullscreen mode

你在挠头想另一个吗？据我所知，没有。这就是为什么我们最终称之为“三元运算符”，而不是使用一个合适的名称，如“条件运算符”。在编程语言中没有其他的三元运算符。

### 连锁运算符

我们先回避一下。有些运算符序列和组合可能看起来像三元运算符，但实际上不是。

例如，这个 Python 比较似乎涉及三个参数。

```
if a < b < c:
    go() 
```

Enter fullscreen mode Exit fullscreen mode

这看起来像一个三元运算符。它必须考虑所有三个参数才能正确评估。

然而，深入研究一下，这与其说是真正的三元运算符，不如说是一种语法糖。相当于以下。

```
once_b = b
if a < once_b and once_b < c:
    go() 
```

Enter fullscreen mode Exit fullscreen mode

你可能会找到几个只显示`a < b and b < c`的例子，但那些都是不正确的。最初的形式保证`b`只被评估一次。我用`once_b = b`来表示这种保证；我假设一个不同的内部特征在实践中实现了这一点。

这个 python 结构可以扩展为包含更多的值。

```
if a < b < c < d <e < e:
    go() 
```

Enter fullscreen mode Exit fullscreen mode

这是一个连锁运营商。可以添加的内容没有限制。

C++中的`<<`流操作符也可以链接。

```
cout << "Hello " << first_name << " " << last_name << endl; 
```

Enter fullscreen mode Exit fullscreen mode

与 Python 的链式比较不同，这个 C++序列不需要任何解析器支持。`<<`是一个普通的二元运算符，恰好是可链接的。基础数学也有这个相同的性质，比如`a + b + c + d`。

## 杂乱的语法和解析

我说过在编程语言中没有其他的三元运算符。这是有充分理由的。

看`cond ? true_value : false_value`。有没有什么东西让它看起来像是涉及到了三个论点？如果我稍微改变一下操作符，变成未知操作符会怎么样:`expr ⋇ value_a ⊸ value_b`。这看起来像两个二元运算符。即使我保留相同的符号，但添加嵌套的表达式，它看起来也不一样:`cond ? value_a + value_b : value_c * value_d`。没有直观的迹象表明`? ... :`是一个统一的运营商。这种语法限制阻止引入任何新的三元运算符。这会引起反弹。`?:`已经在一些程序员的黑名单中，因为它很容易被滥用。

尽管提供了有价值的语法，三元运算符在语法上是混乱的。用两个分开的符号定义三个参数很奇怪。有三个参数的函数不成问题，因为我们有一个健壮的语法。

优先级增加了复杂性。所有运算符都要求优先级。例如，`a + b * c`被评估为`a + (b*c)`。乘法的优先级高于加法。它的结果在加法之前被评估。

相同优先级的运算符也有结合性。例如，`5 - 2 + 3`被评估为`(5 - 2) + 3 = 6`，它是左关联的。右结合性将被评估为`5 - (2 + 3) = 0`，这是错误的。

右结合性通常保留给一元运算符(只有右参数)和赋值运算符。例如，如果你疯狂地写下了`a = b += c`，右结合性将它评估为`a = (b += c)`。

三元运算符是右结合的。虽然感觉不太对。一个有三个参数和两个符号的操作符怎么能有仅仅定义为左或右的结合性呢？

```
cond_a ? val_one : cond_b ? val_two : val_three

//right? associative
cond_a ? val_one : (cond_b ? val_two : val_three)

//left? associative (wrong)
(cond_a ? val_one : cond_b) ? val_two : val_three

//strict-left? associative (wacky)
(((cond_a ? val_one) : cond_b) ? val_two) : val_three

//strict-right? associative (nonsensical)
cond_a ? (val_one : (cond_b ? (val_two : val_three))) 
```

Enter fullscreen mode Exit fullscreen mode

这两种严格的形式在每个操作符上都应用了结合性，这很奇怪。从解析器的角度考虑一下。这是从语法上讲最有意义的两者之一。前两种形式需要一些技巧来绕过常规的关联解析。

试着想象一下嵌套的三元运算符会发生什么:`a ? b ? c : d : e`。你不会经常在代码中找到嵌套的 ternaries。它们太难在精神上解析了。

你会发现很多代码依赖于准右结合性。这就是连锁的原因。

```
int a = cond_a ? val_one :
    cond_b ? val_two :
    cond_c ? val_three : val_four; 
```

Enter fullscreen mode Exit fullscreen mode

## 一种二元溶液

当我在做 Leaf 的时候，我想避开三元运算符。我喜欢它提供的特性，但是我想找到一种更基本的方法来提供它。

解决方案以语言选项的形式出现。这些在 Leaf 中是基本的，因此有操作者的支持。

第一个经营者是违约者。如果设置了可选值，它将计算为该值。否则，它将评估为提供的默认值。

```
var a : optional
print( a | 1 )  //prints 1, since `a` has no value

var b : optional = 2
print( b | 3 )  //prints 2, since that's the value in `b` 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 中的`||`操作符在很多情况下达到了同样的效果。在 C#中有一个空合并操作符`??`，它使用`null`，就像 Leaf 使用未设置选项一样。

这听起来像三元运算符所做的一半。我们可以这样看:`cond ? some_val | default_val`。也就是说，考虑整个左边部分，`cond ? some_val`产生一个可选值。给定一个可选的，我们已经有操作符在未设置时默认该值。

Leaf 合并了`?`操作符来创建可选的。

```
var a = true ? 5  //a is an optional set to value 5
var b = false ? 6  //b is an optional without a value 
```

Enter fullscreen mode Exit fullscreen mode

就其本身而言，该操作符通常对选项很有用。结合默认操作符，它模仿了传统的三元操作符。

```
var a = cond ? true_value | false_value 
```

Enter fullscreen mode Exit fullscreen mode

```
int a = cond ? true_value : false_value; 
```

Enter fullscreen mode Exit fullscreen mode

`?`和`|`都是二元运算符。不需要有一个实际的三元运算符来产生相同的条件求值。我们得到了相同的特性，而没有语法上的混乱。发生了什么要清楚得多，解析器不需要任何技巧。

它还在不引入更多符号的情况下提高了表达能力，因为这两个运算符本身都很有用。

唉，我还没有看到任何计划将这一点添加到任何语言中。如果你知道一个，请留下评论。我很想看看。