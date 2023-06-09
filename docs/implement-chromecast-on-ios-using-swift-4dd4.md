# 使用 Swift 在 iOS 上实施 Chromecast

> 原文：<https://dev.to/lawgimenez/implement-chromecast-on-ios-using-swift-4dd4>

我打算使用 Swift 在 iOS 上实现谷歌的 Chromecast。我将使用谷歌 Chromecast 2 设备和 Xcode 9.4.1。

首先让我们阅读 [Google Cast 文档](https://developers.google.com/cast/)来熟悉它的实现。在熟悉入门部分之后，我们需要[来注册谷歌 Chromecast 设备。](https://cast.google.com/publish/)Google Cast 开发者注册将一次性收取 5 美元的费用。付款后，我们将被重定向到 [Google Cast SDK 开发者控制台](https://cast.google.com/publish/#/overview)

### 开发设置

假设你已经安装了 Google Home 应用或 Chrome 浏览器扩展，如果没有，请安装。之后，我们将[注册 Chromecast 设备。](https://cast.google.com/publish/#/overview)点击**添加新应用**。现在我将选择**定制接收器**并填写必要的信息。

接下来，在 **Cast 接收器设备**下，点击**添加新设备**并输入您的 Chromecast 序列号和您自己的描述。保存设备后，其状态应显示为*正在注册*。请注意，注册设备需要几分钟时间。

### 发送方申请

现在我们将为 Chromecast 创建 sender 应用程序。假设您已经使用 Cocoapods 设置了 Xcode 项目，如果没有，请这样做。发送者应用程序是指我们的移动设备或笔记本电脑，它将处理播放。

打开您的`Podfile`，在下面添加下面一行，然后在您的终端上键入`pod install`。
`pod ‘google-cast-sdk’, ‘4.3.1’`

### 集成 CAF (Cast 应用框架)

打开`AppDelegate.swift`，在方法`didFinishLaunchingWithOptions`中，在
下面添加以下代码行

```
import UIKit
import GoogleCast

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
    private let appId = "0FFF55BD"

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        // Initialize Google Cast SDK
        let discoveryCriteria = GCKDiscoveryCriteria(applicationID: appId)
        let castOptions = GCKCastOptions(discoveryCriteria: discoveryCriteria)
        GCKCastContext.setSharedInstanceWith(castOptions)
        GCKLogger.sharedInstance().delegate = self
        return true
    }

extension AppDelegate: GCKLoggerDelegate {

    func logMessage(_ message: String, at level: GCKLoggerLevel, fromFunction function: String, location: String) {
        print("Message from Chromecast = \(message)")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意`appId`变量，这是您在 **Google Cast SDK 开发者控制台**中注册应用程序时的应用程序 ID。

让我们在导航栏中添加一个 Cast 按钮。打开`ViewController.swift`并添加以下代码块。

```
import UIKit
import GoogleCast

class HomeViewController: UIViewController {

    @IBOutlet weak var navItem: UINavigationItem!

    override func viewDidLoad() {
        super.viewDidLoad()
        let castButton = GCKUICastButton(frame: CGRect(x: 0, y: 0, width: 24, height: 24))
        castButton.tintColor = UIColor.red
        let castBarButtonItem = UIBarButtonItem(customView: castButton)
        navigationItem.rightBarButtonItem = castBarButtonItem
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

构建并运行。它应该有一个红色的 Chromecast 按钮出现在导航栏的右侧。

[![Simulator-Screen-Shot---iPhone-8---2018-08-19-at-09.51.49](img/de700767cc6058fa5439dddb19391816.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vyeyRMip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ysy59aqy32vi28uvxk20.png) 
点击 Cast 按钮应该会显示一个 ViewController，其中列出了找到的 Chromecast 设备。

[![Simulator-Screen-Shot---iPhone-8---2018-08-19-at-09.53.08](img/756ec2d7734d1c4b03f21ad36f353f80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E5BrRRMZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ub9obkwjmwith3k9lnr4.png) 
如果一开始导航条上没有显示 Cast 按钮，尝试打开你的 Chromecast 设备。根据我的测试，如果没有找到 Chromecast 设备，Cast 按钮将不会出现。

我使用值`https://lawgimenez.me`作为接收者应用程序 URL 的值。当一切顺利时，Chromecast 应该可以将我的网站投射到我的电视上。当我点击 Gimenez 房间的电视时，它应该会显示我的网址的内容。我在真实设备和模拟器上测试了这个实现。

[![IMG_0714](img/51220870a22835d0672c75f75ece1921.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D3-E1goE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x23fqrmdplvwjh22go2m.png)

最初发布于[https://lwg mnz . me/2018/08/20/implement-chrome cast-on-IOs-using-swift/](https://lwgmnz.me/2018/08/20/implement-chromecast-on-ios-using-swift/)