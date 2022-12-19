# 欢迎来到 Mapbox 开发者社区，骄傲风格

> 原文：<https://dev.to/mapbox/welcome-to-the-mapbox-developer-community-pride-style--2md4>

[![](../Images/dd3185c564b11fe714c1166e7d778f19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1oHXsxpH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_ED5911CE99F9C0991DC8A2C060D49FE063DBECB050275BAEF1A1A4F3356F132D_1529008598900_image.png)

我们刚刚在旧金山结束了我们的第一次开发者活动， **Locate** ，在那里我们与我们全球社区的 1，400 名成员进行了联系，并进行了一次绝对的讨论。

在拥挤的两天里，我们发现🔥新产品和合作伙伴关系:

*   [React 原生 AR 和 scene kit SDK](https://blog.mapbox.com/introducing-native-ar-b3c55a50539d)用于增强现实
*   [Vision SDK](https://blog.mapbox.com/vision-sdk-d47486656821) 与 Arm 和 Microsoft Azure 合作
*   GL 自定义图层 API 与[优步的 kepler.gl 团队合作](https://blog.mapbox.com/hello-mapbox-and-keplergl-4d71fc8e1d02)

[![](../Images/e9a6226ae7d9dd307045abfbcc35492a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VvShx0lx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E5C1DAF6D976E65C3B405912D77394ADD81E818BCC902DF11BE371061FF7071_1529087946578_Baran%2Bdancing%2Bastronaut%2Bunity.gif)

<center>*[@brnkhy's](https://twitter.com/bradleege/status/1006756603071291395) dancing astronaut, built with the Mapbox Unity SDK*</center>

作为一家平台公司，我们通过我们的用户生活，难以置信的是，我们遇到了这么多来自全球不同行业的开发人员，他们正在开发惊人的产品，帮助我们探索、移动和连接。

我们学到了什么？

我们了解到**社区对资源和技术升级的胃口很大** -工程代码实验室会议的每个座位在开始前 20 分钟就被占满了，像 Damon 的“Hackbox -创造性误用案例”和 Nadia 的“Mapbox API 101”这样的会议几乎赢得了起立鼓掌。所以本着不断学习的精神..

### 咱们造个东西吧！🌈

* * *

<center>*If you just want to dive in, start here:*</center>

<center>[Create a free developer account](https://www.mapbox.com/signup/?utm_source=junedevto&utm_medium=devto&utm_content=signup-page&utm_campaign=junedevto)
[API documentation](https://www.mapbox.com/api-documentation/?utm_source=junedevto&utm_medium=devto&utm_content=api-docs&utm_campaign=junedevto)
[Step-by-step tutorials](https://www.mapbox.com/help/tutorials/?utm_source=junedevto&utm_medium=devto&utm_content=tutorials&utm_campaign=junedevto)</center>

* * *

为了庆祝 6 月成为骄傲月，我想分享一下如何用我们的制图工具 **Studio** 为这一时刻打造一种特殊风格:

[![](../Images/95252023aefe9602749231c270a71a06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--le2gnSe5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_ED56498C13794E52595D029436FCC69D0132489ED92C0210B052B02F64F464A7_1528912024390_image.png)

使用 Studio，开发人员和设计人员可以上传数据并设置数据样式，从而创建可完全控制每一层的平铺矢量地图。首先，[创建一个免费账户](https://www.mapbox.com/signup/?utm_source=junedevto&utm_medium=devto&utm_content=signup-page&utm_campaign=junedevto)并查看关于[如何使用地图盒子的文档](https://www.mapbox.com/help/how-mapbox-works-overview/?utm_source=junedevto&utm_medium=devto&utm_content=overview&utm_campaign=junedevto)。

制作您自己的主题骄傲游行路线从在[数据集编辑器](https://www.mapbox.com/help/studio-manual-datasets/)中将路线描绘成一条线开始，该编辑器会创建一个包含路线上所有点的经度和纬度的 GeoJSON。将数据集导出到 Tileset，并作为图层*添加到您选择的样式中。在[样式编辑器](https://www.mapbox.com/help/studio-manual-styles/)中，上传一个类似于的彩虹 SVG [到你的图像面板，选择 SVG 作为你的`pattern`线条图层，设置宽度为 20px 可见性:](https://gist.github.com/ErinQuinn/eef75f2e325db744d5ca902f57eff390)

[![](../Images/fc44babcb8bde8773fd45c77b08a0cf8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UlT0vV4X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_ED56498C13794E52595D029436FCC69D0132489ED92C0210B052B02F64F464A7_1528921438199_image.png)

> ** tileset vs Datasets，简而言之，tileset 是一种有损格式(数据被分块成小块并捕捉到最近的像素)，而 dataset 可以编辑，但不太适合可视化。数据集- > Tilesets。*

如果您正在处理多条游行路线，请为每条路线创建唯一的数据集，并利用批量编辑(`shift`键允许您选择多个图层)将它们一起样式化，如上面的截图所示。

当您通过添加和移除图层设计了您喜欢的样式后，单击`publish`并前往[共享、开发和使用](https://www.mapbox.com/help/studio-manual-publish/#share-develop--use-page)页面，在此您可以获取一个独立的共享 URL 以在浏览器中浏览地图。要用你的风格构建一个交互式应用程序，从 [Mapbox GL JS](https://www.mapbox.com/mapbox-gl-js/example/simple-map/?utm_source=junedevto&utm_medium=devto&utm_content=gl-js&utm_campaign=junedevto) 示例中复制风格 ID 和一些 JavaScript 启动代码。您可以添加交互性，如[弹出窗口](https://www.mapbox.com/mapbox-gl-js/example/popup-on-click/)、[动画](https://www.mapbox.com/mapbox-gl-js/example/animate-a-line/)，甚至彩虹[线条渐变](https://www.mapbox.com/mapbox-gl-js/example/line-gradient/):

[![](../Images/f80258a1feafe69e620236941207830f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b9ltlfQb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_ED56498C13794E52595D029436FCC69D0132489ED92C0210B052B02F64F464A7_1529001059036_Screen%2BShot%2B2018-06-14%2Bat%2B2.29.09%2BPM.png)

这就是你要的——项目成功！现在你可以自信地加入我们(刚刚宣布的)[世界地图杯](https://www.mapbox.com/world-map-cup/?utm_source=junedevto&utm_medium=devto&utm_content=world-map-cup&utm_campaign=junedevto)挑战赛，赢取 DJI Mavic Air 无人机、定制 3D 激光打印地形木质地图、 [@AmyLee 的](https://twitter.com/amyleew)地图设计指南等奖品。

## 保持连接

如果你是一个[开源贡献者](https://github.com/mapbox)，一个自由职业者，在我们的[开发者网络中出现](https://www.mapbox.com/developer-network/?utm_source=junedevto&utm_medium=devto&utm_content=dev-net&utm_campaign=junedevto)，或者下一个一百万以上的开发者用 Mapbox 建立位置体验，加入我们吧！

我们是 Twitter 上的 [@Mapbox](https://twitter.com/mapbox) ，所以，很高兴在 Dev.to 这里摇摆。