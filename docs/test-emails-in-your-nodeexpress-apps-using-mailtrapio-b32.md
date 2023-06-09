# 使用 Mailtrap.io 在您的 Node/Express 应用程序中测试电子邮件

> 原文：<https://dev.to/kwabenberko/test-emails-in-your-nodeexpress-apps-using-mailtrapio-b32>

几乎所有软件应用程序都具有的一个特性是电子邮件功能，因为这是与用户联系和沟通的最可靠的方式之一。有许多电子邮件递送服务使这种功能的开发变得轻而易举，如 [Sendgrid](https://sendgrid.com/) 、[邮戳](https://postmarkapp.com/)、 [Mailgun](https://www.mailgun.com/) 和 [Sparkpost](https://www.sparkpost.com/) 。我最喜欢的是发送网格。:)

无论如何，在前期制作阶段开发电子邮件功能时最常见的问题是垃圾邮件，因为在使用上面列出的任何电子邮件发送服务时，您肯定会使用真实的电子邮件地址。因此有必要做沙盒测试。

输入[邮件陷阱](https://mailtrap.io/)。Mailtrap 是一个由 [railsware](https://railsware.com/) 开发的假 SMTP 服务器，它对生产前测试非常有用。理想情况下，你应该使用 mailtrap 之类的服务进行邮件测试，并在生产时切换到 sendgrid 之类的服务。

在这个简短的例子中，我将向您展示我如何在 Node 中进行沙盒电子邮件测试。如果你想跟随，请从 github 下载或克隆[项目](https://github.com/KwabenBerko/mailtrap-tutorial)。在您的终端中打开项目，并使用`npm install`安装所需的 npm 包。一旦安装好，`cd`进入开始文件夹，在你喜欢的编辑器中打开`app.js`文件。如果你还没有这样做，那就去 [mailtrap](https://mailtrap.io/) 网站创建一个免费账户。一旦完成，导航到[收件箱](https://mailtrap.io/inboxes)，你应该看到你的 SMTP 和 POP3 凭证。

[![Mailtrap inbox](img/8155a6e7ecb386dc4d62161b9850af3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vmmES1UX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/rxiLkdI.png)

我们将使用 [nodemailer](https://www.npmjs.com/package/nodemailer) 模块发送我们的电子邮件。让我们创建一个节点邮件 SMTP 传输器，并对其进行配置以供使用。

```
 //Start Here
  const transporter = nodemailer.createTransport({
    host: "smtp.mailtrap.io",
    port: 2525,
    auth: {
        user: "MAILTRAP_SMTP_USERNAME",
        pass: "MAILTRAP_SMTP_PASSWORD"
    }
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们设置我们的消息选项:

```
 const mailOptions= {
    from: '"Test Server" <test@example.com>',
    to: req.body.email,
    subject: "Email Test",
    text: "This is an email test using Mailtrap.io"
  }; 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们使用 transporter 对象的`sendMail()`方法发送消息:

```
 transporter.sendMail(mailOptions, (err, info) => {
    if(err){
        console.log(err);
        return next(err);
    }
    console.log("Info: ", info);
    res.json({
      message: "Email successfully sent."
    });
  }); 
```

Enter fullscreen mode Exit fullscreen mode

让我们测试一下这是否可行。通过键入`node app.js`启动服务器。现在让我们向`http://localhost:8001/email`发送一个 POST 请求。为此，我将使用[卷曲](https://en.wikipedia.org/wiki/CURL)。你可以使用[邮差](https://www.getpostman.com/)或者任何你选择的 REST 客户端。

```
 curl -X POST -H "Content-Type:application/json" -d "{"""email""":"""johndoe@gmail.com"""}" http://localhost:8001/email 
```

Enter fullscreen mode Exit fullscreen mode

希望您得到类似下面这样的响应，这意味着代码工作了:

```
 {"message":"Email successfully sent."} 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过检查我们的邮件陷阱[收件箱](https://mailtrap.io/inboxes)来看看是否是这种情况。

[![Mailtrap inbox](img/aec9ca79a00530517b120c1ab01f2866.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KZ8_U_zj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Y6S6Ixi.png)

[![Mailtrap inbox 2](img/7fc3536a1d641641adad7adcb41365fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JSjqETUc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/tZdD3y1.png)

如您所见，Mailtrap 允许您轻松地测试发送电子邮件，如上所述。本例中使用的 Node.js 应用程序使用了 [Nodemailer](https://nodemailer.com) 来发送电子邮件，但是还有其他传输选项和电子邮件包。如果您正在寻找 Nodemailer 的替代品，请查看这篇文章:[用 Node.js](https://blog.mailtrap.io/send-emails-with-nodejs/) 发送电子邮件。这篇文章深入介绍了一些面向电子邮件的包，如 [Email.js](https://www.emailjs.com/) ，以及如何使用 Gmail 从 Node.js 应用程序发送邮件。

编码快乐！