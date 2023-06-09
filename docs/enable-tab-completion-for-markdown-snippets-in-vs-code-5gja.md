# 在 VS 代码中为 Markdown 代码段启用制表符结束

> 原文：<https://dev.to/chaseadamsio/enable-tab-completion-for-markdown-snippets-in-vs-code-5gja>

在 VS 代码中处理 frontmatter markdown 片段时，我不明白为什么 tab 补全不起作用。原来[一些扩展(包括 Markdown)没有默认启用的代码片段标签完成](https://github.com/Microsoft/vscode/issues/1617)(但是你可以输入`ctrl+Space`来显示你输入的可用代码片段)。

# 为所有扩展启用代码片段标签完成

要在 VS 代码中启用制表符补全，请打开您的**用户设置**(在 MacOS 中为`⌘+,`，在 Windows 中为`ctrl+,`)并添加以下 JSON:

```
"editor.tabCompletion": true, 
```

保存您的**用户设置**配置和您的，以便更有效地使用 Markdown(和其他语言)的代码片段！

[![](img/6c500170bab0c5898cdbf6c85557c561.png "Markdown Snippet Tab Completion")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_jQ8u2oy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.chaseadams.io/img/markdown-expand.gif)