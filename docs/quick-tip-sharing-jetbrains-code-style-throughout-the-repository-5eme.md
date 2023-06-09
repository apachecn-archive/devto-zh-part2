# 快速提示:在整个存储库中共享 JetBrains 代码风格

> 原文：<https://dev.to/igorsantos07/quick-tip-sharing-jetbrains-code-style-throughout-the-repository-5eme>

这是一个快速的。JetBrains 有一篇文章解释了如何在项目库中正确地包含`.idea`文件夹。然而，我一直不明白如何在那里包含代码风格。在三个不同的项目中，我们最终通过 Slack 共享了代码风格输出...哈哈

然而，如果你只是简单地将样式从 IDE 复制到项目本身，它将开始正确地显示在`.idea`文件夹中。问题是，这个选项并不明显，而且几乎没有线索说明为什么这个单独的设置没有包含在所述文件夹中，而项目设置的其他部分都清楚地标记为“共享/项目专用”。

[![Copy style into JetBrains project](img/84f24dd8a14feb555ecdcac5e7c35d3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r5iO1IOG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/toolzap5m4b2ktakd961.png)

这已经在 PHPStorm 上测试过了，但是我很确定它可以在 PyCharm、IntelliJ、WebStorm 或者你使用的任何其他类型的 JetBrains IDE 上运行！

祝你编码愉快。