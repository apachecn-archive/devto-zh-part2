# Vue PWA 教程:如何使用 Vue.js 构建高效的渐进式 Web 应用程序

> 原文：<https://dev.to/developerayo/vue-pwa-tutorial-how-to-build-efficient-progressive-web-apps-using-vuejs-5fdo>

## 介绍在 Vue 中构建渐进式 Web App。JS {第一部分}

[![alt text](img/28991b2f44aa45524c1d22b033fb6571.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DJ--MB3_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/fremer.com.ng/blog/wp-content/uploads/2018/09/vuejs1.jpg%3Fw%3D725%26ssl%3D1%2522Vuejs%2522)

PWA 或渐进式网络应用正在迅速成为数字世界的一种全球趋势。这个 Vue PWA 教程的目的是让你开始学习如何用一个最流行的 JavaScript- VueJS 框架来构建你自己的渐进式 Web 应用程序(PWA)。

什么是 PWA？渐进式 Web 应用程序是 Web 应用程序的移动高效版本。或者基本上是一个类似移动应用程序的网站。

这是一个易于访问的移动版网站。

在应用程序或网站的全功能版本难以运行的领域或条件下，PWA 会表现得更好。

PWA 的一个很酷的例子是 twitter 的“精简”版本。渐进式网络应用程序的一个超级酷的地方是没有长时间的安装过程。你只需要“添加到主屏幕”。

我们通常会看到 PWA 的安装过程，作为无关紧要的事情省略掉。

一个有效的例子是，当你在移动智能手机上访问 twitter 时，例如 android 手机，你很可能会看到一个弹出窗口，上面写着“添加到主屏幕”。

PWA 安装中涉及的整个安装过程只需要用户单击“Ok”按钮。

完成后，你会注意到你的启动器上有一个小图标，类似于你从 Playstore 或 Appstore 安装的其他应用程序。

PWA 的另一个非常惊人的优势是，它们是跨平台的，即它们可以在不同的设备上运行，Android 和 IOS 都一样，这是因为它们是基于浏览器的——它们利用设备浏览器进行操作。

从开发者的角度来看，渐进式网络应用程序比本地应用程序更有优势。因为渐进式网络应用程序是基于网络的；

渐进式 Web 应用程序可以在任何期望的框架中编写；
一个代码统治他们所有人:它是跨平台和跨设备的(因为代码是由用户的浏览器执行的)。
易发货:无需经过通过 Playstore 或 Appstore 商店下载的过程。
由于 Manifest {manifest.json}和 Service Workers {sw.js，service-worker.js}，PWA 能够被搜索引擎发现和索引，这可能有助于提高该应用的受欢迎程度。
什么是 Manifest.json？

渐进式 Web 应用程序的示例 manifest.json 文件

`{
"name": "PWA Tutorial From Fremer Inc",
"short_name": "Fremer Inc",
"icons": [{
"src": "img/icon.png",
"sizes": "62x62",
"type": "image/webp"
}, {
"src": "img/icon2.png",
"sizes": "64x64"
}, {
"src": "img/icon3.png",
"sizes": "128x128",
"density": 2
}],
"scope": "/pwa/",
"start_url": "/pwa/start.html",
"display": "fullscreen",
"orientation": "landscape"
}`

Progressive Web App Manifest 是一个 JSON 文档，它在构建 PWAs 时提供应用程序数据，如“名称”、“简称”、“图像/徽标”等数据...

什么是服务人员？Service workers 是 chrome 浏览器在后台获取并运行的一个脚本，完全独立于网页，为不需要网页或用户交互的功能打开了大门。例如推送通知、后台同步、离线支持等。

Vue PWA 教程
在我们开始这个 Vue.js PWA 教程之前，在开始构建我们的渐进式 Web 应用程序之前，有一些重要的因素我们应该考虑。

在构建渐进式 web 应用程序之前，即使有像 Vue 这样复杂的技术和 Web 框架，也有很多因素需要考虑，以确保应用程序真正是渐进式的。

在所有情况下都保持站点响应性、加载时间快速且同步等因素，确保所有必要的资产都捆绑在一起，并尽可能地最小化

如何用 Vue.js 创建 PWA
用 vue.js 创建 PWA 的一个简单方法是创建一个 starter PWA 模板。

让我们开始吧，

`Npm install –g @vue-cli`

根据终端上安装的 vue 版本，您可能需要将 vue 安装更新到至少 vu3 3.0。当我尝试创建我的第一个 vue 应用程序时，我被要求通过输入

`npm uninstall –g @vue.cli`

然后，我通过输入以下命令重新安装到当前版本

`npm install –g @vue/cli`

安装并运行 vue 后，我们可以...

在 [Vue PWA 教程继续阅读{继续阅读！}](https://fremer.com.ng/blog/vue-pwa-tutorial)