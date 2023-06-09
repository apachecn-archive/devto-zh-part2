# 使用 Node.js 自动化您的 Cognito 托管 UI 样式化过程

> 原文：<https://dev.to/nordcloud/automate-your-cognito-built-in-ui-customization-process-with-nodejs-26o1>

Amazon Cognito 是一项提供认证、授权和用户管理的服务。两个主要组件是用户池和用户身份池。对于用户池，您可以使用内置的登录和注册页面，甚至可以定制他们的 UI！您可以使用 AWS 控制台、API 或 CLI 来提供徽标和 CSS。如果您想自动化这个过程，并且可能将您的定制存储在某个地方，该怎么办呢？Amazon 会在每次发出新的 CLI/API 请求时覆盖旧的更改，因此确保所有样式表更改的安全更加重要。如果你想使用一些在浏览器中工作但亚马逊控制台不允许的前沿 CSS 怎么办？

我是这样解决的——我创建了一个非常基本的 Node.js 脚本，它获取一个 CSS 文件和一个图像文件，并使用 AWS-SDK 上传它。这里有一个小的一步一步的指导你如何也可以创建一个。

我假设您的机器上有全局 AWS 密钥。如果不是，你可以在这里阅读更多关于如何让它工作的信息[。](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-credentials-node.html)

#### 1。初始化项目并安装其依赖项

运行`npm init`，然后回答关于您的软件包的问题。接下来，您需要使用`npm i aws-sdk --save`添加 AWS Javascript SDK。准备好后，创建一个名为 index.js 的新文件，作为 Node.js 应用程序的入口点。在文件开始导入两个节点模块，`fs` -用于文件访问文件系统和`aws-sdk`，您刚刚安装。我们将使用 SDK 来访问 Cognito API。

```
 const fs = require("fs");
    const AWS = require("aws-sdk"); 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。创建您的样式表

创建一个 CSS 文件，例如`congito.css`。有了 Cognito UI，你可以只使用几个类来定制它，你可以在这里找到完整的列表。将您的样式放入样式表文件并保存。你也可以使用注释，你甚至可以添加 SCSS 支持，但我不会覆盖它——我相信使用预处理器是一种矫枉过正的做法，但如果你有一些可视化框架或其他东西——使用它们是可能的。

#### 3。将文件内容加载到 index.js 中，并删除不需要的字符。

接下来，在您的`index.js`中，您需要加载 CSS 并使用正则表达式删除换行符和注释。您可以使用文件系统模块，也可以导入图像文件。

```
const styles = fs
  .readFileSync("cognito.css", "utf8")
  .replace(/(\r\n\t|\n|\r\t)/gm, "") //remove newlines
  .replace("/*.+?*/"); // remove comments

const image = fs.readFileSync(imageFile); 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。现在您需要配置和访问 Cogito API。记得使用正确的地区！

```
const cognitoISP = new AWS.CognitoIdentityServiceProvider({ region: 'region of your cognito user pool' }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 5。下一步——准备好所有的参数并调用 API

```
const params = {
  UserPoolId: 'your-user-pool-id',
  CSS: styles,
  ImageFile: new Buffer(image)
};

cognitoISP.setUICustomization(params, (err, data) => {
  if (err) console.log(err, err.stack); // error 
  else console.log(`Successfully updated, new css version: ${data.UICustomization.CSSVersion}`); // successful response
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 就是这样，

现在，只需进入您的终端并使用`node index.js`，脚本将更新您的 Cognito UI 样式和徽标图像。这样，您可以轻松地管理您的风格，并在不同的环境中使用它们，而不必手动上传它们-您甚至可以在您的 CI/CD 流程中使用它们！甚至有更多的选择，例如在 Cognito 中为不同的应用程序客户端使用不同的风格等等。

一些你可能觉得有用的链接:
[https://docs . AWS . Amazon . com/AWSJavaScriptSDK/latest/AWS/cognitoidentityserviceprovider . html](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/CognitoIdentityServiceProvider.html)
[https://docs . AWS . Amazon . com/AWSJavaScriptSDK/latest/AWS/AWS/cognitoidentityserviceprovider . html # setUICustomization-property](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/CognitoIdentityServiceProvider.html#setUICustomization-property)
[https://docs . AWS . Amazon . com/cognito/latest/developer guide/cognito](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-ux.html)