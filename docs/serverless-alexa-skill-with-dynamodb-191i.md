# 无服务器 Alexa 技能与 DynamoDB

> 原文：<https://dev.to/danquack/serverless-alexa-skill-with-dynamodb-191i>

随着个人助理的兴起，我想我应该学习一项新技能，并尝试发展自己的技能。我见过各种不同的用户使用 Alexa 报告度量标准的例子，以及不同的可维护性模型。在我所能找到的当中，没有一个将动态语句返回和易维护性结合起来。

我的第一个 Alexa 技能是作为一个报告工具创建的，我让不同的设备以不同的数值报告，例如有多少用户点击了一个事件，或者在给定的时间内通过我的路由器发送的总网络数据包。然后，我使用这些指标，并通过正则表达式替换，让 Alexa 向我报告，用一个干净，易于理解，数据库定义的句子。

在我的 DynamoDB 表中，我有三个属性，指标、total_count 和响应。当一个意图被调用时，我取该意图的名称，称之为`MyExampleIntent`，并在数据库中寻找“我的例子”(套管到空间)。如果有，我就取响应值- ex。`The total number of my example is {total_count}` -返回 total_count 属性中的数值。因此，当任何人想要添加新的意图时，他们可以用意图名称和示例话语更新无服务器文件，然后用该度量向表中添加新的项目。在下一次技能部署时，您将看到新的指标。就这么简单！

在我的 [GitHub](https://github.com/danquack/Serverless-Alexa-with-Dynamodb) 上查看这个项目，让我知道你的想法。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [丹嘎嘎](https://github.com/danquack) / [无服务器 Alexa-with-Dynamodb](https://github.com/danquack/Serverless-Alexa-with-Dynamodb)

### Alexa 基于事实的技能，旨在报告 dynamodb 的指标

<article class="markdown-body entry-content container-lg" itemprop="text">

# 带 Dynamodb 的无服务器 Alexa

* * *

[![Build Status](img/d704663b6541a7b732b00629b334c1ce.png)T2】](https://travis-ci.org/danquack/Serverless-Alexa-with-Dynamodb)

这个示例 alexa 技能是一个基于事实的技能，旨在报告 dynamodb 的指标。这个想法是，外部的东西将更新 dynamodb，然后通过 alexa 读取。

## 先决条件

1.  安装 nodejs(https://nodejs . org/en/download/)
2.  安装 aws cli 并设置配置文件([https://docs . AWS . Amazon . com/CLI/latest/user guide/CLI-chap-getting-started . html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html))
3.  创建一个开发者 alexa 账户([https://developer.amazon.com/alexa](https://developer.amazon.com/alexa))
4.  阅读供应商文件([https://github.com/marcy-terui/serverless-alexa-skills](https://github.com/marcy-terui/serverless-alexa-skills))

## 开发时间依赖项

1.  必须为事实创建 dynamodb 表
    *   该表应包含“公制”I 的主键。键应该是小写空格分隔的单词
    *   该表应该有一个“总数”和“响应”属性
2.  OATH2 是为 sls alexa 配置的([https://server less . com/blog/how-to-management-your-Alexa-skills-with-server less/](https://serverless.com/blog/how-to-manage-your-alexa-skills-with-serverless/))
    *   在这些指导中提供的是创造你的技能。创建技能，然后将技能 id 添加到环境变量中。`sls alexa create --name $ALEXA_SKILL_NAME --locale en-US --type custom`
3.  以下环境变量应该是…

</article>

[View on GitHub](https://github.com/danquack/Serverless-Alexa-with-Dynamodb)