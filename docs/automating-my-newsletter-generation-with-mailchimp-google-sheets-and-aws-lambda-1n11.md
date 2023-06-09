# 使用 MailChimp、Google Sheets 和 AWS Lambda 自动生成时事通讯

> 原文：<https://dev.to/kylegalbraith/automating-my-newsletter-generation-with-mailchimp-google-sheets-and-aws-lambda-1n11>

我不断地建立和启动新的项目。当开始一个新项目时，我评估的许多事情之一是我将如何维护它，以便它不会变得陈旧并继续提供价值。为了让人们从我的项目中获得价值，他们必须参与到我正在构建的项目中。因此，我**也必须**订婚。

任何企业家、独立黑客或业余项目爱好者都会告诉你，推出产品/项目只是第一步。启动后你所做的决定了你的成功。这包括市场营销、销售、写作和继续发展项目。有些人认为这是“永远发射”。

我最近的项目，[由云内容、区块链基础知识和通用编码文章组成的时事通讯](http://www.kylegalbraith.com/learn-by-doing/)，是一个周末产生的想法。我经常阅读关于我感兴趣的东西的最新文章，比如 AWS、区块链和编码。所以很自然地，我想为什么不与那些有相似兴趣的人分享我感兴趣的东西。

我用一个 Google 电子表格来跟踪内容，用 MailChimp 创建了一个注册表单，用 Tailwind CSS 构建了一个快速登陆页面。当这份时事通讯最初发行时，有 500 人注册。

为了证明我对时事通讯的想法，我几乎手动做了所有的事情。我添加了电子表格的链接，并用我自己的双手生成的电子邮件创建了新的 MailChimp 活动。我把 MailChimp 活动的内容复制到我的登陆页面，以保持那里的新鲜。

### 开始手动，然后疯狂地自动化

在发布“边做边学”时事通讯后不久，我学到了非常宝贵的一课。

> 手动完成平凡的任务是失去兴趣的最快方式。

这并不是说你不应该从那里开始，你绝对应该**在第一次开始的时候**做一些不可扩展的事情。但是，一旦这个想法被证明是可行的，自动化所有不专注于向你的观众传递价值的事情。

对我来说，我非常喜欢为时事通讯撰写和管理内容。幸运的是，这也是人们订阅我的时事通讯的原因。他们喜欢精选的内容和我对一周内发现的东西的想法，我也喜欢分享这些内容。但是，注册订阅我的时事通讯的人并不关心它是如何创建的，也不关心一周又一周地汇编它的任务有多平凡。

因此，创建 MailChimp 活动、配置电子邮件模板以及将 HTML 复制到我的登录页面并不能提供价值。它们是必要的，但它们只是产生一个高质量项目的幕后魔术。

作为一个人，他们做起来也是非常平凡的。

因此，我使用 AWS Lambda、Google Sheets API 和 MailChimp API 来自动化这些平凡的任务。解放我，让我专注于提供价值的东西，为我的观众策划高质量的内容。

### 将所有的事情自动化

在我们深入实际代码之前，有一些先决条件。所以，如果你想继续下去，你需要以下的东西。

*   一个 AWS 帐户是必须的，但是你也可以注册一个其他的提供类似 AWS Lambda 的服务的提供商。
*   MailChimp API 密钥，您可以通过遵循[文档](https://mailchimp.com/help/about-api-keys/)来生成一个。
*   要安排与特定电子邮件列表相关的 MailChimp 活动，您需要该列表的唯一 id。这个 Mailchimp 文档告诉你在哪里可以找到它。
*   我们将使用[谷歌电子表格](https://www.npmjs.com/package/google-spreadsheet) NPM 包来访问我们的谷歌内容表。这需要我们使用一个服务帐户向谷歌认证。点击进入 [NPM 包](https://www.npmjs.com/package/google-spreadsheet)，向下滚动至“认证”，然后完成“服务账户方法”中的步骤。

好的，所有的先决条件都搞定了吗？让我们深入一些代码。如果你愿意跟随我的开源代码，你可以从我的 GitHub 中获取这个库。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ kylegalbraith ](https://github.com/kylegalbraith) / [快讯-自动化](https://github.com/kylegalbraith/newsletter-automation)

### 一个 AWS Lambda 函数，在 CRON 时间表上运行，从 Google 工作表中获取内容，并生成一个新的 MailChimp 活动。在这里阅读详细的博文-> https://dev . to/Kyle Galbraith/automating-my-newsletter-generation-with-mailchimp-Google-sheets-and-AWS-lambda-1n 11

<article class="markdown-body entry-content container-lg" itemprop="text">

# 时事通讯-自动化

一个 AWS Lambda 函数，在 CRON 时间表上运行，从 Google 工作表中获取内容，并生成一个新的 MailChimp 活动。

## 入门指南

1.  您必须创建一个`g-auth.json`文件，并将其添加到这个项目的 src 中，紧挨着`serverless.yml`。关于如何为 Google Sheets 创建认证文件的说明可以在[这里](https://www.npmjs.com/package/google-spreadsheet)找到。
2.  用适当的环境变量更新`serverless.yml`。

```
environment:
  googleSheetId: <your-google-sheet-id>
  mailChimpKey: <your-mailchimp-api-key>
  mailchimpListId: <your-mailchimp-unique-list-id>
```

Enter fullscreen mode Exit fullscreen mode

3.  从命令行运行`npm install`。
4.  从命令行运行`serverless deploy`。

</article>

[View on GitHub](https://github.com/kylegalbraith/newsletter-automation)

### 利用无服务器框架

在最近的帖子中，我谈了很多关于利用基础设施即代码(IAC)来表示我们的 AWS 资源。这种做法有很多好处。我个人最喜欢的是**不用点击按钮**。没有什么比多次手工操作更快的了。在 IAC 模板中表示这些按钮的点击，并且不再手动提供 AWS 资源。

最近我一直在谈论很多关于 Terraform 的事情，这绝对是我最喜欢的话题之一。但哈希公司并不是云供应领域的唯一参与者。

我借此机会学习了一个新的框架，即[无服务器](https://serverless.com/)，同时为我的日常任务问题构建了一个解决方案。这篇文章绝不是对框架的深入探究，所以如果你想了解更多的细节，[可以看看他们精美的文档](https://serverless.com/framework/docs/)。

要安装无服务器，我们可以从命令行运行单个全局 NPM 安装，并通过进行版本检查来确认它的工作。

```
kylegalbraith:$ npm install -g serverless
...
...
kylegalbraith:$ serverless version
1.27.3 
```

Enter fullscreen mode Exit fullscreen mode

随着无服务器框架在全球范围内的安装，我们现在可以为 Lambda 函数提供基础设施，以便与 Google Sheets 和 MailChimp 进行交互。这是通过来自 [GitHub repo](https://github.com/kylegalbraith/newsletter-automation/blob/master/src/sheets-automation/serverless.yml) 的`serverless.yml`文件完成的。

```
service: sheets-automation
provider:
  name: aws
  runtime: nodejs8.10
  region: us-west-2    

functions:
  generate-next-newsletter:
    handler: handler.generateNewsletter
    events:
      - schedule: cron(0 19 ? 1-10 THU *)
    environment:
      googleSheetId: <your-google-sheet-id>
      mailChimpKey: <your-mailchimp-api-key>
      mailchimpListId: <your-mailchimp-unique-list-id> 
```

Enter fullscreen mode Exit fullscreen mode

这个无服务器的 YAML 正在定义一个函数`generate-next-newsletter`，它有一个在`handler.js`文件中指定的处理程序，这个处理程序有一个名为`generateNewsletter`的函数。

触发这个函数的事件将是一个 CloudWatch 规则，它在 CRON 时间表上运行，这个时间表在这里被定义为`cron(0 19 ? 1-10 THU *)`，或者更广为人知的是太平洋时间每周四中午 12:00。

最后，为该函数定义了三个环境变量，`googleSheetId`、`mailChimpKey`和`mailChimpListId`。这些代表我们的时事通讯 Google Sheet 的唯一 id(在查看该表时可以在 url 中找到)、我们的秘密 MailChimp API 密钥和我们的唯一 MailChimp 列表 id。

现在，保存了无服务器模板，当我们在目录中时，我们可以运行以下命令来部署我们的基础架构。

```
kylegalbraith:$ npm install kylegalbraith:$ serverless deploy 
```

Enter fullscreen mode Exit fullscreen mode

### 转动轮子的代码

此时，基础架构已被调配。我们有一个 Lambda 函数，其中定义了由 CloudWatch 规则触发的环境变量。现在，我们需要配置 Lambda 函数，以便让我们的生活变得更加轻松。

要做到这一点，我们可以看一下作为 Lambda 函数入口点的 [handler.js](https://github.com/kylegalbraith/newsletter-automation/blob/master/src/sheets-automation/handler.js) 。

```
'use strict';
var GoogleSpreadsheet = require('google-spreadsheet');
var Mailchimp = require('mailchimp-api-v3');

var SheetRepository = require('./repository/sheetRepository');
var MailchimpEmailTemplate = require('./templates/mailchimpEmailTemplate');
var NewsletterGenerator = require('./generators/newsletterGenerator');
var MailchimpDelivery = require('./delivery/mailchimpDelivery');

module.exports.generateNewsletter = (event, context, callback) => {
  let sheetId = process.env.googleSheetId;
  let mailchimpApiKey = process.env.mailChimpKey
  let mailchimpList = process.env.mailchimpListId;

  var sheetRepo = new SheetRepository(new GoogleSpreadsheet(sheetId));
  var newsletterGenerator = new NewsletterGenerator();

  sheetRepo.loadLatestSheet(function (data) {
    var mailChimpMarkup = newsletterGenerator.generateMarkup(
      new MailchimpEmailTemplate(), 
      data.results,
      data.volume,
      data.para1,
      data.para2, 
      data.para3
    );

    createNewMailchimpTemplate(
      mailchimpApiKey, 
      mailChimpMarkup, 
      mailchimpList, 
      data.volume,
      data.subject
    ).then((response) => {
        callback(null, response);
    }).catch((err) => {
        callback(err);
    });

  });
};

function createNewMailchimpTemplate(mailChimpKey, markup, listId, volume, subjectText) {
  var mailChimpClient = new MailchimpDelivery(new Mailchimp(mailChimpKey));
  return mailChimpClient.saveTemplate(`Learn By Doing Volume ${volume}`, markup)
    .then((data) => {
      return mailChimpClient.createRegularCampaign(
        listId, 
        `Weekly Learn AWS Newsletter [Vol ${volume}]`,
        `[Learn By Doing] Volume #${volume}: ${subjectText}`,
        data.id);
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

从顶部来看，我们看到我们正在利用`google-spreadsheet`和`mailchimp-api-v3` NPM 软件包来与我们的 Google Sheet 和 MailChimp 活动进行互动。

然后我们定义了四个类。深入探究这些问题超出了这篇博文的范围，但是这里有一个在我们的工作流程中每个问题的概要。

*   是负责与我们的 Google Sheet 交互的类。如果你看一下[源代码](https://github.com/kylegalbraith/newsletter-automation/blob/master/src/sheets-automation/repository/sheetRepository.js)，你会看到函数`loadLatestSheet()`正在为我们的时事通讯加载最新电子表格中的行。**重要提示:存储库正在调用 google 电子表格包中的`useServiceAccountAuth`。该身份验证使用一个`g-auth.json`文件，您可以在按照前面的说明提供服务帐户时下载该文件。不要将此文件签入源代码管理，因为它包含敏感的键。**
*   `MailChimpEmailTemplate`是包含我们 MailChimp 活动的 HTML 模板的类([源代码](https://github.com/kylegalbraith/newsletter-automation/blob/master/src/sheets-automation/templates/mailchimpEmailTemplate.js))。这个模板包含像`#replaceCloud#`这样的特殊标签，这些标签在生成器中用于将内容从 Google Sheet 馈送到 HTML 模板中。
*   `NewsletterGenerator`负责实际获取我们的谷歌表单中的数据，并将其输入到我们的 HTML 模板中([源代码](https://github.com/kylegalbraith/newsletter-automation/blob/master/src/sheets-automation/generators/newsletterGenerator.js))。关键函数是`generateMarkup`,它获取 HTML 模板，我们来自 Google Sheet 的内容，对于我的用例来说，一些额外的参数，如时事通讯的音量和段落内容，输入到模板中。
*   是 MailChimp API 的抽象，包含了保存新邮件模板和创建常规活动所需的方法。([源代码](https://github.com/kylegalbraith/newsletter-automation/blob/master/src/sheets-automation/delivery/mailchimpDelivery.js))。

这就是我们自动化时事通讯生成的抽象。我们从 Google 工作表中加载新闻简报每个部分的数据，布局如下。

[![Learn By Doing Newsletter Google Sheet](img/b3a040a4da307e463b2e3b04a236c78c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TqNoAon4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bfjz978bire7ujhmszr9.PNG)

如果我们深入到`SheetRepository.loadLatestSheet()`中，我们会看到它正在解析这个 Google 工作表中的行，并将它们作为一个对象数组返回。

现在，让我们深入了解 Lambda 函数中的一切是如何连接在一起的，以使这一切发生。

```
module.exports.generateNewsletter = (event, context, callback) => {
  let sheetId = process.env.googleSheetId;
  let mailchimpApiKey = process.env.mailChimpKey
  let mailchimpList = process.env.mailchimpListId;

  var sheetRepo = new SheetRepository(new GoogleSpreadsheet(sheetId));
  var newsletterGenerator = new NewsletterGenerator();

  sheetRepo.loadLatestSheet(function (data) {
    var mailChimpMarkup = newsletterGenerator.generateMarkup(
      new MailchimpEmailTemplate(), 
      data.results,
      data.volume,
      data.para1,
      data.para2, 
      data.para3
    );

    createNewMailchimpTemplate(
      mailchimpApiKey, 
      mailChimpMarkup, 
      mailchimpList, 
      data.volume,
      data.subject
    ).then((response) => {
        callback(null, response);
    }).catch((err) => {
        callback(err);
    });

  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

记得在`serverless.yml`文件中，我们声明了我们定义为`handler.generateNewsletter`的 Lambda 函数的处理程序，这是我们在这里看到的`module.exports.generateNewsletter`的入口点。

每个 Lambda 函数调用都包含一个`event`、`context`和`callback`参数。我们在这里没有使用事件参数，但是这个参数包含了触发我们函数的事件信息。context 参数包含关于函数当前调用的信息。我们在这里看到的回调参数用于指示调用是成功的`callback(null, response)`还是失败的`callback(err)`。

深入到`generateNewsletter`函数，我们从获取无服务器模板中定义的环境变量开始。然后使用`sheetId`创建一个新的`GoogleSpreadsheet`客户端，并将其传递给`SheetRepository`来访问内容表。

然后我们调用`sheetRepo.loadLatestSheet()`并给它一个回调函数来发送数据。使用回调是因为`google-spreadsheet`包目前不支持承诺。正如我们看到的，从工作表存储库返回的数据是以下格式:

```
{
    volume: '<current-newsletter-volume>',
    para1: '<first-paragraph-content-for-newsletter>',
    para2: '<second-paragraph-content-for-newsletter>',
    para3: '<third-paragraph-content-for-newsletter>',
    results: [
        volume: '<current-newsletter-volume>',
        category: '<category-of-newsletter-articles>',
        articles: [
            title: '<newsletter-article-title>',
            link: '<newsletter-article-link>',
            text: '<newsletter-article-text>'
        ]
    ]  
} 
```

Enter fullscreen mode Exit fullscreen mode

从 Google Sheet 加载的数据然后被提供给`NewsletterGenerator.generateMarkup()`函数。它采用包含各种新闻通讯类别及其附带文章的`results`数组。此外，它还获取了这一期时事通讯的卷号和我所写的介绍文本的段落。

该函数返回的是一个字符串，其中包含创建新电子邮件模板所需的所有 MailChimp 标记。一旦标记完成，就会调用助手函数`createNewMailchimpTemplate`。

```
function createNewMailchimpTemplate(mailChimpKey, markup, listId, volume, subjectText) {
  var mailChimpClient = new MailchimpDelivery(new Mailchimp(mailChimpKey));
  return mailChimpClient.saveTemplate(`Learn By Doing Volume ${volume}`, markup)
    .then((data) => {
      return mailChimpClient.createRegularCampaign(
        listId, 
        `Weekly Learn AWS Newsletter [Vol ${volume}]`,
        `[Learn By Doing] Volume #${volume}: ${subjectText}`,
        data.id);
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数调用`MailchimpDelivery.saveTemplate()`来保存一个新的电子邮件模板，以便以后在活动中使用。一旦保存了模板，就可以创建一个常规的 Mailchimp 活动，这就是我们在这里看到的对`createRegularCampaign()`的调用。

将为传入的电子邮件列表 id 创建一个常规活动。该活动的电子邮件主题为`[Learn By Doing] Volume #${volume}: ${subjectText}`，并且将使用刚刚创建的电子邮件模板。当活动成功创建时，我们通过调用回调函数`callback(null, response)`在主处理程序中返回成功信号。

### 包装完毕

这是一篇比我通常写的要长得多的博文，但是我相信这篇博文真正强调了像 AWS Lambda 这样的东西的一个关键概念。这是最优雅的解决问题的方法吗，一点也不。我能写出更好的代码吗？绝对的。但这两者都不是重点。

关键是云提供商给开发者和企业带来了难以置信的灵活性，并能显著提高效率。它们允许你快速迭代和开发新的想法。像 AWS Lambda 这样的服务允许你编写这样的代码，自动完成一项任务，这项任务每周需要花费 4-6 个小时，甚至完全不需要。

像 AWS 这样的工具提升了我的敏捷度。Lambda 不再每周花 4-6 个小时点击按钮，而是在不需要我干预的情况下运行，一切都去了它需要去的地方。我可以专注于带来价值的事情，为我的订户管理高质量的内容。

我知道这里的用例很简单，但是我可以向你保证背景概念适用于成百上千的用例。通过花几个小时写一两个 Lambda，将不重要但必要的事情自动化，开始腾出时间专注于对你和你的用户重要的事情。

### 通过实际使用来学习 AWS

如果你喜欢这篇文章，并且渴望开始学习更多关于 Amazon Web Services 的知识，我已经创建了一个关于如何在 AWS 上托管、保护和交付静态网站的新课程！这是一个书籍和视频课程，从信息的海洋中切入，加速你对 AWS 的学习。给你一个框架，让你通过实际使用来学习复杂的东西。

在课程中，我深入研究了 Lambda、S3、CloudFront 和 API Gateway 等核心 AWS 服务，以便为您提供以可扩展的方式开始学习该平台所需的工具。

[前往登录页面获取一份副本，开始您的 AWS 认证之旅！](https://www.kylegalbraith.com/learn-aws/)