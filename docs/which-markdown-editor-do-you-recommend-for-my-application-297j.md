# 对于我的应用程序，您推荐哪个 Markdown 编辑器？

> 原文：<https://dev.to/trendschau/which-markdown-editor-do-you-recommend-for-my-application-297j>

嘿，现在我正在建立一个最小的平面文件 cms，我正在寻找一个不错的降价编辑器。有两个主要要求:

*   cms 是为非技术人员设计的，所以 markdown-editor 应该可以选择某种可选的 wysiwyg 模式来弥补这个差距。
*   在服务器端，我使用了支持[TOC]等标签的扩展，并集成了 KaTeX、MathJax 和其他特殊的东西，所以编辑器的 markdown 语法应该在某种程度上是可扩展的。

现在我在想:

*   Slatejs :目前为止我最喜欢的，类似于 prosemirror，但更容易上手(基于 react)。
*   看起来不错，但我认为学习曲线相当高。
*   SimpleMDE :相当快，更传统，基于 codemirror，但我不认为它很容易扩展？
*   [Tui](http://ui.toast.com/tui-editor/) :好看但也有点晦涩。也是基于 codemirror。

对于我的要求，你会推荐哪一个？