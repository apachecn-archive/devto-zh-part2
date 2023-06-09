# 变更日志:导出帖子

> 原文：<https://dev.to/rhymes/changelog-export-posts-1kdm>

*小镇有了新特色*😅

您可以将您的 dev.to 导出到帖子，您将收到一封附有 zip 文件的电子邮件，其中包含您所有帖子的 JSON。

## 我该怎么做？

你去[https://dev.to/settings/misc](https://dev.to/settings/misc)选择导出，但我也准备了一些截图，所以请见谅:

[![](img/cbebd17bd25a2d5049df5100d9baa8e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2e7Kd726--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ig9atdckpkif6muw5wo4.png)

提交表单后，会出现以下情况:

[![](img/a057c7ba1e984761c7bfca996e7776d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uBsEoYpW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/adzmzqzbgjig955ynzvb.png)

过了一会儿，你的收件箱里会收到这样一封邮件:

[![](img/18e21d395909501f328601b0a2ee93da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X-JYtE_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a1307ztb21mxiju7zp1x.png)

这是文件内容的一个示例:

[![](img/debda96dd85c80495991af1258d445df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VWgXNEfJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3069er1zt7uptoe6xgc0.png)

## 我是一名开发人员，你是如何做到的？

从某种意义上说，这个特性非常简单:

*   你要求出口
*   后端会注意到这一点
*   一个后台作业启动，收集帖子，转换 json 中的数据和元数据
*   在内存中创建一个 zip 文件，并将 json 文件添加到这个 zip 文件中
*   zip 文件被附加到电子邮件中
*   电子邮件已发送

## 关于这个特性的一些背景故事

几个月前，在一个[特性请求](https://github.com/thepracticaldev/dev.to/issues/219)的提示下，我开始着手[一个拉取请求](https://github.com/thepracticaldev/dev.to/pull/576)来做上面的事情。然后我把它忘得一干二净，然后我提交了它，但它并不是很好，因为我对代码了解不多(仍然不太了解:D ),因为它本来可以做得更好。

尽管如此，我的公关还是和[@本](https://dev.to/ben)、[@梅斯托马克](https://dev.to/maestromac)和[@杰斯](https://dev.to/jess)开始了一次对话，在一次重构和另一轮审查之后(拉请求万岁！)今天被合并的是一些东西...只是更好。

## 剩下的数据呢？

我知道 dev.to 团队意识到了这一点，这只是第一步。