# 仅在使用自制软件安装 PostgreSQL 后安装 pgAdmin

> 原文：<https://dev.to/letsbsocial1/installing-pgadmin-only-after-installing-postgresql-with-homebrew-2hg0>

[![pgAdmin](img/19daf121f3832d24d78ef1712d03df33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZyKE2tRq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lr6cl6v735pxx42x6uln.png)

本帖于 2018 年 6 月 15 日*首次发表于我的[开发者博客](http://www.mariadcampbell.com/)。*

 ***2018 年 6 月 17 日更新:**我刚刚重温了这个问题，设置真的很容易。我会就此写一篇文章，并把它链接到这里。我更喜欢使用自制软件，尤其是对于本地开发，但是我也喜欢能够使用 GUI。如果你想把你的本地回购推到 Github 这样的远程设备上，这种设置让生活变得简单多了！要查看我在这里发表的题为[安装完 PostgreSQL with Homebrew Part 2](https://dev.to/letsbsocial1/installing-pgadmin-only-after-installing-postgresql-with-homebrew-part-2-4k44) 后才安装 pgAdmin 的帖子，请访问[安装完 PostgreSQL with Homebrew Part 2](https://dev.to/letsbsocial1/installing-pgadmin-only-after-installing-postgresql-with-homebrew-part-2-4k44) 后才安装 pgAdmin。

最近开始学 **PostgreSQL** ，用**自制**安装在我的 **Mac High Sierra** 上。今天，我决定更深入地研究它，并在 [Udemy](https://www.udemy.com/postgresql-from-zero-to-hero/learn/v4/overview) 上发现了几门有趣的课程(我在 **NYCDA** 的课程严重缺乏)。我之前没有安装 **pgAdmin** 和 **Homebrew** ，因为它已经严重过时了，但是我决定我应该在 **pgAdmin** 网站上试一试。我安装了它，并试图登录。收到消息**角色** `postgres`为 ***缺少*** ，无法登录。那是因为我选择了使用我的计算机简称。我决定调查这件事。

经过一番搜索，我从 **Stackoverflow** 中找到了以下内容:

[psql: FATAL:角色“postgres”不存在](https://stackoverflow.com/questions/15301826/psql-fatal-role-postgres-does-not-exist)

对我起作用的部分，也应该对处于类似情况的其他人起作用的部分，是下面这个帖子中的内容:

```
@RogerLipscombe if you run brew link postgresql after the installation, there's no need to append the whole path to createuser, a simple createuser -s postgres will work great 
```

Enter fullscreen mode Exit fullscreen mode

这是一个评论，它的工作就像一个魅力！我重新进入**pg admin**,**GUI**识别出我的 ***新*** **postgres 用户**，我可以登录了。

对我来说，使用**自制软件**来管理我在 **postgres** 中的活动是正确的做法，但我也希望能够进入 **pgAdmin GUI** 并获得我的数据库活动的全面概述。

希望这对你有所帮助，并且**快乐** ***快乐*** ！

**相关资源:**
[如何卸载 OSX 上的 postgreSQL 并用家酿重装](http://www.mariadcampbell.com/2018/05/01/how-to-uninstall-postgresql-on-osx-and-reinstall-with-homebrew/)*