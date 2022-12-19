# 为什么是 XAML？为什么是现在？

> 原文：<https://dev.to/progresstelerik/why-xaml-why-now-1ifl>

XAML 经受住了时间的考验，仍然是一种灵活的 UI 定义语言。这篇文章探讨了 XAML 今天支持的应用平台，并展望了未来可能会发生的事情。

XAML 死了。不，等等，XAML 万岁。XAML(可扩展应用程序标记语言)最初是一个简单的瘦用户界面标记层，但有着令人惊讶的多事之秋。微软技术平台上的开发者见证了 XAML 的惊人崛起，也见证了它徘徊在生命维持的边缘。

事实证明，XAML 可能备受爱戴，也可能遭人憎恨，但显然经受住了时间的考验。多年来，微软没有一个团队拥有 XAML——它原本是一种描述性的标记 UI 语言，用来定义应用程序的可视化树。因此，一些技术采用了 XAML 语，并赋予了它个性和独特的方言。今天，XAML 站在强大的各种应用平台，体育丰富的工具支持，并可能有一个非常光明的未来。这篇文章探讨了 XAML 今天可以提供什么样的动力，并着眼于未来。

## 桌面

### Windows 演示基础(WPF)

[![](../Images/d9a933c1f22b564aab1ef40edd3c8ddf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1cLKq3hx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/wpf.png)

今天，Windows 桌面开发的圣杯仍然是 WPF——XAML 诞生的地方。多年来，XAML 一直是 WPF 应用程序的 UI 层，并享有丰富的工具支持。有人可能会说，XAML 的功能丰富性和易用性很大程度上源于 WPF。微软和开发者社区都在努力让 XAML/C#开发成为开发者的美妙体验。借助代码智能感知和 Blend 等设计工具，WPF 应用的 UI 定义层继续成为 XAML 开发的黄金标准。

### 通用 Windows 平台

[![](../Images/09dc20ddcef18d902dd20cb85a76d398.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hnXtd5ST--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/uwp.png)

从 Windows 8 开始，经过几次迭代之后，微软最终选择了 UWP——为 Windows 开发前瞻性应用的事实方式。果不其然，UI 层由 XAML 提供支持，尽管与 WPF 使用的方言略有不同。XAML 支持 UWP 应用程序对熟悉这种标记语言的开发者来说是一件大事——主要是因为 UWP 的影响力。UWP 可以在所有现代 Windows 设备上运行应用程序，从平板电脑、笔记本电脑和台式机到 XBoxes、Surface Hubs 和 HoloLens。开发人员可以在 XAML 定义他们的用户界面，它可以简单地跨所有形式的因素工作。

虽然这听起来不可思议，但现实需要做一些工作来调整各种设备尺寸的流畅用户界面布局——但这都是 XAML 的幕后工作。UWP 的设备灵活性意味着 XAML 拥有丰富的功能，如响应式布局、高级触摸支持和墨水功能。

## 移动

### Xamarin。形式

能够通过 XAML 访问所有的 Windows 设备是一件好事，但是没有人生活在一个平台孤岛中。对于移动设备，iOS/Android 规则。NET 开发人员需要一座桥梁来跨平台传递他们的技能。输入 [Xamarin。表格](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/)。有了共享的抽象 UI 层，开发人员不仅可以共享业务逻辑，还可以共享 UI 定义——所有这些都是为了构建真正的原生跨平台应用程序。以及抽象 UI 定义的选择？是的，XAML——一种迎合移动应用开发的特殊语言。

[![](../Images/a9e6b7152b10352ed426100b16d7bb53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dPoeiKi7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/XamarinForms.png)

Xamarin。Forms XAML 做了一件很棒的事情——缓解压力。NET 开发人员不必学习本机平台 UI 的复杂性。开发人员在 XAML 定义 UI 可视化树，运行时在每个平台上呈现相应的本地 UI 组件。现代 Xamarin。表单开发为 XAML 开发提供了丰富的工具，如表单预览器、检查器和分析器。

### Uno

是 Xamarin。你不喜欢 XAML 吗？结果，一些来自加拿大蒙特利尔的聪明人和你在一起——他们喜欢 UWP·XAML。进入 [Uno](https://platform.uno/) -现在是一个开源的 UI 抽象库，从 XAML 创建了 iOS 和 Android 的桥梁。Uno 的应用程序运行时由 Mono 和 Xamarin 提供支持——开发人员只需使用 UWP·XAML，而不是 Xamarin。表单头。这是一项不小的工作，因为相应的本机 UI 正在每个平台上呈现。为了 UWP·XAML 的爱，这一切都是值得的。

[![](../Images/f0d979f280c43d34a2657e8ae4422cac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5VC8XFw9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/Uno.png)

### NativeScript

许多传统上拥有完整的。NET 技术堆栈，现在用 SPA 框架——如 Angular、React 或 Vue——来支持他们的 web 应用前端。最现代的。NET 开发人员不羞于编写 JavaScript、CSS 和 HTML。如果 web 技术是你的应用的驱动力，那么 JavaScript 原生应用作为一种移动策略是相当诱人的。开发者可以重用 web 技术来开发真正的本地跨平台移动应用——并且有可能在 web 和移动之间共享代码。

[![](../Images/a70f6fcb8159f8e2aa70a8caabc2fd0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zu4g63fP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/NativeScriptLogos.png)

[NativeScript](https://www.nativescript.org/) 是一个用于构建 JS 原生移动应用的开源框架。开发人员可以直接用 Angular 为应用程序的业务逻辑编写 JavaScript 或 TypeScript。而抽象的 UI 层是用 XML 定义的——JS 桥在运行时渲染相应的原生 UI。虽然是正式的 XML，但让我们看看 NativeScript 中的标准 UI 定义——看起来熟悉吗？是的，这几乎是 XAML，而且。任何有 XAML 经验的. NET 开发人员在这里都会如鱼得水。

[![](../Images/49f6db964656466c16259515bc367433.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yKJEGYfm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/NativeScriptXAML.png)

## 网页

[![Microsoft Silverlight](../Images/664ca94e8176a8f024f8194fca85feff.png "Microsoft Silverlight")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZgBFcj-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/blogs/2018/2018-10/silverlight4d42cf5f8c874f399149351fa66033ea.png%3Fsfvrsn%3De54d6eeb_1%26MaxWidth%3D325%26MaxHeight%3D%26ScaleUp%3Dfalse%26Quality%3DHigh%26Method%3DResizeFitToAreaArguments%26Signature%3D67DEA073D9D95EB41758042C13977BFA39067953)

XAML 为网络浏览器上的应用提供动力有着曲折的过去。可以理解，。开发 Silverlight 的. NET 开发人员仍然受到伤害——当技术消亡、投资减少时，情况从来都不好。网络已经脱离了插件模式，Silverlight 看到了不祥之兆。从事 Silverlight 开发的人实际上非常喜欢开发者体验——XAML/C #在浏览器中非常漂亮，丰富的工具也很有帮助。可能有些人渴望看到 XAML 重回网络——但会用更好的架构。

### web 组装

[![WebAssembly](../Images/d4270680b3dbef70f917c01749ba6a1a.png "WebAssembly")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hpSLPLHD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/blogs/2018/2018-10/wasmlogo4832c37fb3854896b199c5142701fd3f.png%3Fsfvrsn%3De39b4771_1%26MaxWidth%3D325%26MaxHeight%3D%26ScaleUp%3Dfalse%26Quality%3DHigh%26Method%3DResizeFitToAreaArguments%26Signature%3DE538A8E281332CDBDFA32FAB53D1453591C9CDB5)

进入[web assembly](https://webassembly.org/)——现代 evergreen 浏览器可以原生执行的低级指令格式，就像 JavaScript 引擎执行 JS 代码一样。作为一个开放的 web 标准，WebAssembly 得到了大型浏览器供应商的支持，并承诺提供安全性和本地执行速度。WebAssembly 最大的吸引力是将高级语言编写的代码编译成浏览器本地运行的基于堆栈的指令的承诺——这[打开了许多大门](https://dev.to/progresstelerik/goodbye-javascript-hello-webassembly-6oc-temp-slug-9525238)。

### 单色

[![mono](../Images/de3860210255094d8b94b9d856cdb78e.png "mono")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ooTvwBvg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/blogs/2018/2018-10/monoc1dba5be8f3d4f75af22c6fb8938b335.png%3Fsfvrsn%3Dcb8284f9_1%26MaxWidth%3D325%26MaxHeight%3D%26ScaleUp%3Dfalse%26Quality%3DHigh%26Method%3DResizeFitToAreaArguments%26Signature%3D89229B53C7C19266E14E6D88B94E2A3D7969094D)

单核细胞增多症早在 20 世纪 60 年代就出现了。NET 框架，作为流行的移植。NET APIs 到 Windows 之外的平台。多年来，Mono 已经发展成熟，提供了很大的 API 表面积和。NET API 在其他平台上的映射，比如 Mac/Linux，从而支持。NET 应用程序走出窗口。单声道继续为 Xamarin 提供动力——这显然是最简单的方法。NET 代码并在 iOS/Android 上运行。

事实证明，Mono 的人非常聪明，他们正在努力将 [Mono 引入 WebAssembly 平台](https://www.mono-project.com/news/2017/08/09/hello-webassembly/)。这具有将 C#代码编译成浏览器可以本地执行的 WebAssembly 的巨大潜力，哈利路亚！这正是推动 [Blazor](https://blazor.net/index.html) 的原因——实验性的 ASP.NET 网络框架承诺用 Razor 语法将 C#引入浏览器。虽然[目前对 WebAssembly](https://www.mono-project.com/news/2018/01/16/mono-static-webassembly-compilation/) 的 Mono 支持可能是通过 Mono IL 解释器在运行时运行托管代码，但对完全静态提前(AOT)编译的支持并不遥远。这应该会导致 C#代码在浏览器上的出色性能。

[![](../Images/23beae2ad9f82f9a82d73e6a70f9063a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F2Rkyl93--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/MonoWASM.png)

随着 C#原生进入浏览器，XAML afficianados 很快就要求恢复 UI 抽象层——这一次，通过 WebAssembly 而不是使用插件来完成。虽然是实验性的，但事实证明。表单和 UWP·XAML 已经可以通过 WebAssembly 在浏览器中运行 web 应用程序了。

认识一下[Ooui](https://github.com/praeclarum/Ooui)——一个小型的跨平台 ui 库，它将原生 UI 开发[的简单性带到了 web](https://www.telerik.com/blogs/xamarin-forms-on-the-web) 。Ooui。Forms 提供 Xamarin。为 web 提供表单呈现器，用 shadow DOM 驱动前端应用程序——它可以自己托管，也可以作为 ASP.NET 核心的一部分。有几个[在线样本](http://ooui.mecha.parts/)可以玩，看看让 XAML 回到浏览器的乐趣。还有，Ooui。Wasm 允许包装 Xamarin。将带有 XAML UI 标记的应用程序窗体集成到 WebAssembly 运行时中。很高兴看到一个 Xamarin。静态编译并托管在亚马逊 S3 上的表单应用，[纯粹作为 WebAssembly 在浏览器上运行](https://s3.amazonaws.com/praeclarum.org/wasm/index.html)。

为了不被落下，UWP·XAML 也在浏览器中找到了它的归途——多亏了 Uno。实验性的 WebAssembly 支持看起来很完美，尤其是在 Uno 的 WebAssembly 游乐场中进行尝试的时候。编写、编辑和观看 XAML 在浏览器中运行——多么神奇。

## 展望未来

### MVVM 框架

[![](../Images/25f028d736b8fb82c7e22fad0ec5f28d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---mISlkIc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/MVVM.png)

很大一部分原因。NET 开发人员喜欢 XAML 开发的原因是丰富的工具和维护大型代码库的方便性。模型-视图-视图模型(MVVM)是一种非常适合 XAML/C#开发的设计模式，在这方面有很多帮助。有丰富的 MVVM 框架，这些年来已经发展到支持 WPF/UWP 平台开发，现在它们已经被定制为适用于 Xamarin。表格也是。开源软件。NET 开发人员社区确实因这些 MVVM 框架而大放异彩——在为每个框架构建功能丰富性和 Visual Studio 模板方面投入了大量精力。比较流行的有 [MVVM 光](http://www.mvvmlight.net/)、[棱镜](https://github.com/PrismLibrary/Prism)和 [MVVM 十字](https://www.mvvmcross.com/)。

### Xamarin。表单头

[![](../Images/bc21b87c6d5b0f8612157a107ff0fd09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ABeAo_kt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/XFHeads.png)

对 XAML 开发者来说，一组有趣的发展是 Xamarin 的前端/后端数量不断增加。表单——这只是增加了支持的平台数量，并占用了您的代码空间。Xamarin。Forms 是开源的，开发者社区很聪明。结果是新的 Xamarin。形成头像苹果电脑，GTK Linux，WPF，Tizen，甚至网络 Xamarin。除了手机，表单应用程序现在也开始支持许多平台。抽象的 UI 层是你心爱的 XAML。

### 杏仁糖

苹果一直存在应用差距问题——大多数开发者为 iOS 开发，但为 Mac AppStore 开发的不多。他们的补救措施是一个代号为“[杏仁糖](https://www.strv.com/blog/unlocking-marzipan-uikit-on-macos)”的内部项目，目标是在 2019 年的某个时候取得成果。目标是通过 UIKit 和 AppleKit 的混搭让 iOS 应用在 Mac 桌面上运行——苹果已经展示了几个演示。现在，这显然必须小心翼翼地完成——当我们用鼠标/键盘将触摸应用程序带回桌面时，会有一些影响。

所以为什么要？NET/XAML 开发者关心杏仁糖？事实上，我们说的是在 Mac 桌面上运行的 iOS 应用程序，只需做最小的改动。iOS 应用程序可以用 Xamarin 编写。Forms 或 Uno——这意味着 XAML 可以正式在 Mac 上运行桌面应用程序。鉴于 XAML 在处理响应式布局方面的丰富性，人们可以认为 XAML 开发者应该能够相当容易地将其 iOS 应用移植到 Mac 上。

[![](../Images/8a83c04e336ac818bdb08b0565995cb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R3zGuqFW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/Marzipan.png)

### 打磨的 UI

喜欢 XAML/C#开发的另一个原因是丰富的生态系统。大多数专业应用程序需要复杂的高性能用户界面，开发者不需要重新发明轮子——有很多帮助。进入 [Progress Telerik](https://www.telerik.com/) -你的爱人。跨 web、桌面和移动的. NET 工具。

[![](../Images/2c8f75462b4f6e375cbfd9609940fcf3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i7XdB0Wv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/Telerik.png)

Telerik 为 XAML 开发者提供了多种 UI 套件，以满足不同平台的需求。常见的是功能丰富的 UI 控件，完全的 MVVM 支持，一致的 API 和专业的风格主题。每个 UI 组件背后的工程努力表现在性能和像素完美的渲染上——开发人员喜欢复杂的 UI，他们可以直接进入来照亮应用程序。流行的控件包括网格、列表视图、各种图表/图形、日历、侧抽屉和无数其他难以手工创建的用户界面。

当[WPF 的 Telerik UI](https://www.telerik.com/products/wpf/overview.aspx)为桌面应用程序提供强大的 UI 时，【Xamarin 的 Telerik UI 为所有平台的移动应用程序提供支持——是的，所有都使用 XAML 作为 UI 堆栈。

[![](../Images/ee365a612fc4e0bb3e71cd6eb4f5184f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xMdaVnId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/TelerikXamarin.png)

虽然现代 web 可能已经脱离了插件模型，但许多企业 Silverlight 应用程序仍然支持业务流程。为此， [Telerik UI for Silverlight](https://www.telerik.com/products/silverlight/overview.aspx) 提供了帮助，并看到了持续的功能投资。为 Windows 设备构建？[UWP 的 Telerik UI](https://www.telerik.com/universal-windows-platform-ui)是为了帮助完善高性能的 UI——而且是完全免费和开源的。

[![](../Images/f7f87468a00d5c2e3b73695c3a61703b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hCU82TeY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.telerik.com/sfimages/default-source/blogs/2018/2018-10/TelerikUWP.png)

### XAML 标准

如前所述，我们现在有几种口味的 XAML——WPF、UWP 和夏玛琳。各种形式的方言略有不同。这给开发人员带来了一些碎片和挫折，主要是恼人的小差异和变化的 XAML 名称空间。如果所有的平台都讲同一个 XAML，那不是很好吗？

这正是 [XAML 标准](https://developer.telerik.com/content-types/tutorials/xaml-standard-demystified/)的目标——将 WPF、UWP 和厦门的所有 XAML 方言统一成一种通用语言。XAML 标准的规范已经公布[并公开](https://github.com/Microsoft/xaml-standard)开发者社区的反馈，最初的努力旨在创建别名以消除 XAML 跨平台的差异。然而，XAML 标准提出了一个特别困难的问题——跨各种平台的 XAML 通常过于关注平台细节。退一步说，对 XAML 标准来说，未来的路很艰难。

## 统一 UI 栈

很明显，XAML 作为 UI 层深受人们喜爱，并且经受住了时间的考验。如今，XAML 为多个桌面和移动应用平台提供支持，而将 XAML 带回网络浏览器的实验性工作正在进行中。开发人员享受丰富的工具为 XAML 开发和繁荣的社区提供支持。

然而，人们也可以说，XAML 周围的信息是混杂的。有多种方法可以构建移动/桌面的 XAML UI 栈，真正的跨平台解决方案需要大量的工作。地平线上有一线希望吗？

原来，。网络核心更新一波接一波地到来。。NET Core 1.x 完全是关于跨平台的 CLR 所以。网络应用可以走出 Windows。。NET Core 2.x 稳定了这艘船——大部分缺失的 API 得到了恢复，web 开发工具成熟了。。NET Core 3 将充分利用。NET 核心回到桌面的 WPF/WinForms 应用程序。能不能？NET Core 4.0 浪潮看跨平台 UI 故事被解决？

如果有一个统一的 UI 层，它是一致的，并且可以跨平台用于 web/desktop/mobile，那不是很好吗？我们可以猜测未来，但 XAML 很可能是最明显的实施抢劫的候选人。十指交叉，为未来干杯！