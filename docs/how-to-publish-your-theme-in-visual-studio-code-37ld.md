# 如何发布🙀Visual Studio 代码中您自己的主题👍

> 原文：<https://dev.to/sait/how-to-publish-your-theme-in-visual-studio-code-37ld>

### 你是否厌倦了使用你不喜欢的懒散主题？

别担心，我会一步一步地向您展示如何在 Microsoft market Place 中发布您自己的主题。

#### 首先，我们需要安装 Yocode 生成器

```
npm install -g yo generator-code 
```

Enter fullscreen mode Exit fullscreen mode

安装 yo 代码后

现在打开终端，输入

```
yo code 
```

Enter fullscreen mode Exit fullscreen mode

* * *

Yo 代码显示了几个选项

[![yo code](../Images/fdbcf8ab4ddd10d1a413326ba6679c8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LC5FTIGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://code.visualstudio.com/assets/docs/extensions/yocode/yocode.png)

*   使用箭头键选择新的颜色主题

*   选择新的颜色主题后，你会看到更多的选项

[![ss](../Images/e60f021cf8b47a04608dc4aba5f5837f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NG_9lh-x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cusebkgan64oxvdjz4mf.png)

*   选择从新鲜开始

*   现在选择主题名称等..

成功创建后，您可以在磁盘上看到一个可用的文件夹

*   用 Vscode 打开文件夹。
*   点击 F5，开始开发过程

这篇文章清楚地解释了[的开发过程](https://code.visualstudio.com/docs/extensions/themes-snippets-colorizers)

### == **一旦你完成了你的发展** ==

* * *

现在我们需要在 Vscode marketplace 中发布我们的主题

对于这些，我们需要安装一个发布工具

```
npm install -g vsce . //for mac add sudo in front of npm 
```

Enter fullscreen mode Exit fullscreen mode

成功安装后，现在在 vs 代码中打开你开发的主题。

。在 vs 代码
中打开集成终端

```
vsce create-publisher (publisher name) 
```

Enter fullscreen mode Exit fullscreen mode

。例如:vsce create-publisher saigowtamr

现在我们需要创建一个个人访问令牌。

打开此 [Url](https://monacotools.visualstudio.com) 并创建您的帐户

一旦你的账户被创建
[![vscode](../Images/b47efdfdb9263b3c1303f313a1a49db3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uKwqLXyI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/22x5v0km9aar9o189abd.png)

*   选择安全选项，如上图所示。
*   检查完这些[后，张贴](https://code.visualstudio.com/docs/extensions/publish-extension)。

一旦您成功创建了您的 Pat 令牌副本，并将其存储在您的 Pc 上。因为它只显示一次。

*   现在在你的 vs 代码终端里。

```
vsce login (publisher name) // we already created name 
```

Enter fullscreen mode Exit fullscreen mode

*   它将要求 Pat 令牌粘贴令牌并按 enter 键。

*   一旦你完成了整个旅程。

*   我们最终命令发布了。

```
vsce publish -p <token> // enter your pat token in token field 
```

Enter fullscreen mode Exit fullscreen mode

成功发布自己的主题。如果你有任何错误，我会在这里帮助你。

如果你有时间，看看我的主题，我有几个主题

1.[黑金](https://marketplace.visualstudio.com/items?itemName=saigowthamr.black-gold)T2 2。[牛顿 Pro](https://marketplace.visualstudio.com/items?itemName=saigowthamr.black-gold)

**资源**

*   [发布文档](https://code.visualstudio.com/docs/extensions/publish-extension)
*   [主题文档](https://code.visualstudio.com/docs/extensions/themes-snippets-colorizers)