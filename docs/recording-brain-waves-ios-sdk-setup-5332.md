# 记录脑电波- iOS SDK 设置

> 原文：<https://dev.to/ladvien/recording-brain-waves-ios-sdk-setup-5332>

# 第一步:iOS 应用

我假设你已经安装了 Xcode。

### 步骤 1.1:安装 CocoaPods

[CocoaPods](https://guides.cocoapods.org/using/getting-started.html) 是 Xcode 的包处理程序。我们将使用它来安装 [Alamofire](https://github.com/Alamofire/Alamofire) ，这是一个用于发出 HTTP 请求的 Swift 库。我们将需要 HTTP 调用支持，因为我们将调用我们的服务器来存储脑电图样本。

```
sudo gem install cocoapods 
```

Enter fullscreen mode Exit fullscreen mode

当你点击回车键后，它会提示你输入密码

[![cocoapods-installation](../Images/ab6dd870f344bb83b7d733c22511a3d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2b-EYw-E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/cocoapods_installation.png)

### 步骤 1.2:设置 Xcode 项目

现在，让我们设置一个项目文件夹。这是所有 iOS 应用程序代码所在的主文件夹。这是一个坏习惯，但是我通常把我的放在桌面上。

打开 Xcode 并选择“创建新的 Xcode 项目”

[![xcode-project-start](../Images/f25e0e2fea1451f0471d9d8a54bf9ea5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--05wbWSG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/xcode_project_start_1.png)

然后选择“单视图应用程序”并点击“下一步”

[![xcode-project-start](../Images/3796a449f830e15d87e676e5db6dbb68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d66o_tCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/xcode_project_start_2.png)

让我们调用项目`MindWaveJournaler`并点击“下一步”
[![xcode-project-start](../Images/a3a4e27ebe6eabb9dfa897a84d02fde8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XdxvX91a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/xcode_project_start_3.png)

选择你的桌面作为项目的位置，点击【创建】
[![xcode-project-start](../Images/3a24bef8f15b4569b728fa44dc2bf8fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DGmS-lFo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/xcode_project_start_4.png)

### 步骤 1.3:开发环境设置

您已经创建了一个项目文件夹，但是我们必须设置项目文件夹以便与 CocoaPods 一起使用。之后，我们将使用 CocoaPods 安装 Alamofire。

回到终端，键入:

```
cd ~/Desktop/MindWaveJournaler
pod init 
```

Enter fullscreen mode Exit fullscreen mode

这在我们项目的根文件夹中创建了一个`Podfile`。我们可以在 Podfile 中列出 CocoaPod 包，并在同一个目录中运行`pod install`,这将导致 CocoaPods 安装我们列出的所有包。

可悲的是，我们现在这么做只是为了阿拉莫菲尔。但是，后来，当我们开始构建这个应用程序时，它将允许我们快速访问第三方框架。

好了，回到打字:

```
open -a Xcode Podfile 
```

Enter fullscreen mode Exit fullscreen mode

这将在 Xcode 中打开 Podfile 进行编辑。现在让我们插入我们想要的 pod 信息。

复制以下信息并将其粘贴到您的文件中:

```
# Uncomment the next line to define a global platform for your project
platform :ios, '11.4'

target 'MindWaveJournaler' do
  # Comment the next line if you're not using Swift and don't want to use dynamic frameworks
  use_frameworks!

  # Pods for MindWaveJournaler
  pod 'Alamofire', '~> 4.7'

  target 'MindWaveJournalerTests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'MindWaveJournalerUITests' do
    inherit! :search_paths
    # Pods for testing
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

你可能会注意到我们所做的唯一改变是

```
platform :ios, '11.4'
...
pod 'Alamofire', '~> 4.7' 
```

Enter fullscreen mode Exit fullscreen mode

这几行告诉 CocoaPods 我们的应用程序针对的是哪个版本的 iOS(这将消除警告，但不应该是必需的)。另一个是告诉 CocoaPods 我们希望在这个项目中使用哪个版本的 Alamofire。

好，现在让我们运行这个 Podfile。

回到与 Podfile 类型相同的目录:

```
pod install 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到 CocoaPods 的输出如下所示。

[![cocoapods-installed-alamofire](../Images/cf3205a9a829817642beaf5d58dcf422.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dvWU4Cwm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/alamofire_pod_installed.png)

### 步骤 1.4:安装 NeuroSky iOS SDK

NeuroSky 有一个“Swift SDK”实际上，这是一个“桥接”到 Swift 中的 Objective-C SDK。本质上，这意味着我们无法看到 SDK 上发生了什么，但我们可以使用预编译二进制文件中的函数。

我对 NeuroSky 的网站没什么印象。或者 SDK。它完成了工作，但仅此而已。

不管怎么说，SDK 下载在注册墙的后面，令人恼火。

*   [NeuroSky iOS SDK](https://store.neurosky.com/products/ios-developer-tools-4)

访问上面的链接并点击“添加到购物车”

[![neurosky-sdk-sign-up](../Images/f98fd66e6e8c493b54f758f9e89cab22.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tBHqW_KX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/neurosky-sdk-download-1.png)

然后“继续结账”

[![neurosky-sdk-sign-up](../Images/5d4c2c606072b522caf6936008e9611a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3p64UNOu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/neurosky-sdk-download-2.png)

最后，你必须输入你的“账单信息”真的，这只是你的电子邮件地址，姓氏，街道地址，城市和邮政编码。

(真的是 NeuroSky？这很有 1990 年的味道。)

呃，我自己编的。

反正你输入信息后点击，然后点击“继续支付宝”(什么？我只是提供了我的信息...)你应该得到一个下载链接的奖励。点击它并下载文件。

[![neurosky-sdk-sign-up](../Images/0d649385d10cf637ac37cc2a2b622d0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0oJePzCA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/neurosky-sdk-download-5.png)

解压文件并浏览`lib`文件夹

```
iOS Developer Tools 4.8 -> MWM_Comm_SDK_for_iOS_V0.2.9 -> lib 
```

Enter fullscreen mode Exit fullscreen mode

将所有文件从`lib`文件夹复制到`MindWaveJournaler`项目文件夹的主目录中。

[![neurosky-sdk-lib](../Images/715f991799f5fb510b79ba4c7117ff85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0tPgKnqf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/neurosky-sdk-download-7.png)

### 步骤 1.5:工作区设置

CocoaPods 的工作原理是创建一个`.xcworkspace`文件。它包含了编译安装了所有 CocoaPod 包的项目*所需的所有信息。在我们的例子中，这个文件将被称为`MindWaveJournaler.xcworkspace`。每次您想要处理项目时，都必须用这个特定的文件打开它。*

这可能有点令人困惑，因为 Xcode 创建了一个很容易点击的`.xcodeproj`文件。
[![xcworkspace](../Images/7c2977543b7be5dfd1205f9d0cb4c65b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EiaSqaBl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mind-wave-journaler-project-setup-1.png)

继续打开`MindWaveJournaler.xcworkspace`文件。工作区打开时应该会出现一个警告，我们将很快解决这个问题。

但首先，另一个警告。CoreBluetooth，苹果的蓝牙 le 框架，*只有*在实际设备上编译和运行时才有效。*它在 iOS 模拟器中不起作用。*如果你的 Mac 有硬件的话，曾经有过，但是，我认为苹果不喜欢支持这种混乱，所以放弃了它。*

[![eeg-apple-workspace](../Images/23876d6597a2b2e8b3025939b78bfb4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YvLHfAwC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mind-wave-journaler-project-setup-2.png)

继续前进。单击黄色警告。然后点击侧边栏中的警告。这将创建一个提示，询问您是否想要进行一些更改。这应该会自动对构建设置进行一些调整，这应该会使我们的项目更好。

点击`Perform Changes`。
[![eeg-apple-workspace-resolve-warning](../Images/b47ea1d3885774a0e3e837b2cc5251d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tiZJP3to--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mind-wave-journaler-project-setup-3.png)

这应该会消除警告，并使您的项目没有错误。继续点击`Play`按钮，让它编译到模拟器中(我们没有测试蓝牙，所以没问题)。一切都应该正确编译，如果没有，请在评论中让我知道您的问题的具体情况。

### 步骤 1.5:启用安全 HTTP 请求

我们仍然需要对 Xcode 工作区进行一些调整，以使一切正常工作。

首先，打开`ViewController.swift`文件，在`import UIKit`的正下方添加`import Alamofire`。如果自动完成将 Alamofire 列为一个选项，您就知道工作区正在检测它的存在。好交易。

现在，为了让 Alamofire 能够安全地发出 HTTP 请求，需要在`Info.plist`文件中添加一个选项。直到 Manab Kumar Mal 的 StackOverflow 帖子发布后，我才明白为什么 HTTP 调用没有成功:

*   [无法加载资源](https://stackoverflow.com/a/32631185/2108441)

谢了伙计。

好了，按照他的指示打开 MindWaveJournaler 文件夹中的`Info.plist`文件。现在通过右键单击并选择`Add Row`来添加一个条目。将`Application Category`改为`NSAppTransportSecurity`，并确保设置为`dictionary`。现在，单击新字典旁边的加号，将该属性设置为`NSAllowsArbitraryLoads`，设置类型`bool`，值为`YES`。

[![eeg-apple-workspace-add-secure-layer](../Images/ce75dfa00ec48d2c2a1a1b2feaf70f23.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dfYBnjGF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mind-wave-journaler-project-setup-4.png)

### 步骤 1.5:为 MindWave SDK 设置 Objective-C 桥接接头

不过，还有一些其他的家务。正如我前面提到的，MindwAve SDK 是 Objective-C 预编译的二进制文件。它可用于 Swift 项目，但需要设置一个“桥头”文件。

*   [手动创建桥接头](https://stackoverflow.com/a/39615171)

首先创建桥头文件。转到`File -> New -> File...`

[![bridge-header-file](../Images/e6666c212fa0f8afc1cdd2c33a16c836.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p_3Sf_dp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/bridge-header-setup-01.png)

然后选择`Header`，点击`Next`。

[![bridge-header-file](../Images/14dfca09a84bad43f2e4fa2a97d80b02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PawIXk9w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/bridge-header-setup-02.png)

将文件命名为`YourProjectName-Bridging-Header`和**，确保文件保存到包含`.xcworkspace`文件**的同一个文件夹中，然后点击`Create`。

[![bridge-header-file](../Images/415e915187ae20914642cc3e2c3f6d47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G7F0-SJk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/bridge-header-setup-03.png)

头文件应该会自动打开。将以下内容复制并粘贴到头文件的底部。

```
#import "MWMDevice.h"
#import "MWMDelegate.h"
#import "MWMEnum.h" 
```

Enter fullscreen mode Exit fullscreen mode

我的整个档案看起来就像这样。

#### MindWaveJournaler-Bridging-header . h

```
//
//  MindWaveJournaler-Bridging-Header.h
//  MindWaveJournaler
//
//  Created by Casey Brittain on 8/3/18.
//  Copyright © 2018 Honeysuckle Hardware. All rights reserved.
//

#ifndef MindWaveJournaler_Bridging_Header_h
#define MindWaveJournaler_Bridging_Header_h

#endif /* MindWaveJournaler_Bridging_Header_h */

#import "MWMDevice.h"
#import "MWMDelegate.h"
#import "MWMEnum.h" 
```

Enter fullscreen mode Exit fullscreen mode

让我们告诉 Swift 编译器我们有一个头文件。在 Xcode 中，转到`Project File -> Build Settings -> All`，然后在搜索框中输入`Swift Compiler - General`(如果不包括连字符和空格，它就找不到)。

[![bridge-header-file](../Images/ff8f7e9ceb95625d56f0b2d02e8ef1c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QTrMIk23--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/bridge-header-setup-04.png)

双击项目名称正下方的行`Objective-C Bridging Header`(见图中的红框)。将以下内容复制并粘贴到框中，然后单击“关闭”保存更改。

```
$(PROJECT_DIR)/$(PROJECT_NAME)-Bridging-Header.h 
```

Enter fullscreen mode Exit fullscreen mode

这会创建一个到桥接头文件的相对路径。接下来我们将尝试编译，如果您发现这个文件没有被找到，那么它可能没有按照我们的命名方案(`YourProjectName-Bridging-Header`)命名，或者它没有保存在与`.xworkspace`文件相同的文件夹中。没关系，如果你有困难，请在下面给我留言。

[![bridge-header-file](../Images/5d79597ca07fa29056eb93ef52923638.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pUjok_b1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/bridge-header-setup-05.png)

在我们准备编码之前，还有最后一件事要做。我们仍然需要将 MindWave SDK 导入到我们的项目中。

[![bridge-header-file](../Images/496cd2d5422fa6dea99de0f348b7135f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--88Yudd2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mindwave-sdk-1.png)

右键单击您的项目文件并选择`New Group`。将群组命名为`MindWave SDK`。现在右击你创建的文件夹，选择`Add Files to "MindWave SDK"...`。导航到包含 MindWave SDK 的`lib`文件夹，并选择其中的所有文件。

[![mindwave-sdk](../Images/102d9969ed097be98aa2a52533234a5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---JY2XiHo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mindwave-sdk-2.png)

当您添加 SDK 时，Xcode 应该会自动检测二进制文件(`libMWMSDK.a`)并创建一个指向它的链接。但是，让我们确定一下，以防万一。点击您的项目文件，然后转到`General`选项卡。

[![mindwave-sdk](../Images/56aff6433a544da4d78d035361bc9ac8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TtPw3GdA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mindwave-sdk-3.png)

它也需要在`Linked Frameworks and Libraries`下的`Build Phases`选项卡下链接。

[![mindwave-sdk](../Images/5daa29a2f0426cadda381bb9f46c4fa9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZZ7O97Fv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mindwave-sdk-4.png)

就是这样。让我们测试一下，确保你的应用能正确找到 SDK。

打开`ViewController`文件，在现有代码后的`viewDidLoad()`下，键入:

```
let mwDevice = MWMDevice()
mwDevice.scanDevice() 
```

Enter fullscreen mode Exit fullscreen mode

观察自动完成检测 MindWave SDK 的存在

[![mindwave-sdk](../Images/df8e7e86b38a0e9a2c98898ede940c2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3w11BwrL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mindwave-sdk-5.png)

现在进行真正的测试，`Compile`和`Run`。但是，在我们这样做之前，**请注意——这将只在实际的 iOS 设备上工作。如果你试图在 iOS 模拟器中运行它，它将会失败。**它实际上失败有两个原因，首先，`CoreBluetooth`不能在 iOS 模拟器中工作，其次，MindWave SDK 二进制文件是专门针对 ARM 架构编译的。

好吧！序言够了。连接并选择您的 iOS 设备，然后点击`Run`。

[![mindwave-app-run](../Images/a2acadd03db89aff03855777b3c08956.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M5Yc-Uii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/mindwave-app-run-1.png)

如果一切顺利，你应该看到两件事。一个空白的白色屏幕出现在你的手机和 Xcode 控制台的相关信息。

[![corebluetooth-error-api-misuse](../Images/75023d7d0eedaa7bc55e65b8a16518d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P78VPCeh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/corebluetooth-error-1.png)

`CoreBluetooth`错误与启动 iOS 蓝牙服务*有关，没有*检查以确保 iOS BLE 已打开并准备就绪。这是一件好事，这可能意味着 MindWave SDK 已经被找到并且运行正常。

如果有其他错误，我们来聊聊。我会尽力帮忙的。

这是一个系列的一部分，我用我所有的时间小心翼翼地写着。我会尽快完成下一部分。