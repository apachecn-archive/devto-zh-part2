# 可怜人的虫子赏金监控系统

> 原文：<https://dev.to/securitytxt/the-poor-mans-bug-bounty-monitoring-setup-5f1f>

[![](img/c82bffa22904349e48945603d4076f51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0wUVHmPD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.oldbookillustrations.com/wp-content/uploads/2017/07/fishing-station.png)

朱尔斯·劳伦斯的《黑海岸边的捕鱼站》

我必须承认，我一直坚持一个小技巧，它可以让任何人——甚至是那些不喜欢开发和维护软件的人——在几分钟内建立一个监控系统。我之所以称之为穷人的监控设置，只是为了表明这个设置不是非常复杂，但它做得很好。

当虫类赏金猎人监视目标时，他们希望收到新东西出现或有新实例的提示。这样做是为了让一个人可以立即跳到感兴趣的目标和组件上，这在竞争性的 bug 赏金程序中特别有用。

这个设置的主要部分依赖于 [Git](https://git-scm.com/) 。我们希望能够存储来自侦察工具的结果——比如子域暴力脚本——并且能够快速看到变化。我们还需要一个地方来远程存储输出。对于这个特殊的例子，我将使用私有的 GitHub 库。学生可以在 GitHub 上获得免费的私人知识库，如果你在这里申请的话:【https://education.github.com/pack[。请记住，有很多选择，我只是坚持用 GitHub 来写这篇文章。](https://education.github.com/pack)

一旦建立了您的私有存储库，请确保将您想要监控的工具的所有输出存储在本地 Git 文件夹中。运行完工具后，您的监控脚本应该尝试`git commit`输出。这里的巧妙之处在于 Git 不会提交未修改的文件，这意味着您只能提交包含新发现端点的`git commit`文件。`git push`将你的文件保存到私有的 GitHub 中，并包含一个不错的提交消息，因为这在以后会很有用。

[![](img/3d2b50eab6d2a3407e2452e89935f214.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LiSk_EeK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/42733457-f67a872c-8831-11e8-9dce-fd3f1295f324.png)

既然所有的东西都被推送到 GitHub，我们希望有一种方式来通知新的提交。原来，GitHub 有一个漂亮的小功能，允许你在主分支上有新的提交时向一个地址发送电子邮件。

1)导航到[https://github . com/YOUR \ _ USERNAME/REPO/settings/installations](https://github.com/YOUR%5C_USERNAME/REPO/settings/installations)；

2)在“添加服务”下拉菜单下，查找“电子邮件”；

[![](img/f1aee6438fe16742f2740167901542b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rkwicj4g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/42733459-f6af8daa-8831-11e8-9462-846496e0f804.png)

3)在“地址”字段中添加您的电子邮件地址。

[![](img/a308f73ca02e80f865a0fb84499141e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7fIC5ovM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/42733471-1daf4896-8832-11e8-9aa0-173bb798012a.png)

最后，使用 Git 提交流程作为 cron 作业运行您的工具。我用几行 Bash 写了整件事。

```
$ crontab -l
# Edit this file to introduce tasks to be run by cron.
...

0 * * * * /usr/local/bin/scan example.com 
```

Enter fullscreen mode Exit fullscreen mode

你现在已经准备好了。坐下来放松。GitHub 现在会通过电子邮件通知你文件的任何变化。因此，你可以坐在某个地方的咖啡馆里，立即知道你最喜欢的 bug 赏金目标上什么时候发现了新的端点。

另外，我只想补充一点，请不要在监控时执行过度扫描。保持事情的轻量级和优先目标。