# 使用 NSFAI 和 Clarifai 在您的应用中实现 NSFW 检测。

> 原文：<https://dev.to/mogery/implementing-nsfw-detection-into-your-app-with-nsfai-and-clarifai-27bk>

NSFAI 被设计成作为一种“事后想法”易于实现。让我告诉你怎么做！

## 步骤 1 -在 Clarifai 上设置

在 [Clarifai](https://clarifai.com) 创建并登录您的帐户后，将鼠标悬停在顶部栏中您的姓名上，然后点击应用程序。

[![clarifai applications](../Images/98ca1937f4afb49217ef7d209f042421.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hQ61AYOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bbyjins.skiilaa.me/img/nsfai/clarifai_applications.png)

点击创建新的应用程序，给你的应用程序一个名字，并将基本工作流程设置为 NSFW(可选)。默认语言不重要。

[![clarifai create app](../Images/bd1ee4b9a67ffc25e577b132fee41d2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z3F9YTNi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bbyjins.skiilaa.me/img/nsfai/clarifai_create_app.png)

点击创建应用程序后，进入侧边栏的 API Keys 页面。

[![clarifai api keys](../Images/104f71a736f1e874f25615269d402557.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zUU3mi51--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bbyjins.skiilaa.me/img/nsfai/clarifai_api_keys.png)

您已经可以看到一个 API 密钥，并且您可以使用它，但是您应该创建一个具有有限权限的新 API 密钥，这样，如果您的密钥泄露了，您可以撤销它，这样可以减少损失。

点击创建新的 API 密钥，并在应用程序下拉列表中选择您的应用程序。为您的密钥命名，并选择公共和自定义模型上的预测范围。这就是你所需要的。

[![clarifai create api key](../Images/0c28b4db8df3bd0b93cb13edabd15ede.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---3NYPV4x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bbyjins.skiilaa.me/img/nsfai/clarifai_create_api_key.png)

点击 Save Changes，复制你的新的和闪亮的 API 密匙。

[![clarifai api keys new](../Images/d9e77bfa9845b53e17651af82f11a2ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VHvXyFg_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bbyjins.skiilaa.me/img/nsfai/clarifai_api_keys2.png)

## 步骤 2 -设置国家自然科学基金

我们准备安装 NSFAI。可以从 npm:
抢

```
npm install -save nsfai 
```

Enter fullscreen mode Exit fullscreen mode

是时候打开你最喜欢的代码编辑器并开始工作了！

首先，您需要初始化 NSFAI 的一个实例:

```
var NSFAI = require('nsfai');

var nsfai = new NSFAI("CLARIFAI_API_KEY_HERE"); 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，我们可以开始预测！

## 第三步-预测图像

我们可以使用实例中的`predict()`函数来扫描我们的图像。

NSFAI 的一个突出特点是我们的数据解析器。它将 URL、数据 URL 和 Base64 数据转换成 Clarifai 友好的数据。所以你可以在 NSFAI 扔很多东西，它都会处理的恰到好处！

```
nsfai.predict("https://bbyjins.skiilaa.me/img/test.png").then(function(result) {
    if (result.sfw) {
        console.log(`This image is safe for work with a confidence of ${result.confidence}.`);
    } else {
        console.log(`This image is not safe for work with a confidence of ${result.confidence}!`);
    }
}).catch(function (error) {
    console.error(error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 幕间-图像结果

图像结果对象由两个属性组成:

*   这张照片对工作来说是安全的。(布尔型)
*   `confidence`:对结果的信心。(数字)(介于 0 和 1 之间)

示例:

```
{
    "sfw": true,
    "confidence": 0.973727
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步-预测视频

如果您使用数据 URL，数据解析器将自动检测视频，但如果您使用 URL 或 Base64 数据，您需要将`options`参数的`video`属性设置为`true`。

每秒只分析 1 帧。

```
nsfai.predict("https://bbyjins.skiilaa.me/img/test.mp4").then(function(result) {
    if (result.sfw) {
        console.log(`This video is safe for work with a confidence of ${result.confidence}.`);
    } else {
        console.log(`This video is not safe for work with a confidence of ${result.confidence}!`);
    }
}).catch(function (error) {
    console.error(error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 插曲-视频结果

对于每个分析的帧(也就是每秒 1 帧)，视频结果是图像结果对象的数组。

示例:

```
[
    {
        "sfw": true,
        "confidence": 0.973727
    },
    {
        "sfw": true,
        "confidence": 0.973727
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

就是这样！希望我能在国家自然科学基金上帮到你。我们正在寻找贡献者！如果您有兴趣在以下方面提供帮助:

*   向我们的数据解析器的 URL 添加视频检测
*   扩展数据解析器...或者别的什么，那就随便叉开一个 PR 吧！