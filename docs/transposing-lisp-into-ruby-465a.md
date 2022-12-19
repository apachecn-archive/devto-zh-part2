# 将 Lisp 转换为 Ruby

> 原文：<https://dev.to/runtime-revolution/transposing-lisp-into-ruby-465a>

<figure>[![](../Images/9b87f3e0005a4ffb0899f96b92b7f005.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ieQlThF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aqr6Wg3rdX4sFcd3YVpU9eA.jpeg) 

<figcaption>照片由[rawpixel.com](https://unsplash.com/photos/YTq-rFKuOKk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍于 [Unsplash](https://unsplash.com/search/photos/music-sheet-computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 。</figcaption>

</figure>

当你在同一项技术上工作很长时间时，你可能会变得与周围的世界疏远。在过去的八年里，我 90%的时间都花在了开发 Windows 桌面应用程序的 lisp 领域。Web 开发、移动开发、云基础设施、机器学习、数据分析……这些都是我喜欢在时事通讯中读到的话题，但我从未着手去做。这些都不在我公司的路线图中，因此我决定离开以前的公司去追求一些不同的东西。

所以我来到了运行时革命的土地，在那里我们处理网络和移动开发。

必须快速学习 Ruby！因此，在跟随教程的同时，我也记下了在 Ruby 中事情是如何完成的。今天我将分享其中的一些。

<figure>[![](../Images/5cfd261ed628ff9a994fc264802e62eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JrQK1wSF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ae_Os0W7jd_w-0387M2OSmA.png) 

<figcaption>条纹摘自 [Why's(辛酸)导读红宝石](http://poignant.guide/)。</figcaption>

</figure>

### 相似之处

我从 Lisp 转到 Ruby 不是偶然的。他们有许多共同之处。

在我之前的公司，我养成了在 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) (Lisp 的交互控制台)中尝试一些东西的习惯，看看它是否能自己工作，只有这样，我才会把它移到源代码中，并用项目的其余部分进行测试。现在，在运行时，我可以继续以与 Ruby 和 Rails(更不用说 JavaScript)分别提供交互式控制台、$ irb 和$ rails 控制台完全相同的方式进行开发。

在 Lisp 和 Ruby 中，函数参数都是通过值传递的。这意味着功能是非破坏性的，从而将副作用降至最低。这是一件好事！同样，在这两种语言中，变量作用域都被限制在创建它们的块作用域及其后代。对于 Ruby 来说，它可以是全局的、类的、实例的或局部的。

### 列表处理

任何语言都可以进行列表/数组/向量处理。Lisp 和 Ruby 都有足够高的抽象层次，这使得列表处理变得轻而易举。最好的一点是列表可以包含不同类型的对象。Lisp 的标准提供了大量的列表操作符。回过头来看，我用的最多的运算符一定是:mapcar、remove-if 和 loop。

mapcar 对列表中的每个元素应用一个函数，并返回一个新的列表。我经常用这个。