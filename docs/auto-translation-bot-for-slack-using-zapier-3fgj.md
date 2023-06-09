# 使用 Zapier 的 Slack 自动翻译机器人

> 原文：<https://dev.to/rkowase/auto-translation-bot-for-slack-using-zapier-3fgj>

我用 Zapier 为 Slack 创建了一个自动翻译机器人。

## 如何使用

将指定 Zapier 的反应添加到您要翻译的评论中。

## 工作原理

当添加了 Zapier 指定的反应时，slack bot 将翻译后的注释发布到 thread 中。
[![](img/ef50481d04a37591da12eb46f535963f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--pDtPGzIv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6lobrdult35e1kwlp4g7.png)

## 如何创建这个 Zap

### 概述

[![](img/7d742055b1eb4aa650f21513a99e1b70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---XB3ZfUG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/905wdixupisun4yip3g4.png)

### 1。增加触发松弛(增加新的反应)

设置将触发哪个反应。

*   反应:`2en`(指定任意表情符号)
*   频道:(未指定)
*   用户:(未指定)![](img/d91a771aa9907c193986e3a182d6ce77.png)

### 2。添加过滤器-由 Zapier 过滤(仅在以下情况下继续...)

如果您想要触发特定频道，请添加此过滤器。
[![](img/fa45199b24329f6e2acfd5ae14f63d11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qIGud9Lw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdn6za8zrqkg66tlvfzt.png) (此任务可选)

### 3。由 Zapier 添加操作格式化程序(文本)

从永久链接中提取已反应评论的 ID，以防止多次发布。

*   变换:提取图案
*   数值(输入):第一步的`Message Permalink`
*   图案:`.*/(.*)(\?.*)?` ![](img/75a42de5b34833e81ce96579218aa535.png)

### 4。添加搜索松弛(查找消息)

搜索已回复评论的 ID 以防止重复发布。

*   搜索查询:`in:#<MESSAGE_CHANNEL_NAME> from:2en <OUTPUT_OF_STEP3>`
*   排序依据:(未指定)
*   排序方向:(未指定)![](img/27264416cdd5618a1111b7761f200770.png)

### 5。添加过滤器-由 Zapier 过滤(仅在以下情况下继续...)

过滤步骤 4 的`Permalink`，防止多次过账。
[![](img/a4eb1043f484453af476e311ac2b7009.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Oy5KEvI4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xz6a41mmqr7xtvek79ij.png)

### 6。添加操作-由 Zapier 翻译(翻译文本)

将原文翻译成英文。

*   源语言:(未指定)
*   正文:第一步的`Message Text`
*   目标语言:英语![](img/5989319bc92d03efa8f09970f48bed73.png)

### 7。添加行动-松弛(发送渠道消息)

张贴翻译后的评论。

*   频道:使用自定义值
*   通道 ID 的自定义值:步骤 1 的`Message Channel Name`
*   消息正文:`<TRANSLATION_OF_STEP6> <PERMALINK_OF_STEP1> original comment (<OUTPUT_OF_STEP3>) (<SOURCE_LANGUAGE_OF_STEP6>)` ![](img/b833c8d65196723761460ee9ee598b42.png)

发帖入帖。

*   线程:使用自定义值
*   线程时间戳的自定义值:步骤 1 ![](img/0c027360b3deaef0cb103788d1e24400.png)的`Message Ts`