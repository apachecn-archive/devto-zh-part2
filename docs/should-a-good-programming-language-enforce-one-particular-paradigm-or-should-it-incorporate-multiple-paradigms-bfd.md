# 一门好的编程语言应该强制一种特定的范式还是应该包含多种范式？

> 原文：<https://dev.to/donaldsebleung/should-a-good-programming-language-enforce-one-particular-paradigm-or-should-it-incorporate-multiple-paradigms-bfd>

我已经有了几年使用主要支持命令式编程(声明变量、创建循环、维护隐式状态)的编程语言的经验，但我在过去几个月中使用了 Haskell(它严格执行纯函数式编程)，有时我希望 Haskell 至少能为某种形式的命令式编程(类似于 F#)提供基本支持——毕竟，对于某些类型的问题，创建一个循环并改变几个变量比用递归、单子等方式表达一切要容易得多。但是当我看着像 Swift 这样试图将过程化、面向对象和函数式编程合二为一的语言时，我看到的是一片混乱——它试图让使用类似 C 语言的开发人员看起来很熟悉，但几乎不像 C(或者甚至是 Java、C#、JS 等等)；它试图像 Python/Ruby 一样通过借用某些语法特性来使自己变得简洁和可读，但实际上要冗长得多，并且其超强的类型系统及其“功能性”特性似乎在大多数时候都碍事。

你觉得怎么样？一种“好的”编程语言(不管它意味着什么)应该像 Haskell 一样对各种各样的问题严格执行一种特定的范式吗？它应该强调一种特定的范式，但也允许像 F#这样的其他方法吗？还是应该尝试尽可能多地结合不同的范式，让程序员决定对每个问题使用哪一种(例如 Swift 或 C++)？欢迎在下面留下评论来解释你的观点:)