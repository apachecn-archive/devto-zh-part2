# Swift 4.2 的新功能-我的总结

> 原文：<https://dev.to/kamwysoc/whats-new-in-swift-42---my-summary-31e>

我刚刚看了 Swift 2018 年 WWDC 会议的新内容，我认为这是一个很好的动力来写一篇关于这次演讲的博文并总结我所学到的东西。

这里有一些我非常喜欢的 Swift 4.2 新功能。

希望你会喜欢！🤓

## 👉SE-0194 枚举案例的派生集合

如果我们需要打印所有可用的枚举值，我们必须创建一些包含所有枚举情况的帮助变量。比如一个名为`allCases`的静态数组。这种方法的一个很大的缺点是，当我们每次修改 enum 用例时，我们需要记住更新`allCases`数组。

Swift 4.1 方法:

```
enum CarType {
    case sedan
    case crossover
    case hothatch
    case muscle
    case miniVan

    static var allCases : = [.sedan, .crossover, .hothatch, .muscle, miniVan]
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Swift 4.2 中，我们可以使用为我们完成所有工作的`CaseIterable`协议！请看下面的例子:

```
// CaseIterable protocol gave us a `allCases` variable, which is an array of all cases in the Enum.

enum CarType : CaseIterable {
    case sedan
    case crossover
    case hothatch
    case muscle
    case miniVan

    //there is no need to add `allCases` variable. `CaseIterable` protocol do the job!
} 
```

Enter fullscreen mode Exit fullscreen mode

```
for type in CarType.allCases {
    print(type)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 👉条件一致性

```
let arrayOfArrays = [[1,2],[3,4],[5,6]]

arrayOfArrays.contains([1,2]) // return false in Swift 4.1

arrayOfArrays.contains([1,2]) // now it returns True because of fact that the elements in the array conforms to Equatable protocol 
```

Enter fullscreen mode Exit fullscreen mode

它也适用于`Optional`、`Dictionary`类型。
条件一致性与`Hashable`、`Encodable`和`Decodable`协议的工作方式相同。
举个例子，因为`Int`是`Hashable`，这意味着在那种情况下`Int?`也是`Hashable`，结果`[Int?]`也是`Hashable`！

```
let s: Set<[Int?]> = [[1, nil, 2], [3, 4], [5, nil, nil]]
s.contains([1,nil,2]) // returns true 
```

Enter fullscreen mode Exit fullscreen mode

## 👉 Bool toggle

```
var isTheWeatherNice : Bool = true
print(isTheWeatherNice) // prints true
//now it's starts raining
isTheWeatherNice.toggle() // it will change the bool value.
print(isTheWeatherNice) // prints false 
```

Enter fullscreen mode Exit fullscreen mode

小，但在我看来-非常好的功能。我第一次见到这个扩展是在阅读 objc.io 博客文章的时候。

现在它内置在 Swift 4.2 中。🎉

## 👉哈希协议

```
protocol Hashable {
    func hash(into hasher: inout Hasher)
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Swift 4.2 中，我们不必为`hashValue`提供定制算法。现在，swift 处理散列方法质量和运行性能。
重要的是`hashValue`使用随机的每进程种子，它是在每个应用程序启动时创建的。

```
struct City: Hashable {
    let name : String
    let state : String
    let population : String
}
extension City : Hashable {
    func hash(into hasher: inout Hasher) {
        name.hash(into: &hasher)
        state.hash(into: &hasher)
    }
}
let warsaw = City(name : "Warsaw", state: "Mazowieckie")
print(warsaw.hashValue) // will print hash value, using the Swift algorithms from hash function. 
```

Enter fullscreen mode Exit fullscreen mode

⚠️
在这种方法中，你应该将与`hashValue`相关的代码改为常量。在每次应用程序运行时，哈希值都是不同的。
⚠️

## 👉SE-0202 随机统一

Swift 4.1 方法:

```
let randomIntFrom1to10 = 1 + (arc4random() % 10) // return random number is the 1...6 
```

Enter fullscreen mode Exit fullscreen mode

但是在 Swift 4.2 中已经不需要使用`arc4random()`了。🎉

```
let randomIntFrom0To20 = Int.random(in: 0 ..< 20)
let randomFloat = Float.random(in: 0 ..< 1) 
```

Enter fullscreen mode Exit fullscreen mode

超级酷的事情是，我们可以从像`Array`或`Dictionary`这样的集合类型中获得一个随机值。

```
let names = ["John", "Paul", "Peter", "Tim"]
names.randomElement()! 

let playerNumberToName : [Int: String] = [9: "Lewandowski", 7: "Ronaldo"]
playerNumberToName.randomElement()! 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到，`randomElement`函数返回一个可选的，因为我们在空集合上调用这个函数。

```
let emptyCollection : [String] = []
emptyCollection.randomElement() // retuns nil 
```

Enter fullscreen mode Exit fullscreen mode

另一个新功能是`shuffle`或`shuffled`功能。

```
let names = ["John", "Paul", "Peter", "Tim"]
let shuffledNames = names.shuffled() // returns an array of names in shuffled order. 
```

Enter fullscreen mode Exit fullscreen mode

## 👉结论

在稳定版本中使用那些特性会很棒。我对 Xcode 10(beta)和 Swift 4.2 的印象相当惊人。我强烈推荐您观看 2018 年 WWDC Swift talk 的新内容

下面你可以找到一个 GitHub gist 的链接，上面描述了所有的特性。

[https://gist . github . com/kamwysoc/e 9322 c 84 FD 4 fa 051 CB 747 EC 08193 dc0d](https://gist.github.com/kamwysoc/e9322c84fd4fa051cb747ec08193dc0d)

#### 来源

*   [https://developer.apple.com/videos/play/wwdc2018/401/](https://developer.apple.com/videos/play/wwdc2018/401/)

*   [https://swift.org/documentation/](https://swift.org/documentation/)