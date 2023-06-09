# 我如何测试一个新的招聘信工具

> 原文：<https://dev.to/whokilledkevin/how-i-tried-a-new-tool-for-recruiting-letters-2gj>

[![test](img/458aaa903ead388b9e4d73a6fd686051.png)T2】](https://i.giphy.com/media/gw3IWyGkC0rsazTi/giphy.gif)

几天前，我写了一篇文章，是关于我为整理工作提议而制作的机器人。尽管这是我在这里的第一篇帖子，我还是收到了很多评论。据我所知，这个话题相当热门。似乎我变得像“反招聘博客”。哈哈，不！我都喜欢，只是有点无聊...

所以我决定测试一下我最近提到的工具(在即将到来的产品搜索上找到的)。叫做[回复. id](https://reply.id) 。

那是什么？

AI Gmail 过滤器+与招聘人员对话的机器人

现在的工具都是 AI 机器学习大数据(你懂的)。但是让我们看看，它们是什么意思。

**入职员工**

你选择你的技能和水平。您使用 Gmail 帐户登录。

[![skill](img/eeeabcb6bad6b1d4a79120a8c1f8c771.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j3CIWWI2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6y4mn119cqdytuikrfvs.png)

设置机器人会问招聘人员的问题。

[![botquestions](img/8fd29a001ab29ee27c17619f53a01ca0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8Ig5cfRi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s35za4clnohix8ca7dnn.png)

这是我在 Reply.id 上制作的机器人，只需一分钟左右。还记得 Messenger 上的
[我的 bot](https://m.me/296432910906505?ref=bot1) 吗？

**实际工作原理**

我选择了允许 Reply.id 和 Gmail 帐户之间的集成。当我收到招聘人员的信时，出现了一个名为“工作建议”的新文件夹。

[![jp](img/7067b27eba99a84f3f036e263ad7d143.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4RzHaa0K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cig1ktj07soruua9hwj0.png)

因此，该算法会看到招聘人员的信件(所谓的“人工智能 Gmail 过滤器”)，并将它们发送到不同的文件夹。好吧。之后，该工具会自动回复所有这些信件，并提供 bot 和解释的链接(使用我在入职时就已经选择的模板，所以不多)。

这就是你在《送》中看到的:

[![sent](img/c6a847c4362c2b23c1658715467c17b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fg6YSRKz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zsweod9sbmuxsgbgb8xb.png)

如果招聘人员与机器人交谈，你只会在一种电子表格中看到答案。你也可以打开它，看到所有问题的答案。如果需要，也可以手动添加问题。

[![inside](img/0c99e2105e6f911a9d36d743a0718732.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c0S8Gpo6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3bg55oi2ix62j45q8bs9.png)

所以，这是一个很好的工具

*   那些真正挣扎于招聘者；
*   那些不知何故准备谈论新的职业机会的人(然后在社交媒体和 Github 上放一个机器人的链接)；
*   那些希望收件箱空空如也、井然有序的人。

很好。