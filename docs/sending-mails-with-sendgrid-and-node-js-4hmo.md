# 使用 Sendgrid 和 Node.js 发送邮件

> 原文：<https://dev.to/feezyhendrix/sending-mails-with-sendgrid-and-node-js-4hmo>

本质上，在每个 web 应用程序中，都需要向用户发送电子邮件。在本文中，我将向您展示设置 Sendgrid 和向用户发送电子邮件是多么容易。我们将建立一个简单的模块来处理邮件发送。

[![Image result for nodejs](img/124b29e83c6673bb634f8228bd898f02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oVSG-si3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A-8kIoJKyDn-j1WPgfTvmew.jpeg)

#### 安装发送网格

首先，我们需要发送电子邮件的官方软件包。运行以下命令:

```
npm install @sendgrid/mail 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了 Sendgrid 节点包，用于在节点应用程序中发送电子邮件。

要使用这个包，你需要一个 API 密匙，在你创建了一个账户后，你可以在这里得到这个密匙。

#### 配置发送网格

在我的应用程序中，我喜欢在 utils 文件夹的一个单独文件中创建电子邮件发送模块，并将其命名为 emails.js

```
// emails.js

const SGmail = require('@sendgrid/mail')

SGmail.setApikey('xxxxx-xxxxx') // Input Api key or add to environment config 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你不知道如何在 node.js 中使用配置变量，我强烈建议你不要硬编码你的 API 键，而是使用一个环境变量，看看这个很棒的[帖子](https://medium.freecodecamp.org/heres-how-you-can-actually-use-node-environment-variables-8fdf98f53a0a)来开始

#### 邮件发送

让我们创建一个简单的函数来发送电子邮件

```
//emails.js

function newUserEmail(email, name){

const message = {

to : email, //email variable

from : { email : 'your email' , name: 'Name of user you want to send email as'},

message : `Hi there, ${name}`,

subject : "This is a test Email"

}

SGmail.send(message).then((sent) =\> {

// Awesome Logic to check if mail was sent

})

}

module.exports = {

newUserEmail
} 
```

Enter fullscreen mode Exit fullscreen mode

让我解释一下上面创建的消息对象中的每个键和值

⦁发给:收件人的电子邮件。

⦁发件人:发件人对象包含将被用作发件人电子邮件的电子邮件密钥和将被用作发件人姓名的姓名。

⦁信息:这是你的电子邮件的内容。您也可以使用 html 标记，在这种情况下，关键字不是 message，而是 HTML。

⦁主题:电子邮件主题。

#### 结论

我相信您已经看到使用 Sendgrid 发送电子邮件是多么容易。这可以用最少的配置节省大量的开发时间和调试时间。现在，您可以在代码中的任何地方导入该函数并发送邮件，非常简单！

> 通过[swithdevelop1@gmail.com](mailto:swithdevelop1@gmail.com)联系我，并在[推特](https://twitter.com/feezyhendrix)上关注我。

[![](img/33903af16c503067312230fd680a660d.png)T2】](http://bit.ly/codeburst)

> ✉️ *订阅* CodeBurst 的*一周一次*[***email blast***](http://bit.ly/codeburst-email)*🐦*跟随* CodeBurst *上*[***Twitter***](http://bit.ly/codeburst-twitter)*，查看*🗺️[***2018 Web 开发者路线图***](http://bit.ly/2018-web-dev-roadmap) *，以及* 🕸️ [***学全栈 web 开发***](http://bit.ly/learn-web-dev-codeburst) *。**

 ** * **