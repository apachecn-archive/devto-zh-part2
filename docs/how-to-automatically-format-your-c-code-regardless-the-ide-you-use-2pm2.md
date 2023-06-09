# 如何自动格式化你的 C++代码，不管你使用什么 IDE

> 原文：<https://dev.to/sandordargo/how-to-automatically-format-your-c-code-regardless-the-ide-you-use-2pm2>

**你想每天出一道 C++题，为下一次面试做准备吗？免费注册[每日新闻采访](https://www.dailycppinterview.com/)！**

如果你关注我，你可能已经注意到[我是编码指南](http://sandordargo.com//blog/2018/03/28/codereview-guidelines)的忠实粉丝。然而，我并不特别喜欢评论格式，比如缩进、[制表符 vs](https://www.youtube.com/watch?v=SsoOG6ZeyUI)、空格等等...但我确实这么做了，并且一直这么做，因为这是可读性的一个重要部分。

代码格式越紧密，可读性就越好，因此代码也就越容易维护。

为了减少对这类项目的评论、辩论和争论，我们在源代码中引入了自动格式化。

在之前我们用 Java 工作的项目中，我们已经通过使用 [Maven checkstyle 插件](https://maven.apache.org/plugins/maven-checkstyle-plugin/)实现了格式检查的自动化。每次出现不符合我们定义的规则的情况，构建都会失败，所以没有人能够签入不符合特定规则的代码。

在 C++中，我们仍然有很好的旧代码审查验证。但是这种形式的验证不是很有效，因为不幸的是，并不是每个人都足够严格。

他们的时间到了。

我们正在我们的管道中引入 [`clang-format`](https://clang.llvm.org/docs/ClangFormat.html) 。

[`clang-format`](https://clang.llvm.org/docs/ClangFormat.html) 是一个将你的格式化风格应用到 C/C++/Objectiv-C 代码的工具，有很多定制的可能性。我们开始分三步使用它。

## 批量更新

我们认为，当整个代码库都遵循新的格式样式时，应用新的格式样式是最好的。当您必须手动转换代码时，这是不可想象的，但是使用自动格式化工具，这是一个简单的任务。

所以作为第一步，我们在我们的库上运行 [`clang-format`](https://clang.llvm.org/docs/ClangFormat.html) 。即使对于数千个代码文件，这也不会超过几秒钟。

在这之后，或者更好的是在这之前，我们引入两个并行的验证步骤。

## 在预提交钩子中格式化代码

我们在本地 Git 设置中打开了一个[预提交挂钩](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)。在提交之前，Git 运行 [`clang-format`](https://clang.llvm.org/docs/ClangFormat.html) ,并将格式样式应用到您希望提交的代码中。它所花费的时间并不重要，因为它只检查更改的代码，但是请记住，即使是在整个代码库上，它也是很快的。

如果您不希望自动重新格式化，可以只运行检查而不提交。在这种情况下，您还会得到一份检查失败的报告。

这一步需要手动操作，因为签出 Git 存储库不能自动打开任何挂钩。首先，这让我感到惊讶。然而，这很有道理。这太危险了。想象一下，我创建了一个带有钩子的存储库，用来删除你所有的文件和文件夹...我仍然可以在安装脚本中添加这样的挂钩，但是它不会自动安装，而是由您来安装。

## 向您的持续集成管道添加检查

在我们的 Jenkins 管道中，我们添加了一个步骤，以便在每次有新的拉请求时运行 [`clang-format`](https://clang.llvm.org/docs/ClangFormat.html) 。如果有任何差异，管道就会失败，构建甚至不会开始。这只是额外的安全措施。如果每个人都打开 local 上的钩子，管道就不会因为样式问题而失败。但最好对人类的懒惰和健忘有所准备。

关键的一点是，自动格式化 C++代码非常容易，你甚至不必强迫人们使用相同的 IDE。不管怎样，这都不是个好主意。要么对管道进行一些检查，要么完全自动化格式化。你对代码格式自动化有什么体验？

*本文最初发表在[我的博客](http://sandordargo.com/blog/2018/05/30/how-we-format-our-cpp-code)上。*