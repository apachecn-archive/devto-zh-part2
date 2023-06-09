# 构建一个无服务器的“Hello World”函数

> 原文：<https://dev.to/maxkatz/build-a-serverless-hello-world-function-g1m>

[无服务器](https://en.wikipedia.org/wiki/Serverless_computing)、功能即服务(FaaS)或者仅仅是云功能允许你编写将在云中运行的代码。您可以使用多种不同的语言来编写函数代码，如 JavaScript (Node.js)、Swift、Python、Java、PHP 等。好的是不需要担心服务器、容器、部署等。你写代码，云平台会确保它执行！

在这篇博文中，你将学习如何构建一个 [Hello World](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program) 函数。您将使用 [IBM Cloud Functions](https://console.bluemix.net/openwhisk/) 来构建和运行这个函数(更多信息在最后)。现在，让我们跳到创建您的第一个函数。

### 创建并测试云函数

在本节中，您将创建并测试一个新函数。

1.  [注册免费的 Lite IBM Cloud 账户](http://bit.ly/2znrg5V)或[登录现有账户](http://bit.ly/2znrg5V)
2.  点击**目录**
3.  移除**标签:lite** 过滤器，点击**进入**更新服务列表
4.  键入**函数**并点击输入
5.  点击**功能框**

<figure>[![cloudfunctions-catalog](img/da5a6c37a3b7b49f9881ab58a2b548ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xTeDC4qZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/cloudfunctions-catalog.png%3Fw%3D1024%26h%3D445) 

<figcaption>创建新功能</figcaption>

</figure>

1.  点击**开始创建**按钮
2.  点击**创建动作**选项
3.  输入 **helloworld** 作为**动作名称**。
    1.  保持**封装包**的默认值。
    2.  对于**运行时**，也保持 **Node.js** 的默认值
4.  点击**创建**按钮。将加载一个带有函数代码的代码编辑器，如下所示:

<figure>[![clounfunctions-hello-world-created](img/6d4a6c3d19e6b0b8bfd6fb629bf915d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PIJwhQ7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/clounfunctions-hello-world-created.png%3Fw%3D1024%26h%3D243) 

<figcaption>功能码</figcaption>

</figure>

*   您没有使用模板，但即使是默认函数也带有最基本的 JavaScript 代码，它返回 **Hello World** 。
*   该功能称为**主**。
*   **params** 对象允许将参数传递给函数。我将在另一篇博文中讨论这个问题。
*   **return** 返回一个带有函数响应的 JSON 对象。在这种情况下，它只是一个简单的文本。下一步是测试功能。
    1.  点击**调用**按钮(在编辑器的右上角)。结果应该是这样的:

<figure>[![cloudfunctions-invoke](img/f899e47bdca439f5a7096aab44632bc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FSRU2fAI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/cloudfunctions-invoke1.png%3Fw%3D1100) 

<figcaption>功能响应</figcaption>

</figure>

您刚刚创建了一个新的云函数(您没有编写任何代码，但是现在还可以)，并且您还能够在云中测试该函数。

您很可能希望将这个函数作为 REST API 来调用。幸运的是，这很容易做到。

### 作为 REST API 调用

在本节中，您将调用这个函数作为 REST API。

1.  在左侧，点击**端点**
2.  在 **CURL** 部分，您将看到一个 CURL 命令来调用这个函数。点击**眼睛图标**显示用户名/密码。基本认证用于保护该功能

<figure>[![cloudfunctions-api](img/f9dac7b0e688dbdca1f8efa0f74d3be4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3qmk4yOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/cloudfunctions-api.png%3Fw%3D1024%26h%3D126) 

<figcaption>收拢命令</figcaption>

</figure>

1.  点击**复制图标**复制卷曲命令
2.  打开一个**终端**窗口，粘贴 curl 命令。您应该看到来自 API(函数)的完整 JSON 响应:

<figure>[![cloudfunctions-json-formatted](img/4e39fd5aaf49aacca5c2b715955673c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ztu3DTQi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/cloudfunctions-json-formatted.png%3Fw%3D1024%26h%3D378) 

<figcaption>函数响应(JSON 格式化为 jsonlint.com)</figcaption>

</figure>

也可以直接从浏览器地址栏调用 API 函数。为此，请将 URL 格式化为以下格式:

```
https://username:password@openwhisk.ng.bluemix.net/api/v1/namespaces/maxkatzorg\_dev/actions/hello?blocking=true 
```

Enter fullscreen mode Exit fullscreen mode

用实际值替换**用户名**和**密码**。当您点击**眼睛图标**时，您将能够看到这些数值。下面是它在 Chrome 上运行时的样子:

<figure>[![cloudfunctions-invoke-browser](img/1b6d7dce1f380c0f2a74b78bf29e0c8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5wBVQP59--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/cloudfunctions-invoke-browser.png%3Fw%3D1024%26h%3D94) 

<figcaption>从浏览器调用</figcaption>

</figure>

### 作为 Web 动作调用

在本节中，您将学习如何将该函数作为 Web 操作来调用。Web 操作非常类似于您在上一节中调用的 API，只是没有身份验证。在 Web 操作中，身份验证取决于开发人员。

1.  点击**端点**标签
2.  选中**启用为 Web 动作**复选框
3.  点击**保存**按钮
4.  复制 **URL** ，然后粘贴到浏览器地址栏中。您应该会在浏览器中看到:

```
{  "message":  "Hello World"  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用最新版本的 Firefox，您应该会看到此输出的格式化版本(由于 Firefox 的内置工具)

### IBM 云功能

在这篇博文中，我使用 IBM Cloud Functions 创建了云函数。IBM Cloud Functions 基于流行的开源项目 [Apache OpenWhisk 项目](https://openwhisk.apache.org/)。

### 总结

在这篇博文中，你学习了如何构建你的第一个云函数。它又快又简单。现在，这个领域并不新鲜(正如大多数人可能认为的那样)。在基于云的环境中，你可以执行代码，这已经成为了[后端即服务](https://en.wikipedia.org/wiki/Mobile_backend_as_a_service) (BaaS)或[移动后端即服务](https://en.wikipedia.org/wiki/Mobile_backend_as_a_service) (mBaas)类型公司的一部分。像 [Parse](http://parse.com) (被脸书收购，然后开源) [StackMob](http://stackmob.com) (被 PayPal 收购) [Kinvey](http://kinvey.com) (被 Progress 收购) [Appery.io](http://appery.io) 这样的公司提供(Appery.io 和 Kinvey 今天提供)在云中编写和执行代码的选项，而不用太担心服务器。你确实有一些资源限制和不同的定价结构。这是另一篇博文的主题。