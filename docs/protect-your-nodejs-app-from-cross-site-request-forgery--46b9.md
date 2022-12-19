# 保护 Node.js 应用程序免受跨站点请求伪造

> 原文：<https://dev.to/dkundel/protect-your-nodejs-app-from-cross-site-request-forgery--46b9>

*[保护你的 Node.js 应用免受跨站请求伪造](https://www.twilio.com/blog/2018/01/protect-your-node-js-app-from-cross-site-request-forgery.html)最初发表于 2018 年 1 月 [Twilio 博客](https://www.twilio.com/blog)。*

* * *

使用 web 应用程序时的一个经典攻击是[跨站点请求伪造，又名 CSRF/XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) (读 C-Surf)。攻击者使用它们来代表应用程序中的用户执行请求，而不会引起他们的注意。让我们看看他们如何做到这一点，以及我们如何保护我们的应用程序免受这些类型的威胁。

### 让我们来谈谈理论

[![decorative gif](../Images/0fd9aac406210ccca485cec757cd534b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sUGLePfn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ghdgsdamgvt2xn7yyu2.gif)

在我们预防 CSRF 袭击之前，我们需要了解它们是如何运作的。通常，这些攻击是针对使用基于表单的提交(如`POST`请求和基于 cookie 的认证)的 web 应用程序的功能执行的。

攻击者在他们的恶意页面中放置一个隐藏的表单，该表单会自动执行对页面端点的`POST`请求。然后，浏览器会自动发送为该页面存储的所有 cookies 以及请求。例如，如果用户登录到当前会话中，攻击者可以在登录用户不注意的情况下代表登录用户发布消息。为此，攻击者不必访问页面的 cookies。

[![diagram visualizing a CSRF attack](../Images/cedcfb55e5fb29eb022901d102f02fb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kJHNvRbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dbcufyw0gocigyrtzxb3.png)

我们可以通过使用 CSRF 代币来保护自己免受攻击。其概念是，当浏览器从服务器获得一个页面时，它发送一个随机生成的字符串作为 CSRF 令牌作为 cookie。稍后，当您的页面执行 POST 请求时，它会将 CSRF 令牌作为 cookie 发送，也可以通过另一种方式发送，例如在正文中作为参数或者通过类似`X-CSRF-Token`的 HTTP 头发送。

[![diagram visualizing CSRF token protection](../Images/e58596b1ecb6b83bf713251f653164e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4j-WN7kZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/84fgjrf3ho7anat3d3vt.png)

攻击者将无法使用他们的隐藏表单再现相同的行为，因为他们无法访问 cookie 来检索值并将其与恶意 POST 请求一起发送。

这个概念几乎可以在任何 web 应用程序中实现，但是让我们看看如何在一个 [Express](https://expressjs.com/) 应用程序中实现它。

### 准备好棋盘

[![decorative gif](../Images/0d3a4049fe3c46790988a033ada060c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N06-eDnO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aye2w0gzbbtlzunz5i1w.gif)

首先，我们需要一个应用程序来了解 CSRF 漏洞在现实中是如何工作的，以及我们如何保护自己免受其害。如果您已经有一个现有的 [Express](https://expressjs.com/) 应用程序，请随意对其执行以下步骤。或者按照下面的步骤来设置我们的演示应用程序。

在我们开始之前，确保你已经安装了[node . js]([https://nodejs.org](https://nodejs.org))和 [npm](https://npmjs.com) 或者其他的包管理器。在您的终端上运行以下命令来启动新项目:

```
mkdir csrf-demo
cd csrf-demo
npm init -y
npm install express body-parser --save 
```

Enter fullscreen mode Exit fullscreen mode

接下来创建一个名为`index.js`的新文件，并将下面的代码放入其中:

```
const express = require('express');
const bodyParser = require('body-parser');

const PORT = process.env.PORT || 3000;
const app = express();

app.use(bodyParser.urlencoded({
  extended: true
}));

app.get('/', (req, res) => {
  res.send(`
    <h1>Hello World</h1>
    <form action="/entry" method="POST">
      <div>
        <label for="message">Enter a message</label>
        <input id="message" name="message" type="text" />
      </div>
      <input type="submit" value="Submit" />
    </form>
  `);
});

app.post('/entry', (req, res) => {
  console.log(`Message received: ${req.body.message}`);
  res.send(`Message received: ${req.body.message}`);
});

app.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过运行
启动应用程序

```
node . 
```

Enter fullscreen mode Exit fullscreen mode

访问 [http://localhost:3000](http://localhost:3000) ，你应该会看到`Hello World`和它下面的一个小表格。

[![screenshot of the web page displaying hello world and an input field](../Images/bcdcaafed4c22c2c440deb91968096a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h0kIcSht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8eux1ibc9cn2dnqizuw.png)

### 危险的水

当前服务器有两个端点。一个是我们的主页，当你去[的时候就会看到。另一个是](http://localhost:3000/)[http://localhost:3000/entry](http://localhost:3000/entry)上的一个`POST`端点。当我们填写表单并按 Submit 时，我们将向这个端点发出一个`POST`请求。

尝试在表单中输入一些文本，然后按 submit。您应该会看到返回的消息，它也应该会被记录到正在运行的服务器的控制台中。

[![screenshot of terminal window displaying the message output](../Images/faa02eccb47b57d24bc0438d81959b0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oB_tt2p---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l7so7r1mpc0jrfiaqtxy.png)

不幸的是，攻击者能够在他们的页面上执行相同的请求。为了进行模拟，我们在 Glitch 的[页面上实现了相同的表单。访问](https://glitch.com/edit/#!/csrf-attack?path=index.html:1:0) [csrf-attack.glitch.me](http://csrf-attack.glitch.me/) ，输入消息，按提交。该行为将与在`localhost`页面上提交表单相同。它将传输消息以及设置的任何 cookies。

在这种情况下，我们创建了一个用户可以自己提交的表单，但它可能是一个自动提交恶意内容的隐藏表单。让我们看看如何保护我们的网页免受这种情况。

### 打算`csurf` ing

有多个模块可以帮助您在应用程序中实现 CSRF 令牌。其中一个就是 [csurf](https://npm.im/csurf) 。通过运行
，安装该模块以及 [cookie 解析器](https://npm.im/cookie-parser)依赖项

```
npm install cookie-parser csurf --save 
```

Enter fullscreen mode Exit fullscreen mode

这两个模块都是中间件，可以改变 Express 中请求的行为。我们已经在使用`body-parser`解析我们的`POST`主体来检索消息。此外，我们将使用它来检查`_csrf`令牌。`cookie-parser`中间件将检查令牌在 cookie 中是否可用，并且`csurf`将通过检查`_csrf`令牌在 cookie 和请求体中是否存在以及它们是否匹配来自动保护任何`POST`、`PUT`、`PATCH`或`DELETE`操作。

将以下代码添加到您的`index.js`文件中，以配置中间件:

```
const express = require('express');
const bodyParser = require('body-parser');
const csurf = require('csurf');
const cookieParser = require('cookie-parser');

const PORT = process.env.PORT || 3000;
const app = express();

const csrfMiddleware = csurf({
  cookie: true
});

app.use(bodyParser.urlencoded({
  extended: true
}));
app.use(cookieParser());
app.use(csrfMiddleware);

app.get('/', (req, res) => {
  res.send(`
    <h1>Hello World</h1>
    <form action="/entry" method="POST">
      <div>
        <label for="message">Enter a message</label>
        <input id="message" name="message" type="text" />
      </div>
      <input type="submit" value="Submit" />
      <input type="hidden" name="_csrf" value="${req.csrfToken()}" />
    </form>
  `);
});

app.post('/entry', (req, res) => {
  console.log(`Message received: ${req.body.message}`);
  res.send(`CSRF token used: ${req.body._csrf}, Message received: ${req.body.message}`);
});

app.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

重启你的服务器并导航到 [http://localhost:3000](http://localhost:3000) 。在输入框中输入一些文本，然后点击`Submit`。您应该会在控制台中看到该消息，并在浏览器中看到如下消息:

[![screenshot of browser displaying a CSRF token and the message submitted](../Images/5aa71868121697ccf5424e0f10b62f09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fTh1d_yn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4muknc1enqo5hux2q4dt.png)

现在切换回 Glitch 上的[演示页面，并在那里输入信息。当你点击提交，你会看到请求失败，消息不会出现在控制台上。传输了`_csrf` cookie，但是页面没有在`POST`主体中发送与`_csrf`值相同的值。结果，请求被`csurf`中间件阻止，我们保护自己免受 CSRF 攻击。](http://csrf-attack.glitch.me/)

### 接下来是什么？

我们已经看到了如何通过服务器端呈现的代码轻松地将 CSRF 令牌集成到基于 Node.js 的应用程序中。然而，在您的前端框架和库上使用 CSRF 令牌也同样简单。因为我们将令牌作为 cookie 发送，所以您可以轻松地读取它，并在以后将其作为报头与异步请求一起发送。事实上 [Angular 的`HttpClient`已经内置了这个功能](https://angular.io/guide/http#security-xsrf-protection)。

要了解如何保护 Node.js 应用程序的更多信息，请务必查看我关于保护 Express 应用程序的博文。此外，您应该查看 [OWASP 页面](https://www.owasp.org/index.php/Main_Page)，因为它涵盖了广泛的安全相关主题。

如果您有任何问题或任何其他有助于提高 Node.js web 应用程序安全性的工具，请随时 ping 我:

*   🐦推特: [@dkundel](https://dev.to/dkundel)
*   📧电子邮件:[dkundel@twilio.com](mailto:dkundel@twilio.com)
*   🐙🐱GitHub: dkundel

* * *

*[保护你的 Node.js 应用免受跨站请求伪造](https://www.twilio.com/blog/2018/01/protect-your-node-js-app-from-cross-site-request-forgery.html)最初发表于 2018 年 1 月 [Twilio 博客](https://www.twilio.com/blog)。*