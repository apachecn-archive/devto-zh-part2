# 在 iOS 中使用脸书登录运行 UITests

> 原文：<https://dev.to/onmyway133/running-uitests-with-facebook-login-in-ios-1mg0>

[![](../Images/1c4dc85ec380a0463d1a3f8fe32dba91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pFHyE9Ip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AOpf2sAlTPclE_4kE.jpg)

今天，我试图在我的应用程序上运行一些 UITest，它使用脸书登录。这是我的一些笔记。

[![](../Images/c3d8e1b5536adb2a2dad7de73325c79b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l11t0OCV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2Ae0lQASZpw5qGT7jn.gif)

## 挑战

*   脸书的挑战是它使用 Safari 控制器，我们现在主要处理网络视图。从 iOS 9+开始，脸书决定使用 safari 而不是原生的 Facebook 应用程序，以避免应用程序切换。你可以在这里阅读详细内容[为 iOS 9 用户打造最佳脸书登录体验](https://developers.facebook.com/blog/post/2015/10/29/Facebook-Login-iOS9/)

*   它没有所需的可访问性标识符或可访问性标签

*   webview 内容将来可能会发生变化😸

## 创建一个脸书测试用户

幸运的是，您不必创建自己的脸书用户来进行测试。脸书支持测试用户，你可以管理权限和朋友，非常方便

[![](../Images/db999bb88074b882fbf12516779a5b86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HsQqFtau--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/0%2AkVdiqx7CB7b43dRw.png)

创建测试用户时，您可以选择语言。这将是 Safari web 视图中显示的语言。我现在选择挪威的🇳🇴

[![](../Images/c2d162040b182ca272dcae4d950442b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qn5fyFnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AH7V1GZN413eb1y4n.png)

## 点击登录按钮，显示脸书登录

这里我们使用默认的 FBSDKLoginButton

```
var showFacebookLoginFormButton: XCUIElement {
  return buttons["Continue with Facebook"]
} 
```

Enter fullscreen mode Exit fullscreen mode

然后点击它

```
app.showFacebookLoginFormButton.tap() 
```

Enter fullscreen mode Exit fullscreen mode

## 检查登录状态

当进入 safari 脸书表单时，用户可能已经登录或没有登录。所以我们需要处理这两起案件。当用户登录后，脸书会说“你已经登录了”或“确定”按钮。

这里的建议是放断点和 po app.staticTexts，po app.buttons，看看某个点上有哪些 UI 元素。

您可以检查静态文本，或者只检查 OK 按钮

```
var isAlreadyLoggedInSafari: Bool {
  return buttons["OK"].exists || staticTexts["Du har allerede godkjent Blue Sea."].exists
} 
```

Enter fullscreen mode Exit fullscreen mode

## 等待并刷新

但是脸书表单是一个 webview，所以它的内容有点动态。而且 UITest 似乎缓存内容用于快速查询，所以在检查 staticTexts 之前，我们需要等待并刷新缓存

```
app.clearCachedStaticTexts() 
```

Enter fullscreen mode Exit fullscreen mode

这是等待功能

```
extension XCTestCase {
  func wait(for duration: TimeInterval) {
    let waitExpectation = expectation(description: "Waiting")

    let when = DispatchTime.now() + duration
    DispatchQueue.main.asyncAfter(deadline: when) {
      waitExpectation.fulfill()
    }

    // We use a buffer here to avoid flakiness with Timer on CI
    waitForExpectations(timeout: duration + 0.5)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 等待元素出现

但是更可靠方法是等待元素出现。对于脸书登录表单，它们应该在加载后显示一个脸书标签。所以我们应该等待这个元素

```
extension XCTestCase {
  /// Wait for element to appear
  func wait(for element: XCUIElement, timeout duration: TimeInterval) {
    let predicate = NSPredicate(format: "exists == true")
    let _ = expectation(for: predicate, evaluatedWith: element, handler: nil)

    // Here we don't need to call `waitExpectation.fulfill()`

    // We use a buffer here to avoid flakiness with Timer on CI
    waitForExpectations(timeout: duration + 0.5)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并在对脸书登录表单中的元素做任何进一步检查之前调用这个函数

```
wait(for: app.staticTexts["Facebook"], timeout: 5) 
```

Enter fullscreen mode Exit fullscreen mode

## 如果用户已登录

登录后，我的应用程序显示主控制器，内部有地图视图。所以一个基本的测试就是检查地图的存在

```
if app.isAlreadyLoggedInSafari {
  app.okButton.tap()

  handleLocationPermission()
  // Check for the map
  XCTAssertTrue(app.maps.element(boundBy: 0).exists)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理中断

你知道，当显示带有位置的地图时，核心位置会请求许可。所以我们也需要处理这种中断。您需要确保在警报发生之前尽早调用它

```
fileprivate func handleLocationPermission() {
  addUIInterruptionMonitor(withDescription: "Location permission", handler: { alert in
    alert.buttons.element(boundBy: 1).tap()
    return true
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

还有一个问题，这个班长不会叫。因此，解决方法是在警报发生时再次调用 app.tap()。在我的例子中，当我的地图显示了 1.2 秒时，我调用 app.tap()，只是为了确保在显示警告后调用 app.tap()

有关更详细的指南，请阅读 [#48](https://github.com/onmyway133/blog/issues/48)

## 如果用户未登录

在这种情况下，我们需要填写电子邮件和密码。你可以看看下面的完整源代码部分。当事情不工作或者 po 没有向您显示您需要的元素时，可能是因为缓存或者您需要等到动态内容完成呈现。

您需要等待元素出现

## 点击文本字段上的

您可能得不到元素或任何后代都没有键盘焦点，以下是解决方法

*   如果你在模拟器上测试，确保模拟器->硬件->键盘->连接硬件键盘没有被选中

*   点击后稍等片刻

    app.emailTextField.tap()

## 清除所有文本

想法是将插入符号移动到文本字段的末尾，然后对每个字符应用每个 delete 键，然后键入下一个文本

```
extension XCUIElement {
  func deleteAllText() {
    guard let string = value as? String else {
      return
    }

    let lowerRightCorner = coordinate(withNormalizedOffset: CGVector(dx: 0.9, dy: 0.9))
    lowerRightCorner.tap()

    let deletes = string.characters.map({ _ in XCUIKeyboardKeyDelete }).joined(separator: "")
    typeText(deletes)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 改变语言

对于我的例子，我想用挪威语测试，所以我们需要找到挪威语选项并点击它。它被 UI 测试识别为静态文本

```
var norwegianText: XCUIElement {
  return staticTexts["Norsk (bokmål)"]
}

wait(for: app.norwegianText, timeout: 1)
app.norwegianText.tap() 
```

Enter fullscreen mode Exit fullscreen mode

## 邮件文本字段

幸运的是，电子邮件文本字段被 UI 测试检测为文本字段元素，因此我们可以查询它。这使用谓词

```
var emailTextField: XCUIElement {
  let predicate = NSPredicate(format: "placeholderValue == %@", "E-post eller mobil")
  return textFields.element(matching: predicate)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 密码文本字段

UI 测试似乎不能识别密码文本字段，所以我们需要通过坐标来搜索它

```
var passwordCoordinate: XCUICoordinate {
  let vector = CGVector(dx: 1, dy: 1.5)
  return emailTextField.coordinate(withNormalizedOffset: vector)
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 func coordinate(with normalized offset normalized offset:CG vector)-> XCUICoordinate 的文档

> *创建并返回一个带有标准化偏移的新坐标。
> 坐标的屏幕点是通过将标准化偏移量乘以元素框架的大小与元素框架的原点相加来计算的。*

然后输入密码

```
app.passwordCoordinate.tap()
app.typeText("My password") 
```

Enter fullscreen mode Exit fullscreen mode

我们不应使用 app . password coordinate . reference element，因为它将指向电子邮件文本字段❗️😢

## 再次运行那个测试

转到 Xcode ->产品->执行操作->再次测试，再次运行之前的测试

[![](../Images/31d670a85b05a89c1be7b4a2d25490f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---nFopo4I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AkYHd-HY0mLvgdXUs.png)

这里是完整的源代码

## 还有一件事

感谢对我的文章原创故事[https://github.com/onmyway133/blog/issues/44](https://github.com/onmyway133/blog/issues/44)的有益反馈，这里有一些更多的想法

*   要查找密码文本字段，我们实际上可以使用 secureTextFields 来代替 coordinate

*   wait 函数应该作为 XCUIElement 扩展，以便其他元素可以使用它。或者您可以只使用旧的期望样式，它不涉及硬编码的区间值。

## 何去何从

我发现这些指南涵盖了很多方面，值得一看

*   [用户界面测试备忘单](https://github.com/joemasilotti/UI-Testing-Cheat-Sheet)

*   [关于 Xcode UI 测试的一切](http://samwize.com/2016/02/28/everything-about-xcode-ui-testing-snapshot/)

原帖[https://hacker noon . com/running-uit tests-with-Facebook-log in-in-IOs-4ac 998940 c 42](https://hackernoon.com/running-uitests-with-facebook-login-in-ios-4ac998940c42)