# 在 30 分钟内建立一个调度 Slackbot！

> 原文：<https://dev.to/nylas/build-a-scheduler-slackbotin-30-minutes-1i6c>

在你的日历上留下时差来创造一个事件的日子已经过去了！

在本教程中，您将学习如何使用 Nylas Calendar API，通过一个简单的 Slack slash 命令创建一个调度程序 bot，将事件添加到您的个人日历中。

### ![Slack Bot2 (1)](../Images/85f06e478cab2554a95b65cb8626e7dd.png)

## **你事先需要什么**

在开始 DIY Slack bot 之旅之前，您需要准备一些系统。

*   [Git](https://help.github.com/articles/set-up-git/)&[Github](https://github.com/)账号
*   [英雄](https://signup.heroku.com/)账号
*   [时差](https://slack.com/)工作区
*   与谷歌或微软(Office365或 Outlook)日历
*   您选择的文本编辑器——py charm、Atom、Vim、Sublime 等。

## **概述**

以下是构建调度机器人的高级步骤:

[第 0 部分:基础 app 设置](#part-0) TLDR；派生启动代码，运行设置脚本，并在本地运行应用

[第一部分:把你的回购挂到 Heroku &部署它](#part-1) TLDR；创建一个 Heroku 应用程序，将其连接到您的 Github repo，部署它

[第二部分:设置 Heroku 配置变量](#part-2)
TLDR；用 Nylas 认证一个帐户以获得访问令牌，用它来获得日历的 ID，将这些加上日历时区作为配置变量添加到 Heroku 中。

[第三部分:将你的 app 连接到你的 Slack 工作区](#part-3) TLDR；创建一个 Slack 应用程序并配置一个 slash 命令来与你的应用程序通信，在你的应用程序中添加一个路由来监听 slash 命令

[第四部分:添加文本解析功能](#part-4) TLDR；将从 Slack 发送的文本解析成对事件 有意义的组件

[第五部分:构建调度功能](#part-5) TLDR；将解析后的参数发送到 Nylas API 的/events 端点，在您的日历上创建一个事件。

### 准备好了吗？让我们开始吧！

## **第 0 部分:基本应用设置**

**步骤 0:分叉 Slackbot 启动代码**

这个项目的代码存在于位于 https://github.com/nylas/slackbot 的 Nylas Github 账户中。

您将派生并克隆这个存储库，而不仅仅是克隆它，这样您就有了自己的存储库，可以连接到 Heroku。从 Github 开始按照[这些步骤](https://help.github.com/articles/fork-a-repo/)来派生存储库。按照上面的步骤，直到“找到另一个要分叉的存储库”。

[![null](../Images/378ed80661009b9027d3fd5064177c50.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kPBMfmHH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531330588485_image.png)

之后，您将拥有一个名为`slackbot`的目录的本地副本。该目录有五个文件:

1.  Procfile -告诉 heroku 如何运行应用程序
2.  app.py -初始 Flask 应用程序设置的主应用程序文件，您将在此文件中完成所有开发工作
3.  requirements.txt -您需要安装的依赖项列表
4.  告诉 Heroku 你的应用是用 Python3 编写的
5.  setup . sh——设置开发环境的脚本

在本教程的剩余部分，您将执行`/slackbot`目录中的所有命令

除了主分支之外，这个 repo 还包含与教程的每个部分相对应的五个其他分支。在构建 Slack bot 的过程中，您可以停留在主分支上，但是如果您在任何一步遇到困难，那么您可以通过使用`git diff <part-#>.`来查看您的代码和示例应用程序代码之间的差异，因此如果您在第 4 部分遇到困难，`git diff part-4`将显示您的代码和第 4 部分之后的示例代码之间的差异。

**第一步:运行脚本**

安装脚本设置您的开发环境并安装必要的依赖项。运行:

`source setup.sh`

这个脚本完成后，它将打印“设置完成！”在你的终端里

这个脚本将激活一个虚拟环境，您将使用这个虚拟环境来隔离项目的开发。如果你的终端提示符前面加上了`(.venv)`，那么你的虚拟环境已经被成功激活了！

如果您有兴趣了解更多关于虚拟环境和 python3 的信息，请查看[https://help . dream host . com/HC/en-us/articles/115000695551-Installing-and-using-virtualenv-with-Python-3](https://help.dreamhost.com/hc/en-us/articles/115000695551-Installing-and-using-virtualenv-with-Python-3)

**第二步:在本地运行应用**

`export FLASK_ENV=development`

`python3 app.py`

您应该看到:

[![null](../Images/1a53a14b49d441de5f3ed4c83beda7c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dbwb43x---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531180157293_Screen%2BShot%2B2018-07-09%2Bat%2B4.46.21%2BPM.png)

如果你访问第四行的网址，你会看到`Howdy Hacker!`

Wohoo！你通过了应用程序设置。拍拍自己的背，打开一个拉克罗伊。

## **第一部分:将你的回购与 Heroku 挂钩**

**第 0 步:创建一个新的 Heroku 应用**

如果你愿意，你可以使用数字海洋或者 DIY，但是这些说明是给 Heroku 的。

登录 heroku:[https://id.heroku.com/login](https://id.heroku.com/login)，点击右上角`new`创建一个新的应用，完成新的应用表单

您可以输入自己的应用程序名称，也可以留空，让 Heroku 为您生成一个！记下这个 url，因为当您将应用程序连接到 Slack 时，您将在第 3 部分中用到它。

**第一步:将你的 Github 回购挂到 Heroku**

Heroku 与 Github 集成，支持自动部署。这意味着无论何时你将代码推送到 Github 上的一个分支，Heroku 都会部署这段代码。多方便啊！

在创建应用程序后重定向到的部署页面上，在**部署方法**部分，点击`Connect to GitHub`，然后在**连接 Github** 部分，点击`Connect to GitHub`按钮。

[![null](../Images/14c81c18d2890ec41d33ff61fc69ceac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rOEdIWy3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531183138050_Screen%2BShot%2B2018-07-09%2Bat%2B5.25.56%2BPM.png)T4】

接下来，您将被带到一个页面，在这里您可以选择要连接的存储库。如果您已经在 github 帐户下成功创建了一个存储库，那么当您开始在输入中键入存储库名称时，它应该会出现。

一旦连接了存储库，点击`Enable Automatic Deploys`进入主分支。这将简化部署过程，以便任何时候您推送 master，heroku 都会部署最新的更改。

重要的是要注意，如果您开始在一个分支而不是主分支上工作，那么您将不会有自动部署。您可以从另一个分支手动部署代码，如果您将代码推送到另一个分支，从 manual deploy 部分选择该分支并单击`Deploy Branch`。

[![image_preview-1](../Images/f6338a33d577a084978498e82459da63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bwF_Y8DS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/3314308/image_preview-1.png)

保持 Heroku 开放，因为您将在第 4 部分第 3 步中用到它。

**步骤 2:将代码推送到主机以触发部署**

用`CTRL+C`退出本地运行的应用程序。让我们对提交进行更改，并在您的 return 语句中添加另一个感叹号。

该路线现在应该看起来像这样

```
@app.route('/')

def homepage():

   return "Howdy hacker!!"
```

在您的终端中，运行:

`git add .`

`git commit -m 'Part 1: Hook up repo to Heroku'`

`git push origin master`

此部署完成后(可能需要几秒钟),请访问您的应用程序的 url。您应该会看到消息`Howdy Hacker!!`。您的网站是活的，并准备欢迎任何黑客谁访问该网址，这一切都归功于你配置的自动部署。

## **第二部分:设置 Heroku 配置变量**

好的，所以要做到这一点，你需要两件主要的东西。首先，您需要一个访问令牌，表明您已经授权通过 Nylas API 访问您的帐户。这是一个重要的步骤，因为 API 不能将事件添加到任何旧日历中，除非它已经过身份验证并且所有者已经给予许可。其次，您需要想要添加事件的日历的 ID。大多数帐户都有一些日历，有些是自动生成的，有些是用户创建的。如果没有这一点，API 就不知道向哪个日历发送请求。

接下来，您将在 Heroku 中将它们设置为配置变量，这样您就可以避免在应用程序中添加和提交它们。

在步骤 0 中，您将获得一个访问令牌，在步骤 1 中，您将使用这个访问令牌来查找您希望添加事件的日历的 ID。然后，在步骤 3 中，您将把这些作为配置变量添加到您的 Heroku 应用程序中。

**步骤 0:获取访问令牌**

前往[https://dashboard.nylas.com/register](https://dashboard.nylas.com/register)注册一个免费的 Nylas 账户，或者前往[https://dashboard.nylas.com/sign-in](https://dashboard.nylas.com/sign-in)注册一个现有账户。一旦你登录到 Nylas 仪表板，点击左侧栏中的帐户。

[![null](../Images/4a11015a2922505a2d0ca191e9d913dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qkntjU8l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531260178164_Screen%2BShot%2B2018-07-10%2Bat%2B2.56.06%2BPM.png)

在帐户页面上，点击右上角的`Auth Account`,或者如果您之前没有授权的帐户，点击页面中间的`Auth Account`。

[![null](../Images/7f438fe90802bbfb58bec69fbeae14da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iAA36WJv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531260180284_Screen%2BShot%2B2018-07-10%2Bat%2B2.55.47%2BPM.png)

在弹出的输入窗口中，键入与您想要添加事件的日历相关联的电子邮件地址，然后执行验证步骤。

完成身份验证后，您将被重定向回控制面板的帐户页面。页面顶部会有一个蓝色的通知，看起来像是:

[![image_preview-2](../Images/2ad8cf0b7089cea39dbd78d5f78ab248.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mJ_XfN-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/3314308/image_preview-2.png)

从蓝色通知中，将访问令牌的值复制到一个临时位置—您将在下面的两个步骤中需要这个令牌。在这个例子中，访问令牌是`xyz123xyz123xyz123xyz123xyz123.`

如果您取消通知或重新加载页面，访问令牌将会消失。出于安全原因，无法检索该特定令牌，因此如果您丢失了访问令牌并需要获取新令牌，请单击`Auth Account`并按照授权步骤进行操作。你可以重新认证一个以前认证过的帐号，没问题！这将为帐户创建一个新的访问令牌。

您将首先在 Nylas 文档中使用这个标记来获取日历的 ID，然后在将它添加到 Heroku 配置变量中。

您可以保持仪表板页面打开，只要您不离开或重新加载它，带有访问令牌的蓝色通知将保持在页面顶部。

**第一步:获取日历 ID**

如果不告诉事件需要添加到哪个日历，您就无法创建事件，因此您需要获取想要添加事件的日历的 ID。

前往 https://docs.nylas.com/reference#calendars-1 的。这是 Nylas 文档的日历部分，特别是`GET /calendars endpoint`。你可以通过在你的终端中向`https://api.nylas.com/calendars`发出请求来点击 Nylas API，但是文档是交互式的，使得在站点内分两步完成成为可能。

将您的访问令牌粘贴到这个端点部分的输入底部的`Authorization`输入字段中。

[![null](../Images/9c43f228c5bfa485dcc4665d1223dcf6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qwNucD3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531262232159_Screen%2BShot%2B2018-07-10%2Bat%2B3.32.36%2BPM.png)

如果你再向上滚动到上面黑框中的代码，`authorization:`将会自动填写。

[![null](../Images/30cd1bced227a251960ec306f559687e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ovYgmepa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531262370948_Screen%2BShot%2B2018-07-10%2Bat%2B3.35.36%2BPM.png)

接下来，点击右上角的`Try It`。这将在左边运行`cURL`请求，用您的访问令牌点击 Nylas API，并在右边显示响应。这将是您的日历列表。

从 JSON 日历列表中，选择要添加事件的日历。一些你可能从未见过的日历。这是因为有些提供商有自动生成的默认日历。一个常见的例子是“假期”。通常，这些自动日历会有`"read_only": True`。这意味着您没有权限在这些日历上创建事件。选择一个有`"read_only": False.`从你选择的日历中复制`id`的日历是很重要的。

在上面的示例截图中，顶部日历的`id`是`"67qmz3fuk9wfljwj1w8ngshkc".`,在运行 Try 之后，您将有一个不同的值，因为 ID 将是您日历的唯一标识符。

**步骤 2:向 Heroku 添加配置变量**

转到 Heroku 并导航到应用程序的设置页面。点击`Reveal Config Vars`。这将弹出一对输入，其中键，或者你想如何在代码中引用变量，在左边，值在右边。

将您的日历 ID 添加为一个配置变量，其键为`CALENDAR_ID`，ID 值为您在步骤 1 中复制的值。确保删除 ID 中的双引号。

接下来，您将添加步骤 0 中的访问令牌作为一个配置变量，该变量具有关键字`ACCESS_TOKEN`和步骤 0 中的值。

最后，您需要添加一个带有日历时区的配置变量，以便您的日历可以正确地安排事件。如果您没有超过时区，那么您的日历将采用 UTC(协调世界时)，也称为 GMT(格林威治标准时间)。如果您的日历位于任何其他时区，那么事件将会按照时区 UTC 偏移量显示。设置时区配置变量将避免这种情况！

因为我在加利福尼亚写这篇文章，而且现在是夏令时，所以这个值将是`PDT`。您还可以将该值设置为 UTC 偏移量，对我来说就是`-0700`。你可以在这里找到你的时区信息。如果您想使用 UTC 偏移量，那么它需要采用正确的格式。UTC+10 的时区是`+1000`，UTC-3 的时区是`-0300`。

这是我看到的样子:

[![image_preview-3](../Images/23a65e83a08bf71b5dc68412d5338daf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hsbF7RgQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/3314308/image_preview-3.png)

或使用 UTC 偏移:

[![null](../Images/d2dde1ed5b0b5d77ca764b081c385b6e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ArS1KSHv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531264746932_Screen%2BShot%2B2018-07-10%2Bat%2B4.12.02%2BPM.png)

很好，现在 Heroku 中的所有东西都配置好了，我们可以开始令人兴奋的下一步了。

## **第 3 部分:将你的应用连接到 Slack**

**第一步:创建一个 Slack 应用**

去 https://api.slack.com/apps?new_app=1。这是构建新的 Slack 应用程序的登录页面。

[![null](../Images/e0db7edc2082da7bca268e870f201c2c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--flU5ZxEo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_23A4CD528EBB9FE5912B9C3F2BF4F0B1B5BDBBA2881E8512A634DDE0D3EF3B57_1530226678465_Screen%2BShot%2B2018-06-28%2Bat%2B3.56.58%2BPM.png)

如果上面的弹出窗口没有出现，点击`Create New App`。

在`App Name`中填入“Scheduler”或任何你想给这个应用起的名字。您在此选择的名称并不太重要。

接下来，选择您想要在其中使用此应用程序的开发工作区。如果您没有登录到您的 Slack 帐户，当您单击`Development Slack Workspace`下拉菜单时，将出现的唯一选项是`Sign in to another workspace`。选择此选项并按照这些说明进行操作。这将使您登录到桌面上的工作区。一旦成功，返回[https://api.slack.com/apps?new_app=1](https://api.slack.com/apps?new_app=1)并再次填写弹出窗口。这一次，下拉列表中应该有工作区选项。选择你想要添加这个调度程序的工作空间，点击`Create App`。

**第二步:配置 Slack app**

您将被带到一个应用仪表板。在这里，对于您想要构建什么类型的应用程序和功能，有很多选择。您将创建一个斜线命令，因此选择`Slash Commands`选项。

[![null](../Images/08cc429399128782cf9673cd305c0f89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YovQjVTc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_23A4CD528EBB9FE5912B9C3F2BF4F0B1B5BDBBA2881E8512A634DDE0D3EF3B57_1530227635007_Screen%2BShot%2B2018-06-28%2Bat%2B4.06.17%2BPM.png)

然后选择`Create New Command`。这将带您进入斜线命令配置表单。

[![null](../Images/aa793fdea9dd84325fb53969e81a631d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kNj9Zbdo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531184636689_Screen%2BShot%2B2018-07-09%2Bat%2B5.58.05%2BPM.png)

**命令**

在使用该命令时，您可以按照自己希望调用的方式来命名它。我选择`/scheduleme`是因为我认为它在完全调用时看起来很可爱:

`/scheduleme "Dinner with James" "Tomorrow at 7pm" "Tomorrow at 9pm"`

但是你可以选择任何你喜欢的斜线命令。

**请求网址**

对于`Request URL`输入，复制步骤 1 中 heroku 应用程序部署到的 url，并将其粘贴到输入中。然后在 url 的末尾添加一个`/scheduleme`，这样 url 看起来就像`https://my-scheduler-bot.herokuapp.com/scheduleme`。

这是设置 Slack 应该从 slash 命令发送请求的位置。换句话说，当您调用 slash 命令时，Slack 需要知道将数据发送到哪里。这是指定一个 url 和该 url 上的特定路由，`/scheduleme`。您将在第 5 部分的 python 文件中定义这条路线！

**简短描述**

添加简短描述以帮助您记住该命令的作用。当您键入匹配的命令时，这将在 Slack 中弹出。

对于我的，我把`Adds an event to my calendar!`

[![null](../Images/e046afbc1a7dd2bd87d256dd8f6e3ea3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C1y3_qZB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531185146535_Screen%2BShot%2B2018-07-09%2Bat%2B6.12.13%2BPM.png)

**用法提示**

这是关于如何正确调用斜杠命令的提示。正如您在上面的屏幕截图中看到的，当您开始键入命令时，它也会弹出。

对于我的，我放`“title” “start date & time” “end date & time”`。

调度程序需要三个参数，每个都用双引号括起来。第一个参数应该是事件的标题，第二个参数应该是开始日期和时间，第三个参数应该是结束日期和时间。当您在第 4 部分中构建文本解析功能时，您将看到这些是如何被解析的。

点击右下角的`Save`，你将被重定向回你的应用主页。

**第三步:在你的工作区安装应用**

在工具条上，点击`Install App`

[![null](../Images/2b34549cb95fd5251b26e14948677d74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UhmSmGah--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531859978889_image.png)

按照步骤在您的工作区安装应用程序。没有这一步，你的工作空间就不会知道你的应用和它的斜杠命令。

**步骤 4:将/scheduleme 路线添加到您的应用程序中**

在上一步中，您将 Slack slash 命令连接到 heroku app + `/scheduleme`的 url。但是`/scheduleme`指的是什么呢？暂时没有！

让我们解决这个问题。在您的文件`app.py`中，让我们添加另一条路线。您可以添加一条 url 为`scheduleme`的路线，以匹配您在 Slack 上的`Create New Command`表单中添加到 heroku 应用程序 url 末尾的内容。您将使用 slash 命令中的信息在这条路线上进行数据处理。为了表明数据是随请求一起发送的，您需要添加`methods=['POST']`作为第二个`@app.route`参数。在这种情况下，第 14 行上的函数名无关紧要，但是为了保持一致，您也可以将其称为`scheduleme`。

```
from flask import Flask
app = Flask(__name__)
PORT = 4390

@app.route('/')
def homepage():
    return "Howdy hacker!!"

@app.route('/scheduleme', methods=['POST'])
def scheduleme():
    return 'I would like to schedule that, but I haven\'t quite figured out how...'

if __name__ == '__main__':
    app.run(debug=True, port=PORT) 
```

这个函数不做太多事情，它只是返回一些文本。但让我们部署它，以确保您正确地连接您的应用程序到 Slack。

添加、提交和推送这段代码给 master，让它活起来！

然后，进入你的空闲工作区，输入`/scheduleme`并按回车键。您的机器人应该响应:

[![null](../Images/4ca45bb27ccc6254ee123bb7e9f8af5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tEyrKclT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531256268768_Screen%2BShot%2B2018-07-10%2Bat%2B1.57.06%2BPM.png)

你已经正式制作了一个 Slack 机器人！现在让我们添加一些功能！

**调试**

如果您的机器人响应正确，跳过这一部分，继续第 4 部分！

如果你在这里得到一个`404`,有一些策略可以识别出哪里出了问题。

要查看问题是否出在您的应用程序代码上，可以使用 cURL 请求来访问该端点。

`curl -X POST -F 'text="Post tutorial celebration dance" "Now" "Today at 11pm"' https://<your-app-url>/scheduleme`

将`<your-app-url>` 替换为你的 Heroku app 网址。这个命令绕过了 Slack，直接命中应用程序代码。如果 bot 没有正确响应，那么问题一定出在您的应用程序上。如果你的机器人没有正确响应，那么问题很可能来自于你的 Slack slash 命令。

**应用问题**

*   您的代码与上面的代码匹配吗？
*   这个新代码已经部署了吗——是自动部署(如果你已经安装了)还是通过 Heroku UI 或 Heroku 命令行界面手动部署？
*   本地运行应用程序，从命令行将其卷曲

`curl -X POST -F 'text="Post tutorial celebration dance" "Now" "Today at 11pm"' https://127.0.0.1:4390/scheduleme`

*   如果您安装了 Heroku 命令行界面，您可以使用以下命令查看日志

`heroku logs --tail -a <your-app-name>`；

*   你必须通过 heroku 登录才能使用。

**连接问题**

*   在 Slack 中，当你输入`/scheduleme`时，会弹出帮助命令吗？如果没有，您的应用程序可能没有安装到您的工作区。确保步骤 3 已正确完成。
*   在您在步骤 2 中填写的编辑命令页面上，您的请求 url 是否以`/scheduleme`结尾

## **第 4 部分:添加文本解析功能**

当您使用 slash 命令时，您将使用它发送一些文本。在这一步中，您将解析该文本，并确保您拥有正确数量的参数。

**第 0 步:添加新的依赖关系**

为了解析 Slack 发送的请求，您将使用两个新的依赖项。首先，您将从 Flask 导入`request`。您将使用它来访问请求中的信息。接下来，您将使用正则表达式解包请求的文本。为此，您需要将`import re`添加到您的导入部分。

文件的顶部现在看起来像这样:

```
from flask import Flask, request
import re 
```

**步骤 1:从请求中获取文本**

使用`request`库，您将需要获得用斜杠命令发送的文本。您可以通过在您的`scheduleme`路线顶部添加`raw_text = request.form.get('text')`来实现这一点。

第二步:打开文本

从 slash 命令发送的文本是命令名后面的字符串。例如，如果您在 Slack 中键入`/scheduleme "Workout" "Tomorrow at 7:30am" "Tomorrow at 8:30am"`，那么您在上一步中得到的文本将会是`"Workout" "Tomorrow at 7:30am" "Tomorrow at 8:30am"`。需要注意的是，这段文字用单引号括起来，三个部分都用双引号括起来。您可以使用正则表达式解开单引号中的文本，并将所有组件放入一个数组中。

`text_array = re.findall(r'"(.*?)"', raw_text)`

**第三步:错误处理**

您应该确保您有适当数量的参数，如果没有，就发出警告。

```
if len(text_array) != 3: 
    return 'The format is /scheduleme "[title]" "[start date & time]" "[end date & time]"'
```

**第四步:拉出事件组件**

最后，让我们从数组中取出不同的组件，并给它们起一个更好的名字。

`title, start, end = text_array`

然后，为了确保这一切顺利进行，您可以将它们全部传递给一个 return 语句，如下所示:

`return 'Sweet I parsed the title: {}, start: {} and end: {}'.format(title, start, end)`

在本节结束时，您的 app.py 文件将类似于:

```
from flask import Flask, request
import re
import os
import dateparser
import requests

app = Flask(__name__)

PORT = 4390

@app.route('/')
def homepage():
    return "Howdy hacker!!"

@app.route('/scheduleme', methods=['POST'])
def scheduleme():
    raw_text = request.form.get('text')

    text_array = re.findall(r'["“”‘’\'](.*?)["“”‘’\']', raw_text)
    if len(text_array) != 3:
        return 'The format is /scheduleme "[title]" "[start date & time]" "[end date & time]"'
    title, start, end = text_array

    return f'Sweet I parsed the title: {title}, start: {start} and end: {end}'

if __name__ == '__main__': 
    app.run(debug=True, port=PORT)
```

添加、提交和推送这段代码！要测试它，键入一个完整的`/scheduleme`命令来松弛，如下所示:

[![image_preview-5](../Images/f167977358cb479ede6720d1c69abdb9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--acsoJZwq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/3314308/image_preview-5.png)

您应该会看到:

[![null](../Images/95fbf4a8683c069389d216976a54e2e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---1XRs5J8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_9E966E4D153A92A6C1EE7A51F0576B6B726D6CE9A933DDB5AB33E608A1C0317A_1531258937486_Screen%2BShot%2B2018-07-10%2Bat%2B2.41.35%2BPM.png)

## **第 5 部分:构建调度功能**

这是你的最后一部分——万岁，你快完成了！在最后一步中，您将把请求发送给 Nylas API，它将在您的日历上创建一个事件。

**步骤 0:访问代码中的配置变量**

要访问代码中的配置变量，您需要添加另一个依赖项`os`。将行`import os`添加到 import 语句的底部。

接下来，让我们使用`os`将 Heroku 中配置变量的值提取到代码中。将 return 语句替换为:

```
calendar_id = os.environ.get('CALENDAR_ID')
assert calendar_id is not None, 'Missing `CALENDAR_ID` config variable'
access_token = os.environ.get('ACCESS_TOKEN')
assert access_token is not None, 'Missing `ACCESS_TOKEN` config variable'
timezone = os.environ.get('TIMEZONE')
assert timezone is not None, 'Missing `TIMEZONE` config variable'
```

**步骤 1:构建请求组件**

您将使用`requests`库向 Nylas API 发送请求。将`import requests`添加到您的导入语句中。您还将使用`dateparser`，它接受各种输入，例如，“今天下午 3 点”，“2018 年 12 月 31 日晚上 11:30”，“星期五上午 9 点”，并将它们转换为日期时间对象。使用这个库可以更灵活地输入斜杠命令的开始和结束部分。将`import dateparser`添加到您的导入语句中。

请求由几个部分组成——URL、头和 json。首先，您将创建一个`timezone_settings`的字典。您将把它传递给`dateparser.parse()`,以表明您希望将日期解析成一个时区感知的 datetime 对象。

`timezone_settings = {'TIMEZONE': timezone, 'RETURN_AS_TIMEZONE_AWARE': True}`

现在，您已经拥有了创建请求内容的所有信息，即`json`参数。为此，您将创建一个带有`calendar_id`、`title`和`when`键的字典。when 字典有两个键`start_time`和`end_time`。

```
json = {
        'calendar_id': calendar_id, 
        'title': title, 
        'when': {
                'start_time': int(dateparser.parse(start, settings=timezone_settings).timestamp()), 
                'end_time': int(dateparser.parse(end, settings=timezone_settings).timestamp())
            }
        }
```

在`start_time`和`end_time`的值上有很多变化！第一步，`dateparser.parse(start, settings=timezone_settings)`，是将字符串文本解析成一个时区感知的日期时间。然后，`.timestamp()`正在把它变成一个 [unix 时间戳](https://en.wikipedia.org/wiki/Unix_time) 。这个时间戳是一个浮点数，所以最后一步是用`int()`将它转换成一个整数。

请求的最后一个构建块是头，您将在其中传递访问令牌来证明您已经授权了您的帐户。

```
headers = {'authorization': access_token} 
```

最后，将所有这些片段放在一起，包装在一个 try except 块中，以捕获任何异常。

```
try: 
    response = requests.post('https://api.nylas.com/events', headers=headers, json=json) 
    if response.status_code == 200: 
        return f'Wohoo! {title} was scheduled from {start} to {end}' 
    else: 
        return f'Error! Our response has a status of {response.status_code} and text {response.text}' 
except Exception as e: 
    return f'An exception {e} occurred creating the event!'
```

使用 requests 库向 URL `'https://api.nylas.com/events'`发出 post 请求，将头和 json 作为关键字参数传入。

你的 app.py 文件将会像  那样结束

```
from flask import Flask, request
import re
import os
import dateparser
import requests

app = Flask(__name__)
PORT = 4390
@app.route('/')

def homepage():
    return "Howdy hacker!!"

@app.route('/scheduleme', methods=['POST'])
def scheduleme():
    raw_text = request.form.get('text')

    text_array = re.findall(r'["“”‘’\'](.*?)["“”‘’\']', raw_text)
    if len(text_array) != 3:
        return 'The format is /scheduleme "[title]" "[start date & time]" "[end date & time]"'
    title, start, end = text_array

    calendar_id = os.environ.get('CALENDAR_ID')
    assert calendar_id is not None, 'Missing `CALENDAR_ID` config variable'
    access_token = os.environ.get('ACCESS_TOKEN')
    assert access_token is not None, 'Missing `ACCESS_TOKEN` config variable'
    timezone = os.environ.get('TIMEZONE')
    assert timezone is not None, 'Missing `TIMEZONE` config variable'

    timezone_settings = {'TIMEZONE': timezone, 'RETURN_AS_TIMEZONE_AWARE': True
    json = {
        'calendar_id': calendar_id, 
        'title': title, 
        'when': {
                'start_time': int(dateparser.parse(start, settings=timezone_settings).timestamp()), 
                'end_time': int(dateparser.parse(end, settings=timezone_settings).timestamp())
            }
        }
    headers = {'authorization': access_token}

    try:
        if response.status_code == 200:
            return f'Wohoo! {title} was scheduled from {start} to {end}'
        else:
            return f'Error! Our response has a status of {response.status_code} and text {response.text}'
    except Exception as e:
        return f'An exception {e} occurred creating the event!'

if __name__ == '__main__':
    app.run(debug=True, port=PORT)
```

添加、提交和推送此内容。耶，你的懒汉机器人完成了！前往 Slack 并输入`/scheduleme "Post tutorial celebration dance" "Now" "Today at 11pm".`,然后，前往你的日历，你会看到一个当之无愧的庆祝舞蹈，为预定的，适当的，为这一天的剩余时间。

如果您在此过程中遇到任何问题，请查看[调试部分](#debugging)寻求帮助！

## 对于那些有这种倾向的人来说，接下来的步骤…

这是一个基本的调度程序 Slack bot——它只是添加了一个带有标题的事件。Nylas API 允许您创建带有更多相关信息的事件。一些很酷的扩展将会添加一些功能来支持:

*   创建带描述的事件
*   创建带有位置的事件
*   向事件添加参与者
*   创建重复事件
*   更新现有事件
*   删除现有事件
*   向不同的日历添加事件