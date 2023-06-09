# HealthKit —入门— HKObjectType 和授权

> 原文：<https://dev.to/leogdion/healthkit--getting-started--hkobjecttype-and-authorization-5ak3>

HealthKit 是构建健康、健身和锻炼应用程序时最重要的 API。HealthKit 也有许多错综复杂的细节，需要在入门时介绍。

数据结构鼓励精确性，同时具有存储各种值的灵活性。它要求用户提供应用访问 HealthKit 的方式可能与许多 iOS 开发者类似。HealthKit 遵循其他 iOS APIs 中的许多模式，如核心位置或核心数据。同时，它使用更新的编程技术和更新的技术。

## 我为什么要开发一款健身应用

作为一名独立的软件开发人员，锻炼是我时间表中的重要部分。它让我保持健康，思维活跃，身体得到刺激。

为了充分利用时间，我开始研究 **[高强度间歇训练](https://en.m.wikipedia.org/wiki/High-intensity_interval_training)** 。**高强度间歇训练**有很多好处，但主要策略是在长时间的高强度和低强度间歇之间切换。

例如，我已经开始在踏步机上锻炼 30 分钟。在这段时间里，我会改变机器的速度，让我的心率在每分钟 150 到 125 次之间波动，每次 15 到 30 秒。现在，我使用苹果手表上的内置锻炼应用程序，在整个锻炼过程中检查我的心率。

[![](img/63b1dd159559a22442084296562e198c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KL0zStBo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2484/0%2AcVlPZdc5MtuA-t2y.)

然而，如果有一个定制的应用程序能够在一段时间内当我达到一定的心率时通知我，那就更好了。作为 Apple Watch 的所有者，他从第一天起就为 Apple Watch 开发了自己的应用程序，这是一个了解 HealthKit 更多信息的好机会。

## 健康工具包结构—数量类型和工作类型

HealthKit 中的一个关键类是`HKObjectType`。`HKObjectType`是定义不同数据类型的基类。例如在心率的情况下，将是一个`HKQuantitiyType`。若要访问特定类型，请使用正确的 HKQuantityTypeIdentifier 调用 hkquantity . quantity type(for identifier:hkquantity type identifier)。例如，获取心率 HKQuantityType:
hkobjecttype . quantity type(for identifier:。heartRate)
另一个 HKObjectType 是 HKWorkoutType，通过调用 HKObjectType.workoutType()来访问。

[![](img/122728c195a55c3b8382e6d8b07ae9f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B44UQVG3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AU-2aKHIoHZf428h_.)

对于这些类型，我们要做的第一件事是向用户请求读写 HealthKit 的权限。

## 在 HealthKit 中请求授权

苹果尽力做的一件事是确保用户允许各种 API。就像位置信息和通知一样，**用户必须允许应用程序权限才能访问 HealthKit** 。

首先需要做的是通过调用 hkhealthstore . ishealthdatavailable()来检查 HealthKit 是否可用。如果 HealthKit 不可用(比如在 IPad 上)，向用户发送通知。

接下来通过类 HKHealthStore 访问 HealthKit。调用 HKHealthStore()实例化一个要在整个应用程序中使用的对象。使用 HKHealthStore 对象，通过。request authorization(to share:，read:，completion:)方法。toShare 和 read 参数采用一组可选的 HKSampleType(数据样本的 HKObjectType 子类)进行写入，并采用一组可选的 HKObjectType 进行读取。

[![](img/aaba30613a064050faac235df803c9bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hw59DHd6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AgMieSTTWZYpOyy4a.)

**每当这个方法带有新的**HKObjectType** 或 **HKSampleType** 对象时(比如第一次运行 app 时)，就会显示权限屏幕。* *
request authorization 是一个异步方法，这意味着一旦请求完成，就会调用完成。完成闭包是您开始与用户交互并开始查询数据的地方。

让我们来看看如何正确地处理请求授权。

```
if HKHealthStore.isHealthDataAvailable() {
  let healthStore = HKHealthStore()
  let heartRateQuantityType = HKObjectType.quantityType(forIdentifier: .heartRate)!
  let allTypes = Set([HKObjectType.workoutType(),
                      heartRateQuantityType
    ])
  healthStore.requestAuthorization(toShare: nil, read: allTypes) { (result, error) in
    if let error = error {
      // deal with the error
      return
    }
    guard result else {
      // deal with the failed request 
      return
    }
    // begin any necessary work if needed
  }
} 
```

## 结论

HealthKit 是一个复杂但易于操作的 API，用于构建锻炼和健康应用程序。

*   HKObjectType–health kit 数据的基类。

*   HKHealthStore 请求授权读取和数据的接入点。

*   。request authorization(to share:，read:，completion:)-请求授权在 HKHealthStore 对象上读写哪些数据。

*   当对请求的授权进行任何更改时，将显示许可表。

*最初发布于 2018 年 5 月 25 日[learningswift.brightdigit.com](http://learningswift.brightdigit.com/2018/05/25/healthkit-getting-started/)。*