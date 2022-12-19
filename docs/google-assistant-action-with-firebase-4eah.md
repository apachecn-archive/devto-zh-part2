# 带 Firebase 的无服务器谷歌助手

> 原文：<https://dev.to/danquack/google-assistant-action-with-firebase-4eah>

本月早些时候，我[写了一篇关于设计基于事实的 Alexa 技能的帖子](https://dev.to/danquack/serverless-alexa-skill-with-dynamodb-191i)，该技能利用 DynamoDB 来提供响应。在这样做之后，我思考了用谷歌助手的动作来构建一个完全相同的体验会是什么样子。令我惊讶的是，这要难得多。这可能是学习一个新的云提供商的曲线，或者只是因为没有太多的模块来将持续交付引入开发。然而，我学到的是谷歌首先提供了 API 驱动的设计，同时利用机器学习来驱动意图，而不是硬编码的值。

在这个项目中，我想完成两件事。第一个是我可以开发一个解决方案的简单方法，在这个解决方案中，非开发人员可以采取行动并更新响应，所有这些都不需要接触实际的 API 代码。第二个是有一个持续的交付解决方案，这提供了一点头痛，但取悦前者。

在网上搜索，当我找不到一个无服务器的项目，或者至少找不到一个使用简单设计(比如 YAML)来驱动配置的项目时，我感到很惊讶。部署目录有一个脚本，它的唯一目的是维护配置，包含 SDK 提供的示例代码的包装。在设计动作时，利用 firebase 字段来驱动响应，我想利用谷歌提供的机器学习能力。实体名称代表指标，响应来自 firebase 中的 responses 项，利用类似于我的 Alexa 技能的字符串替换。通过更新 entities.yml 文件，可以增加或减少向用户开放的选项数量。

[![action-design](../Images/8867a2d66fd9d8ae6bccc2a06a8af561.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3jasMlK_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lucidchart.com/publicSegments/view/55a1678d-11a8-40d4-a61b-ab56c0af47b8/image.png)

因为我们进入了季后赛，比方说你提供了一个“丁格斯”的度量，和一个同义词“全垒打”。一旦您更新了文件并运行了 update-entities npm 脚本，您就可以让助手告诉您“我有多少个 dingers”，或者“有多少个本垒打”，response 和 total_count 字段将动态返回。
[![assistant-in-action](../Images/272b4220fd134ea12773b4a1bc8d7ce2.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--rRAfM8th--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lucidchart.com/publicSegments/view/6104f5be-debb-4634-9ef1-b2ad5efd9d70/image.png)

我想这就是为什么大联盟仍然没有叫我的号码…此外，这可能不是一个完整的持续交付工作，但它是一项正在进行的工作，同时仍然满足日常用户向您的度量行动添加实体的最初目标。

## ![GitHub logo](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [【丹呱】](https://github.com/danquack) / [无服务器谷歌助手加防火墙](https://github.com/danquack/Serverless-Google-Assistant-with-Firebase)

### google assistant 基于事实的操作，旨在报告 firebase 的指标

<article class="markdown-body entry-content p-5" itemprop="text">

# 带 Firebase 的无服务器谷歌助手

* * *

[![Build Status](../Images/aeab1ceca47db294ba8c8d4e9466fb18.png)T2】](https://travis-ci.org/danquack/Serverless-Google-Assistant-with-Firebase)

这个示例 google assistant dialogflow 是一个基于事实的操作，旨在报告 firebase 的指标。这个想法是，外部的东西将更新 firebase，然后通过谷歌助理读取。

## 设计

[![action-design](../Images/18e756de97e6a8328f93921cfc03ee5f.png)T2】](https://camo.githubusercontent.com/e6fc35a16a600d1508913b5a8badf50ba2004782/68747470733a2f2f7777772e6c7563696463686172742e636f6d2f7075626c69635365676d656e74732f766965772f35356131363738642d313161382d343064342d613631622d6162353663306166343762382f696d6167652e706e67)

## 先决条件

1.  [安装 NodeJS](https://nodejs.org/en/download/)
2.  一个[谷歌项目](https://developers.google.com/authorized-buyers/rtb/open-bidder/google-app-guide#create-a-google-cloud-platform-project)被创建
    *   该项目应该启用 firebase，云功能部署管理器 V2 API
    *   应该创建一个服务帐户。对于生产，角色应该受到限制，但现在所有者将工作。
    *   凭证 JSON 应该放在项目的根目录下，因为 serverless 将需要它

## 开发时间依赖项

1.  必须为事实创建一个 firebase 集合
    *   该集合应包含一个“公制”I 字段。该字段应该是小写空格分隔的单词
    *   集合应该具有“total_count”和“response”属性
2.  以下环境变量应该放入您的系统中
    *   `GOOGLE_APPLICATION_CREDENTIALS` -凭证文件的路径
    *   `PROJECT_ID` -可在…

</article>

[View on GitHub](https://github.com/danquack/Serverless-Google-Assistant-with-Firebase)