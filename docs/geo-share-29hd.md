# 戈沙尔

> 原文：<https://dev.to/webreflection/geo-share-29hd>

[![map demo](img/40efee49d8f1177666c10f042bcbc749.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8czPBZhv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m1obqrp7ieb06c725f2k.png)

# 我造了什么

想象一下，你想和你的朋友见面，但你在一个开放的，也许是拥挤的空间，所以除了分享你自己的位置，你还想看看其他人在哪里，最终在地图上汇聚到一个地方，而不需要安装任何本地应用程序。

这是 Geo Share，一个 PWA，在这里你既可以是主人，也可以是与你共享唯一 URL 的任何其他人的客人。

## 演示链接

只需访问 [geoshare.now.sh](https://geoshare.now.sh/) 即可成为主持人。

一旦你在地图上找到自己，点击右上角👥图标，你将在你的设备剪贴板上有一个独特的网址，你可以粘贴到你的朋友。

就是这样，任何使用该链接的人都会看到其他人知道该链接，并且只要你在使用该应用程序，还会看到你是该群的主持人(黑色图标)。

## 链接到代码

[https://github.com/WebReflection/geoshare](https://github.com/WebReflection/geoshare)

# 我是如何建造的

*   [Zeit now-CLI](https://zeit.co/now)实现有史以来最简单的部署！
*   [传单](https://leafletjs.com)地图库位于 [OpenStreetmap](https://www.openstreetmap.org/) 图块之上
*   [在 longpress 上使用名称](https://nominatim.openstreetmap.org)进行反向地理编码(共享来自主机的工具提示)
*   [推送通道 API](https://pusher.com/channels?utm_source=dev.to&utm_medium=referral&utm_campaign=Devtocontest) 用于实时更新和通信
*   [NoSleep](https://github.com/richtr/NoSleep.js?utm_source=recordnotfound.com#nosleepjs) 实用程序(希望)阻止设备睡眠
*   [用于布局渲染和更新的超 HTML](https://github.com/WebReflection/hyperHTML#hyperhtml)
*   [lightdown](https://github.com/WebReflection/lightdown#lightdown) 为每条信息带来快速、安全和有用的降价风格

# 附加资源/信息

剩下的代码是我写的，基于 [ES 模块](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)，通过 [asbundle](https://github.com/WebReflection/asbundle#asbundle) 捆绑，通过 [Babel](https://babeljs.io) 净化，通过 [UglifyJS](https://github.com/mishoo/UglifyJS#uglifyjs--a-javascript-parsercompressorbeautifier) 缩小。

常见的 PWA 技术如[服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)和 a `manifest.json`也已到位。