# 使用 Masonite 框架发送自动电子邮件

> 原文：<https://dev.to/masonite/sending-automated-emails-with-masonite-framework-13cc>

设想一个场景，您想每周给客户发送一封电子邮件(可能是一份时事通讯💌).所以你需要在特定的日期和时间发送邮件。

这篇文章将介绍如何使用 [Masonite 框架](https://github.com/MasoniteFramework/masonite)创建和发送自动电子邮件。

# 你需要什么

要编写这篇文章的代码，您需要:

*   Python(我用的是 Python 3 . 6 . 5)；
*   Pipenv:面向人类的 Python 开发工作流；
*   Masonite :现代的以开发者为中心的 Python Web 框架
*   Mailtrap.io:开发团队的电子邮件测试；

# 设置您的应用

首先，创建一个新目录和一个 Python virtualenv:

```
$ mkdir masonite-weekly-email
$ cd masonite-weekly-email
$ pipenv install --three
$ pipenv shell 
```

Enter fullscreen mode Exit fullscreen mode

在那之后，你需要安装`masonite-cli`包并制作一个新的
[Masonite](https://github.com/MasoniteFramework/masonite) 应用程序。

```
$ pipenv install masonite-cli
$ craft new masonite_weekly_email .
$ craft install 
```

Enter fullscreen mode Exit fullscreen mode

这将在当前目录下创建一个新的 Masonite 应用程序(在`craft new`命令的末尾使用`.`)。

# 创建和发送电子邮件

Masonite 自带电子邮件支持🎉。所以你可以很轻松的发一封这样的邮件:

```
Mail.to('hello@email.com').template('mail/welcome').send() 
```

Enter fullscreen mode Exit fullscreen mode

# 任务调度

既然我们已经完成了发送电子邮件，让我们安排它。
Masonite 提供了一个名为 [`masonite-scheduler`](https://docs.masoniteproject.com/useful-features/task-scheduling) 的包。它使您的应用程序能够调度 cron 任务。首先，你需要运行一个命令来安装这个包:

```
$ pipenv install masonite-scheduler 
```

Enter fullscreen mode Exit fullscreen mode

第二个命令是一个`craft task`命令，它将在`app/tasks`目录下创建一个新任务。

```
$ craft task WeeklyEmail 
```

Enter fullscreen mode Exit fullscreen mode

在每周运行任务之前，让我们每 1 分钟运行一次。

```
from scheduler.Task import Task

class WeeklyEmail(Task):

    run_every = '1 minute'

    def __init__(self, Mail):
        self.mail = Mail

    def handle(self):
        self.mail.subject('Build your next SaaS with Masonite 🚀')\
                .to('hello@email.com').template('mail/weekly')\
                .send() 
```

Enter fullscreen mode Exit fullscreen mode

上图中，您发送了一封电子邮件，其模板位于`mail`文件夹下。创建一个名为`weekly.html`的文件，并将随机文本放入其中。

```
<h2>Hello world!</h2>
<p><a href="https://github.com/MasoniteFramework/masonite">Masonite</a> is magic. ✨ </p>
<p>
    There are many benefits Masonite brings to your next SaaS project.
</p>
<img src="https://media.giphy.com/media/3o6Ztqh4JSlVqi2Z20/giphy.gif" alt="just do it"> 
```

Enter fullscreen mode Exit fullscreen mode

让我们在设置 cron 作业之前对此进行测试。将您的[邮件陷阱](https://mailtrap.io/)凭证放到您的`.env`文件中，并运行这个命令。

```
$ craft schedule:run 
```

Enter fullscreen mode Exit fullscreen mode

让我们 Masonite 获取并运行您的任务！

[![Alt Text](../Images/493dcc79a63bd437a3875580d3dd5d4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LPIcPsMV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wxs20gtlvzsz0cidi2xo.gif)

让我们输入正确的参数，告诉任务应该在什么时候运行(记得每周一封邮件)。

```
class WeeklyEmail(Task):
    run_every = '7 days'
    run_at = '17:00' 
```

Enter fullscreen mode Exit fullscreen mode

# 克朗乔布斯

您需要设置 Cron 作业来自动运行我们的任务。每个 crontab 行必须以命令应该运行的时间开始，然后是命令:
因此，要在每周一 17:00 运行`command`，您应该做:

```
00 17 * * 1 command 
```

Enter fullscreen mode Exit fullscreen mode

您需要添加`&& craft schedule:run`来运行您的运行计划任务。

[Masonite 任务调度](https://docs.masoniteproject.com/useful-features/task-scheduling)文档页面可以提供进一步的信息。读吧，✊！

如果你想为 [Masonite](https://github.com/MasoniteFramework/masonite) 的开发做贡献或者感兴趣，那么一定要加入 [Slack](http://slack.masoniteproject.com/) 频道或者在 [GitHub](https://github.com/MasoniteFramework/masonite) 上发起回购。