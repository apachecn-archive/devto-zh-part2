# 总结 Elm 扩展策略

> 原文：<https://dev.to/elmupdate/summarising-elm-scaling-strategy-1bjn>

重新观看了 Richard Feldman 在 Elm Europe 2017 上关于扩展 Elm 应用的演讲。你可能看过。我参加了这次活动，但更多的是因为重复。

[https://www.youtube.com/embed/DoA4Txr4GUs](https://www.youtube.com/embed/DoA4Txr4GUs)

这是一个关于当不需要或不打算重用时，不要分解你的更新、模型和消息定义的极好的演讲。过早地分解代码是一种常见的趋势，尤其是如果你来自面向对象的世界。

Evan Czaplicki 在同一活动的“文件生活”中说，Javascript 思维也导致尽可能快地将所有东西分解成更小的文件和模块，即使只是为了避免意外突变带来的错误。
[https://www.youtube.com/embed/XpDsk374LDE](https://www.youtube.com/embed/XpDsk374LDE)T2】

特别是对于 update，其思想是避免返回 model 和 cmd 且必须映射回主更新函数的不必要的单独的小更新函数。

相反，Feldman 说要从过于庞大的更新分支中分离出较小的功能。给这些函数尽可能少的参数，并返回所需的最小值。

这使得调试更容易，因为您可以更快地看到代码的哪些部分与类型签名相关，并且您的升级函数更容易扫描。

为了避免打破消息定义和模型，我们的想法是创建使用可扩展记录的函数——考虑多态性—“关注接受模型子集的函数”。同样，这样做是为了缩小类型签名的范围，因此更容易调试。

您需要分解成一个单独的模型、消息、更新、视图的合法时间是对于一个可重用的“组件”,它有自己的状态。很明显，如果你重复使用一个函数，那么就有必要为每次使用准备一份单独的状态副本。

在这种情况下，html.map 和 cmd.map 是合并结果的好朋友。你也应该传递和返回你喜欢的东西，或者最少，而不是每次都不必要地发送消息，模型和返回模型，cmd 消息。

总的来说，费尔德曼的主题是让事物“小到足以装进我们的脑袋”，但不引入不必要的复杂性。记住这个目标，不要陷入从其他语言思考可能没有效果。