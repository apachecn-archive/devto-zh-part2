# 请使用 List::all utils；

> 原文：<https://dev.to/jplindstrom/use-listallutils-944>

你知道模块[列表::AllUtils](https://metacpan.org/pod/List::AllUtils) 吗？

它把`List::Utils`和`List::MoreUtils`的优点放在一个漂亮的包装里。或者...是`List::Util`和`List::MoreUtils`吗？`List::MoreUtil`？还是实际上是`List::SomeUtil`？ *Gaaahrhg！*

嗯，这就是它解决的问题。现在你可以忘记这些模块的名字了，继续你的生活。它还增加了一些来自`List::UtilsBy`的更好的功能。

感谢戴夫·罗尔斯基代表我们偷懒。

## RTFM

大致了解一下哪些类型的操作可用于处理列表，以便下次需要时使用，这是一个好主意。

在 [meta::cpan](https://metacpan.org/pod/List::AllUtils) 上阅读手册，或者在本地使用`perldoc`(安装模块后，可能使用 [cpanm](https://metacpan.org/pod/App::cpanminus) 客户端)。

```
perldoc List::AllUtils 
```

## 高亮显示

这里有一些你应该知道的基本语言特性的标准。

*   `sum`，`sum0`
*   `min`，`max`
*   `uniq`

和一些值得一看的细节。

*   `any`，`all`
*   `pairmap`，`pairgrep`
*   `pairwise`
*   `part`
*   `sort_by`，`nsort_by`

但是里面有很多更有用的功能。

正确使用这些方法，可以用更有表现力和更精确的流控制来代替 for 循环，从而为您节省大量时间和错误。

哪个功能对你帮助最大？*添加到下面的讨论*，或者/r/perl 上的！