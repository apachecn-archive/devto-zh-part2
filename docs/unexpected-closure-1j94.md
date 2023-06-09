# 意外关闭

> 原文：<https://dev.to/externconst/unexpected-closure-1j94>

Swift 中的闭包是一个很好的特性，对于网络回叫、通知订阅等任务非常有用，并且提供了一种替代委托模式的方法。

最近，我发现像`&&` (and)条件这样不显眼的代码也可以利用这个特性。

让我们假设我们正在建模一个基于全球订阅的应用程序的`User`,用户可以定制他们的主题颜色。

我们可以在 Swift 中将其表示为一个结构，该结构具有以下属性:标识符、订阅状态、国家代码和主题颜色。
最终的`User`模型可能是这样的:

```
struct User {
    var id: String
    var isSubscribed: Bool
    var themeColor: UIColor?
    var countryCode: String
} 
```

我们在这里将`themeColor`设置为可选的，因为如果用户想要自定义它，这将由用户决定。

假设我们的应用程序已经有了一个对象来表示名为`Theme` :
的主题

```
class Theme {
    let backgroundColor: UIColor

    init(backgroundColor: UIColor?) {
        self.backgroundColor = backgroundColor ?? .white
    }
} 
```

注意，如果提供的`backgroundColor`属性是**而不是**，这个`init`将退回到`UIColor.white`(或`.white`)。

有了这些知识，我们可以为自定义用户主题创建一个新的类。
我们可以用一个用户初始化自定义主题，以获取它的`themeColor`。主题颜色作为它的`backgroundColor`传递给它的父类。

一个`Theme`子类可以表示定制的用户主题对象:

```
class UserTheme: Theme {
    let user: User

    init(user: User) {
        self.user = user
        super.init(backgroundColor: user.themeColor)
    }
} 
```

让我们假设我们已经收到了额外的要求，限制用户主题化为:

*   订阅用户
*   加拿大用户

然后我们可以在`UserTheme`上更新我们的`init`方法来处理这些情况，如下所示:

```
var themeColor: UIColor?
if self.user.isSubscribed && self.user.countryCode == "CA" {
    themeColor = self.user.themeColor
}
super.init(backgroundColor: themeColor) 
```

虽然这看起来不错，但实际上它将无法编译。您将在 Xcode 中看到以下错误日志:

```
error: 'self' captured by a closure before all members were initialized
        if self.user.isSubscribed && self.user.countryCode == "CA" {
                                     ^ 
```

这条错误消息虽然信息丰富，但有点令人困惑。它指的是什么闭包？

我们可以通过移除显式的`self`并依赖作为参数传入的`user`属性来轻松解决这个问题。这只是治标不治本，因为这行代码中有一个隐式闭包。

在将这个错误消息发布到 [tacow](https://www.meetup.com/tacow-org/) Slack group 之后， [@rydermackay](https://twitter.com/rydermackay) 向我指出 Swift 语言正在捕获闭包中条件的右边。

也就是给定条件:`lhs && rhs`(“lhs”和“rhs”分别代表左手边和右手边)。`rhs`被包裹在封闭物中。更具体地说，它被包装在一个`autoclosure`中，这样，如果左边的条件为假，它就可以懒洋洋地评估右边的条件。

这可以通过查看 [& &操作符](https://github.com/apple/swift/blob/7f105e4e3a994e6ac87860d5bd7bf9942c52b4bb/stdlib/public/core/Bool.swift#L289) :
的源代码来显示

```
public static func && (lhs: Bool, rhs: @autoclosure () throws -> Bool) rethrows -> Bool {
    return lhs ? try rhs() : false
} 
```

为了更好地理解警告，我们需要知道什么是`autoclosure`。

从苹果的文档来看，一个`autoclosure`:

> ...是一个自动创建的闭包，用来包装作为参数传递给函数的表达式。它不带任何参数，当它被调用时，它返回包装在它里面的表达式的值。

但最重要的是:

> autoclosure 允许您延迟求值，因为其中的代码在您调用闭包之前不会运行。延迟计算对于有副作用或计算量大的代码很有用，因为它允许您控制何时计算代码。

这意味着上面的`&&`实现在高层次上等同于下面的。注意:我们实际上不能在没有`rhs`的情况下编写`&&`，因为它被定义为两个参数都有。

```
// For simplicity, I've removed all throws
public static func && (lhs: Bool, rhs: () -> Bool) -> Bool {
    return lhs ?? rhs()
}

// Example: Assume A and B are some boolean conditions
A && { () -> Bool in
    return B
} 
```

如果我们用这个重新解释来更新我们之前的`UserTheme` `init`，它看起来会像这个

```
let result = self.user.isSubscribed && { () -> Bool in
    return self.user.countryCode == "CA"
}
if result {
    themeColor = self.user.themeColor
} 
```

正如你所看到的，这个块捕获了`self.user.countryCode == "CA"`，因为我们是在所有成员都初始化了`super.init()`之前做的，所以编译失败了。

有几种方法可以解决这个问题，我们可以:

*   将所有这些有问题的代码移到下面的`super.init()`
*   将右侧表达式的结果存储在单独的变量中，或者；
*   我们可以将主题的创建转移到一个[工厂](https://en.wikipedia.org/wiki/Factory_method_pattern)类来避免一个`Theme`子类

无论哪种方式，这些都可以，但是一般来说，在这种情况下，你应该避免在`super.init()`之前引用`self`。
我希望你学到了一些东西，并有可能让你有兴趣了解 Swift 编程语言的更多实施细节。

示例代码可以在这里找到[。](https://github.com/ajfigueroa/blog-code/tree/master/posts/3-Unexpected-Closure.playground)

## 附加资源

*   [自动关闭文档](https://docs.swift.org/swift-book/LanguageGuide/Closures.html#ID543)
*   [克拉肯德夫的潮人斯威夫特帖子](https://krakendev.io/blog/hipster-swift#autoclosure)
*   [Swift 回购](https://github.com/apple/swift)

最初发布在我的[网站](https://alexanderfigueroa.com)。