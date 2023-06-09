# Clojure | Vasavi 开发者社区的函数式编程

> 原文：<https://dev.to/vasavideveloperscommunity/functional-programming-with-clojure--vasavi-developers-community-5h3h>

你好 Vasavi 开发者们，

我们非常兴奋地向您介绍函数式编程的概念，并学习一种叫做 Clojure 的新语言。

函数式编程(FP)是一种类似面向对象编程(OOP)的编程范式。在 OOP 中，你以类和对象的形式考虑程序，而在 FP 中，你写的一切都是函数。

在研讨会中，我们将向您介绍使用 Clojure 的函数式编程的概念。Clojure 是一种纯函数式编程语言，被苹果、网飞、Spotify 和脸书等公司用来构建 iTunes 软件。事实上，聊天应用 Whatsapp 是用另一种叫做 Erlang 的函数式语言编写的。

#### Clojure 在行动

假设您想要创建一个由前 10 个数字组成的数组，然后过滤掉偶数。在 Clojure 中，你只需简单地说

```
(->> (range 10)(filter even?) )
=> (0 2 4 6 8) 
```

Enter fullscreen mode Exit fullscreen mode

很有趣，对吧？

现在，假设我们想要在数组中的每两个元素之间插入数字 5，你只需要简单地调用`interpose 5`
就可以了

```
(->> (range 10)(filter even?)(interpose 5) )
=> (0 5 2 5 4 5 6 5 8) 
```

Enter fullscreen mode Exit fullscreen mode

相当干净！！！祝你在 C++中顺利完成所有的数组移位，新数组分配等等。

#### 议程

*   首先是一个让你兴奋的演示
*   什么是函数式编程？
*   函数式编程的核心概念
*   OOP 与 FP
*   函数式语言和谁使用它们
*   酷演示
*   了解更多信息的资源

该研讨会将于 2018 年 3 月 28 日在 Youtube 上发布。敬请关注！

如果你有任何建议或问题想问，请在下面评论。我鼓励愚蠢的问题:p。它甚至不需要与 Clojure 相关。