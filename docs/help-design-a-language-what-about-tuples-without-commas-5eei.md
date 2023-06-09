# 帮助设计一门语言:没有逗号的元组怎么办？

> 原文：<https://dev.to/mortoray/help-design-a-language-what-about-tuples-without-commas-5eei>

我发现你喜欢[给](https://dev.to/ben/why-doesnt-css-move-faster-like-javascript-1lel) [关于语言设计的反馈](https://dev.to/17cupsofcoffee/how-do-you-feel-about-braces-and-semicolons-2bpc)。为设计贡献一种新的和不断发展的语言怎么样:[叶](http://leaflang.org)。

> 即使你对一门新语言不感兴趣，也可以随意参与。

让我们从一个简单的语法决策开始。是否应该允许您编写不带逗号的元组，就像我允许语句不带分号一样:

带逗号的元组:

```
var items = [
    one,
    two,
    three,
] 
```

Enter fullscreen mode Exit fullscreen mode

没有逗号的元组:

```
var items = [
    one
    two
    three
] 
```

Enter fullscreen mode Exit fullscreen mode

第二种方法依靠行尾来分割值。如果你需要在一行上做一个内联元组，逗号形式总是可用的`[one, two, three]`。

如果你想忽略问题的“元组”部分，就把它们当作“没有逗号的数组”。

## 询问、评论、批评

我将让这些问题稍微开放一些，没有太多的背景或动机。得到一种本能的反应是有帮助的。我显然有自己的观点，但我很乐意改变我的语言。

请提问，关于语言设计，语法，编译器，或者任何你想问的问题。有时候有细节帮助你决定，有时候你可能只是好奇。例如，有理由问“删除这些逗号会产生任何技术解析问题吗？”