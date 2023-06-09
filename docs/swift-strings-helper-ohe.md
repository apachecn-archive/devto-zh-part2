# 迅捷弦乐助手

> 原文：<https://dev.to/trozware/swift-strings-helper-ohe>

谁还记得如何使用 Swift 字符串，尤其是子字符串和范围，请举手？….

没人吗？

我也没有。所以我决定，我要解决它一次，并为自己创建一个图书馆，以便将来方便自己。然后我想其他人可能也会喜欢。所以我创建了我的第一个开源项目。

## Swift 中的字符串

斯威夫特的字符串处理非常出色。它是真正的 Unicode 兼容的，所以 Swift `String`并不关心你的字符串是由基本的字母数字、重音字符还是由几个不同的表情符号组合而成的表情符号组成。

但这种能力是有代价的，Swift 的每个版本都改变了我们与字符串的交互方式，似乎让那些试图跟上时代的可怜程序员越来越困惑。我已经到了这样一个阶段，每年我都会阅读新的弦乐特性，然后很快就会忘记它们。对于每一次使用，我都必须回去寻找如何做应该简单的事情。

## 出现了问题

在我看来，主要有两个问题:索引和子字符串。

在大多数语言中，可以获得字符串的第 n 个字符，但在 Swift 中不行。在 Swift 中，您必须向字符串请求它的 startIndex(或 endIndex ),然后使用一个偏移量将该索引调整一定的数值，从而得到一个`String.Index`。做同样的事情得到第二个索引，然后你就可以得到这两个索引之间的字符串。

要获得从字符串的第 7 个字符到第 11 个字符的子字符串，您需要做的是:

```
let str = "Hello, playground"

let subStart = str.index(str.startIndex, offsetBy: 7)
let subEnd = str.index(str.startIndex, offsetBy: 11)

let subStr = str[subStart ..< subEnd] // "play" 
```

像这样把线分开至少有助于显示正在发生的事情，但是你会看到这样令人厌恶的事情

```
let sub = str[str.index(str.startIndex, offsetBy: 7)..<str.index(str.startIndex, offsetBy: 11)] 
```

我真的不知道人们应该怎么读它。

更糟糕的是:结果(上面例子中的`subStr`和`sub`)不属于`String`类型！它们属于`Substring`类型，所以当你使用它们时，它们不会像预期的那样工作。

我猜有使用`Substring`的情况，但我总是最终强制转换为`String`来解决这个问题，但只是在编译器一直认为是`String`的地方卡住了之后。

## 解

为了让我的生活更容易，我想出了所有的方法，我想能够分裂字符串。基本上只有两种方式:按字符数或按子串。Swift 已经有了一个`substring`方法，但是它使用了如上所示的`String.Index`。为了避免混淆，我将我的函数都命名为`sub`。

但是现在我可以做下面的事情来按照字符数分割字符串:

```
let subStr = startingString.sub(from: 9)
let subStr = startingString.sub(from: -3)

let subStr = startingString.sub(upTo: 4)
let subStr = startingString.sub(upTo: -3)

let subStr = startingString.sub(from: 3, upTo: 7)
let subStr = startingString.sub(from: 3, upTo: -5) 
```

负数从字符串的末尾开始倒数。而且都是返回`String`对象！

或者按子字符串拆分字符串，我可以这样做:

```
let subStr = startingString.sub(from: "abc")

let subStr = startingString.sub(upTo: "xyz")

let subStr = startingString.sub(from: "abc", upTo: "xyz") 
```

## 加贺

一旦我创建了一个字符串助手库，我就开始考虑所有其他有用的字符串实用程序。所以我开始添加各种设施:

*   计算属性:
    *   length(为什么字符串的长度应该被称为 count——这没有意义)
    *   话
    *   线
    *   字数
    *   行计数
    *   标题案例
*   编码:
    *   查询或表单的 URL 编码和解码
    *   base64 编码和解码
*   修剪:
    *   整齐
    *   向左修剪
    *   向右修剪
    *   使用额外字符修剪
*   衬垫:
    *   向左填充
    *   向右填充
    *   使用默认空格或指定的其他字符

这是目前为止我所知道的，但是我希望有一些社区的参与来扩展或编辑这个库，使它有更广泛的适用性。

## 开源

我以前从未创建过开源项目，也很少为开源做出贡献。但是我现在已经在 GitHub 上发布了这个库，并获得了麻省理工学院的许可。请到[https://github.com/trozware/swift-strings-helper](https://github.com/trozware/swift-strings-helper)查看。GitHub repo 包含一个 Xcode 项目，其中包含所有的源文件，加上为 macOS 或 iOS 构建框架的目标，以及一个作为文档并对其进行测试的平台。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [特洛伊人](https://github.com/trozware) / [快手助手](https://github.com/trozware/swift-strings-helper)

### 一个框架/库，使在 Swift 中使用字符串变得更加容易。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 迅捷弦乐助手

一个框架/库，使在 Swift 中使用字符串变得更加容易。因为谁能记得怎么用`String.Index`和`Substring`？

### 如何用作框架:

*   选择所需的方案:StringsHelper-iOS 或 StringsHelper-Mac。
*   建立方案。
*   从“产品”中，将所需的框架拖到项目常规设置的“嵌入的二进制文件”部分，选择“如果需要，复制项目”和“创建组”。
*   在您的 swift 文件中，使用`import StringsHelper_Mac`或`import StringsHelper_iOS`
*   查看框架项目中的操场，了解包含的函数的详细信息。

### 如何使用普通 Swift 文件:

*   在 Finder 中打开此项目文件夹。
*   将“助手”文件夹拖到您自己的项目中，选择“需要时复制项目”和“创建组”。
*   查看框架项目中的操场，了解包含的函数的详细信息。

CocoaPODs 和迦太基支援可能会来，取决于兴趣水平。或者我可能…

</article>

[View on GitHub](https://github.com/trozware/swift-strings-helper)

我希望得到尽可能多的明星，如果有人想记录一个问题或直接做出贡献，那就太棒了。作为一个发现整个开源世界相当令人生畏的人，我想向每个人保证，不会有任何想法被否决。我期待收到你的来信。

我确信还有其他的 Swift 库致力于解决同样的问题，但是我希望我的库能够对一些人有用。如果你只是想使用它而没有贡献，请随意。使用说明都在 GitHub 页面的 ReadMe 中。