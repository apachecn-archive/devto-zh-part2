# Enmeti 项目更新日志 2018/08/20

> 原文：<https://dev.to/4lch4-industries/project-enmeti-update-log-20180820-4297>

*封面照片由[法鲁尔·阿兹米](https://unsplash.com/photos/BnWDqUCWQDU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com)T5 拍摄*

# 项目 Enmeti 更新日志#1

因此，当我开始我的 [100DaysOfCode](https://www.100daysofcode.com) 挑战时，我[着手](https://dev.to/hf-solutions/project-enmeti-o0g)创建一个扩展，当我写文章、日志或其他任何东西时，它可以更容易地将链接插入到 Markdown 文件中。我觉得我已经在一个舒适的状态，然而，我想能够尝试它多一点之前，我可以肯定地说。

例如，我写这篇文章不仅仅是为了发布一个更新，说我已经让这个[扩展在](https://marketplace.visualstudio.com/items?itemName=hf-solutions-llc.enmeti)工作，而是为了在一个“生产”环境中测试它😅因此，我需要改变的第一件事是，我可以`Alt+Tab`离开 VSCode，实际上*得到*我需要的链接，并且输入框不会消失...

啊，[我们开始吧](https://github.com/HF-Solutions/Enmeti/commit/66eb63d3951329eb704f4ca03b64eb3e96cdd4b4)🤗好多了。

接下来，如果你试图添加一个已经被添加的链接会怎么样？当我们可以引用[预先存在的链接时，为什么要添加两次？](https://github.com/HF-Solutions/Enmeti/commit/c1d5c504a854036eea134464a21e1bcf8ae4af5a)

## 如何使用

因此，与其只是谈论扩展，不如我来告诉你如何使用它？插入链接最简单的方法是选择一些文本，右键单击，然后选择`Insert Link`。它会显示一个**输入框**，这样你就可以给它你的链接的网址:

[![Example-1](img/d83c7831ecfbe6eb691a528ea55b5af0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ihckxNk0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zesvk29zyzdqlgvlvqp8.gif)

正如你在 gif 中看到的，你也可以使用默认的`Ctrl+Alt+I`键绑定，或者甚至在 [keybindings.json](https://code.visualstudio.com/docs/getstarted/keybindings) 中把它改成你喜欢的:

[![Keybindings.json](img/4cdd3cbb1d9752a18a8588a174a088ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Es43AoGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7NKAy7e.png)

## 接下来

感谢[T2【安德鲁·伯恩](https://dev.to/link2twenty/comment/4k81)的一个很棒的建议，我的下一步是让你可以像插入链接一样插入图片。我的想法是这样的:

*   选择要转换成图像的文本
*   执行命令(`Ctrl+Shift+I`)
*   在**输入框**中输入图像的路径
*   就像我们做链接一样，在最后添加对图像的引用

像往常一样，任何和所有的投入都是受欢迎的，非常感谢😊