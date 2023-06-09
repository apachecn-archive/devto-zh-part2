# JQ 是一种症状

> 原文：<https://dev.to/ilya_sher_prog/jq-is-a-symptom-2e92>

jq 是一个很棒的工具。它做了 bash 做不到的事情——处理结构化数据。我用它。我希望*而不是*使用它。

在我看来，处理结构化数据是一件如此基础的事情，由语言本身来处理更有意义。我希望我的 shell 能够胜任，我强烈反对 shell“不应该这样做”的观点。壳牌应该尽一切努力让我的生活更轻松。处理结构化数据就是其中之一。

如果“shell 不应该这样做”，按照这种逻辑，bash 不应该做任何事情，除了运行外部命令和在它们之间路由数据。那么 bash *的*有内置的字符串操作是不是很奇怪？也许 bash 不应该在版本 4 中添加关联数组？…还是版本 2 中的阵列？`if`和`while`怎么样？也许巴什也不应该有它们？

[![woman-698943_640](img/8c58a0c6f9a9e40ae1cbd966c4d82e4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KGe76ZlZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ilyasherdotorg.files.wordpress.com/2018/09/woman-698943_640.jpg%3Fw%3D201)

**jq 是 bash 无法处理当今现实的征兆:结构化数据。**世界越来越关注 API。API 使用并返回结构化数据。我*用壳牌的 API 做*工作。你们不使用 AWS CLI 或任何其他返回 JSON 的 API 吗？

现实已经变了。巴什没有。我在研究 bash 替代方案。请帮我做这件事。或者至少传播这个消息。

如果你不喜欢我的项目，加入[精灵语](https://github.com/elves/elvish)。Elvish 是另一种支持结构化数据的 shell。

* * *

编码快乐！希望不是在巴什。