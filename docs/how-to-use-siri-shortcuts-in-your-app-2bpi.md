# 如何在应用程序中使用 Siri 快捷方式

> 原文：<https://dev.to/himaratsu/how-to-use-siri-shortcuts-in-your-app-2bpi>

# 介绍

Siri 快捷方式是 iOS 12 中最大的更新之一。
如果你对 Siri 说了你的任务，Siri 会回应你安装的应用程序。
SiriKit 在 iOS 12 中发布，这使得开发者能够将 Siri 适配到他们的应用中。

最近，我的应用程序有了自适应 SiriKit。我介绍我的代码。

使用 Siri 快捷方式，我们有两种方法。不积极，或意图。今天我介绍“安全”,因为它非常简单。

# 如何使用 Siri 快捷键

## 1.注册 Siri 快捷方式

我的代码:

```
import UIKit
import Intents  // 1\. import Intents framework

class CheckinViewController: UIViewController {

   override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)

        // 2\. register Siri shortcut action when user did some of action
        userActivity = NSUserActivity.checkinActivity
    }
}

extension NSUserActivity {

    public static let checkinType = "com.example.checkin"

    public static var checkinActivity: NSUserActivity {
        let userActivity = NSUserActivity(activityType: checkinType)

        userActivity.isEligibleForSearch = true

        // 3\. set `isEligibleForPrediction` true
        //    you should set recommend phrase to `suggestedInvocationPhrase`
        if #available(iOS 12.0, *) {
            userActivity.isEligibleForPrediction = true
            userActivity.suggestedInvocationPhrase = "Check in"
        }
        userActivity.title = "Check in to the nearest station"

        return userActivity
    }
} 
```

这段代码与苹果公司的样本代码基本相同。当用户做某件事时，你可以用非安全性将它注册到 iOS。

## 2.使用 NSUserActivity

注册 Siri 后，用户可以在设置应用程序> Siri 快捷方式部分进行设置。

通过 Siri 处理用户动作，你实现了委托方法。

```
func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([Any]?) -> Void) -> Bool {

        if userActivity.activityType == NSUserActivity.checkinType {
            // Handle Siri Action Here
            return true
        }

        return false
    } 
```

并在你的 info.plist 中写下定义

```
<key>NSUserActivityTypes</key>
<array>
    <string>com.example.checkin</string>
</array> 
```

# 概述

在这篇文章中，我用我的实际代码写了如何在你的应用程序中使用 Siri 快捷方式。使用 NSUserActivity 的方法非常简单易行。

希望这篇文章对任何人有帮助！

———