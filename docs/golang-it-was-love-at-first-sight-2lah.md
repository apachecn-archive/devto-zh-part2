# Golang，那是一见钟情。

> 原文：<https://dev.to/bgadrian/golang-it-was-love-at-first-sight-2lah>

> 这篇文章最初发布在我的个人网站[https://coder.today/go-go-go-flash-bang-d66f4c42eb7c](https://coder.today/go-go-go-flash-bang-d66f4c42eb7c)上。与此同时，我在 Go 中开发了一些库和小应用程序，甚至为 Go Tour 做了贡献，我改变了一些观点，但我会保持这篇文章的原样，我喜欢不变性。

## “Go Go Go…闪光弹”

当我听到“开始”时，我总是会有这种[闪光…返回](https://soundcloud.com/cruizv/cs-go-go-go)。

## [T1】简介](#intro)

通常，当人们有空的时候，他们会做一些 R&R，但是我，很奇怪，我喜欢学习，尝试和玩新的东西。我有足够多的新爱好(今年早些时候我学会了撬锁&开始做[手机摄影](https://www.instagram.com/b.g.adrian/))，所以我决定加快我丢失的编程步伐。

**如果**{你和我一样(web 或移动开发者，出于任何原因避免了低级语言和并发)继续阅读，
**否则**你已经知道真相，跳到最后分享文章😁。}

我离开 web 开发有几年了，做 Unity3D (C#) & mobile，所以很自然地我开始追赶最新的 Web 趋势。我用[meteorojs(node+hot code+~ MongoDB+其他类固醇)](https://www.meteor.com/)、 **React** 和 **[D3JS](https://d3js.org/)** 做了一些小项目。通过切换到 nodeJS，我放弃了 PHP，只用一种语言工作，共享代码，我极大地提高了我的生产率。

**如果**{你是一名 JavaScript 开发者，请查看[这个 2017 年播放列表](https://www.youtube.com/playlist?list=PLEXf82oPg5EPIyYa1dbsLH3i3UcCa3kXp) }

## Go —编程语言

阅读关于新的架构风格、**可扩展性** &云我发现了一个模式，大部分[大公司(www 骨干网)](https://github.com/golang/go/wiki/GoUsers)都以这样或那样的方式转投了去。

当我读到它时，我发现我们有很多共同点(我和 Go)，这对任何关系来说都是一个好的开始。

[![](../Images/2bc41fc64326e89c20667c40376a52e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4gJx43Br--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AjwSiKwsOX7ONrx4kUksrJw.jpeg)

*   我们俩都非常**固执己见**

*   我们喜欢使用 JavaScript(构建 API 很容易，或者它们可以互补)

*   我们非常关心开发人员的生产力

*   我们喜欢**亲吻**。[简单更好(vs cpp)](https://github.com/SalGnt/cscs#c-2)

*   **1 种方法**去做(习惯用法、代码格式)。方法多了，项目就复杂了，开发者的大脑，团队工作，编译器的生活也就复杂了。

*   我们在 RegEx 很烂[(但是贡献者是](http://benchmarksgame.alioth.debian.org/u64q/go.html)[正在努力](https://github.com/golang/go/issues?utf8=%E2%9C%93&q=is%3Aissue%20is%3Aopen%20regex)

*   我们讨厌**bug**(所以我们实施编码规则)

*   我们喜欢[多任务](https://tour.golang.org/concurrency/1)和使用**大数据**。

**如果**{你是地鼠请帮我学快一点，复习一下我的代码:}
[BG Adrian/data-structures
* data-structures-Abstract data structures Go 包，构建时考虑了性能和并发性来学习 Go。](https://github.com/bgadrian/data-structures)

[![](../Images/15d6ef1bfa6cc53320a04077f5b7c7e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U63PcbvP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AUc-Ey0te8v80aV15zR_Jhg.png)

对我来说，Golang 还有几个优点:

*   它是低级语言和高级语言的完美结合。我想快速交货，把事情做完，所以倒垃圾不适合我。陡峭的学习曲线让我远离低级。

*   它集成了单元测试，我正在寻找开始实践 TDD 的理由。

*   经过 60 多个小时的会议、小组讨论和阅读论坛，我可以说这是一个不错的社区。

*   我想更多地参与到**开源**社区中，这是我的行动标志。

*   它有自己的身份&一个很酷的吉祥物

*   它创造了一个**愉快的环境**(好的工具、编译器警告、习惯用法等)。

*   学习编译语言将有助于获得优势&享受同时编写 JavaScript 和 WebAssembly 模块的乐趣。

[![](../Images/c792fb16750a6e05d2bad214632b07bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---rhQ4_Xu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2294/1%2Arkbogy-oxQQwOJiBSan9Vg.jpeg)

大多数[“反对”去的理由](https://github.com/ksimka/go-is-not-good)我认为它们是“赞成”的理由，这里有几个例子:

*   **缺少像异常这样的特性**会导致更好的代码，[谁想要这种混乱？](https://t.co/KRvLFdTqwd)。

*   当双方争论超过 15 分钟时，他们都是错的在这种情况下战争持续了十几年，所以死心眼的**固执己见的**是好事。不再有空格 vs 制表符，函数 vs 对象，for vs while …开发者写同样的代码，编译器可以优化得更好。

*   **重载**——通常会创建太多相似的代码，而且没有多态性，所以当你以 Go 风格编码时，它没有多大用处。重载使得“代码看起来很酷”，但是更难调试、维护和解释。我认为有了显式转换，接口就足够了(1 个函数做 1 件事，可以关联到 1 个接口)。

[![Actual c++ code](../Images/ee1a31ad1c77d93c13bb52210eddec2b.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--41gzGFb---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Atqxky0NtFY9AUu02XRHgSw.jpeg) *实际 c++代码*

*   代码是艺术，没有灵魂——我认为美妙之处不在于源代码的格式，而在于系统/架构层面，用简单的解决方案解决困难的问题。

*   大多数原因很容易被忽略:他们太主观，不理解 Go 范式，他们的思想停留在 OOP never land，或者他们将它与其他语言如 C/C++(它不是的替代品)进行比较，或者应用于 Go 范围之外的问题。

**如果**{你想通过练习[形来成为一名更好的程序员，检查代码战](http://www.codewars.com/r/vWPS5A) }

## 从 30 年的发展错误中吸取教训

好了，我说了！它回到了根，像凤凰一样回来了，更好了，有了新的面貌。

我花了太多时间调试和修复错误。我记得多年来我修复的许多修复、错误和反模式，其中许多都是在 Go 的编译器规则和编码风格中解决的[。](https://github.com/golang/go/wiki/CodeReviewComments)

大多数程序员都是人，我们有情绪和糟糕的日子，不同的经历和背景，不同的专业水平，技术/环境围绕代码发展。**糟糕的代码会一直存在**，但是如果我们说的语言( **Go** )可以修复很多问题(甚至在它们存在之前)，那就好了！

**如果**{您想学习围棋，请查看我的第一步:}
[**学习围棋的第一步**
*我如何学习作为我的新主要语言的围棋，我使用了哪些资源&技巧？*coder.today](https://coder.today/first-steps-in-learning-golang-d0f3dbb3b6d7)

[![](../Images/3b234f412d54a934701c984f0d9187a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JWJRgfJ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ape4RBWcHsCYwlw7XTOVlTw.jpeg)

完成，咆哮结束，感谢阅读！

感谢 ashleymcnamara，她的 gopher 小组激励我开始投稿。