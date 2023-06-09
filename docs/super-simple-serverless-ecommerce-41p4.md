# 超级简单的无服务器电子商务

> 原文：<https://dev.to/squaredev/super-simple-serverless-ecommerce-41p4>

许多公司已经投入使用[无服务器](https://en.wikipedia.org/wiki/Serverless_computing)来增强他们的技术堆栈(例如[诺德斯特龙](https://read.acloud.guru/serverless-event-sourcing-at-nordstrom-ea69bd8fb7cc)、[网飞](https://medium.com/netflix-techblog/developer-experience-lessons-operating-a-serverless-like-platform-at-netflix-a8bbd5b899a0)和[可口可乐](https://aws.amazon.com/serverless/videos/video-lambda-coca-cola/))。看起来无服务器只是一种日益流行的时尚，但这也是许多人对 Node 的想法，直到沃尔玛在黑色星期五之前推出了一个新网站。对于无服务器来说，我们可能正在接近一个类似的拐点，以快速提高 Node 已经能够实现的开发速度和规模。

如果您还没有加入无服务器潮流，现在正是时候。我们将通过一个简单的方法来创建无服务器功能，以允许客户立即结帐并从 Square 商店购买商品。我们将在 S3 上创建一个静态站点，创建 AWS Lambda 函数，并创建 AWS API 网关端点，以允许客户使用 Square Checkout API 即时结账。

> 有必要澄清一下[无服务器框架](https://serverless.com/)和[无服务器计算](https://en.wikipedia.org/wiki/Serverless_computing)之间的区别。无服务器框架只是一个工具，可用于帮助创建、部署和管理您与您想要的云提供商一起创建的无服务器功能。无服务器计算是指云提供商动态管理机器资源的分配。

在这篇文章中，我们将使用无服务器框架来更容易地创建和部署我们的功能，但我们不希望它与无服务器计算相混淆。我们将在未来的帖子中探索不同的平台和不同的工具，因为我们将在这里讨论的概念上进行构建。

建造这个需要的东西:

*   [Square 开发者账号](https://squareup.com/developers)

*   [AWS 账户](https://aws.amazon.com/)

*   [无服务器框架](https://serverless.com/)

### 静态站点使用 S3

我们的小商店的基本结构将是一个静态的 HTML 页面，托管在 S3 上，用 CSS 和 JavaScript 装饰。然后，我们将通过 Lambda 函数处理我们的结账，该函数允许您只购买您点击的一件商品。这是为了说明如何在 AWS 上启动和运行的超级基础——我们可以在未来的帖子中探索更高级的方法。我们将从 HTML 开始，并把它引入 S3 来创建我们的静态网站。

```
<!-- start product -->
<div class='product'>
    <div class='imageContainer'>
        <img class='productImage' src="/0.jpeg" />
        <!-- insert product image url here -->
        <div class='imageOverlay'>
            <button>View Details</button>
        </div>
    </div>
    <div class='modal clearfix'>
        <button class='close'>Close</button>
        <img class='modalImage' src="/0.jpeg" />
        <!-- insert product image url here -->
        <div class='productInfo'>
            <h2 class='productTitle'>Back to Square 1 Sticker</h2>
            <!-- insert product title here -->
            <p class='productDescription'>"If only 88 was a perfect Square..."</p>
            <div class='productMeta'>
                <h2 class='productCost'>
                    <span class='green'>$1.00</span>*
                </h2>

                <!-- insert product cost here -->
                <span class='helperText'>*We're not actually going to charge you.</span>
                <form action='https://UNIQUE_ID.execute-api.us-east-1.amazonaws.com/prod/checkout' method='post'>
                    <input name='itemVarID' type='hidden' value="IBXSHR4PIMEAHHSOAKNBC5PQ" style='display: none' />
                    <input name='price' type='hidden' value="100" style='display: none' />
                    <input name='name' type='hidden' value="Back to Square 1 Sticker" style='display: none' />
                    <button type='submit' class='productPurchase'>Buy This</button>
                </form>
            </div>
        </div>
    </div>
</div>
<!-- end product --> 
```

通过查看产品 div 元素来了解我们的`index.html`的内容。我们将项目的[变量 id](https://developer.squareup.com/docs/api/connect/v2#endpoint-retrievecatalogobject) 硬编码到每个 div 中，并填充到一个隐藏的输入字段中。这将让我们很容易通过 POST 请求将它传递给我们的 Lambda 函数。我采用这种方法的主要原因完全在于它的简单性。我们不需要编写任何 JavaScript 来处理信息传递——我们可以使用表单元素的本机行为将我们的项目信息发送到我们的函数中。

如果我们输入 12 种产品，我们会得到类似下面的结果:

```
<!DOCTYPE html>
<html>

<head>
    Square Swag Shop
    <meta name="description" content="A Simple eCommerce Store">
    <link id="favicon" rel="icon" href="/favicon.ico" type="image/x-icon">
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <link rel='stylesheet' href='/normalize.css'>
    <link rel='stylesheet' href='/main.css'>
</head>

<body>
    <div id="cocoon">
        <header class="container">
            <div id="square-logo">
            </div>
        </header>
        <div id="products" class="container clearfix">
            <div class='product'>
                <!-- Our other product html tags here -->
            </div>
            <div class='product'>
                <!-- Our other product html tags here -->
            </div>
            <div class='product'>
                <!-- Our other product html tags here -->
            </div>
            <div class='product'>
                <!-- Our other product html tags here -->
            </div>
        </div>
        <div class="veil"></div>
    </div>
    <script src='/modernizr-3.5.0.min.js'></script>
    <script src='https://code.jquery.com/jquery-3.2.1.min.js' integrity='sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4=' crossorigin='anonymous'></script>
    <script src='/plugin.js'></script>
    <script src='/main.js'></script>
</body>

</html> 
```

我们将跳过用于创建模态的样式和 jQuery，因为这不是这里的重点。我们所要做的就是将我们所有的文件上传到 S3，并启用属性中的“静态网站托管”来拥有一个可公开访问的网站。

现在我们应该有一个漂亮的页面让每个人都能看到我们的产品，但我们需要有一种方法让每个人都能购买这些产品。现在我们终于开始深入创建我们的 AWS Lambda 函数(无服务器函数)。这就是我们将要创建的放在表单“action”属性中的内容，这样我们的表单就可以发送到我们的函数中。

[![I should hope its clear you have your own bucket name for the URL.](img/f8fd977fd31210655a4074bd540f91b6.png) ](//images.ctfassets.net/1wryd5vd9xez/2BaS7Axhh4MP1WE6VI2Nto/789847564faa6f3f6cfe45b8aae3cf56/https___cdn-images-1.medium.com_max_2102_1_RiQhDFnejiTQYb3B1tNASg.png) *我应该希望其明确你有自己的网址桶名。*

通过访问 AWS 提供的 URL，您应该能够访问您的静态网站，并且您可以看到所有展出的精彩项目。

### 使用 AWS Lambda 的无服务器功能

对于我们的无服务器功能，我们将处理从静态站点中的表单发送的 Square [catalog variant ID](https://developer.squareup.com/docs/api/connect/v2#endpoint-retrievecatalogobject) ，然后创建一个 checkout URL 来重定向我们的用户。为此，我们将在函数中使用 Square 的 [JavaScript SDK](https://github.com/square/connect-javascript-sdk) 来更容易地生成我们需要的 URL。非常欢迎您尝试使用*和* AWS 工具来创建和部署该功能，但是我们将只使用[无服务器](https://serverless.com/)来管理该功能的部署并将其链接到 AWS API 网关。

如果你也想使用无服务器创建你自己的功能，他们有很棒的教程[在这里](https://serverless.com/framework/docs/providers/aws/guide/quick-start/)。你的机器上需要有 Node，用`npm install -g serverless`安装 serverless(或者你不想全局安装，就用`[npx`([https://medium . com/@ maybe Katz/introducing-npx-an-NPM-package-runner-55 f 7d 4 BD 282 b)](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)))。

您可以运行`serverless create --template aws-nodejs`让模板运行起来，但是它创建的东西比需要的多得多。你可以随意复制`handler.js`和`serverless.yml`来修改成你自己的模板。我们将从一个基本功能开始，测试我们的部署是否按预期工作，然后为我们的检查添加功能。

```
'use strict';
const SquareConnect = require('square-connect');

module.exports.checkout = (event, context, callback) => {
  let response = "Success! Our function is running!";
  callback(null, response);
}; 
```

这只是我们要测试的例子。接下来，我们想要创建我们的`serverless.yml`文件，用于部署我们的功能和创建 AWS API 网关端点。

```
service: serverless-checkout # NOTE: update this with your service name

provider:
  name: aws
  runtime: nodejs8.10
  stage: prod
  memorySize: 256

package:
  include:
    - node_modules/square-connect/**
    - node_modules/superagent/**
  excludeDevDependencies: true

functions:
  checkout:
    environment:
      ACCESS_TOKEN: YOUR_ACCESS_TOKEN
      LOCATION_ID: YOUR_LOCATION_ID
    handler: handler.checkout
    events:
      - http:
          path: /checkout
          method: any 
```

现在，只需运行`serverless deploy`就可以将我们的功能发布到云中。部署时，您应该会看到类似下面的内容:

```
Serverless: Packaging service...
Serverless: Excluding development dependencies...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Uploading service .zip file to S3 (742.81 KB)...
Serverless: Validating template...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
..............
Serverless: Stack update finished...
Service Information
service: serverless-checkout
stage: prod
region: us-east-1
stack: serverless-checkout-prod
api keys:
  None
endpoints:
  ANY - https://UNIQUE_ID.execute-api.us-east-1.amazonaws.com/prod/checkout
functions:
  checkout: serverless-checkout-prod-checkout
Serverless: Removing old service versions... 
```

您应该能够在浏览器中访问无服务器退出控制台的 URL，并在浏览器中找到我们的消息`“Success! Our function is running!”`。我们只需要重构 Lambda 函数来处理目录变量 ID，以便请求我们的 checkout URL 并重定向用户。

```
const SquareConnect = require('square-connect');
const crypto = require('crypto');
const querystring = require('querystring');

module.exports.checkout = (event, context, callback) => {
  (SquareConnect.ApiClient.instance).authentications["oauth2"].accessToken = process.env.ACCESS_TOKEN;
  const formData = querystring.parse(event.body);
  const locationId = process.env.LOCATION_ID;
  const checkoutRequest = {
    idempotency_key: crypto.randomBytes(48).toString('base64'),
    order: {
      idempotency_key: crypto.randomBytes(48).toString('base64'),
      reference_id: Date.now().toString(),
      line_items: [
          {
            catalog_object_id: formData.itemVarID,
            quantity: "1"
          }
      ]
    },
    merchant_support_email: "support@squareup.com"
  };
  const checkoutApi = new SquareConnect.CheckoutApi();

  checkoutApi.createCheckout(locationId, checkoutRequest).then((checkoutResponse)=>{
    const response = {
      statusCode: 302,
      headers: { Location: `${checkoutResponse.checkout.checkout_page_url}` }
    };
    callback(null, response);
  }).catch(error => {
      console.log(JSON.stringify(error, null, 2));
      callback(error)
  });
}; 
```

在这里，我们有我们的全功能(双关语)Lambda 函数，可以创建结帐 URL，让用户购买我们的产品。

同样，值得提醒的是，这是故意最小化的实现，仅适用于像数字商品(不需要纳税)这样的东西，但演示了创建您的结账和重定向客户是多么容易，而无需启动我们自己的服务器。事实上，如果您要将目录变量 ID 作为查询字符串参数附加到函数 URL 中，您可以在函数中解析出这些内容，从而拥有自己的无服务器“立即购买”链接。此外，您可以添加一个运费项目，以轻松处理统一运费，甚至在收银台捕捉客户的运费细节(请参见[此处](https://developer.squareup.com/docs/payments/checkout/overview#create-the-post-request)了解更多详情)。

[![A preview of our eCommerce store. Those are our stickers we give away at developer conferences.](img/9a65598b09e5e746e4ba1d151b710e4d.png) ](//images.ctfassets.net/1wryd5vd9xez/4IyeEguXo7RZQw9l32K7n4/d2861fa5fcacc72a0550add41f1033b4/https___cdn-images-1.medium.com_max_3004_1_VC8FjM4vSbPwtsTdDDDKNw.png) *我们的电子商务商店预览。这些是我们在开发者大会上分发的贴纸。*

### 无服务器的误解

关于无服务器计算及其组成，似乎存在广泛的误解。是按调用付费吗？按内存 Mb/s 付费？就像 Node 一样，serverless 也有点渴望“做所有的事情”没错，这是一项需要探索的新技术，但是出现了许多奇怪的用法，它们被视为反模式(在我看来)。Node 过去和现在都因其单线程特性而在处理 CPU 密集型任务方面表现不佳，对于使用无服务器进行面向用户的交互或 API 也是如此。用户期望事情有响应，冷启动扼杀了预期的响应。您当然可以尝试通过保持函数温暖来解决这个问题，但这听起来像是一种反模式。无服务器的全部好处是不必担心您的基础设施，然而人们正在编写更多的代码来修改他们实际上无法控制的基础设施的行为。

如果您计划将无服务器应用于面向用户的应用程序，请仔细查看您的终端用户流量模式，因为这将严重影响整体用户体验。恒定的流量相对来说没什么问题——功能会保持温暖，很少一部分用户会受到缓慢响应的影响——但是如果你的流量特别大，用户体验就会受到影响，因为你的平台提供商正在[为你的流量提供额外的功能实例](https://theburningmonk.com/2018/01/im-afraid-youre-thinking-about-aws-lambda-cold-starts-all-wrong/)。

随着技术的不断成熟，使用无服务器计算有许多可能性可以探索，并且随着时间的推移，今天的问题将会减少。现在开始的真正原因是，你可以对未来所有新的无服务器采用者居高临下，告诉他们无服务器编程在你那个时代有多难。因此，现在就开始构建无服务器系统吧！

要跟进这篇文章，你需要[注册一个 Square 开发者账户](https://squareup.com/developers)。即使你不想跟进，也要注册，这是免费的！

*想要更多？[注册](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog)* *获取我们每月的开发者简讯。*