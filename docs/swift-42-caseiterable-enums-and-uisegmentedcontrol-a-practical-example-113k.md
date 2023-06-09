# Swift 4.2 CaseIterable enums 和 UISegmentedControl，一个实际例子

> 原文：<https://dev.to/alexito4/swift-42-caseiterable-enums-and-uisegmentedcontrol-a-practical-example-113k>

2014 年，我写了一篇关于如何用新的强大的 Swift 枚举驱动 UISegmentedControl 的[帖子](http://alejandromp.com/blog/2014/07/24/swift-enums/)。当时主要的抱怨是，要获得枚举的所有事例，您必须手动列出枚举的事例，这既烦人又容易出错。

这篇文章是同一想法的新版本，但更新到了 Swift 4.2 和新的 CaseIterable 协议。

## CaseIterable

这个新协议是由提案 [SE-0194](https://github.com/apple/swift-evolution/blob/master/proposals/0194-derived-collection-of-enum-cases.md) 提出的。它定义了一个只有一个需求的协议，一个返回该类型所有可能值的集合的`allCases`静态变量。

但是最好的事情是编译器自动为一些简单的“T0”合成“T2”的一致性

## 驱动 UISegmentedControl

我们先来看看 2014 年的执行情况是怎么看的。

```
enum Size: Int, CustomStringConvertible {
    case S = 0
    case M
    case L
    case XL

    static func allValues() -> [String] {
        return [S, M, L, XL].map({$0.description})
    }

    static func count() -> Int {
        return allValues().count
    }

    public var description: String {
        switch self {
        case .S:
            return "S"
        case .M:
            return "M"
        case .L:
            return "L"
        case .XL:
            return "XL"
        }
    }
}

Size.count()
Size.allValues()

let segmented = UISegmentedControl(items: Size.allValues()) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到我们如何需要实现一个自定义函数，并且需要符合`CustomStringConvertible`。

> 请注意，CustomStringConvertible 是一种折衷。我们必须在实现自定义`rawValue`或自定义字符串之间做出选择。

现在有了 Swift 4.2，我们可以摆脱这两种定制实现，剩下我们的现代化 enum 如下:

```
enum Size: Int, CaseIterable {
    case S = 0
    case M
    case L
    case XL
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这种一致性，我们就可以访问自动生成的`allCases`变量。

还可以删除`CustomStringConvertible`,因为 Swift 现在提供了更好的运行时信息，允许字符串插值来访问案例名称。

有了这些改进，我们需要维护的代码更少了，我们仍然可以像以前一样驾驶我们的`UISegmentedControl`。

```
UISegmentedControl(items: Size.allCases.map({ "\($0)" }))
...
let index = sender.selectedSegmentIndex
Size(rawValue: index) 
```

Enter fullscreen mode Exit fullscreen mode

像往常一样，你可以在[游乐场](http://alejandromp.com/static/SwiftCaseIterable.playground-f80eab7fe171a4b3be19052556223af6.zip)找到这篇文章的代码。

**如果你想了解更多关于`CaseIterable`的信息，请查看我关于该主题的视频: [Swift 4.2 - CaseIterable -跟随 Swift 进化](https://youtu.be/c3W8-57Bdx8)**