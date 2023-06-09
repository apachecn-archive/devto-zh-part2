# 如何从云函数调用外部 REST API

> 原文：<https://dev.to/maxkatz/how-to-invoke-an-external-rest-api-from-a-cloud-function-2e0>

在之前的一篇博文中，我展示了如何创建你的第一个云函数(外加一个[视频](https://dev.to/maxkatz/video-build-your-first-cloud-function-3b4b-temp-slug-162187))。很可能您的云函数需要调用一个外部 REST API。下面的教程将向你展示如何创建这样的函数(非常简单)。

1.  [登录 IBM 云账户](http://bit.ly/2znrg5V)
2.  点击**目录**
3.  去掉**标签:lite** 滤镜，键入 ***功能***
4.  点击**功能框**
5.  点击**开始创建**按钮
6.  点击**创建动作**
7.  对于**动作名称**，输入 **ajoke** 并点击**创建**按钮。一个新的云函数将被创建，带有 **Hello World** 消息
8.  用以下代码替换函数代码，该代码调用第三方 REST API，该 API 返回一个随机笑话:

```
var request = require("request"); 
function main(params) { 
   var options = { url: "https://api.icndb.com/jokes/random", json: true }; 

   return new Promise(function (resolve, reject) { 
      request(options, function (err, resp) { 
         if (err) { 
            console.log(err); 
            return reject({err: err}); 
         } 
         return resolve({joke:resp.body.value.joke}); 
      }); 
   }); 
} 
```

Enter fullscreen mode Exit fullscreen mode

*   代码很简单。它使用**请求** Node.js 包来连接外部 REST API
*   外部 REST API 返回一个随机笑话
*   JavaScript [Promise](https://developers.google.com/web/fundamentals/primers/promises) 用于调用 REST API
*   最后，云函数以 JSON 格式返回一个响应
    1.  现在点击**保存**按钮保存代码。代码保存后，按钮将变为**调用**。单击按钮调用该功能。在右边的面板中，您应该看到带有随机笑话的输出:

```
{  "joke":  "Project managers never ask Chuck Norris for estimations... ever."  } 
```

Enter fullscreen mode Exit fullscreen mode

这是它在 IBM Cloud Functions 编辑器中的外观:

<figure>[![cloudfunctions-invoke-restapi](img/03177545a3fe161d8f2e598286b96db4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XIkiVLED--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/cloudfunctions-invoke-restapi.png%3Fw%3D1024%26h%3D504) 

<figcaption>云功能码</figcaption>

</figure>

当然，您也可以使用 CLI 构建和测试云功能。我将在另一篇博文中讨论这个问题。

现在，让我们将这个云函数公开为 REST API，这样我们就可以在控制台之外调用它。事实上，一旦我们将它变成一个 [Web 动作](https://console.bluemix.net/docs/openwhisk/openwhisk_webactions.html)，你将能够直接从浏览器调用它。

1.  在左侧，点击**端点**
2.  勾选**启用为 Web** 动作，点击**保存**
3.  复制 URL 并输入浏览器的地址栏

这是它在 Firefox 中的样子:

<figure>[![cloudfunctions-test-browser](img/26cf694a5af73279c0661cb6f1d77cdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--trs4UiFX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2018/07/cloudfunctions-test-browser.png%3Fw%3D1024%26h%3D179) 

<figcaption>调用云函数</figcaption>

</figure>

很简单，对吧？

在这篇博文中，你学习了如何创建一个调用外部(第三方)API 的云函数。很可能即使是最简单的应用程序也需要从外部 API 获取数据，所以这是一个很好的例子/模板。