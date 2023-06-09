# 如何构建您的第一个无服务器 Website♡Aws

> 原文：<https://dev.to/sait/how-to-build-your-first-serverless-webpage-using-aws-lambda-31de>

今天，我们使用 Aws Lambda 构建了第一个无服务器网页。

# 什么是无服务器？

无服务器意味着它说不要担心更多的服务器，而是集中于核心产品。

Aws，照顾好服务器，不需要担心规模的扩大和缩小，无服务器的一个好处是，Aws 只在有人点击你的网页时对你收费，如果没有流量，这意味着不收费。

我想现在您已经对无服务器有了一些了解，让我们通过构建来了解更多。

### 什么是需求？

*   [Aws 自由层账户](https://aws.amazon.com/free/)

*   Nodejs v8+

**我正在使用无服务器框架 Cli 来创建和部署应用**。

打开你的终端

```
npm install -g serverless

sls login // SLS is a shortcut of serverless 
```

Enter fullscreen mode Exit fullscreen mode

sls 登录后，您需要用无服务器框架配置您的 Aws 凭证

[获取 Aws 凭证](https://www.youtube.com/watch?v=tgb_MRVylWw)

一旦完成，你就可以走了。

```
mkdir serverless-app

cd serverless-app 
```

Enter fullscreen mode Exit fullscreen mode

```
sls create --template  hello-world 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令生成了样板文件。

[![structure](img/d500758f26ff7376d534835153729ebe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cNqFgdl_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5tx01hpr8mvrils39w3.png)

样板文件生成如上图所示的三个文件。

现在我们需要安装 express 和其他包。

```
npm init  // to generate a package.json file

npm i --save express body-parser hbs serverless-http 
```

Enter fullscreen mode Exit fullscreen mode

现在打开 handler.js 文件并清除它，因为我们是从头开始写的。