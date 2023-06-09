# 通往艾滋病毒/艾滋病的道路-第一部分

> 原文：<https://dev.to/s1hofmann/-the-road-to-pwa---part-1-21gf>

> 这篇文章最初发布在[我的个人博客](https://blog.simon-hofmann.org/pwa/js/2018/05/07/road-to-pwa01.html)

正如我在
的上一篇帖子[中所说，我已经为我的婚礼建造了一座 PWA。
在这篇文章中，我还承诺会写一篇关于我的 PWA 的技术细节的更深入的文章。](https://blog.simon-hofmann.org/fullstack/pwa/js/python/flask/2018/04/21/wddng.html)

因此...

```
Promise.resolve(writeBlogPost()).then(post => readBlog(post)); 
```

Enter fullscreen mode Exit fullscreen mode

# 通往 PWA 之路

艾滋病毒感染者(希望)会留在这里。正如我在之前的帖子中提到的，原生应用的问题在于它们应该通过专用的应用商店来分发。为了能够通过这些商店分发应用程序，你需要注册一个开发者帐户，这需要一次性或定期付款。
(我确实知道开源应用商店，比如安卓版的 [F-Droid](https://f-droid.org/) ，但我确实认为大部分智能手机用户不知道。)

这正是 PWA 发挥作用的地方！

## 好吧，听起来很酷，但是**到底是什么**一个 PWA？

PWAs 最重要的一点是，它可以用先进的特性增强任何现有的 web 应用程序。PWA 特性无缝集成到现有应用程序中，当小心添加时，它们将在支持浏览器中可用，但也不会在不支持它们的浏览器上引起任何差异或错误。

在我看来，构成 PWA 的三个核心构件如下:

1.  反应灵敏、引人入胜的设计
2.  现代 JavaScript APIs
3.  服务人员
4.  HTTPS

我想在这篇文章中谈论的是关于 PWAs 的设计，但是更多的文章将会接踵而至。
(HTTPS 除外。这里你只需要知道全功能的 pwa 需要 HTTPS。你会在关于服务人员的文章中找到原因。如果你还没有有效的 TLS 证书，我只能推荐 [Let's Encrypt - Free SSL/TLS 证书](https://letsencrypt.org/getting-started/)。作为替代， [GitHub 页面](https://pages.github.com/)也通过 HTTPS 提供服务，并允许自定义域名。)

## 响应式设计

多年来，智能手机应用程序中已经建立了某些用户界面/ UX 模式。最引人注目的两种风格当然是 [iOS 界面指南](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/)和谷歌的[材质设计](https://material.io/guidelines/material-design/introduction.html)。

这两个风格指南包含了如何为各自的平台设计用户界面的详细信息，以及如何处理用户交互的指南。考虑到这些指导方针，新用户会更容易接触到你的应用，因为整体的外观和感觉似乎已经“熟悉”了。

这些指导方针对渐进式 web 应用程序也很有意义。由于 PWA 可以作为完整网页的轻量级替代物(例如，Twitter lite T1)，甚至可以作为原生应用的完全替代物，因此无论用户是使用原生应用还是访问 web 应用，PWA 用户都应该有宾至如归的感觉。

### 先移动

老派设计网页的方法是让它在桌面浏览器上看起来不错，然后让它在移动浏览器上看起来也不错。“移动优先”的方法正好相反，在设计网页时考虑到了移动设备。为智能手机设计，但也应用技术，使网页看起来很好，当通过桌面浏览器访问。

要做到这一点，你可以依靠像 Twitter 的 [Bootstrap](https://getbootstrap.com/) 或 [Material Design Lite](https://getmdl.io/) 这样的 CSS 框架，但阅读 CSS 媒体查询[ [1](https://www.w3schools.com/css/css_rwd_mediaqueries.asp) ， [2](https://www.w3schools.com/cssref/css3_pr_mediaquery.asp) ]肯定没有坏处。

有了 mobile first UIs，区分 web 应用和本地应用的另一个要点是，web 应用仍然在浏览器中运行。

PWAs 的一个很酷的事情是，它们可以像本地应用程序一样“安装”到设备上(准确地说，目前是 Android 设备)。对此的要求是所谓的“**应用清单**”。

## Web 应用清单

web 应用程序清单包含有关 web 应用程序的元数据。它实际上是一个简单的 JSON 文件，但是将文件命名为
也很常见

```
manifest.webmanifest 
```

Enter fullscreen mode Exit fullscreen mode

manifest 文件允许配置应用程序名称、图标、显示风格、配色方案等等。

下面的清单显示了 wddng 清单的内容:

```
{
  "name": "wddng",
  "short_name": "wddng",
  "start_url": "./index.html",
  "scope": ".",
  "display": "standalone",
  "background_color": "#fff",
  "theme_color": "#fff",
  "description": "Keine Hochzeit ohne Technik!",
  "dir": "ltr",
  "lang": "en-US",
  "orientation": "any",
  "icons": [
    {
      "src": "./simg/icons/app-icon-48x48.png",
      "type": "image/png",
      "sizes": "48x48"
    },
    {
      "src": "./simg/icons/app-icon-96x96.png",
      "type": "image/png",
      "sizes": "96x96"
    },
    {
      "src": "./simg/icons/app-icon-144x144.png",
      "type": "image/png",
      "sizes": "144x144"
    },
    {
      "src": "./simg/icons/app-icon-192x192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "./simg/icons/app-icon-256x256.png",
      "type": "image/png",
      "sizes": "256x256"
    },
    {
      "src": "./simg/icons/app-icon-384x384.png",
      "type": "image/png",
      "sizes": "384x384"
    },
    {
      "src": "./simg/icons/app-icon-512x512.png",
      "type": "image/png",
      "sizes": "512x512"
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

`name`和`short_name`应该是不言自明的。`name`包含完整的应用程序名称，将显示在启动闪屏上。`short_name`将用于应用程序图标。

`start_url`配置在应用程序启动时加载哪个 URL，通过可选的应用程序`scope`可以配置 PWA 清单应该应用于哪些范围。当在范围之外导航时，web 应用将作为常规网页。
设置`"display": "standalone"`将像本地应用程序一样显示 web 应用程序，包括启动图标、隐藏的 URL 栏、隐藏的导航元素等。

有许多可用的选项，应用程序清单规范仍在积极开发中。根据最新草案，还可以通过清单选项安装服务人员。

我不会详细讨论所有可用的值，你可以查看文章末尾提供的参考资料。

### 包括货单

使用关系设置为`"manifest"`的`<link ...>`标签包含清单文件。

```
<link rel="manifest" href="/manifest.webmanifest"> 
```

Enter fullscreen mode Exit fullscreen mode

#### 边注:打包使用 [parcel.js](https://parceljs.org/)

在撰写本文时， [parcel.js](https://parceljs.org/) 只会打包以`*.webmanifest`结尾的清单文件。

## 有用的资源

*   [Web 应用清单规范](https://w3c.github.io/manifest/)
*   [Web 应用清单上的 MDN webdoc](https://developer.mozilla.org/en-US/docs/Web/Manifest)
*   [浏览器支持 app 清单](https://caniuse.com/#feat=web-app-manifest)。

## 总结

将响应式设计与应用程序清单结合使用，可以使您的 web 应用程序在移动设备上友好且可安装。也可以通过清单文件部分定制应用主题。

向 web 应用程序添加清单是我们实现全功能渐进式 web 应用程序的第一步。

敬请关注更多内容！

再见

西蒙