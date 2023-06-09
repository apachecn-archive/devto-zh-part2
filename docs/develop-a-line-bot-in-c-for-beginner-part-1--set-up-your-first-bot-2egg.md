# 用 C#为初学者开发一个直线机器人第 1 部分:设置你的第一个机器人

> 原文：<https://dev.to/kenakamu/develop-a-line-bot-in-c-for-beginner-part-1--set-up-your-first-bot-2egg>

# 概述

这是两篇文章中的第一篇。在本文中，我将解释如何开始用 C#创建第一个 LINE Bot。该系列包括以下内容。

*   如何将 LINE bot 注册到 LINE 平台
*   如何为 LINE Bot 创建 C#解决方案
*   如何部署到云
*   自然语言理解

# 先决条件

*   对 C#和 ASP.NET 核心有基本的了解
*   [Visual Studio 代码](https://code.visualstudio.com/)的基本用法
*   dot net core SDK[Windows](https://www.microsoft.com/net/learn/get-started/windows)/[Linux](https://www.microsoft.com/net/learn/get-started/linuxredhat)/[Mac](https://www.microsoft.com/net/learn/get-started/macos)
*   [Azure 订阅](https://azure.microsoft.com/en-us/free/)
*   [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
*   [Node.js](https://nodejs.org/ja/download/) :针对解决方案模板和模拟器。
*   [去](https://git-scm.com/download)

# 本文议程

*   在 LINE Developer 控制台中创建 LINE Bot 帐户。我在本文中将注册的 bot 称为“通道”。
*   用 C#开发 LINE Bot，部署到 Azure Web App。
*   设置通道，并从物理设备上进行测试。
*   设置模拟器来实现剩余的代码。
*   将完成的 bot 部署到 Azure。

语言理解和对话将在第 2 部分中讨论。

# 这个 bot 是做什么的？

你不能创建“通用”机器人，因为用户不容易知道它能做什么。作为最佳实践，我强烈建议为 bot 设置范围，以便用户从一开始就知道它应该做什么。在本文中，假设我们为“Contoso Burger”开发了一个机器人，它接受订单并送货上门。

# 步骤

## 创建通道

1.要创建一个渠道，你需要是线开发。前往 [LINE 开发者控制台](https://developers.line.me/en/)并使用您的 LINE 账户登录。您必须拥有至少一个 LINE 帐户，并在您的智能手机上安装 LINE client。按照说明，将自己注册为开发人员。

2.如果你还没有或者需要一个新的，创建一个。提供商可以有多个机器人。就我而言。
[![image](img/854af955393b255306a99418e97ddca1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NjNZmvqe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bifphvztowrk1f4i91xp.PNG)

3.为了演示，我单击“添加新提供商”。输入提供商名称并单击“确认”，然后单击“创建”。避免使用包含“行”的名称。
[![image](img/5247f2eb4a7720070e805696c5cb7a44.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--FXFXXoJP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i4b7wawav59tnw4de642.PNG)

4.创建提供者后，单击“消息传递 API”。“线登录”是不同的功能，我会写一篇关于登录的文章在单独的文章。然后输入名称和描述。图像是可选的。

[![image](img/8708fc30ad5c4be549df37161e6658d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PACyVjlw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7isjpwo60jfxodfn1w6c.PNG)

5.对于计划，请选择“开发者试用版”。设置适当的类别，然后单击“确认”。您需要输入真实的电子邮件地址。
[![image](img/e5bca52825623c95480d42cd4a6220b8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--FuqAeDRi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fgvhmkufc3k9bddp8nhi.PNG)

6.同意使用条款，然后单击“创建”。

7.创建完成后，您会看到下面的屏幕。单击“配置尚未完成”。
[![image](img/3af98bb991917ba4ab5396628b176722.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--LuzKJ5GE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1fa51xdjvo2ajg43xbbu.PNG)

8.向下滚动到通道访问令牌部分，然后单击“发布”。
[![image](img/bdfeef89828981d31002d101b30acc9a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WreKVWeP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kf0i3314xtj9zhxu9a0d.PNG)

9.点击“发布”0 小时。则颁发的令牌不会过期。
[![image](img/9b8eae34a33ef34c1cd9723213d99d9d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--h0JvBnmv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zidw8m2gljc9dz5cnqyt.PNG)

10.如下设置其他相关设置。

*   使用 webhooks:已启用
*   自动回复邮件:禁用
*   问候语:已禁用

11.快好了！因为我们需要颁发的令牌和通道密码，所以保持窗口打开。

## 解决模板和模拟器

有用于 LINE Bot 的 yeoman 包和用于 LINE Developer 的 LINE Client simulator。运行以下命令来安装它们。

窗户

```
npm install -g yo
npm install -g generator-line-bot
npm install -g line-simulator 
```

Enter fullscreen mode Exit fullscreen mode

苹果电脑

```
sudo npm install -g yo
sudo npm install -g generator-line-bot
sudo npm install -g line-simulator 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 Azure 资源

我假设你已经知道如何使用 Azure Portal，所以这次让我们通过 Azure CLI 来做。

1.先安装 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) 。

2.然后登录 Azure。

```
az login 
```

Enter fullscreen mode Exit fullscreen mode

3.按照说明完成登录。

4.登录完成后，如果您有多个套餐，请选择合适的套餐。“列表”命令显示所有订阅，而“显示”命令显示当前设置的订阅。

```
az account list
az account set -s 'subscription id'
az account show 
```

Enter fullscreen mode Exit fullscreen mode

5.首先创建一个资源组。在以下示例中，在美国东部地区创建了“contosoburgerbot”资源组。您可以指定任何名称。

```
az group create -n contosoburgerbot -l eastus 
```

Enter fullscreen mode Exit fullscreen mode

6.然后在资源组上创建一个存储帐户。该名称应该是全球唯一的
。我们将状态信息存储在这个存储器中。g 选项用于指定资源组。

```
az storage account create -n contosoburgerbotstorage -g contosoburgerbot  --sku Standard_LRS 
```

Enter fullscreen mode Exit fullscreen mode

7.完成后，运行命令获取连接字符串密钥。

```
az storage account show-connection-string -n contosoburgerbotstorage -g contosoburgerbot 
```

Enter fullscreen mode Exit fullscreen mode

8.接下来，创建 WebApp。为此，我们需要首先创建一个服务计划。我将 sku 指定为 F1，这是免费的。请为 WebApp 使用全局唯一的名称。

```
az appservice plan create -n contosoburgerbotplan -g contosoburgerbot --sku F1
az webapp create -n contosoburgerbot -g contosoburgerbot -p contosoburgerbotplan 
```

Enter fullscreen mode Exit fullscreen mode

9.我们使用 git deploy，所以我们需要首先设置用户名和密码。这是您的订阅范围设置，因此如果您之前已经设置过，可以跳过第一个命令。第二个命令将本地 git 部署到您刚刚创建的 web 应用程序。将用户名和密码更改为您自己的。

```
az webapp deployment user set --user-name kenakamu --password Pa$$w0rd
az webapp deployment source config-local-git -n contosoburgerbot -g contosoburgerbot 
```

Enter fullscreen mode Exit fullscreen mode

注意返回的地址，因为它是 git deploy 的远程地址。

## 用 C 开发 Bot

1.打开命令提示符或终端，并根据您的选择更改目录。我搬到了桌面。

2.运行以下命令生成解决方案。运行该命令时，它会提示您输入通道密码、令牌和存储密钥。如果你真的想的话，你可以改变机器人的名字，但是这会影响下面的所有步骤，所以这次请使用相同的名字。指定的名称用于生成的解决方案中的文件夹名称和命名空间。

```
yo line-bot contosoburgerbot --csharp --webapi 
```

Enter fullscreen mode Exit fullscreen mode

3.移动到创建的文件夹。

```
cd myfirstbot 
```

Enter fullscreen mode Exit fullscreen mode

## 将 bot 部署到 Azure

1.要使用 git deply，首先要初始化它。

```
git init 
```

Enter fullscreen mode Exit fullscreen mode

2.然后添加远程。

```
git remote add azure https://kenakamu@contosoburgerbot.scm.azurewebsites.net/contosoburgerbot.git 
```

Enter fullscreen mode Exit fullscreen mode

3.在 Visual Studio 代码
中打开解决方案

```
code . --locale=en 
```

Enter fullscreen mode Exit fullscreen mode

4.添加”。gitignore”文件，然后指定不想推送到远程的文件/文件夹。不要忘记保存文件。

```
.vscode
bin
debug 
```

Enter fullscreen mode Exit fullscreen mode

5.然后添加并提交全部。

```
git add .
git commit -m "Initial" 
```

Enter fullscreen mode Exit fullscreen mode

6.运行以下命令进行部署。这需要时间，因为这是最初的推动。如果出现提示，请输入用户名和密码。

```
git push -u azure master 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 web 手册 URL

当 LINE Platform 收到用户发送给你的 bot 的消息时，它会将请求重定向到你在 Webhook 中指定的 URL。因此，我们将 URL 设置为我们的 Azure 地址。模板使用/api/linebot 作为端点，因此 URL 应该是这样的。将主机名更改为您自己的名称。

[![image](img/8dda156cb2fc3f5f53d9a8e046673607.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4mhyHQFb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ryper3mo26ubphub4ugl.PNG)

设置 Webhook 后，使用页面底部的二维码将该机器人添加到您自己的 LINE 帐户中作为朋友。

现在你可以玩你的机器人了。解决方案模板包含回复任何类型的行消息的示例代码。尝试发送文本、图像、位置、贴纸等。

## 苦读

Azure WebApps 提供了一个叫做 Kudu 的工具。您可以使用该工具来确认实际部署了哪些文件以及有用的日志。

1.打开浏览器，输入 git URL 主机地址。
即)[https://contosoburgerbot.scm.azurewebsites.net](https://contosoburgerbot.scm.azurewebsites.net)

2.单击调试控制台-> CMD/PowerShell。

3.转到站点-> wwwroot，您会看到这些文件。

Kudu 提供了许多有用的特性。如果你感兴趣，请玩玩它。

# 回复用户消息。

现在让我们实现您自己的代码。

1.打开 LineBotApp.cs，所有的 bot 逻辑样本代码都在这里。当用户发送文本消息并回复用户时，将触发 HandleTextAsync。用下面的代码替换整个代码。

##### LineBotApp.cs

```
using Line.Messaging;
using Line.Messaging.Webhooks;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using contosoburgerbot.CloudStorage;
using contosoburgerbot.Models;

namespace contosoburgerbot
{
    internal class LineBotApp : WebhookApplication
    {
        private LineMessagingClient messagingClient { get; }
        private TableStorage<EventSourceState> sourceState { get; }
        private BlobStorage blobStorage { get; }

        public LineBotApp(LineMessagingClient lineMessagingClient, TableStorage<EventSourceState> tableStorage, BlobStorage blobStorage)
        {
            this.messagingClient = lineMessagingClient;
            this.sourceState = tableStorage;
            this.blobStorage = blobStorage;
        }

        protected override async Task OnMessageAsync(MessageEvent ev)
        {
            switch (ev.Message.Type)
            {
                case EventMessageType.Text:
                    await HandleTextAsync(ev.ReplyToken, ((TextEventMessage)ev.Message).Text, ev.Source.UserId);
                    break;
            }
        }

        private async Task HandleTextAsync(string replyToken, string userMessage, string userId)
        {  
            var replyMessage = new TextMessage($"You said: {userMessage}");
            if(userMessage.ToLower() == "hello"){
               replyMessage.Text = "Hi!!"; 
            }

            await messagingClient.ReplyMessageAsync(replyToken, new List<ISendMessage> { replyMessage });
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

2.再次部署到 Azure。

```
git commit -am update
git push 
```

Enter fullscreen mode Exit fullscreen mode

3.发送“你好”给你的机器人，你应该得到“嗨！!"作为回答。

# 使用模拟器进行调试。

有几种方法可以调试部署的环境，但这不是超级容易也不是超级第一。这就是为什么我们在这里使用模拟器。

1.打开 Controllers/linebotcontroller . cs，更新构造函数，让你的 bot 知道模拟器在哪里。

##### LineBotController.cs

```
public LineBotController(IOptions<AppSettings> options)
{
    appsettings = options.Value;
    #if DEBUG
    lineMessagingClient = new LineMessagingClient(appsettings.LineSettings.ChannelAccessToken,
        "http://localhost:8080");
    #else
    lineMessagingClient = new LineMessagingClient(appsettings.LineSettings.ChannelAccessToken);
    #endif
} 
```

Enter fullscreen mode Exit fullscreen mode

5.输入 F5 键。检查服务的地址。默认情况下，它应该在 localhost:5000 上运行。您可以忽略屏幕上的错误信息。

6.将断点放在 LineBotApp.cs 的第 39 行。

[![image](img/06724dd7069ed5cad002a72f45187157.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sa5kZKuk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/up35zjjymwxlrvtcq05v.PNG)

7.打开新的命令提示符或终端，运行以下命令启动模拟器

```
line-simulator 
```

Enter fullscreen mode Exit fullscreen mode

8.打开 Chrome，连接到 [http://localhost:8080](http://localhost:8080)

9.您应该会看到连接页面。输入值，然后单击连接。
-用户名可以在 LINE Bot 注册页面底部找到。
- Bot API 服务器地址为[http://localhost:5000/API/line Bot](http://localhost:5000/api/linebot)

10.从聊天栏输入“hello ”,并确认点击断点。

[![image](img/b1cc44df43f0806ab37dcc2362dfef0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--niUO2N3E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fo4i443zrkfitipc7xlw.PNG)

11.输入 F5 键继续调试。

[![image](img/5e03e61c3a4eb57d3d340f4a0e7f89e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SFGPDaph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1qew5860tbpyzibpltmt.PNG)

# 下一步

[用 C#为初学者开发一个直线机器人第 1 部分:给你的机器人增加智能](https://dev.to/kenakamu/develop-a-line-bot-in-c-for-beginner-part-2--adding-ai-to-your-bot--2b5d)