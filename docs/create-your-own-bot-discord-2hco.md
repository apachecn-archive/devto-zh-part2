# 创造你自己的机器人不和谐

> 原文：<https://dev.to/mrrobot/create-your-own-bot-discord-2hco>

| 放弃 | 本教程用于创建带前缀的命令。它不适合使用斜杠命令。 |
| --- | --- |

*   **序言**

在开始本教程之前，请遵循以下说明。
你必须知道 JavaScript。你需要安装几个工具来让机器人工作，并创建一个文件夹，可以用任何名字，最好是短的，不带空格。这将是您包含 bot 文件的文件夹。

工具列表:

*   安装[NodeJS](https://nodejs.org/)的最新稳定版本
*   能够访问终端
*   网络连接良好
*   知道如何阅读最低限度的英文文件
*   文本编辑器或 IDE (Notepad ++，Atom，JetBrains，VSC..)

如果一切都安装好了，我们首先会的。打开您的终端，而不是 NodeJS 安装的终端，并键入:

```
node --version 
```

Enter fullscreen mode Exit fullscreen mode

它应该带你到你已经安装的版本。在剩余的安装过程中，保持此窗口打开。

*   **安装**

在您成功安装了最新版本的 NodeJS 之后，您必须在您的 bot 文件夹中安装。按顺序移动客人:

```
cd /folderName 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，通过使用 GUI 而不是命令提示符导航到您的文件夹，并在顶部复制 URL。然后通过在开头添加`cd`将其粘贴到终端中，以表明您想要导航到该文件夹。

> ⚠如果你不在你的终端的文件夹里，请检查上面几行。

我们将为机器人安装工作包。这个包叫做 Discord.js，它是一个与 Discord 相关的库，用 JavaScript 开发。

```
npm install discord.js --save 
```

Enter fullscreen mode Exit fullscreen mode

您通常会有一些错误，但不会阻止您启动 bot，以及在您的文件夹中创建一个 **node_modules** 文件夹。这是我们从 NodeJS 安装所有包以使代码工作的地方。

`+ discord.js@13.5.1
updated 1 package in 1.241s`

最后，我们看到 discord.js 版本 13.5.1 的包已经安装在该文件夹中。

*   **例题**

一切都安装好了？精彩！现在请创建一个您将命名为`app.js`的文件。请注意，您的文件扩展名不要以除了`.js`以外的任何内容结尾，因为否则我们将无法执行该脚本。那就打开它。

> ⚠提醒:用文本编辑器或 IDE 打开，如 VSC、Atom、Notepad ++。

对于每个 JavaScript 文件，我们必须调用 discord.js 以便一切都完美地运行，我们还将声明机器人客户端:

```
const Discord = require('discord.js')
const client = new Discord.Client()

client.on("message", message => {

  // Your commands here

}) 
```

Enter fullscreen mode Exit fullscreen mode

乒乓消息示例:

```
// Result in: If the user's message contains "ping" then

if(message.content === "ping"){

   // the bot answers pong!
   message.channel.send("Pong!")

} 
```

Enter fullscreen mode Exit fullscreen mode

带前缀的示例消息:

```
// We declare the prefix
const prefix = '?'

// The bot will answer this if a user does ?Help
if(message.content.startsWith(prefix + "help")){
   message.channel.send("You did `?help` to get help.")
} 
```

Enter fullscreen mode Exit fullscreen mode

私信示例:

```
if(message.content == "mp") {
   message.author.send("Here is a private message !")
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **机器人创建**

现在，您必须在 Discord 平台上创建 bot，并将其添加到您的服务器中。为此，我们将分两个阶段进行。遵循以下流程:

> ⚠你必须登录到你的 Discord 帐户才能访问这个页面。

*   前往:[https://discord.com/developers/applications/me](https://discord.com/developers/applications/me)
*   然后**我的应用** → **新应用**。
*   完成表格。完成后，点击“**创建应用程序**”，然后在同一按钮上再次点击**。**
***   点击**创建一个机器人用户**然后点击**是的，做吧！****

 **[![This is a preview](img/bcce9a5fa272754778a9ae9e965b3513.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cbgMW4oJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8016yfpogcurzb3gsjph.gif)

激活“模式”机器人

[![This is a preview for activating the 'mode' bot](img/6b9d32c1759cb89e39b9f60adf3d6c65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--92L8uiVS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lcpkmvhiby8n61nsyykm.png)

对于权限，机器人需要读取服务器上的消息(消息内容意图)。

它仍然需要添加到您自己的服务器上。为此，只需点击“**生成 OAuth2 URL** ”和**生成您的链接**。复制它并在一个新标签中打开它，你被要求**选择一个服务器**。选择你的，点击**允许**。

现在，您的 bot 已经在服务器上，但是它是离线的。这是完全正常的！按照顺序打开它。

*   **启动**

您必须从 Discord for Developers 页面复制您的 bot 令牌，并在您的`app.js` :
末尾插入这段代码

```
client.login("YOUR_TOKEN_HERE_WITHOUT_SPACE"); 
```

Enter fullscreen mode Exit fullscreen mode

复制令牌的示例:

[![Screen of example of a copy token](img/c5dd1a5671be6715c10cd89e8154aaff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fNouYkPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ywvuv78t9y0uv8fcabuz.png)

保存您的文件，并在命令提示符下执行该命令:

```
node app.js 
```

Enter fullscreen mode Exit fullscreen mode

这就对了。你的机器人启动了！🎉

[![Preview](img/d83e2faa75c1a6ae4f2d506d1a38c638.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MZBP91-E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jyjac7sagqzplicjvuy3.gif)

* * *

🇫🇷教程[来自我的网站](https://thomasbnt.dev/blog/robot-discord-basique)，它是用法语写的。
可以[下载文件](https://github.com/thomasbnt/Robot-Discord-Basique-en-JS/releases/)和[这里是 discord.js 文档](https://discord.js.org/#/docs/main/stable/general/welcome)。

| ☕ | 查看我的[推特账号](https://twitter.com/thomasbnt_)。您可以看到许多项目和更新。你也可以[在给我买杯咖啡上支持我，Stripe 或者 GitHub 赞助商](https://thomasbnt.dev/donate)。感谢阅读我的帖子！🤩 |
| --- | --- |**