# 为什么应该包装(JavaScript)依赖项

> 原文：<https://dev.to/hugo__df/why-you-should-wrap-your-javascript-dependencies-48o2>

> 一个邮件发送的例子。

*   [使用发送网格的示例](#example-using-sendgrid)
*   我们如何消费这个？
*   [切换到另一个电子邮件提供商](#switching-to-another-email-provider)

> 这是用雨果简讯的[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

## 使用 SendGrid 的例子

在非常基本的层面上，我们希望能够发送电子邮件。

用代码术语来说，我们想要一个`send`函数。最好有一种抽象消息创建的方法，这是一个`makeMailMessage`函数。

参见下面的代码:

```
const sgMail = require('@sendgrid/mail');

const {
  SENDGRID_API_KEY,
  EMAIL_OVERRIDE_TO_ADDRESS,
  EMAIL_FROM_ADDRESS
} = process.env;

sgMail.setApiKey(SENDGRID_API_KEY);

function makeMailMessage ({
  to,
  from = EMAIL_FROM_ADDRESS,
  subject,
  text,
  html
}) {
  return {
    to: EMAIL_OVERRIDE_TO_ADDRESS || to,
    from,
    subject,
    ...(html ? { html } : { text })
  };
}
function send (message) {
  return sgMail.send(message);
}
module.exports = {
  makeMailMessage,
  send
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 我们如何消费这个？

```
function doSomeSending(name, email) {
  const message = makeMailMessage({
    from: 'hi@codewithhugo.com',
    to: email,
    subject: 'Welcome to Code with Hugo',
    text: `Hi ${name}, welcome to Code with Hugo.`
  });
  return send(message); 
} 
```

Enter fullscreen mode Exit fullscreen mode

## 切换到另一个电子邮件提供商

比如说我们想换一个提供商，也就是 Mailgun。理想情况下，我们不想改变任何应用程序代码。

```
const {
  MAILGUN_API_KEY,
  EMAIL_DOMAIN,
  EMAIL_OVERRIDE_TO_ADDRESS,
  EMAIL_FROM_ADDRESS
} = process.env;
const mailgun = require('mailgun-js')({
  apiKey: MAILGUN_API_KEY,
  domain: EMAIL_DOMAIN
});

function makeMailMessage ({
  to,
  from = EMAIL_FROM_ADDRESS,
  subject,
  text,
  html
}) {
  return {
    to: EMAIL_OVERRIDE_TO_ADDRESS || to,
    from,
    subject,
    ...(html ? { html } : { text })
  };
}

function send (message) {
  return new Promise((resolve, reject) => {
    mailgun.messages().send(message, (error, body) => {
      if (error) {
        return reject(error);
      }
      resolve(body);
    });
  });
}

module.exports = {
  makeMailMessage,
  send
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们的`mail`模块的 API 保持不变，但是我们已经改变了幕后的提供者👍。

塞缪尔·泽勒