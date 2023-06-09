# 使用 AWS Lambda 和 AWS SES 构建无服务器联系人表单

> 原文：<https://dev.to/adnanrahic/building-a-serverless-contact-form-with-aws-lambda-and-aws-ses-4jm0>

如果我告诉你它可以零依赖地完成呢？希望你能接受挑战，因为这正是我们要做的。

本教程将涵盖使用普通 JavaScript 的前端联系表单和托管在 [AWS Lambda](https://aws.amazon.com/lambda/) 上的无服务器后端的基础知识。 [AWS SES](https://aws.amazon.com/ses/) 是你用来发送实际电子邮件的服务，相信我，它非常简单，配置只需 13 秒。是的，我给自己计时。😁

那好吧。让我们跳进来吧！

### TL；速度三角形定位法(dead reckoning)

为了确保你对我们今天所做的有一个大概的了解，这里有一个简短的 TL；博士你可以跳到你感兴趣的部分，严重伤害我的感情，或者就从这里继续读下去。你挑吧...我不会默默的评判你。😐

*   我们在建造什么？
*   配置 [AWS SES](https://aws.amazon.com/ses/)
*   用[无服务器框架](https://serverless.com/)构建 API
*   将 API 部署到 [AWS Lambda](https://aws.amazon.com/lambda/)
*   用 [Dashbird](https://dashbird.io/) 测试 API
*   构建表单

***注**:我把这个[代码](https://github.com/adnanrahic/lambda-mailer)变成了一个 [npm 模块](https://www.npmjs.com/package/lambda-mailer)以便于重用，所以当你需要一个快速联系表单时，你不需要自己写所有的代码。*

## 我们在建造什么？

总体思路是建立一个可以添加到静态网站的联系表单。我们希望在不管理任何服务器的情况下添加它，并且最好不要为它的运行支付任何费用。这里有一个 AWS Lambda 的惊人用例。

我们想要构建的结构非常简单。我们有一个简单的表单，用一小段 JavaScript 来解析 JSON 的参数，并将它们发送给 API 端点。

端点是一个 [AWS API Gateway](https://aws.amazon.com/api-gateway/) 事件，它将触发一个 AWS Lambda 函数。该功能将告诉 AWS SES 向您的电子邮件地址发送一封包含相关内容的电子邮件。从那里，你可以继续与填写表格的人交换电子邮件。简单吧？

[![overview](img/ee9c0443812358667a24d86c29f12f2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gKuuon3W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adnanrahic/cdn/master/lambda-mailer/lambda-mailer.png)

开始黑吧！

## 配置 AWS SES

为了使用 AWS 提供的简单电子邮件服务发送电子邮件，您需要验证将用于发送电子邮件的电子邮件地址。就像导航到 AWS 控制台并搜索**简单电子邮件服务**一样简单。

[![ses-1](img/3b3305c4f5ebcc6602c6b113685df8a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zohP5aXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adnanrahic/cdn/master/building-a-serverless-contact-form/ses-2.png)

点击左侧导航栏上的**电子邮件地址**链接。你会看到一个名为**的蓝色大按钮，用于验证新的电子邮件地址**。按下它并添加您的电子邮件地址。

[![ses-2](img/08cb089d026f72720f78ddb9b596498f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bnKRIC3q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adnanrahic/cdn/master/building-a-serverless-contact-form/ses-1.png)

AWS 现在将向您发送一封验证电子邮件到该地址。去验证一下吧。差不多就是这样。准备好写一些代码了吗？

## 用无服务器框架构建 API

构建实际的 API 有几个主要步骤。第一件事，一如既往，是配置。

#### 1。安装无服务器框架

为了让无服务器开发**不**成为绝对的折磨，继续安装[无服务器框架](https://serverless.com/)。

```
$ npm i -g serverless 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**如果你使用的是 Linux，你可能需要以 sudo 的身份运行这个命令。*

一旦全局安装到您的机器上，您就可以在终端的任何地方使用这些命令。但是为了让它与您的 AWS 帐户通信，您需要配置一个 IAM 用户。跳过[这里的解释](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)，然后返回，用提供的键运行下面的命令。

```
$ serverless config credentials \
    --provider aws \
    --key xxxxxxxxxxxxxx \
    --secret xxxxxxxxxxxxxx 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您运行任何终端命令时，您的无服务器安装知道要连接到哪个帐户。让我们来看看它是如何工作的。

#### 2。创建服务

创建一个新目录来存放您的无服务器应用程序服务。在那里启动一个终端。现在您已经准备好创建一个新的服务了。

你问的服务是什么？把它当成一个项目。但也不尽然。在这里定义 AWS Lambda 函数、触发它们的事件以及它们需要的任何 AWS 基础设施资源，所有这些都在一个名为 **serverless.yml** 的文件中。

回到你的终端类型:

```
$ serverless create --template aws-nodejs --path contact-form-api 
```

Enter fullscreen mode Exit fullscreen mode

create 命令将创建一个新的**服务**。修卡！但有趣的部分来了。我们需要为函数选择一个运行时。这被称为**模板**。传入`aws-nodejs`会将运行时设置为 Node.js .这正是我们想要的。路径将为服务创建一个文件夹。

#### 3。使用代码编辑器浏览服务目录

用你最喜欢的代码编辑器打开 **contact-form-api** 文件夹。这里应该有三个文件，但是现在，我们只关注**的 serverless.yml** 。它包含此服务的所有配置设置。您可以在这里指定常规配置设置和每个功能的设置。你的 **serverless.yml** 将充满样板代码和注释。请随意删除它，并粘贴这个。

```
# serverless.yml

service: contact-form-api

custom:
  secrets: ${file(secrets.json)}

provider:
  name: aws
  runtime: nodejs8.10
  stage: ${self:custom.secrets.NODE_ENV}
  region: us-east-1
  environment: 
    NODE_ENV: ${self:custom.secrets.NODE_ENV}
    EMAIL: ${self:custom.secrets.EMAIL}
    DOMAIN: ${self:custom.secrets.DOMAIN}
  iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "ses:SendEmail"
      Resource: "*"

functions:
  send:
    handler: handler.send
    events:
      - http:
          path: email/send
          method: post
          cors: true 
```

Enter fullscreen mode Exit fullscreen mode

属性列出了服务中的所有函数。不过，我们只需要一个功能来处理电子邮件的发送。**处理程序**引用它是哪个函数。

看一看`iamRoleStatements`，他们指定 Lambda 有权限触发**简单邮件服务**。

我们在顶部还有一个`custom`部分。这是一种将环境变量安全加载到服务中的方法。稍后通过使用`${self:custom.secrets.<environment_var>}`来引用它们，实际值保存在一个名为`secrets.json`的简单文件中。

厉害！

#### 4。添加机密文件

我们都知道把私钥推给 GitHub 会害死小狗。请不要这样做。用无服务器框架处理这个问题很简单。添加一个`secrets.json`文件并将这些值粘贴到。

```
{  "NODE_ENV":"dev",  "EMAIL":"john.doe@mail.com",  "DOMAIN":"*"  } 
```

Enter fullscreen mode Exit fullscreen mode

在测试时，您可以将域保持为`'*'`，但是，请确保在生产中将其更改为您的实际域。`EMAIL`字段应该包含您通过 AWS SES 验证的电子邮件。

#### 5。编写业务逻辑

至此，让我们来编写实际的代码。总而言之，代码本身相当简单。我们需要 [SES](https://aws.amazon.com/ses/) 模块，创建电子邮件参数并用`.sendMail()`方法发送它们。在底部，我们导出函数，确保它在`serverless.yml`中可用。

```
// handler.js

const aws = require('aws-sdk')
const ses = new aws.SES()
const myEmail = process.env.EMAIL
const myDomain = process.env.DOMAIN

function generateResponse (code, payload) {
  return {
    statusCode: code,
    headers: {
      'Access-Control-Allow-Origin': myDomain,
      'Access-Control-Allow-Headers': 'x-requested-with',
      'Access-Control-Allow-Credentials': true
    },
    body: JSON.stringify(payload)
  }
}

function generateError (code, err) {
  console.log(err)
  return {
    statusCode: code,
    headers: {
      'Access-Control-Allow-Origin': myDomain,
      'Access-Control-Allow-Headers': 'x-requested-with',
      'Access-Control-Allow-Credentials': true
    },
    body: JSON.stringify(err.message)
  }
}

function generateEmailParams (body) {
  const { email, name, content } = JSON.parse(body)
  console.log(email, name, content)
  if (!(email && name && content)) {
    throw new Error('Missing parameters! Make sure to add parameters \'email\', \'name\', \'content\'.')
  }

  return {
    Source: myEmail,
    Destination: { ToAddresses: [myEmail] },
    ReplyToAddresses: [email],
    Message: {
      Body: {
        Text: {
          Charset: 'UTF-8',
          Data: `Message sent from email ${email} by ${name} \nContent: ${content}`
        }
      },
      Subject: {
        Charset: 'UTF-8',
        Data: `You received a message from ${myDomain}!`
      }
    }
  }
}

module.exports.send = async (event) => {
  try {
    const emailParams = generateEmailParams(event.body)
    const data = await ses.sendEmail(emailParams).promise()
    return generateResponse(200, data)
  } catch (err) {
    return generateError(500, err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，全部 60 行左右的代码，绝对没有依赖。太棒了。

## 将 API 部署到 AWS Lambda

简单的部分来了。部署 API 就像运行一个命令一样简单。

```
$ serverless deploy 
```

Enter fullscreen mode Exit fullscreen mode

[![deploy](img/79071764555a5fa81510903521642b5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ApCvDzmq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adnanrahic/cdn/master/building-a-serverless-contact-form/deploy.png)

您可以看到端点被记录到控制台。这是您发送请求的地方。

## 用 Dashbird 测试 API

测试 API 最简单的方法是使用 CURL。让我们创建一个简单的 CURL 命令，并向端点发送一个 JSON 有效负载。

```
$ curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"email":"john.doe@email.com","name":"John Doe","content":"Hey!"}' \
  https://{id}.execute-api.{region}.amazonaws.com/{stage}/email/send 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，你会很快收到一封电子邮件。如果不是，那么你就不走运了。在这种情况下，我默认使用 [Dashbird](https://dashbird.io/register/) 来调试正在发生的事情。

[![dashbird-lambda-mailer-2](img/67a13a1188639abd66b5bb7bbcd1128c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--218ktGCL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/adnanrahic/cdn/master/building-a-serverless-contact-form/dashbird.lambda-mailer-2.gif)

我这边的日志显示都是绿色的，所以它工作得很好！这就是完成的 API 部分。让我们继续联系表单本身。

## 构建联系人表单

因为我不是世界上最好的 CSS 专家，我将完全跳过这一部分，并向您展示如何让它工作。😁

让我们从 HTML 标记开始。

```
<form id="contactForm">
  <label>Name</label>
  <input type="text" placeholder="Name" name="name" required>
  <label>Email Address</label>
  <input type="email" placeholder="Email Address" name="email" required>
  <label>Message</label>
  <textarea rows="5" placeholder="Message" name="content" required></textarea>
  <div id="toast"></div>
  <button type="submit" id="submit">Send</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的表单，有三个字段和一个按钮。让我们继续看 JavaScript。

```
const form = document.getElementById('contactForm')
const url = 'https://{id}.execute-api.{region}.amazonaws.com/{stage}/email/send'
const toast = document.getElementById('toast')
const submit = document.getElementById('submit')

function post(url, body, callback) {
  var req = new XMLHttpRequest();
  req.open("POST", url, true);
  req.setRequestHeader("Content-Type", "application/json");
  req.addEventListener("load", function () {
    if (req.status < 400) {
      callback(null, JSON.parse(req.responseText));
    } else {
      callback(new Error("Request failed: " + req.statusText));
    }
  });
  req.send(JSON.stringify(body));
}
function success () {
  toast.innerHTML = 'Thanks for sending me a message! I\'ll get in touch with you ASAP. :)'
  submit.disabled = false
  submit.blur()
  form.name.focus()
  form.name.value = ''
  form.email.value = ''
  form.content.value = ''
}
function error (err) {
  toast.innerHTML = 'There was an error with sending your message, hold up until I fix it. Thanks for waiting.'
  submit.disabled = false
  console.log(err)
}

form.addEventListener('submit', function (e) {
  e.preventDefault()
  toast.innerHTML = 'Sending'
  submit.disabled = true

  const payload = {
    name: form.name.value,
    email: form.email.value,
    content: form.content.value
  }
  post(url, payload, function (err, res) {
    if (err) { return error(err) }
    success()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

再过 50 行，客户端逻辑就完成了。请随意将它放到您的网站中，将`url`常量更改为您在上面部署的 API 端点。嘿，很快，你的无服务器联系表单就完成了，可以投入生产了！

## 包装完毕

现在你有了，一个快速简单的方法来添加一个无服务器的联系表单到一个网站。像这样对奇怪的、孤立的端点使用无服务器是很棒的。绝对没有你需要担心的服务器。只需部署代码，并保证它会工作。如果有什么东西坏了，你会有仪表板鸟在背后看着你，如果有什么不对劲，它会提醒你。该死，我喜欢松散的集成。

总之，我花了时间从上面的代码中创建了一个 [npm 模块](https://www.npmjs.com/package/lambda-mailer)，所以在未来，没有人需要写两次。只需安装软件包，不到一分钟，您的 contact form endpoint 就会启动并运行。如果你想看的话，可以在 [GitHub repo](https://github.com/adnanrahic/lambda-mailer) 中找到说明。如果你想让更多的人在 GitHub 上看到它，给它一颗星吧。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [阿德纳尼奇](https://github.com/adnanrahic) / [拉姆达-梅勒](https://github.com/adnanrahic/lambda-mailer)

### 一个简单的模块，用于从您网站上的联系人表单接收电子邮件。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![dependencies](img/b7c022726835b739c01c3896b4e6bfdf.png)](https://camo.githubusercontent.com/617833e613d2a20bec6f337f4a56041850d81547161c74d7e8d1f0f08b4cf045/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f646570656e64656e636965732d302d627269676874677265656e2e737667)[![contributors](img/a325f8e3f1e4abad52683d0aaf0ac1fc.png)](https://camo.githubusercontent.com/a5bcf36669e36d98e99fbe2188c2969664b0c05443dc5aa94b2c4893b9fdf242/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f636f6e7472696275746f72732d312d626c75652e737667)[![License: MIT](img/1ebeb1b87ced0f5afda04a6122ee23ad.png)](https://opensource.org/licenses/MIT)[![license](img/955f3528819eda8770cca65909bdd341.png)T11】](https://camo.githubusercontent.com/d156644f8995e4482e77bef1d9461f742fbe53edbcd49f2b7cea3492db5ed771/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f65736c696e742d7374616e646172642d79656c6c6f77677265656e2e737667)

[![cover](img/ee9c0443812358667a24d86c29f12f2f.png)T2】](https://raw.githubusercontent.com/adnanrahic/cdn/master/lambda-mailer/lambda-mailer.png)

# 拉姆达·梅勒

一个简单的模块，用于从您网站上的联系人表单接收电子邮件。

# 注意！

**模块需要 Node.js 版本 8 或以上。**

## 使用

配置相当简单。

#### 1.在 AWS 控制台->简单电子邮件服务中启用您的电子邮件地址

#### 2.安装模块

```
$ npm i lambda-mailer
```

Enter fullscreen mode Exit fullscreen mode

#### 3.`require()`它在你的`handler.js`

```
// define the options for your email and domain
const options = {
  myEmail: process.env.EMAIL, // myEmail is the email address you enabled in AWS SES in the AWS Console
  myDomain: process.env.DOMAIN // add the domain of your website or '*' if you want to accept requests from any domain
}
// initialize the function
const { sendJSON, sendFormEncoded } = require('lambda-mailer')(options)

// Content-Type: application/json
// The event.body needs to be a JSON object with 3 properties
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/adnanrahic/lambda-mailer)

如果你想阅读我以前的一些无服务器思考，请前往[我的简介](https://dev.to/adnanrahic)或[加入我的时事通讯！](https://upscri.be/b6f3d5/)

或者，马上看看我的几篇文章:

*   【Express 和 MongoDB 的无服务器 API 速成班
*   [使用无服务器和 MongoDB 解决不可见的扩展问题](https://dev.to/adnanrahic/solving-invisible-scaling-issues-with-serverless-and-mongodb-4m55)
*   [如何使用无服务器将 Node.js 应用程序部署到 AWS Lambda](https://dev.to/adnanrahic/how-to-deploy-a-nodejs-application-to-aws-lambda-using-serverless-2nc7)
*   [AWS Lambda 和 Node.js 入门](https://dev.to/adnanrahic/getting-started-with-aws-lambda-and-nodejs-1kcf)
*   [用 JSON web 令牌保护无服务器 API 的速成班](https://dev.to/adnanrahic/a-crash-course-on-securing-serverless-apis-with-json-web-tokens-22fa)
*   [将 Node.js REST API 迁移到无服务器](https://dev.to/adnanrahic/migrating-your-nodejs-rest-api-to-serverless-3gej)
*   [用 Node.js 和 MongoDB 构建无服务器 REST API](https://dev.to/adnanrahic/building-a-serverless-rest-api-with-nodejs-and-mongodb-43db)
*   [node . js 无服务器速成班](https://dev.to/adnanrahic/a-crash-course-on-serverless-with-nodejs-5jp)

希望你们喜欢读这篇文章，就像我喜欢写这篇文章一样。如果你喜欢，拍一下那个小心脏，这样 dev.to 上的更多人会看到这个教程。下次见，保持好奇，玩得开心。

* * *

[![This months sponsor is Zeet.](img/a4da9440f5519d3156594813d3a02017.png)T2】](https://bit.ly/adnan-zeet)

免责声明: [Zeet](https://bit.ly/adnan-zeet) 将在下个月赞助这篇博客。前几天我试过了。这就像无服务器，但运行整个后端。你可以自动托管和扩展应用。相当整洁。

* * *