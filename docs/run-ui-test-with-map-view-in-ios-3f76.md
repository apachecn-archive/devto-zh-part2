# 在 iOS 中使用地图视图运行 UI 测试

> 原文：<https://dev.to/onmyway133/run-ui-test-with-map-view-in-ios-3f76>

[![](img/28dc1d0aeb78ec6367032db8efddf1f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uN_rv2zi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A04GmSrYKIeWIT9-n.gif)

## 嘲弄地点

您应该模拟一个位置以确保可靠的测试

### 创建 gpx 文件

进入 Xcode ->文件->新建-> GPX 文件

[![](img/e53401acf171c09d387f5140ff085e9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eVaUM58L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2ABl6CKaJxrFYZN9gn.png)

似乎

```
<?xml version="1.0"?>
<gpx version="1.1" creator="Xcode">
  <wpt lat="59.913590" lon="10.733750">
    <name>Oslo S</name>
    <time>2017-05-31T14:55:37Z</time>
  </wpt>
  <wpt lat="59.913590" lon="10.733750">
    <name>Oslo S</name>
    <time>2017-05-31T14:55:40Z</time>
  </wpt>
</gpx> 
```

Enter fullscreen mode Exit fullscreen mode

gpx 文件非常强大，因为它允许你指定一条不同移动速度的路线。

> *提供一个或多个包含纬度/经度对的航路点。如果您提供一个
> 航路点，Xcode 将模拟该特定位置。如果您提供多个航点，
> Xcode 会模拟一条访问每个航点的路线。*
> *可选地为每个航点提供一个时间元素。Xcode 将根据每个航路点之间经过的时间，以一定的速度插入移动
> 。如果你不提供
> 一个时间元素，那么 Xcode 将使用一个固定的速率。航路点必须按时间升序排列。*

### 使用 gpx 文件

*   在 app target 中声明 gpx 文件，而不是 UITests target。进入你的应用方案->运行->选项

[![](img/32b9c961d5f028edd34d0b051dabf95c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AhaEp1FK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AxWPbMJ7JXmETRE0A.png)

*   进入模拟器->调试->位置->自定义位置，并选择相同位置，只是为了确保。它不需要是相同的，但是我发现如果没有自定义位置，它就不能在 UITests 中工作

## 测试你在初始位置附近

```
let map = app.maps.element(boundBy: 0)
let predicate = NSPredicate(format: "label CONTAINS 'City Hall'")
let cityHall = map.otherElements.matching(predicate).element(boundBy: 0)

// wait for the map to finish loading and zooming
wait(for: cityHall, timeout: 2)
XCTAssertTrue(cityHall.exists) 
```

Enter fullscreen mode Exit fullscreen mode

等待功能来自 [#44](https://github.com/onmyway133/blog/issues/44)

## 测试您是否可以与您的自定义 pin 进行交互

您需要指定 accessibilityIdentifier，比如

```
class MyPin: MKAnnotationView {
  override func didMoveToSuperview() {
    super.didMoveToSuperview()

    accessibilityIdentifier = "myPin"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后查询该 pin。不是说不在地图里面，是在 app 里面

```
let pin = app.otherElements.matching(identifier: "myPin").element(boundBy: 0)

XCTAssertTrue(pin.exists) 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
你应该使用 accessibilityIdentifier

accessibilityIdentifier 来自 UIAccessibilityIdentification 协议。您不应使用 accessibilityLabel，请参见 [kif-framework/KIF#243](https://github.com/kif-framework/KIF/issues/243)

> *鉴于 accessibilityLabel 是一个面向外部的字符串，实际上是由辅助功能屏幕阅读器使用的(并且应该本地化为设备用户的语言)，Apple 现在提供了一个专门用于 UI 自动化目的的替代属性(iOS 5+)*

原帖[https://medium . com/fantageek/run-ui-test-with-map-view-in-IOs-7 ebfe 925d 7 f 7](https://medium.com/fantageek/run-ui-test-with-map-view-in-ios-7ebfe925d7f7)