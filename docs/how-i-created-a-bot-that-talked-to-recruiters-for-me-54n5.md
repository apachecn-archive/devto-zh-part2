# 我创造了一个能帮我和招聘人员交流的机器人(它太棒了！)

> 原文：<https://dev.to/whokilledkevin/how-i-created-a-bot-that-talked-to-recruiters-for-me-54n5>

[![Main](../Images/a0776a01882b34da2855310356e29816.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j0A4qBAh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.com/images/9ac208d112c709a4980815f9f30ff6fc/tenor.gif%3Fitemid%3D5665698)

我的名字叫凯文，我想告诉你一个关于我如何阻止招聘人员发送垃圾邮件的简单方法的故事。

我是一名前端开发人员，我有一份好工作，我领导着一个由 7 名开发人员组成的团队。但是我收到了太多招聘人员的来信，有时候我真的不想查看我的收件箱。我知道他们在那里说，“希望你一切都好！”问，“你想聊聊吗？”不，我不会。我想独自完成我的任务和真正的工作。

有时招聘人员甚至试图在脸书问题上攻击我。查看消息请求:

[![Requests](../Images/9f77ae1a4f7f7f66f3c73fcd4e926354.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n89gll5p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2A9_CtWDhvrT8rHqDQLhurGg.png)

这就是为什么我决定宁愿要一个机器人，也不要再读这些烦人的信。我已经决定使用 Messenger 平台来创建我的机器人，因为所有的招聘人员都使用脸书，他们可以很容易地与我的数字弟弟聊天。

对于机器人来说，有大量有用的工具，当然，你也可以不使用构造函数自己完成。我决定继续使用聊天燃料。它很容易使用，尤其是当你理解了其中的逻辑。我花了几个小时创造我的机器人，这是[这是](https://m.me/296432910906505?ref=bot1)，闪闪发光！

# 对我来说什么是重要的？

因为我每天都会收到很多提案，所以我很清楚什么是重要的，什么是不重要的。我是摇滚明星(你好，招聘人员！)从这些巨大而愚蠢的信件中找到我需要的信息:

地点:我目前住在芝加哥，我希望能在这里呆得越久越好。因此，我对搬迁不感兴趣(除非是像…巴厘岛一样)。这就是为什么我为招聘人员添加了关于工作地点的问题。这样，无论工作来自哪里，我都可以稍后过滤提议。

**技能**:说真的，招聘人员，别再问我 Java 了。我不是 JAVA 开发人员。我想添加一个关于 Java 和 Javascript 区别的小测试或评论。我认为这对我有好处…对招聘人员也有好处。看，我并不邪恶。关于我的其他事情——我真的对可以帮助我提高反应能力的酷项目感兴趣，这是我添加这个问题的另一个原因。

**经历**:我有 4 年的经历，收到白纸黑字写着 5 年的信感觉怪怪的。不要再用这个作为标准，或者确切地知道你需要多少年的经验。

**工资**:还有一件重要的事情。一开始加了滤镜，但是好像不太礼貌，就去掉了。
这就是安装我的机器人所需要的一切——简单易用，希望能让我从收件箱的垃圾风暴中解脱出来。

**但是如何让招聘人员使用呢？**

我把这个链接放在我能放的任何地方——我的 LinkedIn 页面，GitHub，脸书，Slack，Instagram。我还制作了一个 Gmail 模板，回答了招聘人员这样的问题:

“嗨，亲爱的招聘人员！
感谢您的关注。
如果你想告诉我一些职业机会，请使用这个机器人:
这对我们双方都好:我收到了关于你的工作建议的有用和结构化的信息，你不必花几个小时写信和试图找到个人细节，让我打开你的信。
谢谢，
凯文"

后来，我开始通过 bot 收到第一份工作提议。

这是它在 Chatfuel 上的样子:

[![Chatfuel interface](../Images/887c1db8ef207294116d3f798b5606e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x8Bm83ZS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AWNTrDS_qRS9GksXLvLzB5Q.png)

里面看起来是这样的:

[![Chatfuel interface2](../Images/76ab1d9cab03de57a84b05347520a741.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6ZkvtboE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AIChAK6_qJtgymUP3VfrB7Q.png)

# 收到这样的工作提议，最棒的事情是什么？

我可以按我想要的方式接收工作建议和工作描述。正如你在截图中看到的，在 Chatfuel 上你可以按你想看的字段排序。我的收件箱较少受到招聘人员的攻击。我可以群发信息给他们！给每个人留下反馈。
现在招聘人员更有可能不会消失，因为我有他或她的脸书档案，我可以看到我们是否有一些共同的朋友。
经过一个月的测试，我分析了发生的变化:

我不像以前那样被招聘人员烦了。我有我的工作邮箱，可以随心所欲地将信件分类。

我收到了 4 个有趣的提议。

我现在对就业市场、所需技能、薪水等等有了更多的了解。我有招聘人员提供给我的统计数据。

还挺好玩的！招聘人员告诉我，他们喜欢我解决问题的方式。

没有它我能活下去吗？

当然了。但这让我的生活变得更好。细节和时间很重要，所以我建议你也这样做。

附言

后来我在 ProductHunt Upcomings 上发现了 [Reply.id](//reply.id) ，在我看来，这些人是为像我这样的人制作工具的。也许以后会试试。