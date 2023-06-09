# 使用 Serverless、Express 和 Nodejs 构建和部署 Rest API

> 原文：<https://dev.to/sait/build-and-deploy-a-rest-api--using-serverless-express-and-nodejs-3331>

今天我将展示如何使用无服务器 lambda 构建和部署 express/Node Rest API。

我们知道无服务器正在快速发展，许多公司通过使用无服务器计算节省了资金。

Amazon web services 为我们提供了各种各样的服务来构建和部署小型到大型的应用程序。

# 什么是 Aws Lambda？

AWS Lambda 无需供应或管理服务器即可运行您的代码，这意味着您无需购买或管理服务器。您只需在您的代码运行或用户访问您的网站时付费。

[![lamda](img/9c51538de1b3a7f2d3b7e0f4ae9b4989.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2LCugFcU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/blqwkpsbt4ld7iwxnstj.png)

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

sls 登录后，您需要用无服务器框架配置您的 Aws 凭证。

[获取 Aws 凭证](https://www.youtube.com/watch?v=tgb_MRVylWw)

一旦完成，你就可以走了。

#### 创建和部署 Rest API 端点

在您的工作目录中创建一个模板

```
serverless create --template aws-nodejs 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令生成了样板文件。

现在我们需要初始化 Package.json 文件并安装
一些依赖项。

```
npm init -y // generates package.json file

npm i -s body-parser cors express mongoose serverless-http 
```

Enter fullscreen mode Exit fullscreen mode

在您最喜欢的代码编辑器中打开您的应用程序文件夹。

创建一个 app.js 文件，并导入我使用的 mongodb 数据库的需求。