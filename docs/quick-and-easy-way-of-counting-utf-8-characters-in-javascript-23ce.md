# 在 Javascript 中计算 UTF-8 字符的快速简单的方法

> 原文：<https://dev.to/coolgoose/quick-and-easy-way-of-counting-utf-8-characters-in-javascript-23ce>

阅读下面关于显示文本区字符数的 VueJS 组件的教程让我思考了一下。

你看，问题是当 Javascript 最初被创建时，它没有适当的 UTF-8 支持。Javascript 的内部编码是 UCS-2 或 UTF-16，这取决于您在互联网上找到的文章。(实际上，2012 年有一篇很棒的[文章详细解释了这一点)。](https://mathiasbynens.be/notes/javascript-encoding)

你说的是什么意思？这很简单，如果你试图获取一个包含 UTF 83/4 字节(翻译成 UTF-16 代理对字符)的字符串的*长度*属性，你的长度将为每个字符返回 **2** 。

> 这通常可能不是一个问题，但是如果您的密码策略有 8 个字符，可以用 4”来填充，这就是一个大问题😹🐶😹🐶”(好吧，不是最好的例子，但是大家都喜欢猫和狗)

```
let lengthTest = "😹🐶😹🐶";
console.log(lengthTest.length);
// will display 8 
```

Enter fullscreen mode Exit fullscreen mode

现在，用现代 Javascript 解决这个问题相当容易，因为它在数组中正确地支持代理，并且使用[数组析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)使它成为一个快速简单的一行。

```
let lengthTest = "😹🐶😹🐶";
console.log([...lengthTest].length);
// will display 4 
```

Enter fullscreen mode Exit fullscreen mode

我很想知道你对 UTF-8 有没有什么奇怪/有趣的经历

PS:使用[这个链接](http://unicodebook.readthedocs.io/unicode_encodings.html)对 Unicode 编码进行简单的解释