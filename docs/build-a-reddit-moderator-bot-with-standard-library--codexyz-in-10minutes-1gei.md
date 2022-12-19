# 用标准库+ Code.xyz 在 10 分钟内构建一个 Reddit 版主机器人

> 原文：<https://dev.to/eileenzhong/build-a-reddit-moderator-bot-with-standard-library--codexyz-in-10minutes-1gei>

[如果你是 subreddit 版主，你可能会收到社区成员的请求](https://medium.com/@eileenzhong/build-a-reddit-moderator-bot-with-standard-library-code-xyz-in-10-minutes-d85d878d4a0d),要求你删除不恰当的评论或禁止不礼貌的用户。但 2019 年是 2018 年，我们正处于最大的科技时代，被人工智能、自动化和机器人等东西宠坏了。我们不再需要执行像监视子街道这样的琐碎任务。相反，我将通过 3 个简单的步骤教你如何构建一个 Reddit 机器人，它将自动检查子编辑中有标记的评论。

[![Community members can use "!delete" or "!ban" to request bot moderation](../Images/d2a3c838c2f92afe7ab8a6e7bbbc6acb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LEQ5mFzd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539630949/Screen_Shot_2018-10-12_at_3.52.34_PM.png)

无论你是没有编程知识还是有 15 年的专业经验，你都可以在 10 分钟之内用[标准库](https://www.stdlib.com)部署这个机器人。

如果你不熟悉[标准库](https://www.stdlib.com)，我们是一个无服务器平台，可以让**每个人**(是的，*每个人*！致电所有项目经理、设计师、营销团队、销售人员、职业健康和工程师…🙋)在几秒钟内构建、发布和集成可伸缩的 API。在我们的在线编辑器 [Code.xyz](https://code.xyz) 的帮助下，你可以在浏览器上完成所有这些工作。

[![Standard Library](../Images/9228d2de6535e0c1df0ab27c030704e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mo4WnrHV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631205/standard-library-logo-dark.png)

# **要求**

*   [Reddit](https://www.reddit.com) 版主账户(针对您想要监控的子编辑)
*   [标准库](https://www.stdlib.com)账号(免费)

# **第一步:获取 Reddit bot 模板**

*预计时间:30 秒*
在你的浏览器中前往 [Code.xyz](https://code.xyz) ，选择`reddit-moderator-bot by @eileenzhong`(就是我！🤓👋)在“社区 API 源”选项卡中:

[![Select "reddit-moderator-bot" by @eileenzhong](../Images/298cd374ed13d685a95cfc6360106048.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_5O8pq3B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631295/Screen_Shot_2018-10-11_at_3.01.12_PM.png)

命名您的 API:

[![Name your API](../Images/4144d4343c0fee3044e469019124bc98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---1Z3f4z7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631296/Screen_Shot_2018-10-11_at_3.03.43_PM.png)

一旦完成，您将方便地进入带有 Reddit bot 项目代码的 [Code.xyz](https://code.xyz) (标准库的浏览器内文本编辑器)。

# **第二步:复制粘贴**

*预计时间:2-5 分钟*
下一步涉及复制粘贴技巧的专业知识，以设置一些所需的环境变量。您会注意到在您的`env.json`文件中列出了三组变量:`local`、`dev`和`release`。我们将把代码部署到开发环境中，所以我们只需要担心本教程中的`dev`变量！

所有字段都可以在`env.json`中找到:

[![Open env.json](../Images/c88e252d895d8d225fc6822c3b20360e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zQ5aX2FO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631377/Screen_Shot_2018-10-11_at_3.13.11_PM.png)

要检索您的`STDLIB_LIBRARY_TOKEN`，右键单击引号并选择“插入库令牌…”:

[![Insert library token by accessing the context menu](../Images/fe1215bb6fd968e29267950918fe0133.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7fR7IU4E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631453/Screen_Shot_2018-10-12_at_3.09.06_PM.png)

以`REDDIT_` -*开头的密钥可以在 [Reddit](https://www.reddit.com/prefs/apps) 上找到(`REDDIT_USERNAME`和`REDDIT_PASSWORD`分别是你的版主账号的用户名和密码)。
如果您还没有 Reddit 应用程序，请在这里用您的版主帐户注册一个[:](https://www.reddit.com/prefs/apps)

[![Register a Reddit app](../Images/0d11086e601eb1c34f8a992b601822a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QmZQfa51--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631551/1_5TGHBJGNptiFxHs3oyDaqw.png)

复制`REDDIT_CLIENT_ID`和`REDDIT_SECRET_KEY`:
[![Click edit to view private Reddit credentials](../Images/62f54bd6a07b3d28ef28faa2c7d3abc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ujCDBx1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631581/Screen_Shot_2018-10-11_at_3.25.50_PM.png)
[![Retrieve your REDDIT_CLIENT_ID and REDDIT_SECRET_KEY](../Images/c0d36c3577b63b3eb8f8d047b574ff79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XGSJxVNP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631581/Screen_Shot_2018-10-11_at_3.26.04_PM.png)

将二者粘贴成`env.json` :
[![Filled out env.json](../Images/c98d906ee2f929dd4fd6d7612332cdf2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RrIZj5Sq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539660124/Screen_Shot_2018-10-15_at_8.17.21_PM.png)

当您的`env.json`文件像上面的截图一样正确填写后，机器人就准备好进行一些初步测试了。
填写所需参数(定义和截图如下):

```
**Parameter definitions**

subreddit: the name of the subreddit you are moderating
threshold: what score do you want the comment to have in order to perform the requested action? 
allowBanPermission: true (allow ban requests) / false (ignore ban requests) 
```

[![Click Debug to launch the parameter editor](../Images/82f0259276783802412b37e72c92e148.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LLrFOs9a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631711/Screen_Shot_2018-10-12_at_9.27.26_AM.png)

然后点击“运行”按钮，这将做两件事:*执行*和*部署*程序。
[![Custom parameters](../Images/be1593ec9d0f446af9257adcc9bf4818.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U0TTPy1c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631711/Screen_Shot_2018-10-12_at_9.44.16_AM.png)

恭喜你，你已经为标准库部署了一个 API！😎 🎉 🎊

# **第三步:置之不理**

*预计时间:1 分钟*

[![Customize the task schedule](../Images/be1593ec9d0f446af9257adcc9bf4818.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U0TTPy1c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631711/Screen_Shot_2018-10-12_at_9.44.16_AM.png)
[![Schedule the task](../Images/44396d7309d4bf889610402ea48cfaea.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--yFmhSJF6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/eileenstdlib/image/upload/v1539631781/Screen_Shot_2018-10-12_at_9.57.31_AM.png)

最后一步是让你的机器人工作。您可以在 [Code.xyz](https://code.xyz) 中通过调度一个任务来实现这一点，该任务将根据您选择的调度自动执行您刚刚创建的 API。

*建议:根据 subreddit 的流量，您可以增加或减少 bot 调用的频率。一个好的开始是让机器人每 5 分钟扫描一次。*

一旦你点击右下角的“计划任务”按钮，你的 Reddit 机器人将扫描`r/{subreddit}`到**回复任何新的！班/！删除请求**和**根据您选择的时间表，对任何符合分数阈值**的评论执行这些任务。

就是这样！🌟现在想象一下……如果部署一个 Reddit 版主机器人这么快而且不费力，那么[标准库](https://www.stdlib.com)还能为你做什么？如有任何想法或问题，请随时联系:。

* * *

艾琳是标准图书馆的软件工程师。