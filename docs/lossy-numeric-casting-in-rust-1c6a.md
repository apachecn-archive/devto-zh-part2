# Rust 中的有损数字铸造

> 原文：<https://dev.to/benrcongdon/lossy-numeric-casting-in-rust-1c6a>

最近，我在试用[clippy](https://github.com/rust-lang-nursery/rust-clippy)——一款新的林挺和静态 Rust 分析工具，当时我遇到了一个 lint 警告，但我并没有立即意识到:`warning: casting u8 to u16 may become silently lossy if types change`。

对于上下文，这是我运行的代码片段`clippy` :

```
fn main() {
    let a: u8 = 0x12;
    let b: u8 = 0x34;
    assert_eq!(silly_max(a, b), b as u16);
}

fn silly_max(a: u8, b: u8) -> u16 {
    let x = a as u16;
    let y = b as u16;

    if x > y {
        x
    } else {
        y
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

本质上，我们获取两个 8 位无符号整数，将它们转换为 16 位无符号整数，并比较转换后的结果，返回两者中较大的一个。

在这段代码上运行`clippy`，这是我收到的 lint 错误:

```
warning: casting u8 to u16 may become silently lossy if types change
 --> src/main.rs:8:13
  |
8 | let x = a as u16;
  | ^^^^^^^^ help: try: `u16::from(a)`
  |
  = note: #[warn(cast_lossless)] on by default
  = help: for further information visit https://rust-lang-nursery.github.io/rust-clippy/v0.0.212/index.html#cast_lossless 
```

Enter fullscreen mode Exit fullscreen mode

现在，这段代码的问题还不是很明显。这是因为这段代码是*【正确】*的，目前，它做了它应该做的事情。

潜在的错误，正如`clippy`所暗示的，是如果我以后修改这段代码的话*会发生什么。假设在未来，程序的设计改变了，我实际上想找到两个`u32`的最大值，看看会发生什么:* 

```
fn main() {
    let a: u32 = 0x00000002;
    let b: u32 = 0x10000001;
    assert_eq!(silly_max(a, b), b as u16);
}

fn silly_max(a: u32, b: u32) -> u16 {
    let x = a as u16;
    let y = b as u16;

    if x > y {
        x
    } else {
        y
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们试图运行这段代码时，我们得到:

```
Running `target/debug/silly_max`
thread 'main' panicked at 'assertion failed: `(left == right)`
  left: `2`,
 right: `1`', src/main.rs:4:5 
```

Enter fullscreen mode Exit fullscreen mode

显然出了问题。这是一个典型的截断问题。当我们对`u32->u16`进行 cast 时，我们丢失了整数的前 16 个最高有效位。`0x10000001`被截短为`0x0001`，而`0x00000002`变成了`0x0002`。在截断的情况下，我们的比较操作并不总是像我们预期的那样工作。

幸运的是，在这种情况下，截断相当清楚。但是，我们的代码仍然可以编译。我们只是在运行时才注意到*我们悄悄引入了一个 bug。(正如`clippy`所警告的)*

让我们回到第一个版本，实现建议的修正`clippy`，使用`u16::from`而不是通过`as`强制转换。

```
fn silly_max(a: u8, b: u8) -> u16 {
    let x = u16::from(a);
    let y = u16::from(b);

    if x > y {
        x
    } else {
        y
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码如预期的那样编译和运行。现在，让我们进行相同的代码更改(将`a`和`b`的类型切换为`u32`)。

```
fn silly_max(a: u32, b: u32) -> u16 {
    let x = u16::from(a);
    let y = u16::from(b);

    if x > y {
        x
    } else {
        y
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们得到一个友好的编译器错误:

```
 --> src/main.rs:8:13
  |
8 | let x = u16::from(a);
  | ^^^^^^^^^ the trait `std::convert::From<u32>` is not implemented for `u16`
  |
  = help: the following implementations were found:
            <u16 as std::convert::From<bool>>
            <u16 as std::convert::From<u8>> 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们想要的！编译器让我们知道我们正在尝试进行不安全的翻译，而不是悄悄地将截断的可能性引入我们的程序。

但是如果我们*想要*这种可能的截断行为呢？嗯，你仍然可以使用`as`，你将得到截断的造型。有趣的是，如果你通过`as`做`u32 -> u16`，`clippy`不会抱怨——它假设你知道你在做什么。

那么，这个故事的寓意是什么？

> 更喜欢使用`std::convert::From`特征进行类型转换。关键字`as`执行“安全”的类型转换(类型转换不会引起恐慌)，但是您仍然可能遇到可以通过使用指定的转换器方法来避免的错误类别。

如果您有兴趣了解更多信息，这里有一些资源:

*   [Cast 操作语义 Rust 引用](https://doc.rust-lang.org/reference/expressions/operator-expr.html#semantics)
*   [cast _ 无损解释- Clippy](https://rust-lang-nursery.github.io/rust-clippy/v0.0.212/index.html#cast_lossless)