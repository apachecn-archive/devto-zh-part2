# Swift 中的实例属性与参数

> 原文：<https://dev.to/onmyway133/instance-property-vs-parameter-in-swift-37ae>

[![](img/340d7294b63613b76d35688135c33630.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HlHMuVx2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A89A3rxRYnDn64se_.png)

前几天，我正在重构我代码。我有

```
extension MainController: TabBarViewDelegate {

  func buttonDidPress index: Int) {
    let initialIndex = tabBarView.selectedIndex
    let wholeAppContentView = updateWholeAppContentView()
    view.addSubview(wholeAppContentView)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

委托方法看起来不正确，因为很难区分必需的委托方法还是实例方法。它也缺少一个主题。我喜欢这个帖子 [API 设计](https://mattgemmell.com/api-design/)，你可以阅读第 19 条规则:总是说谁在说话

> 这是一个简单的规则，也是一个同样容易犯的错误。在您的委托方法中，始终将发送方作为参数传递。一直都是。即使是单身。即使是你想象不到的东西也会被同时使用不止一次。没有例外。

所以我重构了这个委托，并遵循它。

```
extension MainController: TabBarViewDelegate {

  func tabBarView(_ view: TabBarView, buttonDidPress index: Int) {
    let initialIndex = tabBarView.selectedIndex
    let wholeAppContentView = updateWholeAppContentView()
    view.addSubview(wholeAppContentView) // This is the culprit ⚠️
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

即使 MainController.swift 中只有一行更改，整个 UI 也会崩溃，因为所有视图都被添加到了选项卡栏中。奇怪的😡。

没过多久，我就记住了，如果参数和实例属性同名，那么它们优先于实例属性。所以在这种情况下，编译器在没有警告的情况下，假设你正在处理来自 TabBarView ⚠️的视图

这就是为什么你经常用自我来消除歧义。

```
struct User: Codable, Equatable {
  let firstName: String
  let lastName: String

  init(firstName: String, lastName: String) {
    self.firstName = firstName
    self.lastName = lastName
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

回到我们的代码。解决方法是指定 self 来指定主控制器的视图

```
self.view.addSubview(wholeAppContentView) 
```

Enter fullscreen mode Exit fullscreen mode

你可能会说，如果标签栏发生变化，谁应该重新添加视图呢😬这是一个坏的例子，但我们从中吸取了教训😇

原帖[https://medium . com/fantageek/instance-property-vs-parameter-in-swift-498 c 65252 df8](https://medium.com/fantageek/instance-property-vs-parameter-in-swift-498c65252df8)