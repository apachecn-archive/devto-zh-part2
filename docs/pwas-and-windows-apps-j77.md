# PWA 和 Windows 应用程序

> 原文：<https://dev.to/tunaxor/pwas-and-windows-apps-j77>

# 大家好！

所以，我以前发表过为什么如果你唯一想要/需要的是 windows，并且你需要自带的本地访问 API，比如媒体播放器，或者 POS API 访问，那么瞄准 UWP 而不是电子 T1 是一个好的选择。

这包括完全在 Visual Studio 上用 JavaScript 创建你的应用程序，也许是像 *fuse-box* 或 webpack 这样的捆绑器，只要你说得出名字，重点是创建一个能吐出 html/css/js 包的东西，这样你的 UWP 应用程序就能运行它。

今天我带来了一种不同的方法，你可能听说过 [PWA](https://developers.google.com/web/progressive-web-apps/) s Google 已经在 PWA 开发和传播知识上投入了大量的资源，微软[也在 2 月份发布了【Windows 10 和 MS Edge 将支持 PWA，所以如果你需要 PWA 信息，你可以点击那些链接，因为这些信息有点超出了本文的范围。](https://blogs.windows.com/msedgedev/2018/02/06/welcoming-progressive-web-apps-edge-windows-10/)

**Dev.to** 本身是一个 PWA，如果你曾经在你的 android 手机(不确定 iOS)中访问过该网站，chrome 会问你是否要在你的手机中添加该网站，如果你接受，那么 Dev.to 就变成了你手机中的另一个应用程序。

我有一个问题要问你

> 现在，如果你已经有了一个网站，但你看到了一个机会，可以包含一些似乎只适用于 windows 的功能，那该怎么办？

你需要遵循两个小步骤

1.  更新你的站点成为一个 pwa(添加一个清单，一个服务人员，和其他小的需求，记得点击 PWA 链接获取更多信息)
2.  为 Windows 应用商店提交创建 Appx。

当你的应用被接受时，嘣！您已经有了一个 PWA 和一个 Windows 10 应用程序，可以在运行 Windows 10 的每个平台上访问，这还包括访问任何 **WinRT** API！

```
if (window.Windows) {
  // yay we're on windows!
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，你可以拥有一个媒体播放器网站，当它作为一个应用程序从 windows 启动时，它可以在本机 API 上运行，而不是在 HTML5 媒体 API 上运行。

我现在还没有一个本地 API 访问的演示，但是我可以和你分享我为我的家庭使用(以及实践)而构建的东西

## [满大人](#mandadin)

Mandadin 是一个 PWA，它使用 Aurelia、Pouchdb、Workbox(用于服务人员)和你的标准网络材料，托管在 firebase 中，并与本地设备隔离，所以你在手机上做的任何事情都留在你的手机上，因为它使用 IndexedDB 来存储你想存储在那里的任何东西
你可以在这里找到代码

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [安吉尔·穆诺兹](https://github.com/AngelMunoz) / [店员](https://github.com/AngelMunoz/shoppinator)

### 这是我和妻子去杂货店时用的一款玩具应用:)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 购物者

这是我和妻子去杂货店时用的一款玩具应用:)

</article>

[View on GitHub](https://github.com/AngelMunoz/shoppinator)

你会发现代码可能有点乱，但它可以记录我们的购物清单。为什么我要重新发明轮子而不是用 X？因为它可以让我练习。

你可以在这里找到 [Windows store 应用程序，如果你安装它，你会看到一些东西](https://www.microsoft.com/store/apps/9NKDDF1M5MTG)

1.  是西班牙语的。-是啊，这不是为了提供一个普遍的观众解决方案，这是我妻子的要求和类似 app 的某种简历。
2.  当你访问[mandat in](https://mandadin.tunaxor.me)时，它是完全相同的应用程序

关于第 2 点的事情是，你正在一个 Windows 应用程序外壳内运行！所以**你可以访问 WinRT API！**

你能用它做什么？这里有更多关于它的信息

[![tunaxor](img/dd42ae2af93474df85932b5a5fee0bcd.png)](/tunaxor) [## JavaScript Windows 通用应用程序第 2 部分

### 安吉尔丹尼尔穆尼奥斯冈萨雷斯 6 月 19 日 186 分钟阅读

#javascript #uwp #vue #native](/tunaxor/javascript-windows-universal-applications-part-2-15nl)

所以耶！你可以用你现有的代码库瞄准 UWP！只要稍加组织和努力，您就可以在 Windows 应用商店应用程序中拥有本机功能，它基本上只是一个网站！

编辑:
我是不是忘了说你不需要更新 Appx 包了？它总是会选择你的网站，并注册新的服务人员！太棒了。持续部署已经在进行中！

那么，这是否让你想到了可以在 windows store 应用程序中发布的你自己的某种副业项目？

所以感谢你阅读这篇文章:)我希望你有一个美好的周末，并请在评论区分享你对此的想法。