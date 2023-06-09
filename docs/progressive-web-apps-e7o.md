# 渐进式网络应用

> 原文：<https://dev.to/mahmoudelmahdi/progressive-web-apps-e7o>

渐进式网络应用程序(PWAs)是一种现代网络功能，结合了网络的所有优点和本地应用程序的顶级功能。虽然本地移动应用程序提供了最高质量的 UX，但它们也给用户带来了摩擦。PWAs 和原生移动应用之间的最大区别之一是，它不需要要求应用市场(App Store、Google Play 等)的麻烦。)来访问，他们只是依靠移动网络浏览器。

## 关键原则

下面列出了一个 web 应用程序应该遵守的 10 个关键原则:

1.  **渐进式**:适用于所有用户，无论选择何种浏览器，在旧版本浏览器上具有基本水平，但在最新版本浏览器上功能齐全。
2.  **响应式**:适合任何带屏幕和浏览器的设备，包括手机、平板电脑、笔记本电脑、电视、电子书阅读器等...
3.  **独立连接**:离线工作或网络连接不良。
4.  **类 App**:使用 app-shell 模型提供 App 风格的导航和交互。
5.  **新鲜**:得益于服务人员的更新流程，始终保持最新。
6.  **安全**:通过 HTTPS 提供，防止窥探，确保内容不被篡改。
7.  **可发现的**:由于 W3C 清单和服务工作者注册范围允许搜索引擎找到它们，它们可以被识别为“应用程序”。
8.  **重新参与**:通过推送通知等功能使重新参与变得容易。
9.  可安装的(Installable):允许用户在主屏幕上“保留”他们认为最有用的应用程序，而没有应用程序商店的麻烦。
10.  **可链接**:通过 URL 轻松共享，不需要复杂的安装。

## PWA vs .原生手机 App

如下图所示，渐进式网络应用和本地应用之间的表面差异与终端用户访问它们的方式有关。

[![pwa_vs_native_app](img/fc3b001cb42c38c8560c56b06fbac9a1.png "elmahdim.com")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nzILvvwK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cwq1afwl0v38ovmmupsq.png)

渐进式 Web 应用程序被认为是一种回避甚至消除许多与本地应用程序开发相关的头痛问题的方法。然而，原生应用程序仍然更加健壮，可以为任何用途编写。

## 建立 PWA 的第一步

### 谷歌

这个 [codelab](https://developers.google.com/web/fundamentals/codelabs/your-first-pwapp/) ，将带你创建你自己的渐进式 Web 应用，包括设计考虑因素，以及实现细节，以确保你的应用符合渐进式 Web 应用的上述关键原则。

### MDN

[渐进式网络应用架构](https://developer.mozilla.org/en-US/docs/Web/Apps/Progressive/App_structure)。这种架构允许网站从所有 PWA 功能中获得最大收益，它缓存应用程序外壳并以一种大大提高性能的方式管理动态内容。

### 工具和资源

下面列出的工具和资源将帮助您构建和发布出色的渐进式 Web 应用程序，并加快这一过程:

*   要查看 PWA 的运行情况，您可以参考 [PWA.rocks](https://pwa.rocks/) 中几个类别的示例，这将帮助您想象未来的 PWA 可能是什么样子。
*   创建 PWA 的最快方法是使用 [PWABuilder](https://preview.pwabuilder.com/)
*   为 PWA 人员调试工具。
*   用 [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 来衡量完整性，它是一个[开源](https://github.com/GoogleChrome/lighthouse)，提高网页质量的自动化工具。你可以在任何网页上运行它，无论是公开的还是需要认证的。它对性能、可访问性、渐进式网络应用等进行审计。
*   渐进式网络应用[清单](https://developers.google.com/web/progressive-web-apps/checklist#baseline)。
*   [Application Shell Architecture](https://github.com/GoogleChromeLabs/application-shell)，是一个全面的渐进式 Web 应用起点，涵盖了模型的客户端和服务器端部分，其中有很多内容，您需要花一些时间来熟悉代码库。
*   [Web Starter Kit](https://github.com/google/web-starter-kit) 多设备网站的工作流程。

上面列出的工具和资源只是几吨，但是它们应该足够好，可以让你走上正确的道路。

> 最初发布于[进步网络应用——马哈茂德·埃尔马赫迪](https://elmahdim.com/progressive-web-apps/)