# 为什么你也应该用香草编码

> 原文：<https://dev.to/dhruv/why-you-should-code-in-vanilla-too--45kf>

在过去的几年里，我变成了一个到处都喜欢香草、冰淇淋、奶昔、蛋糕或代码的人。

就像几乎(所有人)一样，我开始用 [jquery](https://jquery.com/) 构建网站，因为它相对容易掌握/理解，并且需要编写的代码较少。然后是角吊杆，我开始在[角梁](https://angularjs.org/)上建造项目。

尽管我曾经不时地写一些普通的 javascript，但是随着我的项目越来越大，这些错误变得难以理解。

是的，框架将帮助你更快地构建东西。是的，框架是为了让事情变得更简单。但是随着项目的发展，有时你知道有问题，但是不知道为什么。框架是为解决特定类型的问题而构建的，因此我们有不同的框架来解决不同类型的问题。随着项目的增长，你将会有不同的用例以及不同的问题，我向你保证，你通过使用一个框架节省下来的所有时间将会浪费在解决一个你不知道的 bug 上。

现在我来告诉你为什么要写更多更难(不是)的代码。

1.  **轻量级应用**
    添加任何依赖都会自动增加你的应用大小、更多 http 请求和更多加载时间。
    最常用的 JavaScript 库/框架的文件大小(minified):
    [*Vue*](https://vuejs.org/):58kb
    [*Jquery*](https://jquery.com/):93kb
    [*React*](https://reactjs.org/):98kb
    [*Angular 1 . x*](https://angularjs.org/):143 kb
    [*Ember*](https://www.emberjs.com/)

2.  没有依赖性
    你完全拥有自己的项目。你对你的项目了如指掌。如果有一个你需要的 bug/特性请求，那么你不必等待和期待你的库/框架的下一个版本，或者更糟的是增加一个额外的依赖来完成事情。

3.  **学习新事物**
    当你没有任何抽象层时，你倾向于探索和学习新事物。您将寻找该语言提供的解决方案，并了解 JavaScript 隐藏的瑰宝。你对语言的理解变得更好，显然你对你的项目理解得更好。

4.  **为开源做贡献**
    你使用的所有(大部分)库都是用普通 JS 编写的。既然现在你对 JavaScript 有了更好的理解，你可以很快理解任何库的大代码库，并为它们做出贡献。

5.  **更好的安全**
    是的你没看错。通过使用库，您可能会使您的网站更容易受到攻击，或者可能会危及您的用户数据。有可能是某个外部依赖获取了您的用户数据，并将其发送到他们的服务器。更多细节请阅读这篇[帖子](https://hackernoon.com/im-harvesting-credit-card-numbers-and-passwords-from-your-site-here-s-how-9a8cb347c5b5)。

6.  **深入到服务器端**
    随着对语言的更好理解，你可以很容易地工作 [node.js](https://nodejs.org/en/) (构建于 [v8](https://developers.google.com/v8/) )并且最终可以编写你自己的后端。

这篇文章最初发表在[媒体](https://medium.com/@nendhruv/why-you-should-code-in-vanilla-too-e85baa076919)上