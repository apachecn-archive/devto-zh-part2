# 使用 Rustlang 作弊重新发明车轮

> 原文：<https://dev.to/jeffrey04/reinventing-wheels-with-cheats-using-rustlang-1b1c>

我终于花了一些时间和精力让自己学会了一点生锈的 T2。虽然我仍然在所有权和生存期(老实说，这基本上是该语言的一切)方面挣扎，但我发现它比 Golang 更有趣，Golang 相对来说很无聊，但很实用(没有双关语)。在学习语言的时候，我经常碰到的一件事是`Option` enum，然后我记得我读过[一些关于单子](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)的东西。

这是一个伟大的啊哈时刻！

因此，当我逐渐习惯于看到~~由于生存期和所有权~~枚举`Option`导致的编译错误，即`Some(value)`和`None`时，我觉得我找到了某种东西，以某种方式解决了我的沮丧，而这种沮丧是类型化语言无法解决的。我喜欢动态类型语言中的一点，这在 Golang 之类的类型语言中是不可能的，那就是当用户向函数发送垃圾时返回什么。当然，发送零值通常是我假设大多数人做的，但我更喜欢以不同的类型发送回一些东西，以区分“嘿，你在给我发送废话”和“嘿，结果是零”。

Rust 通过使用 enum 来回答这个问题，其中最常用的一个是标准库中提供的`Option`。后来我意识到在很多地方都有被使用，所以我开始多读一些，用它们做实验。所以在习惯了 enum 之后，重温这篇文章让我对理解 Monad 有了新的认识。在这之前我真的不明白这个帖子是关于什么的。总的来说，范畴理论太抽象了，我什么也没得到，甚至阅读/观看了许多演讲和教程帖子。

但是我现在知道的稍微多一点。

我想，如果有可能在 Rust 中通过一点欺骗做到这一点，因为最初的文章是为 Haskell 程序员写的。然后我想出了这样的东西(代码是概念验证的质量，很明显，可能违反了所有的所有权规则)。

首先，我从实现仿函数开始。函子是一个可以被函数映射的包装值。在拉斯特朗的土地上，我们大概可以把这表示为一种特质，如下:

```
pub trait Functor<T, U, V> {
    fn fmap(self, func: fn(T) -> U) -> V;
} 
```

所以 fmaps 接受一个函子，和一个接受展开值的函数，并返回另一个展开值。经过一些计算后，fmap 返回一个包装值。包装值的类比通常用于描述单子、应用程序和函子，这实际上是将值放入包装它的东西中，也许是一个盒子。发表这篇文章的包装值的类型是`Option`，但也有其他类型，例如向量形式的值列表，甚至是另一个函数/匿名函数/闭包。因此，对于上述特征，我们有以下三种形式的实现

```
impl Functor<i32, i32, Option<i32>> for Option<i32> {
    fn fmap(self, func: fn(i32) -> i32) -> Option<i32> {
        self.map(func)
    }
}

impl Functor<i32, i32, Vec<i32>> for Vec<i32> {
    fn fmap(self, func: fn(i32) -> i32) -> Vec<i32> {
        self.into_iter().map(|x| func(x)).collect()
    }
}

impl<F: 'static> Functor<i32, i32, Box<Fn(i32) -> i32>> for F where F: Fn(i32) -> i32 {
    fn fmap(self, func: fn(i32) -> i32) -> Box<Fn(i32) -> i32> {
        Box::new(move |x| {
            func(self(x))
        })
    }
} 
```

这正是我所说的欺骗。函数体清楚地表明 fmap 已经在 Rust 中以不同的方式实现了，我只是以不同的方式重新连接了它们。但是我这样做是为了进一步了解那些东西是什么，所以我猜想是这样的...还好吗？关于代码，我不确定的一点是，我可能应该借用一些东西，而不是将它们移入函数中，但是我放弃了与编译器搏斗，最重要的是，它们以某种方式工作了。

```
// Returns Some(2)
println!("Functor (wrapped value) {:?}", Some(1).fmap(|x| x * 2));
// Returns vec![2, 4, 6]
println!("Functor (vec) {:?}", vec![1, 2, 3].fmap(|x| x * 2));
// Returns 6
println!("Functor (function composition) {:?}", (|x| x + 1).fmap(|x| x * 2)(2)); 
```

然后我继续和 Monad。另一方面，Monad 实现了一个叫做 bind 的功能。

```
pub trait Monad<T, U> {
    fn bind(self, func: fn(T) -> U) -> U;
} 
```

与 fmap 不同，bind 所需的函数接受一个未包装的值，并返回一个包装的值。绑定本身返回一个包装的值。所以作弊

```
impl Monad<i32, Option<i32>> for Option<i32> {
    fn bind(self, func: fn(i32) -> Option<i32>) -> Option<i32> {
        self.and_then(func)
    }
} 
```

这次我只做了`Option<i32>`,因为我有点厌倦了与编译器错误搏斗，而且我已经有点明白这是什么了。懒惰也是我跳过应用程序的原因，部分原因也是因为我对 Rust 重新实现 lift 不太适应，lift 是定义应用程序的函数。也许当我对 Rust 更加熟悉时，我会再次尝试(也许会重新实现)。

所以我就说到这里，继续我的工作。