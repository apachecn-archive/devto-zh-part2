# 在 Chrome DevTools 编辑器中复制行

> 原文：<https://dev.to/jlogix/duplicating-lines-in-chrome-devtools-editor-4c2m>

[![](img/36b73d1df470f4fa850be9c92032939d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BG1g9iwl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0tectm0h9lyhril2uxp7.jpg) 
对于那些想要在 **Chrome DevTools 编辑器**中编辑代码时快速复制单行或多行相邻行的人来说，这里有一个有用的提示。在**版本 62** 之前， **Windows** 上的 **Chrome DevTools** 曾经有这样一个漂亮的功能，通过在那一行上按下 **Ctrl+C** 然后按下 **Ctrl+V** 来快速复制一行，而无需选择任何文本。后来的 Chrome DevTools 版本，由于某种原因，停止了这个极其有用的功能。即使是通常的 Windows 方法，在按住 Ctrl 键的同时进行拖放，也不会在 Chrome DevTools 中产生选择的副本——而是移动选择。我已经在 Chromium 项目 bug 追踪器中创建了一个[标签来恢复这个特性——但是什么也没有做。](https://bugs.chromium.org/p/chromium/issues/detail?id=800028#c1)

但是最近我发现了一种使拖放复制工作的方法:在选择了你想要复制的文本后，如果你按下 Ctrl 键并开始拖动选择，然后按下 Shift 键并将选择放在目标位置，它会在目标位置创建一个选择的副本。我注意到操作的顺序很重要:

**1。开始拖动选择之前按 Ctrl 键**

**2。将选择放在目的地时按 Shift 键**

我测试过的版本是 **Chrome 70** 。YMMV。希望它能在一定程度上帮助那些像我一样还在为 Windows 上 Chrome DevTools 编辑器中缺失的行复制功能而悲伤的人。