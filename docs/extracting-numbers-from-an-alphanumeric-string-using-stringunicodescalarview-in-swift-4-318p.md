# 使用字符串从字母数字字符串中提取数字。Swift 4 中的 UnicodeScalarView

> 原文：<https://dev.to/tiptopgs/extracting-numbers-from-an-alphanumeric-string-using-stringunicodescalarview-in-swift-4-318p>

如何从由随机字母数字字符组成的字符串中提取数字？我使用字符的字符串视图作为 Unicode 标量值的集合来完成这项任务。字符串中的每个字符都有一个 Unicode 标量值，您可以从字符串的 unicodeScalars 属性中获取该值。让我们看看下面的例子:

<figure>[![](img/5a3850e6466393f1a08482fccab57a1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_nR7AhHl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AskFokJjUGKQg8VQ4bzMCfw.png) 

<figcaption>从字符串中访问 Unicode 标量值</figcaption>

</figure>

对于上面的例子，我们遍历了 alphaNumericString 变量中的每个字符，并打印出相应的 Unicode 标量值。每个值都是字符的唯一表示。

因此，对于数字，您可以标识所有对应的 Unicode 标量值，如下所示:

<figure>[![](img/83c48cfa7dbc7d5b8e4d254943bcae1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mdrUqtnD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aijz29oR-fySe4QUt2XBdpQ.png) 

<figcaption>打印所有的 Unicode 标量值为位数</figcaption>

</figure>

请注意，digit 是 Unicode.scalar 类型，您可以通过访问 valueproperty 来访问 Unicode 值，如上例所示。

Unicode 标量值是 UTF-32 位表示。因此，给定一个 UTF-32 位表示的值，您可以创建自己的 Unicode 标量实例，如下所示。

<figure>[![](img/dd6f14647897722c574a4363d25da64b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZnDM0oaY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3OoO1TbT-07ta0gMpOGCNg.png) 

<figcaption>创建和使用 Unicode 标量值</figcaption>

</figure>

这里发生了什么？第一部分通过显式指定类型并分配字符串 0 和 9 来创建两个变量。第二部分使用 Unicode 标量值创建从 0 到 9 的数字。

### 把所有的东西放在一起

因为您有对应于某些数字的 Unicode 标量值的范围，所以您可以识别该字符是数字还是其他:

<figure>[![](img/522927b9e80faf15c5a2f4be24c93986.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1hrqAYnu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfB9xndpjZ-r3-OcUg6Jt3w.png) 

<figcaption>检查字符是数字还是其他</figcaption>

</figure>

最后一步是使用字符串中的数字，所以让我们提取数字并将其转换为更有用的内容:

<figure>[![](img/cbb92a818f21014cbb8124a5dab092ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tbOhvb3o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKxjUUktU8i9Vzk4b5UAFAw.png) 

<figcaption>从字符串中提取数字</figcaption>

</figure>

因此，当我们遍历字符串中的 Unicode 标量时，您将检查该值是否匹配 48 到 57 范围内的任何数字。您获取每个数字，并将其存储到一个变量中，一旦发现非数字字母，就退出循环。

### 何去何从？

查看苹果关于[字符串的 API 文档。UnicodeScalarView](https://developer.apple.com/documentation/swift/string/unicodescalarview#overview) 和 [Unicode。标量](https://developer.apple.com/documentation/swift/unicode/scalar)。

如果你想找点乐子，你也可以用你所学的知识尝试这道 [leetcode 题](https://leetcode.com/problems/string-to-integer-atoi/description/)。请在下面张贴您的解决方案，我们可以比较我们的解决方案。