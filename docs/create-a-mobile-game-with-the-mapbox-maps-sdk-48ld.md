# 用 Mapbox Maps SDK 创建一个手机游戏

> 原文：<https://dev.to/langsmith/create-a-mobile-game-with-the-mapbox-maps-sdk-48ld>

借助我们的 iOS 和 Android 地图软件开发套件，您可以为您的手机游戏创建自定义地图界面。作为一个例子，我用我们的 Android 版地图 SDK 制作了一个简单的地理问答游戏。别笑，它现在的名字叫“Tapper GeoChallenge”。是的，这是我能想到的最好的名字。我已经在 GitHub 上公开了代码[，所以你可以复制这个项目或者用它来构建新的东西。](https://github.com/mapbox/mapbox-geography-quiz-demo)

[![](../Images/1dd255065f126513071038a9ce2179c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XzWq3vH5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AoJwqztAZH1atbacPf5Xxfw.jpeg)

下面是 [Tapper GeoChallenge](https://github.com/mapbox/mapbox-geography-quiz-demo) 的工作方式:

在一个单人游戏中，应用程序随机选择一个城市，用户猜测它在哪个国家。如果猜错了，app 会返回玩家的猜测与目标城市的距离:

[![](../Images/b32b73693f341007974b71af248c0d66.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_M_27Esn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AdsGi8K6w2ThYUUrX.gif)

在双人游戏中，每个玩家都有机会猜猜目标城市所在的国家，每猜对一个都会获得分数。

[![](../Images/2125633859b67e6a2390d4e03cddd626.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mbqixj0U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AWVcx_aaaiMk5gU4unRPAiA.gif)

整个游戏存在于一个屏幕和一张使用我们的[户外风格](https://www.mapbox.com/maps/outdoors/)的地图上。这款游戏只使用了 Android 版地图软件开发套件的功能，以及一些[草皮方法](https://github.com/mapbox/mapbox-java/tree/master/services-turf/src/main/java/com/mapbox/turf)来计算距离。最复杂的代码基本上是跟踪轮到谁了，以及每个玩家对应哪个标记/地图点击。试试看！

访问游戏的 [Github 库](https://github.com/mapbox/mapbox-geography-quiz-demo)，按照自述文件的安装说明，下载代码，开始在 Android Studio 中开发自己的版本。以下是一些改进的想法:

*   使用不同的位置列表进行猜测

*   你可以使用[维基数据 API](https://www.wikidata.org/wiki/Special:ApiSandbox) 来检索世界各地的位置，而不是将 GeoJSON 城市列表存储在应用程序的 assets 文件夹中(这个文件夹*对于离线游戏来说*很棒)。或者你可以[使用你的工作室账号](https://www.mapbox.com/studio/styles/)创建一个 GeoJSON 数据集，然后使用[我们的数据集 API](https://www.mapbox.com/api-documentation/#datasets) 来检索文件。

*   创建使用不同城市列表的简单、中等和困难模式。轻松模式=仅世界上 100 个最大的城市，等等。

*   使用你的工作室账号为游戏创建自定义地图风格。如果你想要令人敬畏的预制样式，请查看[我们的设计师地图样式](https://www.mapbox.com/designer-maps/)。

*   使用我们的[方向 API](https://www.mapbox.com/api-documentation/#directions) 将游戏变成里程猜测游戏。例如，从伊斯坦布尔到里斯本需要行驶多少英里？东京到大阪？

*   用不同的图像替换玩家和靶心标记图标。探索我们的[新标记操场](https://www.mapbox.com/help/marker-playground/)进行实验。

使用[Maps SDK for Mobile](https://www.mapbox.com/mobile/)将地图整合到您的手机游戏中。如果你想在 3D、AR 或主机游戏中创造身临其境的体验，那么请查看我们的[地图 SDK for Unity](https://www.mapbox.com/unity/) 。让我们知道你在 Twitter 上建了什么， [#BuiltWithMapbox](https://twitter.com/search?f=tweets&vertical=default&q=%23BuiltWithMapbox&src=tyah) 。