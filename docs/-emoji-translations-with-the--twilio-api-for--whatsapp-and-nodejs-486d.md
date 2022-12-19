# 👉表情符号翻译📞Twilio API 用于💬WhatsApp 和 Node.js

> 原文：<https://dev.to/twilio/-emoji-translations-with-the--twilio-api-for--whatsapp-and-nodejs-486d>

我💖表情符号，所以当我听说 WhatsApp 的新的 Twilio API 时，我想做一些东西，比如 T2 的表情符号。受 [Monica Dinculescu 的](https://twitter.com/notwaldorf) [to_emoji Twitter bot](https://twitter.com/to_emoji/) 和 [emoji translator](https://meowni.ca/emoji-translate/) 的启发，我决定开发一个 WhatsApp 文本到 emoji translator。您现在可以通过[将您的消息发送到我们的 WhatsApp 号码+441745472072](https://wa.me/441745472072?text=she%20sells%20sea%20shells%20on%20the%20seashore) 来尝试一下。

[![An example of sending a message to the app we're going to build. The WhatsApp number responds with the message translated to emoji.](../Images/a74c8b372dcac96a7b43216f20e9a2ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zGzFPnWB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/07/example.png)

以下是你也可以构建这个应用程序的方法。

## 🛠工具

我决定跟随 Monica 项目的脚步，使用 Node.js 构建这个项目。WhatsApp 通过 Twilio 发送消息会导致 webhooks，这与接收发给 Twilio 号码的短信非常相似，所以如果你之前已经开发过 Twilio 短信应用程序，这应该很熟悉。为了便于部署，我将把它构建成一个 [Twilio 函数](https://www.twilio.com/docs/runtime/functions)。

如果你想继续开发表情符号翻译器，你需要:

*   Twilio 帐户([在此注册免费的 Twilio 帐户](https://www.twilio.com/try-twilio)
*   WhatsApp 沙盒频道。按照这些说明在你的账户中安装 WhatsApp 沙盒频道。你还需要[将你自己的 WhatsApp 账户与沙盒连接](https://www.twilio.com/docs/sms/whatsapp/api#joining-a-sandbox)

仅此而已。让我们开始建造吧！

## 🏗构建应用程序

首先，让我们看看 Monica 的应用程序是由什么驱动的。

为它们两个提供动力的是 [`moji-translate`模块](https://github.com/notwaldorf/emoji-translate)，它又使用了[慕安池欧](https://github.com/muan)的 [`emojilib`](https://github.com/muan/emojilib) 关键词库。要在 Twilio 函数中使用`moji-translate`,我们需要安装它。

在 Twilio 控制台中，进入*运行时*部分来[配置你的功能](https://www.twilio.com/console/runtime/functions/configure)。在依赖项部分添加版本 1.0.8 的`moji-translate`。保存配置，我们准备好构建函数了。

[![Add moji-translate and the version 1.0.8 to the dependencies section, then save.](../Images/1f282d5ea0a39169e7855bdf9a5272e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kiZ-TLAp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/07/dependencies-1024x525.png)

从[管理页面](https://www.twilio.com/console/runtime/functions/manage)添加一个新功能，并选择“Hello SMS”模板，因为回复收到的 WhatsApp 消息使用与回复收到的 SMS 消息相同的 [TwiML](https://www.twilio.com/docs/sms/twiml) 。给你的函数一个名字和一个路径。

[![Give your function a name and a path.](../Images/066032ecdb930b0ae09da1fb2a17016b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EWmsjUqT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/07/function.png)

到目前为止，代码应该是这样的:

```
exports.handler = function(context, event, callback) {
  let twiml = new Twilio.twiml.MessagingResponse();
  twiml.message("Hello World");
  callback(null, twiml);
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了构建我们的表情符号翻译器，我们首先需要从`event`对象中获取传入消息的正文。然后我们可以通过`moji-translate`模块传递它，并在 TwiML 中返回它，代替上面例子中的“Hello World”。

```
exports.handler = function(context, event, callback) {
  const { translate } = require('moji-translate');
  const incomingBody = event.Body;
  const translatedBody = translate(incomingBody);

  const response = new Twilio.twiml.MessagingResponse();
  response.message(translatedBody);

  callback(null, response);
}; 
```

Enter fullscreen mode Exit fullscreen mode

保存该功能，它将自动部署。复制 URL，因为我们将需要它来配置 WhatsApp 频道。

打开 [WhatsApp 沙箱](https://www.twilio.com/console/sms/whatsapp/sandbox)，找到有消息时的字段，粘贴到函数 URL 中。保存频道，准备测试！

## 📱测试应用程序

在你的手机上打开 WhatsApp，向沙盒号码发送一条消息，你会收到一条回复，你的消息会被翻译成表情符号。

或者用表情符号:

> 👐 🆙WhatsApp🔛你的🤳，发送一个💬去沙箱💯你会收到一个回复💬翻译成表情符号。

[![A screen shot of the original and translated message.](../Images/5145ad50f7d7382ba3371a1b66960732.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4IBcSUd8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/07/result.png)

如果您收到一条消息，说您的号码与沙盒频道无关，请确保您按照[的说明将您的号码连接到沙盒](https://www.twilio.com/docs/sms/whatsapp/api#using-phone-numbers-with-whatsapp)。

如果你想在不连接沙盒的情况下试用这个应用程序，[发送你的消息到我们的 WhatsApp 号码+441745472072](https://wa.me/441745472072?text=she%20sells%20sea%20shells%20on%20the%20seashore) 。你也可以用手机扫描这个二维码开始对话。

[![Scan this QR code and start translating your text to emoji over WhatsApp.](../Images/89e7b600b74c731634413b2bbd149856.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4Umk9qJA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/original_images/qr-1.png)

## 👞后续步骤

当你手头有合适的工具时，通过 WhatsApp 翻译表情符号只需要几行代码。在这篇文章中，我们看到了如何使用 Node.js 和 Twilio 函数构建、部署和扩展应用程序，以及由 [`moji-translate`](https://github.com/notwaldorf/emoji-translate) 提供的所有表情符号功能。不过这只是开始，你可以在此基础上创建更多与 Twilio、WhatsApp 和其他通过 Twilio 消息 API 可用的[渠道](https://www.twilio.com/channels)的互动应用。

现在我们有了 WhatsApp 沙盒可以玩了，你还想开发什么应用呢？在中取得联系💬下面评论，📧给我发电子邮件到[philnash@twilio.com](mailto:philnash@twilio.com)或者发送你最喜欢的表情符号给我🐦在 [@philnash](https://twitter.com/philnash) 发推特。

* * *

*[👉表情符号翻译📞Twilio API 用于💬WhatsApp 和 Node.js](https://www.twilio.com/blog/2018/08/emoji-translations-twilio-api-whatsapp-node-js.html) 最初发表于 2018 年 8 月 1 日的 [Twilio 博客](https://www.twilio.com/blog)。*