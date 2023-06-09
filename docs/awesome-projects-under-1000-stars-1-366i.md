# 1000 颗星以下的牛逼项目(1) - Badgen

> 原文：<https://dev.to/gmartigny/awesome-projects-under-1000-stars-1-366i>

发现新项目不是一件容易的事情。每天都有成千上万的人流行，少数快乐的人变得出名。这使得大量的工作被每个人遗忘，有时是出于好的原因(无用的，复制的，维护不足...)有时毫无理由。

通过这篇文章，我打算每月一次对一个有趣的项目进行阐述。

因为这是第一篇帖子，我给自己定了一些基本规则:

*   web 领域中的任何项目都是合格的(库、工具、网站...)
*   只有托管在 github 上的开源软件(我想推广 OOS，但是我没有时间解析很多源代码)
*   1000 星以下的项目也在考虑之列，但越低越好
*   在给出任何评价之前，我会仔细挑选和测试每一个项目
*   项目应该是稳定的，维护和超过 1 个月的老

我非常高兴地欢迎评论中的任何建议。我会把它们都看一遍，如果它尊重最初的规则，我一定会写下来。

事不宜迟，我向你介绍

# [badgen](https://github.com/amio/badgen) 和 [badgen-service](https://github.com/amio/badgen-service)

> 是的，买一送一！

Badgen 可以用作在线服务或 javascript 库。

我相信你已经见过这样的储存库徽章:
[![example badge](img/13f9752a62903c7562116d5d2e6647aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---kYchcmO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://badgen.net/badge/dev/hello%2520world) 
嗯，使用 badgen 渲染从来没有这么容易和快速过。

badgen 是一款比 shields.io 更快的替代产品，速度惊人。构建 badge 的语法非常清晰且易于掌握。您甚至可以使用任意端点返回 JSON 格式的响应。

你可以在 [badgen 网站](https://badgen.net/)上查看所有的例子，但这里有一些例子，你可以通过相应的网址来体验一下:

*   静态值: [/badge/dev/to/black](https://badgen.net/badge/dev/to/black) ![static](img/bce6320da3d3d0d45e2a40e937aa5b60.png)
*   github stars:[/github/stars/the practical dev/dev . to](https://badgen.net/github/stars/thepracticaldev/dev.to)![github star](img/e17d80339d29fd54c14261d4b5e89c04.png)
*   NPM 家眷:[/NPM/家眷/左键](https://badgen.net/npm/dependents/left-pad) ![github star](img/b5b850f0c9e654a1b96731276231f1f3.png)
*   代码覆盖率:[/code climate/coverage/the practical dev/dev . to](https://badgen.net/codeclimate/coverage/thepracticaldev/dev.to)![code coverage](img/23152a40bdbf4863be291bcaf0fe5194.png)

为 JSON 提供“主题”、“状态”和“颜色”字段允许您构建定制的动态徽章。这里有一个愚蠢的例子，粗略地展示了我现在应该做什么。我创建了一个输出正确数据的 [runkit](https://runkit.com/gmartigny/5b8fa7a29cf9530012642696/) 脚本。然后在 badgen URL 中使用这个端点。

[/https/untitled-ljts 6 i6 xow 1 . run kit . sh](https://badgen.net/https/untitled-ljtss6i6xow1.runkit.sh)
[![doing now](img/c50b3cac62e58e176d89cd6274d57078.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WMl4sESI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://badgen.net/https/untitled-ljtss6i6xow1.runkit.sh)

最牛逼的，在我看来就是主维护者的反应能力。该项目非常活跃，您可以在几个小时内得到您的问题或公关的答案。

额外收获:代码清晰且组织良好(即使缺少文档)，是一个很好的学习点。

去看看吧，下个月再见。