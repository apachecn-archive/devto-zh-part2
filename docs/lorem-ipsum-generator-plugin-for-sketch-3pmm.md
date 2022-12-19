# 用于草图的 Lorem Ipsum 生成器插件

> 原文：<https://dev.to/whoisryosuke/lorem-ipsum-generator-plugin-for-sketch-3pmm>

我最近在做一个 Sketch 中的快速项目，我需要在几个字段中插入一些 Lorem Ipsum 文本。我在谷歌上快速搜索了一个插件，在 Github 上看到了@RichardGong 的[这个插件。它工作得很好，但是只在每个文本字段中添加了一行 Lorem，并且是相同的 Lorem 文本。这个解决方案只有在您处理具有多个文本字段的项目时才有效。](https://github.com/richgong/sketch-lorem-ipsum-2017)

所以我分叉了他的项目，加了**更多的** Lipsum，因为*你知道*，Lipsum 越多越好。

[![GIF of plugin in action](../Images/7bea36d3aabda021f29d4ad899c5de58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J46HaKLE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/whoisryosuke/sketch-lorem-ipsum-2017/blob/master/example.gif%3Fraw%3Dtrue)

## 工作原理

我在[Lipsum.com](http://lipsum.com)上生成了 10 个段落，并将它们添加到一个数组中。该插件获取当前选择，遍历每一层，如果是文本字段，则添加一段 Lorem Ipsum。我使用一个变量来记录每个循环的计数，一旦它达到 10，我就重置计数，从列表的顶部开始抓取(所以你可以选择 30 个字段，它仍然会填充它们，只是重复 3 次)。

## 但是它过度填充了我的文本字段！

是的，那是一件事。我正在寻找一种方法来计算当前文本字段允许的字符长度，但是 Sketch API 没有这么简单，需要 20-30 行代码才能完成。看起来 [sketch-highlighter](https://github.com/matt-curtis/Sketch-Highlighter/blob/master/Sketch-Highlighter.sketchplugin/Contents/Sketch/script.js) 插件有一个我可以分解并用作基础的片段。当我有时间添加它时，这个特性将不得不在以后出现。

## 下载插件

[前往 Github，点击“克隆或下载”按钮](https://github.com/whoisryosuke/sketch-lorem-ipsum-2017)。

如果有任何问题，请告诉我！
良

* * *

**参考文献**

*   [Lorem Ipsum 生成器草图插件](https://github.com/whoisryosuke/sketch-lorem-ipsum-2017)
*   [草图插件文档](https://developer.sketchapp.com/guides/plugin-scripts/)
*   [Github 上@RichardGong 原创的插件](https://github.com/richgong/sketch-lorem-ipsum-2017)