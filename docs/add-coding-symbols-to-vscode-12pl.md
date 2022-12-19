# 向 VSCode 添加编码符号

> 原文：<https://dev.to/mattferderer/add-coding-symbols-to-vscode-12pl>

作为开发人员，我们花更多的时间阅读代码，而不是写作。我们做的最常见的任务之一是比较项目。使用符号可以让你的代码更容易阅读，更愉快。

以下是添加编码符号(也称为字体连字)的示例。

[![VS Code using Fira Code font ligatures.](../Images/853ba634fe351aed60f715d4c093943b.png)T2】](///static/code-symbol-preview-2ab22531392495cde253634d93667edf-25de8.jpg)

要将它添加到 VS 代码中，请执行以下操作:

在你的电脑上安装 [Fira 代码字体](https://github.com/tonsky/FiraCode)。

在 VS 代码中编辑您的用户设置，以使用字体连字和“Fira 代码”字体。

```
"editor.fontFamily": "'Fira Code', Consolas, 'Courier New', monospace, 'Segoe UI Emoji'",
"editor.fontLigatures": true, 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你完了！享受改进的编码体验！