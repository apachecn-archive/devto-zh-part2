# 如何使用无服务器功能自动为内容丰富的资产添加水印

> 原文：<https://dev.to/contentful_blog/how-to-automatically-watermark-contentful-assets-using-a-serverless-function-6j3>

[![How to automatically watermark Contentful assets using a serverless function](../Images/3bb3c55977da80fb664456e7bfadd08c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MfvEdouC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/3KcGEFM59K02KG6eUMiewu/660f655eb78e2e0f4a9c05ba2d4096bc/20181108_Watermark_webhooks.png)

我们交了朋友！我们终于达到了开发者可以部署 HTTP 端点而不用担心基础设施的地步。你可能已经猜到了，你是对的——我说的是无服务器的流行词！大型基础设施公司；目前，AWS、微软、IBM 和其他公司都将功能作为服务来提供。无服务器功能使您可以编写 100 行代码，测试并交付——100%可扩展，并且在部署的代码不运行时不会花费您一分钱。

### 为什么一个 HTTP 端点这么重要？

行业正在转向专业化服务，以比我(可能还有你)今天开始构建解决方案时更好、更快的方式解决具体问题。你注册了一项你选择的服务，就可以开始使用它——为你节省几周甚至几个月的时间来开发不属于你核心产品的软件。在你的软件生涯中，你建立过几次登录机制？对我来说，已经很多很多次了...

问题是，当您无法控制未来技术堆栈中所有服务的源代码时，您如何能够自动化某些事情。答案是两个核心组件——API 和 webhooks。

如果您以前没有使用过它们，webhooks 是由服务发送到定义的 HTTP 端点的 HTTP 请求。这些请求允许您将一个服务与另一个连接起来，然后将所有的小功能组合成更大、更好、更强大的东西。

然而，在你购买大量软件即服务(SaaS)产品之前，让我给你一点建议。不是每个提供 API 的云服务都是一样的，也不是每个 webhook 实现都是有用的。您应该检查两个主要特征:

1.  所提供的 API 必须包括读取、创建和更新功能
2.  网络挂钩必须是安全的，可过滤的和可调节的

在本文中，我想描述在 Contentful 中处理图像时，如何实现 webhook 流来提高生产率。让我们假设您正在处理的照片应该作为包含您公司徽标的水印版本提供给您的应用程序。您的内容创建者可以每次在每张图片上手动添加水印，但是您也可以自动完成这项任务以节省时间和精力。

## 主流程——触发无服务器功能的 webhooks

计划是这样的:每当有人在 Contentful 中创建新资产并上传图像时，就会发送一个 webhook。这个 webhook 触发一个下载上传图像的无服务器功能。例如，带有公司徽标的图像将作为带水印的版本重新上传到 Contentful。

{:img }
[![The flow with webhooks and serverless functions for image watermarking](../Images/96ba3e3c3617acf952e08528dd5b31d5.png)T3】](//images.ctfassets.net/fo9twyrwpveg/7KhQyRQNMsmkSGuemeAESa/ffc2a891b9d54d0b51f8d890fe670a26/serverless-flow.jpg)

这样，你既可以保留原始图像，又可以省去自己修改这些图像的工作。听起来不错？我们开始吧！

### 无服务器框架入门

要开始开发运行在云中的功能，[无服务器框架](https://serverless.com/)总是我的首选。它在所有基础设施提供商提供的功能之上，为您提供了一个很好的抽象层。您可以直接从您的机器上开发和部署功能，这比通常由在线编辑器提供的要舒服得多。

作为第一步，前往[他们的入门指南](https://serverless.com/framework/docs/getting-started/)。它解释了如何安装`serverless`可执行文件以及需要配置什么。一切就绪后，您只需要一个命令就可以创建一个可以部署到 AWS 的项目。

```
$ serverless create --template aws-nodejs 
```

上面的命令只为您创建了两个文件，其中`serverless.yml`是您的无服务器项目的主要配置文件。它最初的创建包括大量的文档和注释，但是此时不会在其中定义太多的配置参数和值。

```
service: aws-nodejs # NOTE: update this with your service name

# define environment
provider:
  name: aws
  runtime: nodejs8.10
# define available functions
functions:
  hello:
    handler: handler.hello 
```

`serverless.yml`定义了一个服务名(这是您稍后在 AWS 中识别它的方式)，您可以在 AWS 中配置环境(8.10 版中的 Node.js)，它列出了稍后应该可用的功能。Node.js 第 8 版意味着您可以使用像`async/await`这样的新语言，这使得源代码更具可读性！

另一个文件`handler.js`导出一个准备好部署的函数，返回“hello world”JSON 示例消息。

```
'use strict';

module.exports.hello = async (event, context) => {
  return {
    statusCode: 200,
    body: JSON.stringify({
      message: 'Go Serverless v1.0! Your function executed successfully!',
      input: event,
    }),
  };
}; 
```

### 通过 HTTP 使功能可执行

虽然这两个文件足以将您的第一个函数部署到 AWS，但是这个函数还没有连接到 HTTP 端点，因此不能从外部访问。要做到这一点，您必须将您的第一个定制配置添加到`serverless.yml`中。

在 AWS 中， [API 网关](https://aws.amazon.com/api-gateway/)处理 HTTP 端点的配置。无服务器框架的酷之处在于，它提供了配置端点的方法，而无需在 AWS 控制台的所有不同页面和设置之间跳转。

由基础设施的不同服务触发的事件负责 AWS 中的功能执行。在这种情况下，HTTP 只是其中之一，但它是构建无服务器功能驱动的 API 的基础。

通过将`events`的配置添加到特定功能，无服务器框架为您配置 API 网关，并使该功能在互联网上可用。

```
# … service configuration
# … provider configuration

functions:
  hello:
    handler: handler.hello
    events:
      - http:
          path: /hello
          method: get 
```

您现在可以使用`serverless deploy`部署您的新功能。

```
$ serverless deploy
[15:38:58]
Serverless: Packaging service...
Serverless: Excluding development dependencies...
Serverless: Creating Stack...
Serverless: Checking Stack create progress...
Serverless: Stack create finished...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Uploading service .zip file to S3 (387 B)...
Serverless: Validating template...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
Serverless: Stack update finished...
Service Information
service: hello-world
stage: dev
region: us-east-1
stack: hello-world-dev
api keys:
  None
endpoints:
  GET - https://lbrdiu0h92.execute-api.us-east-1.amazonaws.com/dev/hello
functions:
  hello: hello-world-dev-hello 
```

部署日志为您提供了端点部署位置的信息，以便您可以在浏览器中打开它并立即看到 JSON 响应。

{:img }
[![Change the method name and HTTP method to POST](../Images/468cdbe6728b18706f701e42b88c37f5.png)T3】](//images.ctfassets.net/fo9twyrwpveg/6ozEksTGfeQEmsyCqAWY68/76fe98aa88e169e04ddefea09a35f791/image_1.png)

作为最后的准备步骤，让我们将方法名从`hello`改为`watermark`，并且使用 HTTP 方法`POST`代替`GET`。

```
# … service configuration
# … provider configuration

functions:
  watermark:
    handler: handler.watermark

    events:
      - http:
          path: /watermark
          method: post 
```

这两个文件和几行额外的配置为一个能够处理内容丰富的 webhooks 的端点打下了基础，从而为图像添加水印并重新上传它们。

### 根据您的需求定制 webhook

在 Contentful 中，你可以定义一个新的 webhook，每当你发布一个新的资产时，它就会被发送。

{:img }
[![Define a new webhook to be sent after publishing a new asset](../Images/a693ef4c8a904d62994838a5208282bc.png)T3】](//images.ctfassets.net/fo9twyrwpveg/fFnXAMHeRaSk8ecsyUESC/963f5b6b2547bdc49380957affce42d2/image_2.png)

酷的是你可以在 webhooks 配置中定义定制的有效负载。使用 JSON 指针，您可以减少负载并避免函数的复杂性，因为它只发送您感兴趣的数据。

当您想要直接连接两个服务时，可调 webhooks 有效负载也很重要，因为服务可能只接受带有特定头或有效负载的请求。

{:img }
[![The definition of the entire webhook payload sent to the serverless function](../Images/1c30a5b60960e57ef76bcd345124c57d.png)T3】](//images.ctfassets.net/fo9twyrwpveg/20MQxKhM4YqI24aS2kyqQK/73c012e719a0cb9d90e51795db31e836/image_3.png)

上面看到的是发送给无服务器函数的整个 webhook 有效负载的定义。JSON 指针(`{ /payload/… }`)的语法使得访问原始有效负载和根据您的需求重新构建一切成为可能。在本例中，您将使用以下内容:

*   在无服务器功能中下载上传图像的 URL
*   该文件的文件名，用作水印图像文件名的基础
*   上传文件的内容类型，以便仅对 jpegs 运行水印
*   图像的宽度和高度，以避免水印图像太小
*   要在带水印的资产中重用的已上传资产的标题和描述

## 使用 Jimp 在无服务器功能中操作图像

有趣的部分来了...图像操作通常需要您安装本机依赖项，这在处理无服务器功能时会有点棘手。在这种情况下， [Jimp 包](https://github.com/oliver-moran/jimp)非常方便，因为它完全用 JavaScript 执行图像操作。操作可能比原生实现慢一点，但是您不必处理在 AWS 上安装原生依赖项的麻烦。

### 向无服务器项目添加 npm 依赖项

你可以用`npm init --yes`给项目根添加一个`package.json`，然后用`npm install --save jimp`把这个包作为依赖项安装。当您稍后部署项目时，无服务器框架会扫描您的项目，并找出哪些依赖项需要包含在发送给 AWS 的包中。它甚至排除了开发依赖性，以使包尽可能小。这真的很酷！

您可以愉快地使用 Jimp 包并在 Lambda 代码中操作图像。

```
const original = await Jimp.read(url).opacity(0.4); 
```

此时，您的项目结构应该如下所示:

```
handler.js
serverless.yml
package.json
package-lock.json
node_modules/ 
```

### 用 Jimp 合成两幅图像

水印图像的创建可以分为四个部分:

*   读取应该添加水印的原始图像
*   读取作为水印的图像
*   使水印图像透明
*   组合两幅图像

幸运的是，所有这些步骤都反映在 Jimp 提供的方法中。

```
// step 1
const original = await Jimp.read('https://url/to/image');
// step 2
const mark = await Jimp.read('https://url/or/filepath/to/mark); 
// step 3
mark.opacity(0.5);
// step 4
const watermarkedImage = await original.composite(mark, 50, 50); 
```

关于`Jimp.read`非常方便的是，它接受文件路径和 URL，这意味着你不必自己处理文件下载。

### 读取 webhook 有效载荷，对上传的图片进行水印处理

还记得我们在 Contentful 中定义的有效负载吗？这个有效载荷可以在 lambda 函数内部的函数参数`event`的`body`属性中找到。您可以将传递的 URL 与 Jimp 功能结合起来，并开始对图像加水印。

```
module.exports.watermark = async (event) => {
  try {
    console.log(
      {% raw %}`Received webhook: ${JSON.stringify(JSON.parse(event.body), null, 2)}`{% endraw %}
    );
    const {
      url,
      title,
      description,
      fileName,
      contentType,
      width,
      height
    } = JSON.parse(event.body);

    // validate and check if a new watermark version
    // has to be created
    if ( contentType !== ‘image/jpeg’) {
      return {
        statusCode: 200,
        message: ‘Nothing to do...' }
    }

    // ... 

    const original = await Jimp.read(url);
    const mark = await Jimp.read('https://path/to/mark); 
    mark.opacity(0.5);
    const watermarkedImage = await original.composite(mark, 50, 50); 
  } catch(e) {
    return {
      statusCode: 500,
      error: e.message
    }
  } 
```

上面的代码片段还包括验证步骤，以避免总是创建新的水印图像，并避免永无止境的循环。不包括水印 logo 的下载以免复杂，不过你可以在 GitHub 上找到完整版[。](https://github.com/stefanjudis/serverless-contentful-watermark/blob/master/handler.js)

### 将新的水印资产上传到 Contentful

要上传生成的图像，您可以使用[内容管理 SDK](https://github.com/contentful/contentful-management.js?files=1) ，它为您提供了`createAssetsFromFiles`功能，通过[内容管理 API](https://www.contentful.com/developers/docs/references/content-management-api/?utm_campaign=automatically-watermark-contentful-assets-using-serverless&utm_medium=referral&utm_source=devto&utm_content=automatically-watermark-contentful-assets-using-serverless&utm_term=) 上传图像。

```
const contentful = require(‘contentful-management’);

 const 【上传资产】  =  【异步】(【title】【create client】(【access token】:-我...。}；
【const】=【await】【客户】 。【get space】(【space _ id】)；
【const】【env】=【await】空间 。(【t107【master】)；
【T1114】【let】【资产】=【T1119】【await】。【create asset fromfiles】【T1128】(【T1129】【T1130】【田】【T1132】:【T1135】【T1137】【T1138】标题【标题】 
: 【en-us】【t  
 【资产】=【await】【资产】 。 【局部加工】()； 
 【资产】=【await】【资产】 。 【发布】()；
}； 
```

### 
  
整体结构

读取和验证 JSON 有效负载、下载和处理所需的图像以及向 Contentful 上传新资产是该功能的核心部分。完整功能的整体实现大约有 200 行代码，本文仅描述其中的一些细节。如果你想了解更多或者尝试一下，你可以去 GitHub 的知识库。

代码流应该如下所示:

```
Module.exports.watermark = async (event) {

  // read webhook payload

  // validate payload and check if you can skip watermark creation

  // read the image of the published asset

  // read the watermark logo

  // composite both images

  // upload it back to Contentful

} 
```

## 
  
结合 webhooks 的无服务器功能是未来的 SaaS 胶水

如果您使用无服务器功能来丰富单个服务的功能，就像本例中 Contentful 的资产创建一样，这并不重要。函数也非常适合组合不同的服务。

以 Algolia(一家 SaaS 搜索提供商)为例。要使用他们的搜索，你必须索引数据。有了 webhooks，你可以直接将数据推入 Algolia，或者，对于复杂的情况，你可以设置一个无服务器的功能来控制哪些数据应该进入。前往[文档](https://www.contentful.com/developers/docs/tutorials/general/enhancing-search-experience-with-algolia/?utm_campaign=automatically-watermark-contentful-assets-using-serverless&utm_medium=referral&utm_source=devto&utm_content=automatically-watermark-contentful-assets-using-serverless&utm_term=)了解更多信息。

在我看来，我们已经进入了一个非常光明的未来，有能力部署这些基于功能的 HTTP 端点，你和我可以专注于构建伟大的产品，而不用重新发明轮子！

### 附加注释和说明

#### 如何局部开发 Lambda 函数？

为了开发部署到本地 AWS 的无服务器功能，有一个优秀的[无服务器离线包](https://www.npmjs.com/package/serverless-offline)，它在本地机器上模拟 AWS。我强烈建议去看看。

#### 环境变量

所描述的将资产重新上传到 Contentful 的用例要求您创建一个管理令牌，它对您的 Contentful 空间具有写访问权。请注意，这个令牌不应该进入您的版本控制，而应该通过环境变量(这是您将在[提供的 GitHub 库](https://github.com/stefanjudis/serverless-contentful-watermark)中找到的解决方案)或配置文件来处理。保重！

#### API 网关的限制

在这个例子中，API gateway 触发了处理图像处理的功能。API 网关目前的最大超时是 30 秒，而 Lambda 函数[可以运行 15 分钟](https://aws.amazon.com/about-aws/whats-new/2018/10/aws-lambda-supports-functions-that-can-run-up-to-15-minutes/)。对于处理时间超过 30 秒的任务，建议将这个函数分成两个函数，以避免超时。