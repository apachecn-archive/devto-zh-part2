# 我的第一个 NuGet 包！克里特纳。太阳能气候

> 原文：<https://dev.to/kritner/my-first-nuget-package-kritnersolarestimate-227k>

我发布了我的第一个 NuGet 包([https://www.nuget.org/packages/Kritner.SolarProjection/](https://www.nuget.org/packages/Kritner.SolarProjection/))！克里斯汀和我最近在考虑(并且已经资助了)一个 17，000 千瓦/年的太阳能电池阵列，在我们房子的后面——https://photos.app.goo.gl/gZzp7DnkeEcg1zQMA。在决定是否要这样做的过程中，我开始做一些电子表格，试图弄清楚什么时候“拥有太阳能电池板比我们正常的能源账单更便宜”。

有了这个电子表格，我觉得这似乎是一个尝试一些我没有做过的新事物的好机会——额外的 docker 工作、NuGet 包、一些基本的 angular、typescript、更多的单元测试。所以在晚饭后的几个星期里，我一直在修补我的网站，制作了[太阳能投影](http://www.kritner.com/solar-projection)。它最初包含在我的 https://github.com/Kritner/KritnerWebsite 的 github 回购中，但我需要弄清楚如何开始使用 NuGet，所以现在我在它自己的包中有了核心的“太阳能投影”逻辑——它的回购位于 https://github.com/Kritner/Kritner.SolarProjection 的[。](https://github.com/Kritner/Kritner.SolarProjection)

该网站目前并不漂亮，但它(希望是准确的)告诉了我我想知道的信息——太阳能电池板似乎对我们很好，只要我们能从电池板获得 90-100%的电力(它们保证 17k 的 90%)。

我希望能够继续探索 angular，通过一些图表和数据网格让它变得更漂亮，也许把它变成一个愚蠢的小应用程序，我可以从中赚几分钱？目前该网站只估计***我的* * *太阳能电池板阵列，但它应该是一个快速的变化，允许一些用户输入，所以其他人也可以使用它。

哦！如果有人感兴趣，可以在我得到用户输入之前用这个包来运行数字(或者你可以帮我做这件事？:D)。太阳能电池板是通过 [Vivint Solar](https://www.vivintsolar.com/) 安装的，如果你有兴趣获得一个系统，我会得到推荐奖金，找我吧！