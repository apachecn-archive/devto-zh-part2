# 调试:似曾相识

> 原文：<https://dev.to/larsgw/debugging-deja-vu-3mc5>

我要和大家分享一些关于调试的故事，~~因为我为他们感到骄傲~~。(写完第一个故事，我就不再特别骄傲了，但还是想分享一下故事。)这里是第一个:一个看起来很熟悉的 bug。

在模拟 API 请求遇到一些麻烦之后，我决定在浏览器中支持模拟没有支持模拟重要，所以我安装了 T2 模拟请求 T3(我没有让 T4 代理查询 T5 工作)。现在，为了确认测试包脚本实际上省略了包中应该省略的 API 模拟代码，我在浏览器中加载了测试套件。你猜怎么着？错误无处不在！或者说，错误无处不在。每个测试用例都给出了相同的错误:

```
TypeError: Cannot assign to read only property 'length' of function 'function () {
      old.apply(self, arguments);
    }'
  at new Assertion (/build/test.citation.js:1810:30)
  at new Assertion (/build/test.citation.js:1799:25)
  at new Assertion (/build/test.citation.js:1799:25)
  at new Assertion (/build/test.citation.js:1799:25)
  at expect (/build/test.citation.js:1775:12)
  at Context._callee2$ (/build/test.citation.js:3582:35)
  at tryCatch (/build/citation.js:7891:17)
  at Generator.invoke [as _invoke] (/build/citation.js:8064:22)
  at Generator.prototype.(anonymous function) [as next] (/build/citation.js:7934:21)
  at step (/build/test.citation.js:3542:221) 
```

Enter fullscreen mode Exit fullscreen mode

自然地，像一个优秀的程序员一样，我立即谷歌了错误消息以及我为这个包使用的各种工具和框架，而不是查看显示 [expect.js](https://github.com/Automattic/expect.js) 有问题的堆栈跟踪。总之，过了一段时间，我发现一个 [GitHub 问题](https://github.com/Automattic/expect.js/issues/149)准确描述了我遇到的问题。浏览回复时，我惊呆了:

> **[larsgw](https://github.com/larsgw)** 评论[2017 年 7 月 29 日](https://github.com/Automattic/expect.js/issues/149#issuecomment-318820971)
> 
> +1:同一期，但针对所有断言

不知何故，我在 10 个月前报告了同样的问题，但是这个网站，从 2 个月前开始运行一个捆绑包，没有这个问题。显然，我之前已经解决了这个问题，但是忘记了解决方案，我也不知道解决方案是什么。于是我开始调试。首先，有问题的代码与任何其他工作包没有什么不同。它将一堆函数注册为一个函数的属性，并且在`length`属性上阻塞了。有道理， [`length`属性在函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/length)上是不可写的。但是运行一些简单的测试代码表明这应该不是问题:

```
let f = function () {}
console.log(f.length) // 0
f.length = 3          // (no error)
console.log(f.length) // 0 
```

Enter fullscreen mode Exit fullscreen mode

当然，它实际上没有做任何事情，但也没有抛出异常。此外，这与 GitHub repo 中的代码行完全相同，这怎么可能是问题呢？我唯一能做的就是与工作实例进行比较。将我的包与发布的包区别开来，这很困难，因为它是生成的代码。检查提交，直到我发现哪一个有效，这很痛苦，因为我反复忘记重新安装依赖项，这可能是很关键的。

> 侧栏(小剧透):上次我“解决”这个问题时，要容易得多，因为这是第一次设置系统，所以我没有把旧的提交作为工作示例，而是查看了文档。

过了很久，我才意识到之前的解决方法是什么:我没有将 expect.js 与 Browserify 捆绑在一起，而是将其作为一个脚本(正如[推荐的](https://github.com/Automattic/expect.js#browser))包含在内，并创建了一个包装器来公开`expect.js`模块，然后简单地抓取并导出脚本公开的全局`expect`变量。我认为这是因为要求脚本的顺序很重要，但是一些测试证明事实并非如此。不，实际上将 expect.js 包含到启用了 Babelify transform 的 Browserify 包中，或者只是通过 Babel 编译器运行它都会导致错误。

回到区分包的问题，我想:如果没有任何语法需要转换，或者 API 需要填充，那么 babel-ed 文件和它的源代码之间的区别是什么？

事实证明，并不多。在这些文件之间，唯一真正的区别是注释的位置(或者是否存在),以及由 Babel 的代码生成器引起的一些风格差异。

还有`'use strict'`。

显然， [`'use strict'`让原本会失败的任务无声地抛出一个错误](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)。如果我早一点阅读该文档，或者如果我对`Function.prototype.length`文档(链接如上)给予了适当的关注，我就会知道了。现在只是漫长旅途的一个无聊结局。但是，嘿，至少我学到了一些东西。

* * *

要解决这个问题，要么需要对 4(！idspnonenote)中没有更新的工具包的内部进行重大修改。)年，还是一个变通办法。我不想再使用包含两个额外文件的变通方法，现在我知道是什么导致了这个问题，但是另一个变通方法本身也是一个问题，涉及过时的文档和 Babelify 中的一个 bug。稍后会详细介绍。

相关说明:我正在为 Citation.js 开发一个新版本，改进解析插件系统。除了名称空间容易改变之外，API 现在应该非常稳定，所以我可能会将时间表改为一次更新，更新所有当前的 API。