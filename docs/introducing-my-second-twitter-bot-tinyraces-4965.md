# 介绍我的第二个推特机器人@tinyraces

> 原文：<https://dev.to/michael/introducing-my-second-twitter-bot-tinyraces-4965>

TL；博士，看看我的新推特机器人🤖 [@tinyraces](https://twitter.com/tinyraces)

去年的今天，我宣布了我的第一个推特机器人、 [@year_left](https://twitter.com/year_left) 。自从发布@year_left 以来，每当这个账户的一条推文出现在我的时间线中，我都会很高兴。像魔术一样，一个机器人(更像一个 lambda 函数)运行一个脚本，噗，它出现在 Twitter 上。

尽管我热爱构建 web 应用程序，但我真的很喜欢构建 Twitter 机器人和命令行工具。这是因为我不必太关注设计，而是关注代码的内部工作。它们的范围也非常有限，通常只做一件事。

几天前，很高兴在我的时间线上再次看到@year_left tweet，我想我应该创造另一个机器人。我想用一些更古怪的东西。看着其他机器人，比如@tiny_star_field 和@tiny_forests，我决定让我的下一个机器人使用更多的表情符号，并在视觉上吸引人。

我想做一些与上面提到的其他机器人有所不同的事情，因为我想让它与关注者有更多的互动。想到点子，我想到了我所在城市一年一度的州博览会。有一个展览，各种各样的农场动物像鸭子和猪在一个正方形的跑道上比赛。这是如此滑稽，每个看过的人都非常喜欢。

所以我决定做一个 Twitter 机器人，让表情符号的竞争者在间隔时间里相互比赛，直到出现一个获胜者，它被称为 [@tinyraces](https://twitter.com/tinyraces) 。

@tinyraces 报道了全天发生的四场比赛。每场比赛都有随机的参赛者，包括车辆、动物甚至便便表情符号。当每场比赛开始时，比赛的每一段都会以 6 分钟的间隔在推特上发布，直到决出一名或多名获胜者。

每场比赛都应该是线程化的，这样就很容易从开始到结束捕捉比赛。

我喜欢做“tinyraces ”,但我承认，我遇到了很多障碍，有时几乎让我放弃这个想法。

*   从图形上看，当我第一次开始时，比赛看起来有点复杂，一旦竞争者赢得了比赛，他们就会“越过”终点线。相反，当参赛者到达终点线时，我最终宣布比赛结束。
*   当算法已经准备好生成比赛的不同阶段时，我不得不重构它，以便我可以保存每个间隔并发布它。在我进行重构的时候，我引入了一个错误，当我从上一次比赛结束的地方开始时，我没有正确地清理数据。这样做是为了给我的球道引入更多的角色。
*   发现表情符号具有 2 个字符的字符串长度，并确定它们在更新每个车道中的位置。
*   最后一个问题几乎让我放弃，那就是设置 cron 作业来运行脚本。我忘了我以前遇到过这个问题。问题是我选择的为比赛保存数据的方法是使用文本文件。每次我的 cron 作业运行时，文件都不会被读取或写入。仅谷歌一下[就浮现出我自己的 StackOverflow 问题](https://stackoverflow.com/q/44165640/703220)，这是我上次遇到这个问题时问的。不幸的是，这个解决方案并不奏效。直到我发现这个 [StackOverflow 线程](https://stackoverflow.com/questions/38937282/issue-with-node-fs-readfilesync-and-cron#comment65230519_38937282)，我才意识到问题是我的读写是相对于我运行的脚本设置的。在更新代码以使用读写文件的绝对路径后，我的机器人开始按预期工作了！

所以今天，我很高兴地宣布 [@tinyraces](https://twitter.com/tinyraces) ，这个推特机器人一整天都在进行小比赛。我希望你能关注它，我更希望它能让你开心。

* * *

最初[发布在 michaelsoolee.com](https://michaelsoolee.com/tinyraces/)上。

感谢您花时间阅读这篇文章！我很想保持联系，并通过我的时事通讯给你发送编程和设计的技巧，在家工作和制作副业。[点击这里报名](http://eepurl.com/bGXerj)。