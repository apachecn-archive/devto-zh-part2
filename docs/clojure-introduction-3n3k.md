# Clojure:简介

> 原文：<https://dev.to/maksugr/clojure-introduction-3n3k>

#### Lisp、Clojure 和 ClojureScript 快速入门

[**Clojure**](https://en.wikipedia.org/wiki/Clojure) 是 [Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language)) 的方言，具有动态类型系统的通用语言。它运行在 [JVM](https://ru.wikipedia.org/wiki/Java_Virtual_Machine) 上，可以编译成 JavaScript(这里我们已经在说 [**ClojureScript**](https://clojurescript.org/) )。Clojure 的主要特性(主要是 Lisp):

*   [同象性](https://en.wikipedia.org/wiki/Homoiconicity)
*   [反射](https://en.wikipedia.org/wiki/Reflection_(computer_programming))和[元编程](https://en.wikipedia.org/wiki/Metaprogramming)
*   不变
*   [一级功能](https://en.wikipedia.org/wiki/First-class_functions)
*   [功能编程](https://en.wikipedia.org/wiki/Functional_programming)
*   [REPL 驱动发展](https://clojure.org/about/dynamic)

最佳概览可在[官方网站](https://clojure.org/about/rationale)上找到。

如果我说 Clojure 没有语法，那是真的。它只是使用符号表达式( [s 表达式](https://en.wikipedia.org/wiki/S-expression)):函数调用是一个[列表](https://en.wikipedia.org/wiki/List_(abstract_data_type))，其中第一项是函数名，其余是参数。它被称为[前缀符号或波兰语符号](https://en.wikipedia.org/wiki/Polish_notation)。

```
; clojure

(+ 1 2) ; 3
(max 13 9) ; 13 
```

clojure 语法的核心概念到此结束！

List 是 Clojure 中最常用的数据结构之一，但是如果我们在代码中使用它，我们如何将它作为数据使用呢？我们需要将代码视为数据:在列表前添加引号前缀(')或使用特殊形式的引号。这真的有点像普通文本中的引用。当然，quote 不仅对列表有意义。

```
; clojure

'(1 2 3) ; (1 2 3)
(quote (+ 1 2 3)) ; (+ 1 2 3) 
```

太好了，我们将代码作为数据 Clojure 最独特的特性之一——感谢 quote！要将数据作为代码执行，可以使用 eval。

```
; clojure

(eval (quote (+ 1 2 3))) ; 6 
```

所以你可以做代码->数据->新数据->代码，并在运行时转换你的代码。这只是 Clojure 强大概念的一个例子！

这足以快速介绍 Clojure，请随意阅读本文中的所有链接。在下一篇文章中，我们将深入讨论语法。

**随时欢迎您关注** [**中**](https://medium.com/functionalhick) **和** [**推特**](https://twitter.com/maksugr) **、⭐️**[**【github】**](https://github.com/maksugr/functionalhick))**，阅读**[**functional hick . online**](https://functionalhick.online/)**。随时给我反馈，我会和你联系的🙌🏻**

* * *