# 部署未发布，让我们使用功能标志

> 原文：<https://dev.to/geshan/deployment-is-not-release-lets-use-feature-flags-2ihd>

[![Deployment Is Not Release, Let’s Use Feature Flags](img/7e2ae3a1aae68c9a893cb3badbd6021d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S8NZzb3l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AsNyPMg_QtC3Un9LNNh-skw.jpeg)

将一个大的特征分解成史诗和故事总是很棘手的。然后你有子故事使过程变得复杂。如果您能够部署能够为业务带来价值的已完成任务，将会有所帮助。输入特性标志，使用它你可以将你的代码部署到产品中。代码和特性正在生产中，但没有完全发布给每个人。

> 这篇文章将帮助你调整你的思维模式，从特性标志中获得更多的好处。

[![Switches image from [pixabay](https://pixabay.com/en/electric-electricity-building-1080585/)](img/554f5ae77e30d678f3fd7fd8ec2ee693.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--wECc7Bbe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2AvhQOETtP_3LQmHIKpB1ZZQ.jpeg) *将图像从[切换到](https://pixabay.com/en/electric-electricity-building-1080585/)*

## 为什么要使用特征标志？

您是否曾经面临过这样的情况:您需要部署一个 epic，但是由于已经完成了 80%,这是不可能的？这是一部要么全部“释放”要么什么都不释放的史诗。这就是特性标志发挥作用的地方。您可以部署(而不是发布给每个人)每个添加到生产中的新价值。这里的技巧是把它放在一个简单的逻辑后面，就像电子邮件以@yourcompany.com 结尾一样。

> 你必须将技术部署过程与发布新功能的业务过程分开。

## 如何使用功能标志？

功能开关、功能切换是功能标志的一些其他名称。它可以毫不费力地像如果登录用户的电子邮件是在我们的白名单，我们显示这个表单。它甚至可以是当您在浏览器中添加具有定义值的特定 cookie 时显示的功能。使用 SAAS 的特性标志也可以高度协调。[黑暗发射](https://launchdarkly.com/)是作为服务公司的特色标志。如果你有时间和资源投资它，你可以使用它。

无论您选择简单还是复杂的路径，结果都是您可以控制谁可以访问新功能。该功能并非面向所有人发布。不同之处在于如何激活/禁用功能标志。只需点击一个复选框或进行另一次部署，就可以向所有人开放一项功能。

> 我的建议是从小处着手，在代码中做一个 if 条件，然后开始试验。如果它运行良好，尝试其他方式，如白名单，甚至是浏览器中的特殊 cookie。

## 特征标志的优点

在生产中使用特性标志有很多好处。让我们列出几个亮点:

1.  能够与一组选定的用户私下测试生产中的特性。
2.  能够轻松添加或删除可以使用该功能的用户。
3.  发布近乎无 bug 特性的信心。软件工程师和 QA 甚至可以多次测试产品。只有当他们有信心时，才在错误修复后发布该功能。
4.  将会有较少的代码冲突。当任务完成时，代码更改在部署之前/之后被合并到主分支。这也节省了一些宝贵的开发时间。
5.  在生产中对白名单中的人试验一些东西的好处。这甚至会带来好的反馈和积极的改变。

## 结论

通过正确使用特性标志，您甚至可以将小任务部署到生产环境中。考虑为客户增加价值并经常部署。在产品上进行测试，当你有信心的时候，把它发布给每个人。永远记住部署！=发布。快乐特征标记！

* * *

*最初发表于[geshan.com.np](https://geshan.com.np/blog/2018/10/deployment-is-not-release/)。*