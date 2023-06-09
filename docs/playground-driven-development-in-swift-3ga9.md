# Swift 的操场驱动开发

> 原文：<https://dev.to/onmyway133/playground-driven-development-in-swift-3ga9>

[![](img/5df0823820b6715702efca615b76fe56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KLFB9KjP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AEbrVuz1m60emAKFrBdboCg.png)

## 需要快速调整 UI

作为移动开发人员，我们的使命是为最终用户提供最佳的用户体验，通过专用应用程序让他们的生活更精彩、更轻松。其中一项任务是确保用户界面，即用户看到的东西，看起来是好的和正确的。大多数时候，我们可以说 app 是数据的美化器。我们主要从后端获取 JSON 数据，将其解析为模型，然后使用 UIView(主要是 UITableView 或 UICollectionView)呈现它。

对于 iOS，我们需要根据设计不断调整用户界面，使其适合小型手持设备。这个过程包括我们修改代码、编译、等待、检查，然后修改代码等等……像[无瑕应用](https://flawlessapp.io/)这样的工具有助于轻松比较 iOS 应用和草图设计的结果。但是真正的痛苦在于[编译](https://medium.com/@johnsundell/improving-swift-compile-times-ee1d52fb9bd)的部分，这部分花费的时间最多，而对于 [Swift](https://github.com/fastred/Optimizing-Swift-Build-Times) 来说就更糟糕了。快速迭代会降低我们的效率。看起来像是编译器在假装编译的同时偷偷挖掘比特币😅

如果你用 [React](https://reactjs.org/) 工作，你知道它仅仅是状态 UI = f(state)的 UI 表示。你得到一些数据，你构建一个 UI 来表示它。React 有[热加载器](https://github.com/gaearon/react-hot-loader)和[故事书](https://github.com/storybooks/storybook)，这使得它可以超快地进行 UI 迭代。你做了一些改变，并立即看到结果。您还可以获得每个状态的所有可能 ui 的完整概述。你知道你想在 iOS 中得到同样的东西！

## 游乐场

在 WWDC 2014 推出 Swift 的同时，苹果还推出了 Playground，据说这是“探索 Swift 编程语言的一种新的创新方式”。

一开始我并不太相信，我看到了很多关于慢或反应迟钝的操场的抱怨。但在看到 [Kickstarter iOS 应用](https://github.com/kickstarter/ios-oss)使用 Playground 来加快他们的造型和开发过程后，给我留下了深刻的印象。所以我开始在一些应用程序中成功地使用它。它不会像 [React Native](https://facebook.github.io/react-native/) 或 [Injection App](http://johnholdsworth.com/injection.html) 那样立即重新渲染，但希望这些年它会变得更好😇

或者至少它取决于开发社区。Playground 的情况是，我们一次只设计一个屏幕或组件。这迫使我仔细考虑依赖性，所以我可以导入一个特定的屏幕，并在 Playground 中迭代。

## 自定义游乐场框架

Xcode 9 允许在 Playground 中导入[自定义框架，只要框架和 Playground 在同一个工作区。我们可以使用](https://help.apple.com/xcode/mac/9.0/#/devc9b33111c) [Carthage](https://github.com/Carthage/Carthage) 获取自定义框架并构建它。但是如果我们使用椰子，那也是可行的。

[![](img/bc637fad95c49fb3994b1d7134c287ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y28vhOub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2394/1%2AZYy8VCrA3i2tI3zpIXwmEw.png)

## 创建 app 框架

如果 Playground 作为嵌套项目添加，则 Playground 不能访问同一工作区或父项目中的代码。为此，您需要创建一个框架，并添加您打算在 Playground 中使用的源文件。姑且称之为 app 框架吧。

本文的[演示](https://github.com/onmyway133/UsingPlayground)是一个使用 CocoaPods 管理依赖关系的 iOS 项目。这个帖子的时间，是 Xcode 9.3 和 Swift 4.1。

让我们通过使用 CocoaPods 的项目来完成操场工作的步骤。也有一些好的做法。

### 第一步:添加 pod

我主要使用 CocoaPods 来管理依赖关系。在某些屏幕中，肯定会涉及到一些 pod。因此，为了让我们的应用程序框架工作，它需要与一些 pod 链接。

创建一个新项目，姑且称之为 UsingPlayground。该应用程序显示某种纸屑颗粒🎊。有许多选项可以调整这些粒子的显示方式，我选择 Playground 来迭代。

对于这个演示，我们将使用 CocoaPods 来获取一个名为 [Cheers](https://github.com/hyperoslo/Cheers) 的依赖项，因为我们想要一些有趣的东西。如果你想用一些成就来祝贺用户，Cheers 有助于显示花哨的五彩纸屑效果。

使用 UsingPlayground 作为 app [目标](https://guides.cocoapods.org/syntax/podfile.html#target)创建一个 Podfile:

### 步骤 2:在应用程序项目中使用 pod

运行 pod install 后，CocoaPods 会生成一个包含 2 个项目的新工作区。一个是我们的应用程序项目，另一个是一个包含所有 pod 的项目，现在只有欢呼。关闭您的项目并打开生成的工作空间。

这非常简单，只是为了确保 pod 正常工作。编写一些代码来使用 Cheers:

构建并运行项目，享受一个非常迷人的五彩纸屑🎊

### 第三步:添加 CocoaTouch 框架

为了让我们的代码在 Playground 中可访问，我们需要将它设置到一个框架中。在 iOS 中，它是 CocoaTouch 框架目标。

在您的工作区中，选择 UsingPlayground 项目并添加新的 CocoaTouch 框架。这是包含我们的应用程序代码的框架。姑且称之为 AppFramework 吧。

[![](img/95da1138a7a0115f8ce34d7d8394f463.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oahFHhhc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A0C17R-Oym31N9BYA.png)

现在将您想要测试的源文件添加到这个框架中。现在，只需检查文件 ViewController.swift 并将其添加到 AppFramework 目标中。

[![](img/f5c047df7b6b3e4f382854b5c6fb8508.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rFAc0Dk9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AJap3CnRcDmSyo-4aykWsLA.png)

对于这个简单的项目，只有一个 ViewController.swift。如果该文件引用了其他文件中的代码，您也需要将相关文件添加到 AppFramework 目标中。这就是你应该如何巧妙处理[依赖](https://en.wikipedia.org/wiki/Dependency_inversion_principle)的原因。

### 步骤 4:向 AppFramework 添加文件

iOS 中的 ViewController 大部分位于 UI 层，所以它应该只是获取解析后的数据，并使用 UI 组件呈现出来。如果你有一些逻辑，这些可能涉及其他部分，如缓存，网络，…这需要你添加更多的文件到 AppFramework。小的、独立的框架更合理，允许我们快速迭代。

操场没有魔力。每次更改代码时，您都需要编译 AppFramework，否则这些更改不会反映在您的游戏中。如果您不介意缓慢的编译时间，您可以将所有文件添加到 AppFramework 中。简单地扩展组文件夹，选择和添加文件到目标需要很多时间。更不用说，如果你同时选择了文件夹和文件，你将无法将它们添加到你目标中。您只能向目标添加文件。

[![](img/8d131f1c521131a54be28a1f27340bc9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3geVztYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AcOThYP8EGPrjsDnx06Zg1A.png)

更快的方法是在 AppFramework 目标构建阶段编译源代码。这里所有的文件都会自动展开，你需要做的就是选择它们并点击 Add。

[![](img/0749e9b1315345f161988a86dc7c0d26.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4P12-u_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2586/1%2AbROv-S-aMElSPB7BpEOhwA.png)

### 第五步:公开

默认情况下，Swift 类型和方法是内部的。所以为了让它们在操场上可见，我们需要将它们声明为 public。请随意阅读有关 Swift 中[访问级别](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html#//apple_ref/doc/uid/TP40014097-CH41-ID5)的更多信息:

> *开放访问*和*公共访问*使实体可以在它们的定义模块的任何源文件中使用，也可以在导入该定义模块的另一个模块的源文件中使用。当指定框架的公共接口时，通常使用开放或公共访问。

### 第六步:向 AppFramework 添加 pod

为了让 AppFramework 使用我们的 pod，我们还需要将这些 pod 添加到框架目标中。将目标“AppFramework”添加到您的 Podfile:

现在再次运行 pod 安装。在一些罕见的情况下，您需要运行 pod 解除集成和 pod 安装来从头开始。

### 第七步:添加游乐场

添加一个操场，并将其拖动到我们的工作区。让我们称它为我的操场吧。

[![](img/9d4ab507fdbb81859d31884e8a273ab8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oxslg2tR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Aj9II1EmZWpOCFiY3TQl0YA.png)

[![](img/259e265c88460f4d3403bbc252f4b648.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cj1WLFR2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8YWhaZtgb7aSQF1pthuNZA.png)

### 第八步:享受

现在是最后一步:编写一些代码。这里我们需要导入我们的 AppFramework 和我们操场上的 Cheers。我们需要导入操场上使用的所有 pod，就像我们在应用程序项目中所做的一样。

Playground 最适合独立测试我们的框架或我们的应用。选择 MyPlayground 并在下面输入代码。这里我们告诉 liveView 渲染我们的 ViewController:

有时你想测试你想使用的吊舱的一部分。创建一个名为 CheersAlone 的新游乐场页面。这里你只需要导入 Cheers。

[![](img/e3d50024176987a931e7be60135e23c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qFpbZ14A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ak6eGq11QDCwJInOxGBf9AQ.png)

让我们使用 PlaygroundPage 的 [liveView](https://developer.apple.com/documentation/playgroundsupport/playgroundpage/1964506-liveview) 来显示一个实时视图。记得切换编辑器模式，这样你就可以看到操场的结果。和🎉

[![](img/34facb59c68f92118e8e4f646156f018.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9c6f8cii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AfY6TpydIPaDMRUBudSLopw.png)

Xcode 的底部面板有一个按钮。您可以在自动运行和手动运行行为之间切换。而且你可以自己停下来开始游乐场。相当整洁🤘

## 桥接表头

您的应用程序可能需要处理一些预构建的二进制 pod，这些 pod 通过 header 公开 API。在一些应用程序中，我使用 [BuddyBuildSDK](https://cocoapods.org/?q=buddybuildsdk) 进行崩溃报告。如果你看一下它的 [podspec](https://github.com/CocoaPods/Specs/blob/master/Specs/d/4/5/BuddyBuildSDK/1.0.17/BuddyBuildSDK.podspec.json#L24) ，你会看到它使用了一个名为 BuddyBuildSDK.h 的公共头。在我们的应用程序项目中，CocoaPods 很好地管理了这一点。你所需要做的就是通过 Bridging-Header.h 将标题导入到你的应用目标中

如果你需要复习如何使用桥接头，请阅读同一项目中的 [Swift 和 Objective-C。](https://developer.apple.com/library/content/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)

只需确保头的路径是正确的:

[![](img/65f67084ff2a7cf1abb2f793f09ec974.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eQD6EwQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2322/1%2AibjorHdbDd_XMSRGOf3J8Q.png)

### 第一步:导入桥接表头

但是我们的 AppFramework 目标将很难找到 BuddyBuildSDK.h

> 不支持对框架目标使用桥接头

解决方案是在你的 AppFramework.h 里面引用那个桥接头

[![](img/dfd607d9f389a2af9e68da225965e480.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6kUh--vh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AiKT_k0n8gozJSEAxvx2uUA.png)

### 第二步:公共表头

做完以上，你会得到

> 包括框架模块内的非模块化头部

为此，您需要将 Bridging-Header.h 添加到框架中，并将其声明为 public。在销售订单上搜索显示此[报价](https://stackoverflow.com/questions/7439192/xcode-copy-headers-public-vs-private-vs-project)

> **Public:** 这个接口已经完成，并打算供你的产品的客户使用。公共头文件作为可读源代码包含在产品中，没有任何限制。
> **Private:** 这个界面不是为你的客户设计的，或者它还处于开发的早期阶段。产品中包含一个私有标题，但它标记为“私有”。因此，这些符号对所有客户都是可见的，但是客户应该明白他们不应该使用它们。
> **项目:**该接口仅供当前项目中的实现文件使用。除了在目标代码中，项目头不包括在目标中。客户根本看不到这些符号，只有您能看到。

因此，选择 Bridging-Header.h 并将其添加到 AppFramework，并将可见性设置为 public:

[![](img/5eee5044716874c92320bbdfade54791.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UhI97qgi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2852/1%2AMp-FeCeU9qtEWc5Thx75PA.png)

如果你进入 AppFramework 的构建阶段，你会在那里看到 2 个头文件。

[![](img/ba73b74a63814a0f49987ce3c7e107d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HzxLlP_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2570/1%2AnQv6XSSH_-ptsDX_nUOQHg.png)

现在，选择 scheme AppFramework 并点击 Build，它应该编译没有任何错误。

## 字体、本地化字符串、图像和捆绑

我们的屏幕不仅仅包含来自另一个吊舱的视图。更常见的是，我们会展示捆绑包中的文本和图像。让我们向我们的资产目录和 Localizable.strings 添加一个钢铁侠图像。

这里我使用[锚](https://github.com/onmyway133/Anchors)来方便和声明性的自动布局🤘。这也是为了稍后展示 Swift Playground 如何处理任意数量的框架。

现在，使用 Playground 选择应用程序方案，并点击构建和运行。该应用程序应该看起来像下面，当然，它可以拿起正确的图像和本地化的字符串。

[![](img/36973d249ff1929fc522d998e75f5b73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--waL0D_Ai--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A4gH9VnqAP7wvJfRAQIoo1w.png)

让我们看看我们的操场能否识别这些资产。在 MyPlayground 中创建一个名为 Resource 的新页面，并键入以下内容

等一会儿操场跑完。哎呀。操场上的情况不太好，它不识别图像和本地化的字符串😢

[![](img/df475ff5a692bf95de3f3b8ec39de775.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x13F5MTL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2722/1%2AVgzy7nGWLfnydX3SOjmD4Q.png)

### 资源文件夹

实际上，每个游乐场页面都有一个资源文件夹，我们可以在其中放置这个特定页面看到的资源文件。但是这里我们需要访问我们的应用程序包中的资源。

### 主束

当访问图像和本地化字符串时，如果您没有指定捆绑包，运行的应用程序将默认选择主捆绑包中的资源。这里是更多信息[寻找并打开一个包](https://developer.apple.com/documentation/foundation/bundle)。

> 在定位资源之前，必须首先指定包含它的包。Bundleclass 有很多构造函数，但是你最常用的是 [main](https://developer.apple.com/documentation/foundation/bundle/1410786-main) 。主包表示包含当前正在执行的代码的包目录。因此，对于一个应用程序，主 bundle 对象让您可以访问应用程序附带的资源。
> 如果你的应用直接与插件、框架或其他捆绑内容交互，你可以使用这个类的其他方法来创建适当的捆绑对象。

```
// Get the app's main bundle
let mainBundle = Bundle.main

// Get the bundle containing the specified private class.
let myBundle = Bundle(for: NSClassFromString("MyPrivateClass")!) 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 1:向 AppFramework 目标添加资源

因此，首先，我们需要将资源文件添加到 AppFramework 目标中。选择 Asset Catalog 和 Localizable.strings 并将它们添加到我们的 AppFramework 目标中。

[![](img/7e6b1bd2d6abf83c3de064db606ba406.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4RH2QWNw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2808/1%2AmI2C1ode8HGlBe4-zp_5ew.png)

### 第二步:指定捆绑包

如果我们没有指定 bundle，那么默认使用 mainBundle。在已执行的 Playground 的上下文中，mainBundle 指的是它的 Resources 文件夹。但是我们希望 Playground 访问 AppFramework 中的资源，所以我们需要使用 [Bundle.nit(for:)](https://developer.apple.com/documentation/foundation/bundle/1417717-init) 和 AppFramework 中的一个类来引用 AppFramework 内部的 Bundle。该类可以是 ResourceViewController，因为它也被添加到 AppFramework 目标中。

将 ResourceViewController 中的代码更改为

每次我们在 AppFramework 中修改代码，都需要重新编译。这很重要。现在打开操场，它应该拿起正确的资产。

[![](img/83609bcc1ac0b9b3c758eec4fe914fe4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ws4nIPsB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2750/1%2AM0_mNdOVjjV3FjAY4eRy7A.png)

### 自定义字体呢？

我们需要注册字体才能使用。我们可以使用 ctfontmanagerregistersfontsforurl 注册自定义字体，而不是使用 plist 中 application key 提供的字体。这很方便，因为字体也可以在 Playground 中动态注册。

下载名为[avengence](http://www.fontspace.com/the-fontry/avengeance)的免费字体，并将该字体添加到我们的应用和 AppFramework 目标中。

在 ResourceViewController 中添加指定字体的代码，记住要重新编译 AppFramework:

tada，你的应用程序和 Playground 都可以看到自定义字体🎉

[![](img/b96a6789270026086af4487737f700e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q7PCb30l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2686/1%2AIz6t5ai_1hZa0lkdtAkblg.png)

## 设备尺寸和特性集合

iOS 8 引入了 [TraitCollection](https://developer.apple.com/documentation/uikit/uitraitcollection) ，定义了大小等级、比例和用户界面习语，简化了设备描述。kickstarter-ios 项目提供了方便的工具来准备 UIViewController，以便在不同的特性下在操场上使用。参见[游乐场控制器](https://github.com/kickstarter/ios-oss/blob/master/Kickstarter-iOS.playground/Sources/playgroundController.swift):

以及 [AppEnvironment](https://github.com/kickstarter/ios-oss/blob/1b21ce9100edc2700b30f41432f4c6077febba69/Library/AppEnvironment.swift) ，它就像一个堆栈，可以改变依赖关系和应用属性，比如 bundle、locale 和 language。参见一个关于[注册屏幕](https://github.com/kickstarter/ios-oss/blob/7b7be2f6ca7012bef04db90a6ed64f03f661a8f2/Kickstarter-iOS.playground/Pages/Signup.xcplaygroundpage/Contents.swift)的例子:

## 无法查找符号

使用 Playground 时，您可能会遇到一些错误。有些是因为你的代码，有些是因为框架的配置方式。对我来说，在升级到 [CocoaPods 1.5.0](http://blog.cocoapods.org/CocoaPods-1.5.0/) 之后，我得到了这个:

```
error: Couldn’t lookup symbols:

__T06Cheers9CheerViewCMa

__T012AppFramework14ViewControllerCMa

__T06Cheers8ParticleO13ConfettiShapeON

__T06Cheers6ConfigVN 
```

Enter fullscreen mode Exit fullscreen mode

符号查找的问题意味着 Playground 找不到你的代码。这可能是因为您的类没有公开为 public，或者您忘记向 AppFramework 目标添加文件。或者引用的窗格在 AppFramework 框架搜索路径中不可见，…

版本 1.5.0 带来了静态库支持，也改变了模块化头。同时，演示切换回 CocoaPods 1.4.0，你可以使用操场演示来看看[。](https://github.com/onmyway133/UsingPlayground)

在终端中，键入 bundler init 以生成 Gemfile。为 cocoapods gem 指定 1.4.0:

现在运行 bundler exec pod install，从 CocoaPods 1.4.0 运行 pod 命令。这个问题应该得到解决。

## 何去何从

Swift Playground 也支持 macOS 和 tvOS。如果你想了解更多，这里有一些有趣的链接

*   [Playground Driven Development](https://www.youtube.com/watch?v=DrdxSNG-_DE)[Brandon Williams](https://dev.toundefined)和 [kickstarter-ios](https://github.com/kickstarter/ios-oss/tree/master/Kickstarter-iOS.playground) 项目的演示启发了如何在生产应用中使用 Playground。此外，objc.io 上关于[操场驱动开发](https://talk.objc.io/episodes/S01E51-playground-driven-development-at-kickstarter)的演讲非常好。

*   point free:[网站](https://github.com/pointfreeco/pointfreeco/tree/master/PointFree.playground)是借助 Playground 完成的。仅仅通过阅读代码和它们的项目结构，你就可以学到很多东西。

*   [使用 Swift 可视化算法](https://www.youtube.com/watch?v=7e13FierAF8&index=3&list=PLCl5NM4qD3u92PwamgwWr3e_j3GmKRVTs) : Playground 也有助于原型化和可视化您的想法。

*   我的朋友菲利普也写了他是如何在工作中成功利用操场的

*   此外，如果你想感到惊讶，翁贝托·莱蒙迪还策划了一系列[的超棒游乐场](https://github.com/uraimo/Awesome-Swift-Playgrounds)。

原帖[https://medium . com/无瑕-app-stories/playground-driven-development-in-swift-cf 167489 Fe 7b](https://medium.com/flawless-app-stories/playground-driven-development-in-swift-cf167489fe7b)