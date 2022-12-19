# Swift 提示:初始化和配置属性

> 原文：<https://dev.to/zdnk/swift-tip-initializing--configuring-properties-227d>

**这篇文章也发表在[我的博客](https://blg.zdnkt.com/swift-tip-initializing-configuring-properties/)上。**

有时，您需要做的不仅仅是在初始化时给属性赋值。

```
class Foo {
    let formatter = DateFormatter()
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能需要配置一些属性。你想到的第一件事可能是在初始化式里做。

```
class Foo {
    let formatter = DateFormatter()

    init() {
        formatter.dateStyle = .short
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有更多的方法来配置你的属性，而不用把代码放在初始化器里。

```
class Foo {
    let formatter: DateFormatter = {
        $0.dateStyle = .short
        return $0
    }(DateFormatter())
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，您可以看到属性`Foo.formatter`是用一个闭包构造的，该闭包将`DateFormatter`的实例作为参数。您可以直接在闭包:
中初始化`DateFormatter`来做同样的事情

```
class Foo {
    let formatter: DateFormatter = {
        let formatter = DateFormatter()
        formatter.dateStyle = .short
        return formatter
    }()
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用函数或静态方法来构造属性。

```
func dateFormatter() -> DateFormatter {
    let formatter = DateFormatter()
    formatter.dateStyle = .short
    return formatter
}

class Foo {
    let formatter: DateFormatter = dateFormatter()
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class Foo {
    let formatter: DateFormatter = Foo.dateFormatter()

    static func dateFormatter() -> DateFormatter {
        let formatter = DateFormatter()
        formatter.dateStyle = .short
        return formatter
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些选项也适用于局部变量。

联系 [Twitter @zdnkt](https://twitter.com/zdnkt) 获取评论、讨论反馈或想法！

**这篇文章也发表在[我的博客](https://blg.zdnkt.com/swift-tip-initializing-configuring-properties/)上。**