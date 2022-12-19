# 你必须让你的 Python 代码运行得更快吗？

> 原文：<https://dev.to/cathodion/have-you-had-to-make-your-python-code-run-fast-36bp>

虽然性能经常被认为是人们怀疑 Python 的一个原因(见[@ grok code](https://dev.to/grokcode)[最近的帖子和对它的回复](https://dev.to/grokcode/99-problems-but-python-aint-one-1gj4))，但我自己在 Python 中并没有遇到很多性能问题。另一方面，我在 Python 中完成的大多数编程都不是毫秒级的(或者如果它们是重要的，它已经被像 NumPy 和 Matplotlib 这样的库优化过了)。

我必须处理的一个性能问题不是 Python 本身，而是必须等待许多数据库查询的结果，因此需要一次运行多个查询。在认识到线程已经足够(因为 Python 代码本身不受 CPU 限制)之前，我花了一些时间尝试让多处理工作起来。

您在 Python 中遇到过性能问题，或者不得不在资源受限的环境中使用它吗？你有什么样的问题？你做了什么来满足你的性能需求？

编辑补充:如果您在大量使用 Python 后从未遇到过性能问题，尤其是在您认为性能会成为问题的情况下，我也想听听您的看法。