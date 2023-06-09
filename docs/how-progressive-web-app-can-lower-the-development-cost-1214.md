# 渐进式 web 应用程序如何降低开发成本？

> 原文：<https://dev.to/tirthbodawala/how-progressive-web-app-can-lower-the-development-cost-1214>

渐进式 Web App 和 Native App 在性能和开发成本上哪个更好？进步的应用程序无疑赢得了比赛为什么？让我在这个帖子里给你解释一下。一方面，我们有本地应用，在某些情况下无疑非常快速和高效。另一方面，我们的网站速度很慢，而且存在连接问题，情况只会变得更糟。

为了解决这个问题，twitter 和谷歌在 2016 年提出了加速移动页面项目(AMP ),解决了连接速度慢的问题。这在一定程度上解决了问题，但我们仍然面临着连接不良的问题。尤其是当我们看到空白的屏幕，我们只能等待，等待无限的时间盯着空白的白色屏幕。这就是 PWA 来拯救我们的地方。PWAs 最棒的地方是——你可以在慢速连接和无连接的情况下获得最佳的用户体验(是的，你没看错..)在这篇文章中，我将与您讨论您需要了解的关于 PWA 的所有可能的事情，以及您如何从中获得最大收益。

## 传统原生应用的问题

开发原生应用有许多相关问题，例如:

*   不总是易于开发和支持；尤其是在开发和支持不同平台、版本和设备的额外时间方面。
*   这不是一个更便宜的提议，因为支持多个平台、设备和版本通常会增加成本。
*   不容易分发；因为发布到应用商店是一个持续的、乏味的职业
*   更快上市
*   对于原生应用程序，用户需要更新应用程序才能看到变化。对于大多数在手机连接 wi-fi 时设置自动更新的用户来说，这是可以接受的，但并不是对所有人都适用。
*   对应用商店的依赖

## 什么是渐进式 Web App？

它是一种软件开发方法，结合了移动和 web 应用程序中使用的最佳技术。它就像一个充当应用程序的网站，尽管它是使用 web 技术构建的。PWA 利用浏览器的功能和特性，表现得像一个本地应用程序。为了使该应用程序符合渐进式 web 应用程序的资格，它应该具有以下特征。

## PWA 的特性

### 递进

渐进式 web 应用程序应该可以在任何设备上工作，并逐步增强。换句话说，PWA 应该利用用户设备和浏览器上的任何可用功能。

### 可发现的

既然 progressive web app 是一个网站，它应该可以在搜索引擎中被发现。这是相对于原生应用的主要优势之一，原生应用在可搜索性方面仍落后于网站。

### 可链接

作为从网站继承的另一个特征，一个设计良好的网站应该使用 URI 来指示应用程序的当前状态。这将使 web 应用程序能够在用户标记或共享应用程序的 URL 时保留或重新加载其状态。

### 反应灵敏

渐进式网络应用的用户界面必须适合设备的外形和屏幕尺寸。

### 类 App

渐进式 web 应用程序应该看起来像本机应用程序，并建立在应用程序外壳模型上，页面刷新最少。

### 连通性独立

它应该在低连接或离线(我最喜欢的特征)的地区工作。

### 重新订婚

移动应用程序用户更有可能重复使用他们的应用程序，而渐进式网络应用程序旨在通过推送通知等功能来实现相同的目标。

### 可安装

渐进式网络应用程序可以安装在设备的主屏幕上，使其随时可用。

### 新鲜

当新内容发布且用户连接到互联网时，该内容应在应用程序中可用。

### 安全

因为渐进式 web 应用程序具有更亲密的用户体验，并且因为所有网络请求都可以通过服务人员拦截，所以应用程序必须托管在 HTTPS 上，以防止中间人攻击。

## PWA 的特性

### 表现

说到性能，没有什么能打败渐进式网络应用。它们的性能和速度将永远优于典型的 web 应用程序。

[![Image showing improved stats of Eventerprise after implementing PWA](img/e31ec28c8eec565c29604245bbc1bfca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ysaiOMDV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.atyantik.com/wp-content/uploads/2018/05/1-Second-2.png%3Fw%3D640%26ssl%3D1)

这是 Atyantik 使用 PWA 设计的应用程序，你可以看到平均速度和加载时间的显著结果。

PWA 还提供了构建以 60 fps 动画和响应的 UI 的指南。为了取悦用户，移动网络应用需要在性能和界面方面的表现，最好是在这些时间限制内。

[![Site Performance Metrics](img/de5da1bca1f368bae3343957394fbcdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8HezP_lL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.atyantik.com/wp-content/uploads/2018/05/stats.png%3Fresize%3D640%252C187%26ssl%3D1)

### 推送通知

对于原生应用来说，最好的功能之一是它们能够推送通知来吸引用户。当我们向用户发送及时、准确和相关的通知时，就增加了产品的价值。通过启用服务人员，人们可以使用渐进式 Web 应用程序轻松实现这一点。W3C 负责为[推送 API](https://www.w3.org/TR/push-api/) 和[通知 API](https://dvcs.w3.org/hg/notifications/raw-file/tip/Overview.html) 创建规范。

### 硬件访问

我们可以编写渐进式应用程序来访问本地硬件功能。当前可用的功能有:

*   点击呼叫–轻松链接电话号码进行直拨
*   用户位置
*   设备方向
*   音频/视频记录和回放(回放已经在几个移动浏览器中可用)

谷歌正在开发的其他功能包括:

*   通用传感器 API
*   环境光传感器
*   近程传感器
*   加速度传感器
*   陀螺仪传感器
*   磁力仪传感器
*   震动
*   电池状态
*   唤醒锁

这些[硬件访问能力标准](https://www.w3.org/2009/dap/)也由 W3C 管理

### 凭证 API

使用[凭证 API](https://w3c.github.io/webappsec-credential-management/) ，用户不必再次输入登录表单的所有细节。它使网络能够使用一次点击和自动登录功能的设备帐户。因此，它改善了用户体验。

### App 安装横幅

Mobile Chrome 为网页提供了一种简单的方式来提示用户将 web 应用程序安装到他们的主页上。这是对“添加到主屏幕”功能的补充。它有助于提高应用程序的可访问性。

### Web 支付 API

所有设备的购物车放弃率约为 69%。对于移动设备来说，这个数字甚至更高。万维网支付标准由万维网支付工作组(WPWG)开发，改进了当前的在线支付机制。有了目前对 PWA 的支持，用户现在只需轻轻一点就可以付款。

[![Payment API for PWA](img/c103b5fba7c488b585af869280182e8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UDxJCQEv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/www.atyantik.com/wp-content/uploads/2018/05/payment-api.gif%3Fw%3D640%26ssl%3D1)

## PWA 的 2 个主要组成部分

渐进式 web 应用程序有两个主要组件。第一个是应用清单，第二个是服务人员。下面我们来详细了解一下其中的每一个。

### 服务人员

Service worker 是 PWA 最强大的组件之一，它赋予了 it 像本机应用一样工作的能力。它基本上是一个由浏览器在后台运行的脚本组成的标准。服务人员不需要网页或用户交互。他们可以独立工作。到目前为止，它们已经具备了推送通知和后台同步等功能。未来，他们还计划支持定期同步和地理围栏。

[![Image showing how service worker enables the performance](img/8051c44d4229ecbfdf6c6a42dcde11e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BVnTl_tK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.atyantik.com/wp-content/uploads/2018/05/1-Second-2-1.png%3Fw%3D640%26ssl%3D1)

### App 清单

它基本上是一个 JSON 文件，有助于提供配置，以在 web 应用程序中启用几个类似本机的功能。

*   添加到主屏幕–用户现在可以将 web 应用程序添加到他们的主屏幕，以便于访问。主屏幕将显示 web 应用程序的图标。我们可以在 web 应用程序清单文件中为添加到主屏幕功能指定图标集(具有不同的大小)。和其他一些浏览器一样，在手机浏览器中，你可以在菜单中找到一个选项。
*   闪屏——当应用程序还没有准备好显示第一部分信息时，闪屏有助于提供良好的用户体验。在没有闪屏的情况下，用户会对应用程序的状态感到困惑。在 web 清单中，我们可以给出闪屏图像、标题和主题颜色配置。当用户打开 web 应用程序时，他们会看到一个闪屏，就像在本地应用程序中一样。
*   主题颜色–我们可以配置一个主题颜色来为工具栏、地址栏和其他功能添加颜色。
*   启动风格–我们可以为 web 应用程序配置显示和方向。显示选项为浏览器或独立，方向为纵向或横向。

Web 应用清单规范是 W3C 管理的另一个标准。

## 案例分析

### [T1】Flipkart](#flipkart)

[![Image showing how flipkart has achieved improvements by implementing PWA](img/28810533ebf74c0a169892075a3418bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UR6jRv7u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.atyantik.com/wp-content/uploads/2018/05/flipkart-stats.png%3Fw%3D640%26ssl%3D1)

### 预定我的节目

[![Image showing book my show improved stats after implementing Progressive Web App](img/1ea2e81de582e2f0a9a0b1b49d97703f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VHN2ZDQ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.atyantik.com/wp-content/uploads/2018/05/32658813_234826340619303_8989574044693037056_n.png%3Fw%3D640%26ssl%3D1)

### 阿里快递

[![Image showing aliexpress improved stats after implementing Progressive Web App](img/3f9a5593d8b5ab126a3e501fbe47d7ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IQEv_rjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.atyantik.com/wp-content/uploads/2018/05/32821396_234826083952662_5126759060867121152_n.png%3Fw%3D640%26ssl%3D1)

## 开发成本

根据一项研究，一个普通用户花 80%的时间在三个应用上(对我来说是 Chrome、Quora 和 Medium)。

其他应用程序大部分时间都处于闲置状态，消耗了宝贵的一部分内存。此外，开发一个应用程序的成本大约是创建一个网站的十倍。如果你计划为不同的平台如 Android、iOS 和 web 开发和维护单独的代码库，成本会高得多。

### 我们如何在 Atyantik 开发渐进式网络应用

这种方法类似于构建本地应用或任何其他 web 应用。它包括以下步骤

#### **目标**

在这一步，我们要求我们的客户定义构建这个应用程序的目的和目标。这给了我们项目的方向，并在此基础上，我们可以在以后的阶段给出我们的反馈和想法。

#### **观众**

在这一步，我们定义应用程序的潜在用户。在这里，我们要求客户解决几个问题，如目标受众的类型，用户访问的类型和级别等。

#### **功能规格**

我们始终相信敏捷，重视与大量文档的沟通。只记录必要的部分，比如对 API 文档使用 swagger。在这之后，我们从客户那里收集所有的需求，并将其转换成功能规范，这样我们的开发人员就可以在上面工作了。

#### **第三方厂商**

此步骤包括根据客户对任何第三方服务的要求，研究和选择第三方供应商、服务和产品，如服务器提供商、SSL 证书提供商。

#### **PWA 开发**

现在开发团队可能会加入这个过程。其任务如下:

*   创建 PWA 框架和架构
*   为此，我们使用我们的开源计划， [React-Pwa](https://www.reactpwa.com) 样板，建立在 [PawJS](https://github.com/Atyantik/pawjs) 之上，两者都由 Atyantik 维护，并且在[github.com](https://github.com/Atyantik/react-pwa)上有超过 1.8K+的星级(我从不错过吹嘘它的机会)
*   数据库结构设计
*   完成所有功能的开发和实施

### 投资 PWA

如果你是一家成长中的初创公司，并希望跟上日新月异的技术发展步伐，那么这是投资渐进式网络应用的最佳时机。这里有几个因素可能会激励你投资渐进式网络应用。

#### **增加达到**

谷歌 Chrome 发布仅 4 年就宣布月活跃用户达到 10 亿。预计未来几年的增长将超过两倍。查看 ComScore 发布的最新数据，该数据称用户在移动网络应用上的支出是前 1000 名原生/混合应用的 2.5 倍。

[![latest data released by ComScore, which says users are spending 2.5x times on mobile web apps vs the top 1000 native/hybrid apps.](img/4e0b37f5815cb82188b63c95089741e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8Hy70zuw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.atyantik.com/wp-content/uploads/2018/05/32684832_234829043952366_4923998233258098688_n.png%3Fw%3D640%26ssl%3D1)

#### **降低收购成本**

谷歌与 Flipkart 和全球速卖通等几家公司合作，制定了渐进式网络应用战略，并帮助他们建立了更好的移动网络存在。

任何营销团队的关键矩阵之一是平均客户获取成本。Selio 宣称，渐进式网络应用的平均客户获取成本比原生应用低了 10 倍。

[![Image showing selio's average customer acquisition cost for progressive web apps is a whopping 10 times lesser than for native apps.](img/cc7bab785c68c29ae5394e22edfaadb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jV-EzIg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.atyantik.com/wp-content/uploads/2018/05/32659819_234829073952363_4253214295417946112_n.png%3Fw%3D640%26ssl%3D1)

#### **强化转换**

转换是另一个关键矩阵，它解释了被收购客户的实际交易。正如在上述案例研究中所讨论的，你看到了 Flipkart 和其他大公司如何实际使用这种策略来转移到 PWA，并看到了他们的转换率的巨大改善。

## 何去何从？

如果你是一个产品所有者，并想推出自己的 PWA 应用程序，那么你来对地方了。请允许我们帮助你。借助我们的框架，我们可以定制并帮助您尽快交付应用。

如果你是一名开发人员，那么直接去 React-PWA 网站查看我们的 React 样板文件。因为我们相信回报社会的样板代码是开源的，你可以下载并使用它，并根据你的需要进行定制。作为回报，我们请你帮个小忙。我们正在寻找开发人员来帮助我们与适当的文件。如果你能对此有所贡献，那将会帮助我们做得更好。此外，我们还基于样板文件 [Paw.js](https://github.com/Atyantik/pawjs) 创建了一个框架。你也可以检查一下。如果你遇到困难，我们总是在那里帮助你。

编码快乐！

post [渐进式 web 应用如何降低开发成本？](https://www.atyantik.com/how-progressive-web-app-can-lower-the-development-cost/)最早出现在 [Atyantik Technologies](https://www.atyantik.com) 上。