# Visual Studio 2017 扩展

> 原文：<https://dev.to/damdigital/visual-studio-2017-extensions-1kd5>

[![Laptop](img/91c818b60dae77220f1e77872b7cf9a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5RKCZIAT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c6cteil66mufbgc2v387.jpg)

*这篇文章最初出现在[理查德·巴拉德的博客](https://richardballard.co.uk/visual-studio-2017-extensions/)T3 上*

作为我推动“更聪明地工作，而不是更努力地工作”的一部分，这里是我的 Visual Studio 2017 扩展列表，排名不分先后。

当我开始使用新的扩展时，我会尽力更新这个列表。

### [网络要闻 2017](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.WebExtensionPack2017)

Mads Kristensen 是*Visual Studio 扩展专家，他的许多优秀扩展都打包在专门面向 Web 开发人员的 Web Essentials 2017 中。请务必阅读注释，了解每个扩展的相关信息，但下面列出了我的一些个人亮点。虽然这个扩展包将安装所有列出的扩展，但是您可以简单地删除任何您以后不需要的扩展。或者，您可以单独安装扩展。*

**[添加新文件](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.AddNewFile)**
这无疑是对 Visual Studio 最好的扩展。只需点击`Shift + F2`弹出一个对话框，输入新文件的名称和正确的文件扩展名。您甚至可以通过添加“/”从同一个对话框添加新文件夹。比 VS 添加新文件的内置方法更容易使用。

**[打开命令行](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.OpenCommandLine)**
这是对你的 Visual Studio 工作流的一个无价的补充。想要直接跳到命令行中您正在处理的文件的正确路径吗？只需点击`Alt + Space`，这个扩展就可以做到这一点。

**[图像优化器](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer)**
图像优化器允许无损图像压缩，在不影响质量的情况下降低网站的页面重量。在 VS 中直接右键点击图像文件夹，你可以优化所有图像，包括子文件夹中的图像。另一个方便的特性是复制 base64 格式的图像，根本不需要离开 VS。

**[Web 编译器](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.WebCompiler)**
需要编译 SASS/LESS 等等？安装 Web 编译器扩展，并在一些简单的配置后，立即编译这些文件。如果需要，在文件保存、项目构建或手动触发时编译文件。我们的团队正在远离这一点，转而支持其他编译器，但为了让事情进展得更快，它仍然是一个很好的选择。

**[文件图标](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.FileIcons)**
谁不希望一切看起来更漂亮一点，文件图标就是这么做的。当 Visual Studio 发现某些文件扩展名缺少图标时，文件图标就会出现，照亮解决方案资源管理器。

**[学习快捷方式](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.LearntheShortcut)**
这是在 Visual Studio 中尝试学习一些重复性任务的快捷方式的好方法。下次当你感觉你的手离开键盘去够鼠标的时候，看看输出结果，看看是否有一个快捷方式可以整合到你的工作流程中来提高效率！

**[错误捕捉器 II](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ErrorCatcherII)**
在右上角显示与您正在处理的文件相关的错误、警告和消息。这是一个很小的 UI 变化，但它确实能帮上很大的忙。

**[尾随空白可视化工具](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.TrailingWhitespaceVisualizer)**
让尾随空白变得响亮刺耳，让你知道它在那里，并提醒你整理。还有一个选项可以在保存文件时自动删除尾随空格。完美。

**[禁用解决方案资源管理器的动态节点](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.DisableSolutionExplorersDynamicNodes)**
一个扩展，通过移除动态节点(那些橙色的子节点)来移除解决方案资源管理器中的一些干扰..)显示在展开折叠的。cs 文件。我从来不用它们，所以能够完全删除它们是很好的。

### [Visual Studio 拼写检查](https://marketplace.visualstudio.com/items?itemName=EWoodruff.VisualStudioSpellCheckerVS2017andLater)

我不能高度推荐这个扩展，它多次拯救了我和团队的培根。发布带有复制错别字的网页不再是常态。一旦你看到那些巨大的漩涡状下划线，你就很难忽视它们。该扩展只检查纯文本、字符串和注释，因此您的代码可以被安全地忽略。一开始，告诉它在字典里添加一些常用词会花一点时间，但是花这些时间是值得的。

### [针对 Visual Studio 的 GitHub 扩展](https://visualstudio.github.com/)

通过向您显示可从您的帐户访问的可用存储库，使在 Visual Studio 中克隆存储库变得更加容易。让您不必在 GitHub 中查找 url，然后从命令行执行克隆，或者不必离开 VS 切换到另一个 Git 客户端来完成相同的任务。

### [码女仆](https://marketplace.visualstudio.com/items?itemName=SteveCadwallader.CodeMaid)

前几年我使用这个扩展的次数比现在多得多，但我仍然安装了它。这是一个功能丰富的扩展，值得研究。然而，我只使用它的代码清理功能，我仍然喜欢 Resharper 的。使用快捷方式`Ctrl + M + Space`在文件上触发此操作。我确信现在还有其他的选择，但是它现在已经根深蒂固在我的工作流程中了。