# Rust 中的 Python 习语

> 原文：<https://dev.to/benrcongdon/python-idioms-in-rust-4381>

在过去的几个月里，我经历了一段编程语言漫游癖。最近，我对铁锈很感兴趣。我来自 Python，我发现 Rust 的许多语言功能非常强大。

今年早些时候，我深入研究了围棋。在大多数情况下，我喜欢围棋:这是一种简单的语言，可以在几个小时内学会(字面上)。它的结构促进良好的编程实践，并且它有一个奇妙的生态系统。

然而，在围棋中，我感觉受到了语言表现力的限制。它令人扼腕叹息的缺乏泛型并不像人们预期的那样具有破坏性，但却使得聪明的函数式编程技巧变得几乎不可能。这没什么——这不是 Go 的设计初衷——但结果是，我发现使用 Go 有点不“有趣”。

虽然 Rust 确实有额外的复杂性，来自它的[臭名昭著的借用检查器](https://m-decoster.github.io//2017/01/16/fighting-borrowchk/)和泛型的复杂性，但我发现你在表达能力上获得的东西是值得权衡的。

事实上，我发现 Rust 更像是一条蟒蛇 <sup id="fnref:1">[1](#fn:1)</sup> 。✨:当然，你必须小心类型和所有权，但有一个强有力的例子表明 Rust 分享了 Python 语法的许多优点。

在 Rust 中做了一点工作后，我找出了 Rust 和 Python 共有的一些习惯用法 <sup id="fnref:2">[2](#fn:2)</sup> 。这些常见的特性都是我喜欢在 Python 中使用的，我发现在熟悉 Rust 时这些特性很有用。

## 列举

在 Python 中迭代列表时的一个常见模式是使用`enumerate`让获取列表元素的索引和值:

```
arr = ['a', 'b', 'c']
for idx, val in enumerate(arr):
    print("{}, {}".format(idx, val))
# 0, a
# 1, b
# 2, c 
```

Enter fullscreen mode Exit fullscreen mode

Rust 迭代器也支持`enumerate`！(您甚至不必为相同的功能学习新名称)

```
let arr = vec!['a', 'b', 'c'];
for (idx, val) in arr.iter().enumerate() {
    println!("{}, {}", idx, val);
} 
```

Enter fullscreen mode Exit fullscreen mode

## Zip

Python 中另一个常见的迭代器函数是`zip`，它结合了两个元素迭代器，用每个列表的一个元素创建一个元组迭代器。

```
letters = ['a', 'b', 'c']
numbers = [1, 2, 3]
for l, n in zip(letters, numbers):
    print("{}, {}".format(l, n))
# a, 1
# b, 2
# c, 3 
```

Enter fullscreen mode Exit fullscreen mode

同样，Rust 迭代器也支持`zip` :

```
let letters = vec!['a', 'b', 'c'];
let numbers = vec![1, 2, 3];
for zipped in numbers.iter().zip(letters) {
    println!("{:?}", zipped);
}
// (1, 'a')
// (2, 'b')
// (3, 'c') 
```

Enter fullscreen mode Exit fullscreen mode

和 Python 的`zip`一样，Rust 的`zip`在任何一个迭代器耗尽时都会暂停。这允许你用无限迭代器做一些很酷的事情:

```
let start_at_10 = 10..;
let letters = vec!['a', 'b', 'c'];
for zipped in start_at_10.zip(letters) {
    println!("{:?}", zipped);
}
// (10, 'a')
// (11, 'b')
// (12, 'c') 
```

Enter fullscreen mode Exit fullscreen mode

## 元组

Rust 有元组！这是我非常怀念的 Go 功能。是的，元组也可以工作，但是对于“脚本”或原型工作来说，元组是一个很好的语言特性。

Python 的元组系统非常灵活。从功能上来说，Python 元组的行为类似于不可变列表。元组可以通过解包被别名化为单独的变量，并被索引到相似的列表中。

```
foo = ('foo', 1, None)
a, b, c = foo
assert a == 'foo'
assert foo[0] == 'foo'

# Tuples are immutable in Python tup = (1, 2, 3)
tup[0] = 4
# TypeError: 'tuple' object does not support item assignment 
```

Enter fullscreen mode Exit fullscreen mode

Rust 的[元组功能](https://rustbyexample.com/primitives/tuples.html)也相当强大。与 Python 类似，可以使用语法糖将元组值别名化为单独的变量，尽管在 Rust 中这被称为“析构”而不是“解包”。

```
let foo = ("foo", 1, None::<i32>);
let (a, b, c) = foo;
println!("{}", a); // "foo"
println!("{}", foo.0); // "foo" 
```

Enter fullscreen mode Exit fullscreen mode

另外，Rust 元组不是用方括号(`foo[0]`)索引的，而是用点运算符(`foo.0`)索引的。这是 Rust 元组在编译时调整大小的副产品，不像 Python 元组是动态的。

```
# Dynamically sized tuples in Python tup = tuple(['foo' for _ in range(randint(1, 10))]) 
```

Enter fullscreen mode Exit fullscreen mode

与 Python 不同，Rust 元组是可变的:

```
let mut tup = (1, 2, 3);
tup.0 = 4; 
```

Enter fullscreen mode Exit fullscreen mode

## 列举理解(种)

列表理解是 Python 语法糖的基础。这是一种生成/过滤列表的超级表达方式。

```
evens_squared = [x**2 for x in range(10) if x % 2 == 0]
# [0, 4, 16, 36, 64] 
```

Enter fullscreen mode Exit fullscreen mode

Rust 没有列表理解*本身*，但是 Rust 迭代器*有`map`和`filter`，这允许你执行类似的富有表现力的“列表”生成:* 

```
let evens_squared = (0..10)
    .filter(|x| x % 2 == 0)
    .map(|x| x * x)
    .collect();

println!("{:?}", evens_squared);
// [0, 4, 16, 36, 64] 
```

Enter fullscreen mode Exit fullscreen mode

就个人而言，比起 Python 的函数，我更喜欢 Rust 的扩展迭代器函数*。我发现像 [flat_map](https://doc.rust-lang.org/1.15.1/std/iter/trait.Iterator.html#method.flat_map) 和 [scan](https://doc.rust-lang.org/1.15.1/std/iter/struct.Scan.html) 这样的功能非常有用。这些迭代器函数带来了函数式编程的一些方面，这是我非常喜欢的。*

## 单行 If

Python 和 Rust 都没有[三元运算符](https://en.wikipedia.org/wiki/Ternary_operation)(这可能是最好的)。但是，它们都支持单行条件表达式。这在某些情况下可能是有用的，但它实际上只是语法上的糖。

```
result = expensive_function() if some_condition else None

let result = if some_condition { expensive_function() } else { 0 } 
```

Enter fullscreen mode Exit fullscreen mode

## λ函数

Python 的一个经常被抱怨的语言特性是它的`lambda`函数。它们很有用，但是语法不太好。其他语言如 Ruby 和 Javascript 有更简洁的语法来指定闭包或内联函数。

Python `lambda` s 接受一组参数，并返回跟随`:`
的表达式的求值结果

```
arr = [3, 10, 15]
sorted(arr, key=lambda x: abs(10-x))
# [10, 15, 3] 
```

Enter fullscreen mode Exit fullscreen mode

Rust 闭包同样具有表现力。它们使用管道(`|`)来保存参数，后面的表达式用作返回值。

```
let mut arr: Vec<i32> = vec![10, 15, 3];
arr.sort_by_key(|a| (10 - a).abs()); 
```

Enter fullscreen mode Exit fullscreen mode

Rust 闭包也可以是使用括号的多行函数，这非常方便:

```
let mut arr = vec![10, 15, 3];
arr.sort_by_key(|a| {
    let intermediate_value = some_function(a);
    intermediate_value + 10
}); 
```

Enter fullscreen mode Exit fullscreen mode

Python `lambda`和 Rust 闭包都执行变量捕获，因此您可以访问创建 lambda/闭包的范围内的变量:

```
a = 10
plus_a = lambda x: x + a
plus_a(5)
# 15 
```

Enter fullscreen mode Exit fullscreen mode

```
let a = 10;
let plus_a = |x| x + a;
plus_a(5); // 15 
```

Enter fullscreen mode Exit fullscreen mode

## 一等功能

在 Python 中，函数本质上是用`__call__`神奇函数调用的对象。它们可以像任何其他对象一样传递，甚至可以为它们分配属性。这对于传递回调函数很有用。虽然惯用的 Python 没有大量使用函数作为变量，但这个特性经常会派上用场。

```
def plus_one(x):
    return x + 1

def map_on_list(arr, func):
    return [func(x) for x in arr]

arr = [1, 2, 3]
map_on_list(arr, plus_one)
# [2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

Rust 也有一流的功能。函数和闭包可以用作其他函数的参数:

```
fn plus_one(x: i32) -> i32 {
    x + 1
}

fn map_on_vec(vec: &Vec<i32>, func: fn(i32) -> i32) -> Vec<i32>
{
    let mut new_vec = Vec::new();
    for i in 0..vec.len() {
        new_vec.push(func(vec[i]));
    }
    new_vec
}

let vec = vec![1, 2, 3];
println!("{:?}", map_on_vec(&vec, plus_one));
// [2, 3, 4]

// Equivalently:
println!("{:?}", map_on_vec(&vec, |x| x + 1)); 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，Rust 函数和闭包之间有一些微妙的区别。 <sup id="fnref:3">[3](#fn:3)</sup> 一般来说，它们可以被当作同一件事来处理。当然，Rust 中的函数是*而不是*对象，但是 Rust struct 可以实现 [Fn](https://doc.rust-lang.org/std/ops/trait.Fn.html) 、 [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html) 或 [FnOnce](https://doc.rust-lang.org/std/ops/trait.FnOnce.html) 特征，达到类似 Python 的`__call__`的效果(尽管这是[不推荐](https://stackoverflow.com/a/38672502/2421634))。

## 结论

希望我已经给出了一点提示，为什么一个 Python 开发者可能会对生锈感兴趣(出于审美原因，如果没有其他原因的话！).Rust 有一个明确的学习曲线，但它是一种快速的语言，仍然具有很强的表现力。

从语法的角度来看，我发现 Rust 比 Python 更像 Python。使用泛型和类型推断，你可以最终写出只有最少“类型样板”的 Rust 代码，并且*感觉*非常类似于 Python。

如果您对 Rust 感兴趣，我强烈推荐以下资源:

*   Rust 编程语言 -官方(免费)书籍，带你了解 Rust 语言
*   Rust 语言文档——Rust 语言的 API 文档——它的搜索功能非常强大
*   [/r/Rust](https://www.reddit.com/r/rust/)-Rust 子编辑

*封面:[pix abay](https://pixabay.com/en/crab-baskets-scotland-fishing-2550284/)T3】*

## 脚注

1。 [阿明·罗纳彻](https://twitter.com/mitsuhiko)做了一个[关于 Rust 和 Python 配对的精彩演讲](https://www.youtube.com/watch?v=zmtHaZG7pPc)，它扩展了 Rust 是“Python”的概念

2。其中一些可能更好地定义为句法糖或语言特征。😛

3。里卡多·马丁斯写了一篇[的博文](https://ricardomartins.cc/2015/10/12/practical_differences_between_rust_closures_and_functions)讲述函数和闭包之间的区别