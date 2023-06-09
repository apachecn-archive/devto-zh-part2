# 添加一个

> 原文：<https://dev.to/o2sh/add-one-493i>

这是我用 Rust 写的一个小程序，可以让你给任何数字加 1

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ o2sh ](https://github.com/o2sh) / [ add-one](https://github.com/o2sh/add-one)

### 将数字加 1

<article class="markdown-body entry-content container-lg" itemprop="text">

# 添加一个

[![crate](img/40fbb861894d5fbf35021f7f0345733d.png)](https://crates.io/crates/add-one)[![documentation](img/67ddbe17cb25e0858bded9ede396e7ee.png)](https://docs.rs/add-one)[![minimum rustc 1.26](img/6115a55e20daa82e8297eec52d0bfd1e.png)](https://camo.githubusercontent.com/a250965d7b2eefa9bb18173f0f2497a22f239679d2eb8b1c7e1e0a458534e0fb/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f72757374632d253242312e32362d7265642e737667)[![Travis status](img/cd5dac20b33f09c5fd0c39acc6da5b03.png)T11】](https://travis-ci.org/o2sh/add-one)

> 返回 n + 1。

## 使用

将此添加到您的`Cargo.toml`:

```
[dependencies]
add-one = "1.0.0"
```

Enter fullscreen mode Exit fullscreen mode

这是你的箱子根:

```
extern crate add_one;
```

Enter fullscreen mode Exit fullscreen mode

## 例子

```
extern crate add_one;
use add_one::add_one;

use std::str;

fn main() {
    let mut bytes = Vec::new();

    match add_one("123".as_bytes(), &mut bytes) {
        Ok(()) => println!("{}", str::from_utf8(&bytes).unwrap()),
        Err(e) => {
            eprintln!("Error: {}", e);
        }
    }
}
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ cargo run 12
$ 13
```

Enter fullscreen mode Exit fullscreen mode

## 谢谢

*   [贡献者](https://github.com/o2sh/add-one/contributors)

## 许可证

许可依据

*   麻省理工学院执照( [LICENSE.md](https://raw.githubusercontent.com/o2sh/add-one/master/LICENSE.md) 或【http://opensource.org/licenses/MIT】T2

## 和睦相处

`add-one`板条箱经过 rustc 1.26 及更高版本的测试。

</article>

[View on GitHub](https://github.com/o2sh/add-one)

这个想法很简单。事实上，由于增加一个数字会使其大部分保持不变，所以处理十进制数字本身要快得多。

因此，在将输入分成一系列数字后，我们只需增加或减少(在负输入的情况下)最后一个数字。

#### *没那么快...*

如果数字的正输入有尾随的 9，负数有尾随的 0，那该怎么办？

好吧，如果数字有尾随的九(例如:7561325999)或尾随的零(例如:-1645000)，我们只需要递增最后一个“非九”数字(或递减最后一个“非零”数字)并将九转换成零和零转换成九(对于负输入)。

对于浮点输入(例如:15691.12313)，我们只需忽略小数部分，将前面的规则应用于整数部分。

#### *除非...*

...输入属于]-1，0[(例如:-0.2 + 1 = 0，8(不是 1.2！)).在这种情况下，我们必须给它一个特殊的待遇。

感谢阅读！:)