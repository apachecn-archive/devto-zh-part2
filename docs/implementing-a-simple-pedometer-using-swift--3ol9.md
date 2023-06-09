# 使用 Swift 实现简单的计步器

> 原文：<https://dev.to/brightdevs/implementing-a-simple-pedometer-using-swift--3ol9>

好的...但是计步器是什么呢？这是维基百科的定义:

> 计步器是一种设备，通常是便携式的电子或机电设备，通过检测人的手或臀部的运动来计算人走的每一步。

是的，你可以使用名为 CoreMotion 的 iOS 框架创建自己的计步器。

CoreMotion 是众所周知的 iOS 框架。正如我们在[文档](https://developer.apple.com/documentation/coremotion)中读到的，它处理加速度计、陀螺仪、计步器等与环境相关的事件。
在这篇文章中，我想关注计步器事件以及如何处理它们。

## 概述

### cm 计步器

为了使用 CoreMotion 计步器，我们需要仔细看看`CMPedometer`类。它允许用户检索过去采取的步骤的一些信息，例如:过去 3 天我做了多少步？`CMPedometer`类的另一个用途是获取关于已经采取的步骤的实时更新。

```
 open func queryPedometerData(from start: Date, to end: Date, withHandler handler: @escaping CoreMotion.CMPedometerHandler)

open func startUpdates(from start: Date, withHandler handler: @escaping CoreMotion.CMPedometerHandler) 
```

Enter fullscreen mode Exit fullscreen mode

### cmpedomerdata

另一个应该引起我们注意的类是`CMPedometerData`。该类表示在上述函数中每次更新时发送的数据。它包含许多有用的信息，如:

*   `numberOfSteps: NSNumber?`
*   `distance: NSNumber?`
*   `currentPace: NSNumber?`
*   `floorsAscended: NSNumber?`
*   `floorsDescended: NSNumber?`

### CMMotionActivityManager

如果我们想开始计算步数，最好能知道用户此刻正在做什么活动。在一些帮助下，出现了`CMMotionActivityManager`类。使用这个类的实例，我们能够获得关于用户活动类型的更新。为了做到这一点，我们应该调用:

```
open func startActivityUpdates(to queue: OperationQueue, withHandler handler: @escaping CoreMotion.CMMotionActivityHandler) 
```

Enter fullscreen mode Exit fullscreen mode

其结果是用代表单个运动事件更新的数据的`CMMotionActivity`获得更新。该数据是一组布尔值:

*   `stationary: Bool`
*   `walking: Bool`
*   `running: Bool`
*   `automotive: Bool`
*   `cycling: Bool`
*   `unknown: Bool`

## 逐步码...

### 1。将`NSMotionUsageDescription`添加到您的`info.plist`中

正如我们在[苹果文档](https://developer.apple.com/documentation/coremotion)中读到的

> 重要提示
> 在 iOS 10.0 或更高版本上链接的 iOS 应用程序必须在其 Info.plist 文件中包含其所需数据类型的用法描述密钥。不包含这些键将导致应用程序崩溃。为了>访问运动和健身数据，它必须包含 NSMotionUsageDescription。

所以给你的`info.plist` `NSMotionUsageDescription`键添加修改普通文件:

```
<key>NSMotionUsageDescription</key>
<string>In order to count steps I need an access to your pedometer</string> 
```

Enter fullscreen mode Exit fullscreen mode

或者通过 Xcode 添加新密钥

[![info plist motion usage description](img/a0938e6f81bc56ef404901335d173b3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cPlrckbd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tchza38v1v75v15t3280.png)

### [2](#2-create-an-raw-cmmotionactivitymanager-endraw-and-raw-cmpedometer-endraw-instances)。创建一个`CMMotionActivityManager`和`CMPedometer`实例

```
private let activityManager = CMMotionActivityManager()
private let pedometer = CMPedometer() 
```

Enter fullscreen mode Exit fullscreen mode

### 3。创建跟踪活动事件的方法

```
private func startTrackingActivityType() {
  activityManager.startActivityUpdates(to: OperationQueue.main) {
      [weak self] (activity: CMMotionActivity?) in

      guard let activity = activity else { return }
      DispatchQueue.main.async {
          if activity.walking {
              self?.activityTypeLabel.text = "Walking"
          } else if activity.stationary {
              self?.activityTypeLabel.text = "Stationary"
          } else if activity.running {
              self?.activityTypeLabel.text = "Running"
          } else if activity.automotive {
              self?.activityTypeLabel.text = "Automotive"
          }
      }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 4。创建步骤计数更新的方法

```
private func startCountingSteps() {
  pedometer.startUpdates(from: Date()) {
      [weak self] pedometerData, error in
      guard let pedometerData = pedometerData, error == nil else { return }

      DispatchQueue.main.async {
          self?.stepsCountLabel.text = pedometerData.numberOfSteps.stringValue
      }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 5。开始获取更新

```
private func startUpdating() {
  if CMMotionActivityManager.isActivityAvailable() {
      startTrackingActivityType()
  }

  if CMPedometer.isStepCountingAvailable() {
      startCountingSteps()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/1bf7ab9f7180f8491f66ba17e3df3c37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kQ_iDEsa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yo5c8vzy2ikqkog7051q.gif)

## 结论

CoreMotion 是一个强大的框架，除了计步器之外，它还允许您处理来自加速度计和陀螺仪的大量有用数据
您可以在 [Github 资源库](https://github.com/bright/Pedometer-Swift)找到一个示例项目

希望你喜欢这个帖子，欢迎分享和评论。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由 Kamil Wysocki，软件工程师@光明发明
[博客](https://wysockikamil.com)，[推特](https://twitter.com/kamwysoc)