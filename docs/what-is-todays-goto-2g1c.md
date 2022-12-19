# 今天的“goto”是什么？

> 原文：<https://dev.to/jdsteinhauser/what-is-todays-goto-2g1c>

50 年前，Edgar Dijkstra 发表了一篇有争议的(在当时)社论，题为[“去发表被认为有害的声明”](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf)。在书中，他陈述了其他人已经暗示过的东西——特别是东尼·霍尔(ALGOL 和[十亿美元错误](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)的创造者)和约翰·麦卡锡(LISP 的创造者)——高级语言中的`goto`语句阻碍了对代码的理解。从那以后，我们看到了远离`goto`的运动...但是我们还没有完全弄清楚。

作为一名软件工程实习生，我的第一个项目是将传统的 FORTRAN 语言转换成真正的 OO C++...并且当执行代码库的完全转换时，使它成为从其他遗留 FORTRAN 调用的插入替换。这是在吉克斯特拉的社论发表大约 30 年后...1999 年，我和我的搭档发现在长达数百行的函数中,**过度使用了**。把代码打印出来，在标签和它们各自的`goto`之间画线，试图把逻辑改编成`if..else`和`return`、`continue`和`break`的对等词，这花了大半个夏天。

不幸的是，这并不是我最后一次遇到那个可怕的四个字母单词:`goto`。在我以前的一个项目中，我试图利用我在长发布周期中的“空闲时间”来删除一个相对较新的 C#项目中的`goto`。它被深埋在一种数值方法中。我花了一天的大部分时间试图找出如何在世界上我可以摆脱它，和...没有好的干净的方法。我打电话给编写代码的开发人员(他已经跳槽去了另一家公司，但我们仍然是好朋友)，问他“你到底在想什么？”在对我的不幸大笑了一场后，他说在我们的一位数据科学家的建议下，他从 C++移植了那个方法，那就是他无法干净地移除的**goto。因此，它留了下来，大概直到今天。**

对我来说，C#，一种在世纪之交开发的语言，仍然允许开发人员使用`goto`！Java 实际上将`goto`作为保留字，但将其标记为未使用。尽管 JavaScript 有很多缺点，但它没有将 goto 定义为关键字。当然，出于某种原因，这阻止了来自[的人将它们添加到](https://stackoverflow.com/questions/9751207/how-can-i-use-goto-in-javascript)中。

这让我想到了一个有趣的问题:在日常生活中，我们做了哪些我们可以做，但 T2 不应该做的事情？从那时起，人们就认定一些东西是“危险的”，从 [NULL](https://ewontfix.com/11/) [引用](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)，到 [IPv4 包装地址](https://tools.ietf.org/html/draft-itojun-v6ops-v4mapped-harmful-02)，甚至[整个芯片组家族](https://blog.invisiblethings.org/papers/2015/x86_harmful.pdf)。我发现了一个我(在某种程度上)同意的观点，关于电子的过度使用，由于它的严重膨胀。

你“认为有害”的做法和流程有哪些？JavaScript 无处不在？衡量软件质量的错误标准？所有的密码？大家在评论里讨论吧！