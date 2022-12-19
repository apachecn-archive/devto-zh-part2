# 我会给你看所有发布的 iOS 应用程序的源代码！

> 原文：<https://dev.to/ryutamiyamoto/i-will-show-you-all-the-source-code-of-released-ios-application-2a03>

# 我给你看所有发布的 iOS 应用的源代码！

# 简介

这一次，我考虑发布应用程序的源代码，原因如下。

*   我想让很多人看到我做的应用。
*   我想听听各种人的意见。
*   我想通过分享代码来帮助那些制作应用程序的人。

希望这篇文章对大家有用😃

# 应用程序的介绍

Innovy(创新)。这是一款面向 20 至 30 岁(主要是男性)的新闻应用程序，他们正在安装天线，收集最新服务和技术的信息。
※我起了一个想象中的创新者(innovator)的名字。

[![AppIcon 1024 _ 1.jpg](../Images/b47111d62840d3febbd18b7183d291c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ur6IOfTX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/160811/203eb665-a087-c7d5-d762-8ec5546e770e.jpeg)

你可以通过流派和用户的反应看到最新的新闻。
而且，因为有新闻读出功能，还可以边走边查新闻。
App 网址:【https://itunes.apple.com/us/app/Innovy/id1319179144 T2】

### 截图

[![IMG_5140_4.jpg](../Images/eeae8052c470474aecc2ace5b9f7a36d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PlCpTRoY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/160811/ed3c3726-c922-6634-0da7-3d21050a8e1b.jpeg)

#### ①新闻列表屏幕

你可以在应用程序的首页查看各种类型的新闻。
点击新闻，使用详细信息屏幕、水平滑动来更改类型。

#### ②新闻列表屏幕(长按新闻)

长按新闻后，从使用“从这个位置轻按”轻按选择新闻的地方开始大声朗读。

#### ③新闻详情屏幕(阅读文章)

你可以查看新闻的细节。在这个屏幕上，你可以查看关于新闻剪辑和新闻的推文。

#### ④推文列表屏幕

您可以查看列表中选定新闻的推文。您可以按下 tweet 按钮来发送推文。

#### ⑤文章搜索屏幕

你可以搜索过去的新闻。

#### ⑥设置屏幕

您可以查看当前位置的天气预报，并更改应用程序的设置。

# 启动公共源代码和应用程序

首先，我访问下面的 URL。

[https://github.com/RyutaMiyamoto/Innovy](https://github.com/RyutaMiyamoto/Innovy)

`Clone or download` → `Download ZIP`下载项目。
请解压下载的 zip 文件。
[![Screenshot 2018-06-02 13.38.55.png](../Images/62a9d11675e99527e1fc41ce6f14216c.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--_c_gBAeZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/160811/d153df5f-e3f6-d8f3-8390-618ce33f4cb4.png)

使用以下 cocoaPods 安装库。
请参考[另一篇文章](https://qiita.com/ShinokiRyosei/items/3090290cb72434852460)如何安装 cocoaPods。
安装 cocoaPods 后打开终端，进入 Innovy-master 文件夹，输入`pod install`命令，等待库安装完成。

安装完库后，打开 Innovy - master 文件夹，打开`likeNews.xcworkspace`。
推荐环境:XCode 9.3

[![Screenshot 2018-06-02 13.40.00.png](../Images/71c72106d9f4424e5926c57725dcc0bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jM5ZVtRn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/160811/9525f431-af88-134c-0176-e2a9476f4dcd.png)

打开 XCode 后，用`iOS Simulators`选择合适的终端，按下`Run button`。

[![Screenshot 2018-06-02 13.50.29.png](../Images/61d770a35352d9b401f8a654fd4b97fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--givgRRCv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/160811/9adea365-f29c-8ab6-5d8c-e98f350de534.png)

模拟器和应用程序启动时成功。
当然，如果您设置了预配置文件，也可以在真机中操作。

[![Screenshot 2018-06-02 14.33.22.jpg](../Images/d46670975c86c4c104a9e61d2e1bd6e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zDmhSigR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/160811/b8009f75-b0d8-8c12-3b0d-e7769012f20b.jpeg)

有次数限制的 API 的 API 键。被屏蔽，所以有些不会显示。如果您单独与我们联系，我们会传递被遮盖的物品🙃

*   然而，我将把它限制在那些对自己的身份(脸书等)很清楚的人身上。).

# 架构介绍

Innovy 采用 [MVVM(模型-视图-视图模型)](https://en.wikipedia.org/wiki/Model_View_ViewModel)作为架构模式？

例如，当用户在新闻列表屏幕上执行重新获取最新新闻的操作(向上拉屏幕)时，它如下移动。

*   考虑代码中的`NewsListViewController`为`View`，`NewsListViewModel`为`ViewModel`，`NewsListModel`为`Model`。

[![Architecture.jpg](../Images/7baa30f197d372edf7ac1b3005ba58df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k_0RsGQn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/160811/29efe10f-5caa-8af5-7bea-b1334f2b346b.jpeg)

①.`View`检测？用户的更新操作
②。`View`向`ViewModel`请求更新进程
③ `ViewModel`向`Model`请求获取新闻列表
④。`Model`使用新闻列表获取 API 获取最新新闻
⑤。`Model`通知`ViewModel`新闻采集完成
⑥。根据从`Model`
⑦获得的新闻列表`ViewModel`创建用于`View`显示的数据。`ViewModel`通知`View`数据创建完成
⑧。`View`根据`ViewModel`信息更新屏幕

因此最新的新闻信息会反映在应用程序中😉

# 应用中使用的库介绍

### [R .斯威夫特](https://github.com/mac-cain13/R.swift)

因为可以获取图像文件名和故事板名称作为属性，所以它防止了由于键入错误而导致的错误。
图像文件名，警告信息？到处都在使用。

### [阿拉莫菲尔](https://github.com/Alamofire/Alamofire)

一个用于促进 HTTP 通信的库。
大家都在用它进行交流的 iOS 应用开发，不是吗？
用于 API 通信部分。

### [SDWebImage](https://github.com/rs/SDWebImage)

只要为 UIImageView 指定一个 URL 就会显示图像，加载显示，缓存它就会缓存。
用于文章图像的显示部分。

### [拆箱](https://github.com/JohnSundell/Unbox)

一个解析 Json 的库。
用于检索 API 获取的 Json，以便在应用程序内部使用。

### [境界](https://github.com/realm/realm-cocoa)

应用程序版本数据库。数据访问语法非常容易理解。
用于存储物品信息。

### [CTFeedback](https://github.com/rizumita/CTFeedback)

一个可以轻松创建查询屏幕的库。
用于查询屏幕。

### [SVProgressHUD](https://github.com/SVProgressHUD/SVProgressHUD)

用于在屏幕前显示代表处理的指示器的库。
获取商品信息时使用。

### [HockeySDK](https://github.com/bitstadium/HockeySDK-iOS)

当应用程序崩溃时收集崩溃报告的库。
启动时设置。

### [火基](https://github.com/firebase/)

应用程序通用搜索库，如应用程序使用情况调查、崩溃报告查看、推送通知？
启动时设置。

### [【nedsdk _ IOs】](https://github.com/fan-ADN/nendSDK-iOS)

展示广告的图书馆。
展示物品时使用。

# API 介绍

## 文章信息采集 API(自制)(likeNews/getNews.php)

用于检索文章信息的 API？
显示列表时使用。

## 流派信息获取 API(home brew)(like news/get Genre . PHP)

获取流派信息的 API？
流派包括“创业”、“服务”、“设计”等。
启动时使用。

## 文章文章评分添加 API(家酿)(like news/update newscore . PHP)

API 来添加你看到文章细节的次数。
点击文章列表中的任意文章，并在打开文章详情时使用。
这样，当您显示文章时，您可以显示“其他人正在关注这篇文章！”？

## [推文信息采集 API](https://api.twitter.com/)

从 Twitter 获取 tweet 信息的 API 来获取
文章详情页？当您点击底部的 tweet 按钮时，会显示一个 tweet 列表，但它是在获取列表时使用的。

## [气象信息采集 API](https://openweathermap.org/)

获取天气信息的 API。通过指定纬度和经度，可以获得该地的天气信息。
在显示设置屏幕上显示的天气信息时使用。

## [位置信息检索 API](https://geoapi.heartrails.com)

？获取位置信息的 API。如果指定纬度和经度，就可以用日语获得那个地方的地址。
由于天气信息 API 只能获取英文地址，我们将使用该 API 单独获取地址。
配合气象信息 API 使用。

最后

感觉怎么样？

阅读本文并打开您的 iOS 应用程序

原文:
[https://qiita.com/ryupaka/items/ea272c843d3ebf3b245b](https://qiita.com/ryupaka/items/ea272c843d3ebf3b245b)