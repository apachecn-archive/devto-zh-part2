# 用 Netlify 构建 Lambda 函数

> 原文：<https://dev.to/alexmacarthur/building-a-lambda-function-with-netlify-2n5i>

不久前，我用 Firebase 编写了我的第一个 Lambda 函数。就像网络上的每个 Lambda 函数教程告诉你如何做一样，当有人购买地球上最通用的 JavaScript 动画打字工具 [TypeIt](https://typeitjs.com) 的许可证时，mine 通过 Stripe 处理支付。这个过程非常简单——Firebase 的 CLI、文档和支持都非常棒。即便如此，我对这个设置还是有所保留。

首先，TypeIt 的网站是由 Netlify 托管的，我疯狂地爱上了 Netlify，所以功能的开发感觉…超然。我无法轻松地管理同一个存储库中的所有内容，我必须密切关注三种不同的服务来实现这一切— Netlify、Firebase 和 Stripe。

第二，我是个小气鬼，Firebase 没有一个允许调用 Stripe 等外部服务的自由层。也许这已经改变了，但据我所知，是这样的。

考虑到这一点，当 Netlify 的 Lambda 函数服务推出时，我差点尿裤子，我立即将我的 Firebase 函数迁移到 Netlify。这太有趣了，我已经记录了那次经历。我将带您快速了解如何结合 Lambda 函数的力量，利用使 Netlify 变得伟大的一些特性。希望你能体会到为什么像这样的提供商提供的服务是独一无二的棒。

我将在这里使用一些非常简单的东西，但是您可以用任何类型的静态应用程序来实现该功能——Jekyll、Gatsby 等等。*只要确保你有一个可以用 Netlify 托管的静态站点，并且它使用 webpack(我们将特别使用它的`DefinePlugin`)。*我从一个`index.html`文件、一些 CSS、JavaScript 和一些工具开始，将它们整合在一起。你可以在这里看到完整的设置。你还需要一个 Stripe 账户，可以在这里免费获得。

我们走吧。

## 设置本店功能

我将假设您已经建立了一个基本的 JAMStack 项目&准备就绪，包括一个`package.json`文件。打好基础后，完成以下步骤:

**创建目录来存储你的 Lambda 代码。**其中一个将包含您预编译的源代码，另一个将是 Netlify 放置生产就绪代码的地方。在本地，我们将只在`lambda-src`目录之外提供服务，因此创建那个`lambda`目录在技术上是不必要的(稍后，当我们部署时，Netlify 将自动创建那个目录)，但是为了更清楚地了解发生了什么，我们将在这里继续进行。

`mkdir lambda-src lambda`

安装 Netlify Lambda CLI。这将使我们能够在部署到 Netlify 之前在本地开发&测试功能。

`npm install netlify-lambda -D`

**创建一个`netlify.toml`文件**，它将包含我们的功能部署所需的配置信息。

`touch netlify.toml`

`netlify.toml`文件负责定义诸如构建命令、我们的发布目录、环境变量之类的东西，尤其是我们在这里所做的事情，也就是我们的 Lambda 函数目录所在的位置。值得一提的是，您也可以在管理中设置这个目录，但这有点不太酷。

在定义了这个目录以及其他几个好的目录之后，我们就剩下这个了，它位于我们项目的根中。关于这个文件如何工作及其功能的更多信息，[阅读文档](https://www.netlify.com/docs/netlify-toml-reference/)；

```
# netlify.toml 

[build]
  command = "npm run build"
  publish = "src"
  functions = "lambda" 
```

Enter fullscreen mode Exit fullscreen mode

翻译:在构建时，Netlify 将使用指定的构建命令构建我们的东西，从`src`目录服务我们的站点，从`lambda`目录服务我们的函数。

**接下来，让我们在`package.json`中设置一些动作，以便在本地运行我们的代码，并为生产构建它。**

我们将配置一切来同时运行一个小型开发服务器*和*一个 Lambda 功能服务器。为此，我们将使用`concurrently`包，该包也需要安装:

`npm install concurrently -D`

接下来，我们的`scripts`将看起来像这样:

```
"scripts": {
  "lambda-serve": "netlify-lambda serve lambda-src",
  "lambda-build": "netlify-lambda build lambda-src",
  "build": "NODE_ENV=production webpack && npm run lambda-build",
  "dev": "NODE_ENV=development concurrently \"webpack-dev-server --content-base src/\" \"npm run lambda-serve\"",
} 
```

Enter fullscreen mode Exit fullscreen mode

当`npm run dev`启动时，我们将拥有一个加载静态页面的工作开发服务器，以及一个函数的本地端点。请注意，我们没有向这些命令传递任何特定于函数的信息— `netlify-lambda`将从我们的`netlify.toml`文件中提取所有需要的信息。

之后，让我们定义一些我们需要的变量。具体来说，我说的是条带可发布密钥和秘密密钥，它们是 1)我们不想提交给我们的存储库的敏感信息，以及 2)在我们的开发和生产环境之间是不同的。

可以在一个`netlify.toml`文件中定义这样的东西，但是由于这些敏感性问题，我们将在 Netlify 的 admin 中的“Build & deploy”部分下设置这些值。这将使我们不必将它们提交给版本控制。你可以从你的条纹仪表板得到这些钥匙。

[![Define Our Environment Variables](img/4dde601879cfa469e849b724659f46fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8GY6GI-z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://macarthur.me/static/a57f5f6a08b30a89a6516c3cafe5e910/a296c/environment-variables.jpg)

你会注意到我也把我们的`LAMBDA_ENDPOINT`扔在那里了。一会儿会有更多的内容。

为了在本地访问这些条纹密钥，我们将使用 [dotenv](https://github.com/motdotla/dotenv) (感谢 [Phil Hawksworth](https://www.hawksworx.com/) 的提示！).它做的事情非常简单:当我们引用一个没有在 Node 的`process.env`对象中定义的环境变量时(就像我们在本地工作时)，通过引用我们项目中的一个`.env`文件来填充它。这个文件*不会*提交到存储库中，并且将只包含测试密钥。也就是说，把这个文件放到你的`.gitignore`文件中，防止它被上传。这是… *键*。

```
echo ".env" >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

安装软件包并创建那个`.env`文件。

```
npm install dotenv -D
touch .env 
```

Enter fullscreen mode Exit fullscreen mode

在该文件中，继续用测试键填充条带变量，但是在下一节之前忽略`LAMBDA_ENDPOINT`变量。

```
STRIPE_PUBLISHABLE_KEY="XXXXXX"
STRIPE_SECRET_KEY="XXXXXX"
LAMBDA_ENDPOINT="http://localhost:9000/purchase" 
```

Enter fullscreen mode Exit fullscreen mode

### 实际上编写一些 Lambda 代码

**在`lambda-src`中创建一个`purchase.js`文件。当我们准备好发送到我们的函数时，`lambda`目录中的文件名将成为我们的端点。对于我们来说，当在本地使用`netlify-lambda`时，这将是`http://localhost:9000/purchase`。部署完成后，您将使用您网站的 URL: `https://your-site-name.netlify.com/.netlify/functions/purchase`。您看到我已经使用 Netlify admin 中的`LAMBDA_ENDPOINT`变量和我们的本地`.env`文件为我们的每个环境进行了设置。**

通过初始化`dotenv`并用 Stripe 验证来开始我们的函数代码。这将把我们的`.env`文件中的任何变量加载到`process.env`中，除非这些变量已经设置好了。正因为如此，不管我们的环境如何，代码都是灵活的。

```
// purchase.js

require('dotenv').config();

const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们设置合适的标题。我们要确保我们允许通过 AJAX 访问我们的函数，并且确保我们可以成功地传递数据。现在，我将向来自任何域的请求开放它，但是您会希望在部署时更改这一点。因为我想以后利用 JavaScript 增强的对象文字，所以我还保存了一个默认的状态代码:

```
// purchase.js

require('dotenv').config();

const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);

const statusCode = 200;
const headers = {
  "Access-Control-Allow-Origin" : "*",
  "Access-Control-Allow-Headers": "Content-Type"
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建一个 Lambda 函数处理程序。这就是奇迹发生的地方。为了确保一切正常，我将立即返回一个响应，并在正文中包含一条独特的消息。如果你已经用 AWS 构建了 Lambda 函数，并且看起来有些熟悉，这是有原因的。 [Netlify 将你的功能部署到 AWS](https://functions-beta--www.netlify.com/docs/lambda-functions/) 。

```
// purchase.js

exports.handler = async function(event) {
  return {
    statusCode,
    headers,
    body: 'Let there be light!'
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

小提示:我为我的 Lambda 使用了一个`async`函数。这是因为我们稍后将做一些异步工作，我更喜欢干净的语法。但是如果你想写得更详细一点，你需要返回一个`callback`方法来返回你的响应，就像这样:

```
// purchase.js

exports.handler = function(event, context, callback) {
  return callback(null, {
    statusCode,
    headers,
    body: 'Let there be light!'
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行`npm run lambda-serve`并前往`http://localhost:9000/purchase`，我们应该会看到‘让那里有光！’在浏览器中。好兆头！

添加一个小检查以确保我们只处理 POST 请求。很长一段时间以来，我一直无法正确解析请求中的数据，却发现这是因为我试图解析的不是我的 POST 请求中的数据，而是来自预检选项请求，该请求是为了确保理解 CORS 协议。为了防止这种情况发生，使用这个:

```
// purchase.js

exports.handler = async function(event) {
  if (event.httpMethod !== "POST") {
    return {
      statusCode: 200, // <-- Important!
      headers,
      body: "This was not a POST request!"
    };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，确保返回有效的`200`状态代码尤为重要。否则，预检请求将会失败，并会抛出一个与 CORS 相关错误。

现在我们可以可靠地解析身体，并确保我们拥有所需的一切。如果没有，用某种消息执行回调。

```
// purchase.js

exports.handler = async function(event) {

  // We only care to do anything if this is our POST request.
  if (event.httpMethod !== "POST") {
    return {
      statusCode,
      headers,
      body: "This was not a POST request!"
    };
  }

  // Parse the body contents into an object.
  const data = JSON.parse(event.body);

  // Make sure we have all required data. Otherwise, get outta here.
  if (!data.token || !data.amount || !data.idempotency_key) {
    const message = "Required information is missing!";

    console.error(message);

    return {
      statusCode,
      headers,
      body: JSON.stringify({
        status: "failed",
        message
      })
    };
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

既然我们在这里，让我们分解一下我们正在检查的这些`data.token`、`data.amount`和`data.idempotency_key`的东西。

这将是我们的前端结帐表单生成的唯一支付令牌。其中还包括用户的电子邮件地址，这对于触发电子邮件收据来说很方便。

这将是小工具的价格，以美分为单位。所以，`1000`其实就是`$10.00`。

`idempotency_key`:这是一个好主意，通过它可以更好地[防止同一个操作被意外执行两次](https://stripe.com/docs/api?lang=curl#idempotent_requests)。这个值实际上是什么并不重要，重要的是它是唯一的。

如果身体拥有我们所需要的一切，就把它传递给条纹以产生电荷。在我们收到回复后，我们会将该费用的状态返回给浏览器。如果你认为合适的话，请随意详细说明。创造客户，成功充值后触发电子邮件，任何你喜欢的。关键是我们创建了一个费用，并立即让浏览器知道它是否成功。

```
// purchase.js

let charge;

try {
  charge = await stripe.charges.create(
    {
      currency: "usd",
      amount: data.amount,
      source: data.token.id,
      receipt_email: data.token.email,
      description: `charge for a widget`
    },
    {
      idempotency_key: data.idempotency_key
    }
  );
} catch (e) {
  let message = e.message;

  console.error(message);

  return {
    statusCode: 424,
    headers,
    body: JSON.stringify({
      status: "failed",
      message
    })
  };
}

const status = (charge === null || charge.status !== "succeeded") ? "failed" : charge.status;

return {
  statusCode,
  headers,
  body: JSON.stringify({
    status,
    message: "Charge successfully created!"
  })
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 设置前端

此时，我们已经准备好开始前端的工作，这将包括生成一个 Stripe 令牌，然后通过 AJAX 将其发送到我们的 Lambda 函数。为了简单起见，我们将使用条带检验来完成这项工作。基本上，只要把它放进去就可以了。

**将 Stripe Checkout 脚本添加到您的`index.html`文件主体的底部，并添加一个用于打开 Checkout 表单的按钮。**T3】

```
<button>
  Click to Buy! <strong>$10</strong>
</button>

<script src="https://checkout.stripe.com/checkout.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

**配置 webpack，使我们的环境变量在前端可用。**就像我们之前做的一样，我们将初始化`dotenv`，这样我们就可以在本地访问环境变量。在我们的`webpack.config.js`文件的顶部，让我们添加这个:

```
// front-end.js

require('dotenv').config();

const webpack = require('webpack');

module.exports = { 
  // webpack configuration... 
} 
```

Enter fullscreen mode Exit fullscreen mode

和以前一样，如果一个特定的变量还没有定义，我们让`dotenv`填充`process.env`的空白。在此之下，我们使用 webpack 的`DefinePlugin`将这些变量暴露给我们的 JavaScript。

```
// webpack.config.js

module.exports = {
  entry: './src/front-end.js',
  output: {
    path: __dirname + '/src',
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.DefinePlugin({
      LAMBDA_ENDPOINT: JSON.stringify(process.env.LAMBDA_ENDPOINT),
      STRIPE_PUBLISHABLE_KEY: JSON.stringify(process.env.STRIPE_PUBLISHABLE_KEY),
    })
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

**现在，让我们在我们的 front-end.js 文件中创建一个条带检出处理程序，并在`index.html`的底部包含那个捆绑的`bundle.js`文件。**

```
// front-end.js

const handler = StripeCheckout.configure({
  key: STRIPE_PUBLISHABLE_KEY,
  image: "https://stripe.com/img/documentation/checkout/marketplace.png",
  locale: "auto",
  token: token => {
    // We'll fill this out in a second.
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

当有人点击按钮时，让我们打开结帐表单。

```
// front-end.js

// Stripe handles pricing in cents, so this is actually $10.00.
const amount = 1000;

document.querySelector("button").addEventListener("click", function() {
  handler.open({
    amount,
    name: "Test Shop",
    description: "A Fantastic New Widget"
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

此时，当用户单击页面上的按钮时，会弹出一个漂亮的 Stripe checkout 表单，准备收集用户的支付信息。一旦提交了该表单，我们就将该信息发送给我们的函数。

**通过 AJAX 将生成的令牌发送给我们的 Lambda 函数。**在我们的令牌生成后，将此添加到您的`front-end.js`文件中。我正在使用浏览器的[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ，但你选择什么真的不重要。

```
// front-end.js

const handler = StripeCheckout.configure({
  // -- Other stuff we've already gone through.
  token: async (token) => {
    let response, data;

    try {
      response = await fetch(LAMBDA_ENDPOINT, {
        method: "POST",
        body: JSON.stringify({
          token,
          amount,
          idempotency_key: uuid()
        }),
        headers: new Headers({
          "Content-Type": "application/json"
        })
      });

      data = await response.json();
    } catch (error) {
      console.error(error.message);
      return;
    }
  }; 
```

Enter fullscreen mode Exit fullscreen mode

为了生成我们的`idempotency_key`，我使用了 [uuid](https://www.npmjs.com/package/uuid) 包。真的很独特(lol)。运行`npm install uuid -D`并将其导入到文件的顶部。

```
// front-end.js

import uuid from 'uuid/v4'; 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们已经把一堆代码放在一起了。现在…

**让我们在本地旋转吧！**如果您还记得我们设置的动作，使用`npm run dev`将启动一个 webpack 开发服务器，并触发`netlify-lambda`来服务我们的功能。运行该命令，我们的应用程序应该在`http://localhost:8080`可用。点击按钮，输入您的支付信息(在开发模式中使用`4242424242424242`输入卡号)，您*应该*在控制台中看到一个成功的响应。当然，这是我们在这里做的网络开发，很少有第一次就做对的，所以在你解决任何问题时要有耐心。

## 部署时间到了！

**让我们活捉这个阿摩林。**将所有这些放入一个存储库，登录你的 Netlify 账户，连接管道，设置你的环境变量，如果你还没有的话。因为我们的`netlify.toml`文件，Netlify 应该已经知道从哪里为您的功能服务。完成后，您应该会看到我们的`purchase.js`函数列在“函数”页面上，如果您需要，可以在这里查看日志记录。当您调试时，您可以使用`console.log()`将日志转储到这个窗口中。

## 资源

如果你想更深入地了解这个例子，请查看 Github 上的回购。在那里，我有一个简单的工作演示，实际上提交了一个假的支付给条纹。如果你希望付款不是假的，我们可以安排。

当你准备好自己用 Netlify 探索 Lambda 函数的时候，[在这里](https://www.netlify.com/docs/functions/)挖掘。

## 有道理吗？

我希望这个过程大体上很容易完成，没有太多的挫折。如果您有任何问题、更正或改进建议，请联系我们！