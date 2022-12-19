# 将 VS 代码主题添加到 CodeSandBox

> 原文：<https://dev.to/dance2die/adding-vs-code-theme-to-codesandbox-1df7>

你有没有想过让你的 [CodeSandBox](https://codesandbox.io) 编辑器主题与你的 [Visual Studio Code](https://code.visualstudio.com/) 颜色主题相匹配？
*嗯，可以。*

这是因为 CodeSandBox 默认使用 [Monaco](https://github.com/Microsoft/monaco-editor) (一个 VS 代码编辑器引擎)。

让神奇的✨发生吧~

# 👣步伐

以下是实现这一目标的步骤。

1.  获取 VS 代码颜色主题设置
2.  打开 CodeSandBox 首选项
3.  将设置粘贴到 CodeSandBox 首选项模式框中

我们开始吧💪！

### 🍀 1.获取 VS 代码颜色主题设置

让我们从 VS 代码中抓取颜色主题设置。

*我正在 CodeSandBox 上使用[钴 2](https://marketplace.visualstudio.com/items?itemName=wesbos.theme-cobalt2) 主题，并将更新它以在 VS 代码上使用[仙女牙线](https://marketplace.visualstudio.com/items?itemName=nopjmp.fairyfloss)主题。*

我从这篇文章中发现了这个奇妙的主题(仙女牙线)。

[![aspittel image](../Images/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 我的 Visual Studio 代码安装程序

### Ali spit tel 7 月 17 日至 18 日 4 分钟阅读

#webdev #beginners #texteditor #vscode](/aspittel/my-visual-studio-code-setup-1emn)
From VS Code,

1.  [通过按`Control` + `Shift+`P`` 显示所有命令](https://code.visualstudio.com/docs/getstarted/keybindings#_navigation)
2.  进入**开发者:从当前设置**生成颜色主题![show all commands](../Images/2fa88d54bae32c510fdc9d81c39293ef.png)执行该命令后，会看到一个新的标签页`Untitled-1`，弹出 JSON 格式的颜色主题。![untitled-1](../Images/de44c41374cd35e67781ae39d657f6ca.png)现在选择全部(`Ctrl` + `A`)，复制内容。

### 🍀 2.打开 CodeSandBox 首选项

点击【首选项】齿轮⚙按钮打开**首选项**模态框。
[![Click Preference](../Images/ee4900474dd965fa905a5e68353cb1ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EFcg6yxP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/07/Click-Preference.gif%3Fresize%3D1064%252C549%26ssl%3D1)
[![Custom VSCode Theme](../Images/58d114ea59128fe4cc1c95102625c9d1.png)T9】](https://res.cloudinary.com/practicaldev/image/fetch/s--9Hmh9kaC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/07/Custom-VSCode-Theme.gif%3Fresize%3D900%252C741%26ssl%3D1)

### 🍀 3.粘贴设置

现在将从上一步复制的 VS 代码颜色主题粘贴到**自定义 VS Code 主题**文本区域。

你现在可以看到主题已经全球更新。
[![result](../Images/401cf8f349bfcc8038284dd9b5f94b88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4CtGFtkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i0.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/07/result.gif%3Fresize%3D1170%252C638%26ssl%3D1) 
好了，现在享受新的外观&的感觉吧😎。

# 👩‍🏭工作演示

下面是上面步骤的视频。
[https://www.youtube.com/embed/HhaOuFMl9zI](https://www.youtube.com/embed/HhaOuFMl9zI)T2】

# ⚠的一句告诫

请注意，这种变化是全局性的。

*这意味着，所有之前的沙箱(CodeSandBox 就是这么称呼每个项目的)也会受到影响。*

但是我确信默认行为可能是您想要的，无论如何😛。

将 VS 代码主题添加到 CodeSandBox 的帖子[最早出现在](https://www.slightedgecoder.com/2018/07/17/adding-vs-code-theme-to-codesandbox/)[微边缘编码器](https://www.slightedgecoder.com)上。