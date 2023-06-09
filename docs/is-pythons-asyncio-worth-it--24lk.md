# Python 的 asyncio 值不值？

> 原文：<https://dev.to/swizzard/is-pythons-asyncio-worth-it--24lk>

我从事 Python (2)开发已经大约 5 年了；我不是来抨击 Python 的。也就是说，这种语言的几个缺点之一是其糟糕的并发性/并行性。以前也有过对抗 GIL 的尝试，像 [Twisted](http://twistedmatrix.com/trac/) 、 [eventlet](http://eventlet.net/) 和标准库的[线程](https://docs.python.org/2/library/threading.html)模块，但是结果都(在我看来)过于工程化、过于复杂，就是不行...很好。

现在有了 [`asyncio`](https://docs.python.org/3/library/asyncio.html) ，它看起来更好，但仍然比不上 goroutines 或 Clojure 的 core.async(基本上是 goroutines)，或者 Rust 或 Haskell 提供的过多选项。

我对观众的问题是:`asyncio`值得吗？那就是:

1)如果你必须从零开始一个项目，你知道这个项目会涉及到并发和/或并行，并且有足够的选择自由，你会选择 Python 3.6 和`asyncio`？
2)如果你有一个预先存在的 Python 项目，你必须添加并发性/并行性，你会选择`asyncio`(而不是 Twisted、eventlet、gevent 等。)?将 Python 2.x 项目移植到 3.6 值得吗？