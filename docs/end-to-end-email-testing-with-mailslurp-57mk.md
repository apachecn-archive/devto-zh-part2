# 使用 MailSlurp 进行端到端电子邮件测试

> 原文：<https://dev.to/sparkpost/end-to-end-email-testing-with-mailslurp-57mk>

### 如何使用 MailSlurp 进行端到端测试

如果您从应用程序发送电子邮件，您可能需要进行测试来确保您的代码正在尝试发送该电子邮件。但是你有没有一个正确的方法来测试那封邮件是否被发送了呢？MailSlurp 是一款很棒的*免费*工具，可以帮你做到这一点。它允许你轻松地生成随机的收件箱来发送电子邮件，然后通过它的 API 确认发送。

在本文中，我们将使用 MailSlurp [Javascript SDK](https://www.npmjs.com/package/mailslurp-client) 为发送电子邮件的节点函数编写一个端到端测试。我们将使用 [SparkPost](https://sparkpost.com) 发送电子邮件。如果你还没有发送电子邮件，这里有一个[的好地方(免费)](https://www.sparkpost.com/docs/getting-started/getting-started-sparkpost/)。对于我们的测试，我们将使用 [Jest](https://facebook.github.io/jest/) ，但是任何框架都应该工作得很好。

我们的代码将使用一些 es2015 功能，因此请确保运行支持这些功能的 node 版本(版本 [6.4.0](http://node.green/) 或更高版本应该没问题)。我们将使用 [npm](https://www.npmjs.com/) 版本 5.6.0 安装依赖项。

这个[演示](https://github.com/jgzamora/mailslurp-sparkpost)的所有代码都可以在这里找到。

### 开始设置

好吧，让我们开始吧！第一步是建立一个 MailSlurp 账户。又快又免费:[注册【MailSlurp。接下来，登录到您的仪表板并获取您的 API 密钥。](https://www.mailslurp.com/sign-up)

[![](img/010d2541b91ffbd63443cfc12ae5c883.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mVBg44H3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dm7goc8dt9qj1laipbvs.png)

一旦您有了 API 密钥，我们就可以开始测试设置了。确保你的包中有这些依赖项。using 这是我们的样子，使用 SparkPost 客户端库:

```
"dependencies":  {  "sparkpost":  "^2.1.2",  "mailslurp-client":  "^0.1.1514850454",  "jest":  "^22.1.2"  } 
```

Enter fullscreen mode Exit fullscreen mode

然后运行`npm install`这里是我们完整的 [package.json](https://github.com/jgzamora/mailslurp-sparkpost/blob/master/package.json) 。通常像 Mailslurp client 和 jest 这样的包应该放在“dev-dependencies”下，但是在这个演示中我们保持了简单。

现在从我们的测试文件开始，我们需要 Mailslurp-client 和我们的`sendEmail`代码:

```
// test file: mailslurp.test.js
const MailslurpClient = require('mailslurp-client');
const sendEmail = require('./sendEmail.js'); // our SparkPost email function 
```

Enter fullscreen mode Exit fullscreen mode

你可以导入你用来发送邮件的任何功能。

紧接着，我们初始化 MailSlurp 客户端。这也是在变量中存储 API 键的好地方。

```
// initialize the MailSlurp Client
const slurp = new MailslurpClient.InboxcontrollerApi()

// Sign up at MailSlurp.com to get one of these for free.
const slurpKey = process.env.MS_APIKEY; 
```

Enter fullscreen mode Exit fullscreen mode

我们使用[环境](https://medium.com/ibm-watson-data-lab/environment-variables-or-keeping-your-secrets-secret-in-a-node-js-app-99019dfff716)变量来访问我们的变量；如果您以不同的方式存储凭证，也没关系。我们的 slurpKey 变量将是我们传递给每个 MailSlurp 客户端方法的第一个参数。

下一步是创建一个收件箱，我们可以用 MailSlurp 客户端的`createRandomInboxUsingPOST`方法:
发送到这个收件箱

```
describe('SendEmail Tests', () => {

  let address;
  let inboxId;

  beforeAll(() => {
    // Creates the inbox and set address & inboxId for use in tests
    return slurp.createRandomInboxUsingPOST(slurpKey)
      .then(({ payload }) => {
        address = payload.address; // the email address we will be sending to
        inboxId = payload.id; // UUID used to identify the inbox
      });
  });

 /* tests will go here */
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里重要的是:

*   在运行测试之前执行这段代码
*   将回复中的地址和 id 存储为变量，这样您就可以发送和访问您的收件箱

现在，对于实际的测试，我们想发送一封电子邮件(`sendEmail()`函数)，然后使用 MailSlurp 客户端的`getEmailsForInboxUsingGET`方法从收件箱中获取电子邮件:

```
test('Email delivers', () => {
    // increase the default jest timeout to wait for delivery
    jest.setTimeout(60000);

    // pass address given to us by MailSlurp
    return sendEmail(address)
      .then(({ total_accepted_recipients }) => {
        // make sure SparkPost accepted the request
        expect(total_accepted_recipients).toEqual(1);

        // Get the list of emails from the inbox
        return slurp.getEmailsForInboxUsingGET(slurpKey, inboxId,
          {
            minCount: 1, // minimum number of emails to wait for, default wait time is 60s
          })
          .then(({ payload }) => {
            // console.log(payload) // uncomment this to see the full email
            expect(payload).toHaveLength(1);
            const [email] = payload;
            expect(email.subject).toEqual('MailSlurp Test Email');
          });
      });
  }); 
```

Enter fullscreen mode Exit fullscreen mode

电子邮件传递可能需要几秒钟以上的时间，所以请确保您的测试等待足够长的时间，以便电子邮件能够被传递。在上面的代码片段中，我们更改了超时阈值来处理这个问题。注意，我们将`address`传递给我们的`sendEmail`函数，然后将`inboxId`传递给`getEmailsForInboxUsingGET`。

对于这个测试，我们首先断言 MailSlurp 返回一个长度为 1 的电子邮件数组(我们只发送了一封电子邮件)。然后，为了确保这是我们发送的电子邮件，我们断言它的主题是在 [sendEmail.js](https://github.com/jgzamora/mailslurp-sparkpost/blob/master/sendEmail.js) 中定义的“MailSlurp Test Email”

就是这样！像往常一样运行测试。我们的是用 package.json 中的 npm 脚本设置的，它用`npm test`运行。

### 下一步

在我们的示例测试中，有足够的空间来放置更多的断言。当结合复杂的 HTML 模板使用 SparkPost 这样的电子邮件发送服务时，邮件正文会变得非常复杂。要断言电子邮件的实际内容，我们建议使用高级电子邮件正文解析器。一旦您能够从正文中提取 HTML/text 内容，您就可以轻松地使用[快照测试](https://facebook.github.io/jest/docs/en/snapshot-testing.html)来确保您发送的是您想要的内容。

如果你对此感兴趣，你应该看看其余的 [MailSlurp](https://www.mailslurp.com/) 特性。如有任何问题，您可以通过[推特](https://twitter.com/SparkPost)联系我们。你还可以在 [SparkPost](https://pages.sparkpost.com/bl-account-signup-generic.html?src=Blog&pc=bl-rfi-generic-2017&utm_source=blog&utm_medium=blog&utm_campaign=all&utm_content=signup) 上每月免费发送 15000 封电子邮件！特别感谢 [Jack Mahoney](https://github.com/jackmahoney) 建立 MailSlurp 并回答我的问题。

–[何塞](https://twitter.com/josegzamora)

*用 mailsurp 进行端到端邮件测试的帖子[最先出现在](https://www.sparkpost.com/blog/email-testing-mailslurp/) [SparkPost](https://www.sparkpost.com) 上。*