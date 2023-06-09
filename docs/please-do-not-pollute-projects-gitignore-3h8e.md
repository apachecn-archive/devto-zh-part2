# 请不要污染项目的。gitignore

> 原文：<https://dev.to/oleksiyrudenko/please-do-not-pollute-projects-gitignore-3h8e>

`.gitignore`是一个规则集，定义我们不希望被 g it 跟踪的资源，或者换句话说，被 git 忽略。

> **剧透警报。**如果你知道`~/.gitignore`是关于什么的，你就不需要再往下读了。

有 3 类最常见的主要资源是我们不希望或不需要使用 git 跟踪的:

1.  编译/转换成中间代码文件(例如 C/C++ universe 中的`.obj`或`.o`)、分发包、其他可交付成果、日志等的源代码。从被跟踪的源代码中得到的任何东西。
2.  开发环境临时文件和/或例如特定于项目的 IDE 设置(但非常特定于特定的 IDE)
3.  划痕、笔记、剪贴板等。

你不需要跟踪项目 1 的衍生产品，因为你可以在任何时候从资源中构建可交付的产品，让这些产品处于 VCS 的控制之下是没有意义的。所以这些排除是非常有用的，每个项目贡献者都将受益于将它们列在项目的`.gitignore`中。

接下来，您将把您最喜欢的 IDE temps 添加到`.gitignore`，您的同事将添加他们的，并且某一天其他人可能想知道`.DS_store/`是否与项目的数据库有关，或者为什么在项目中不鼓励想法共享(`.idea/`)。

项目贡献者可能也同意，划痕和笔记存储在`tmp/`下，不应该通过 git remote 共享。

好的。

有许多集合和有用的资源可以帮助你编译一个`.gitignore`，它涵盖了你的技术栈(第 1 项)、最常见的 ide(第 2 项)，以及 smth，比如涵盖第 3 项的`tmp/`。

仅举几个例子:

*   [https://github.com/github/gitignore](https://github.com/github/gitignore)
*   [https://www.gitignore.io/](https://www.gitignore.io/)
*   [https://github . com/code zombie/vscode-gitignore](https://github.com/CodeZombieCH/vscode-gitignore)
*   [https://www . npmj . com/package/gitignor](https://www.npmjs.com/package/gitignorer)

但是，为什么您希望在您的项目文件中包含任何与项目无关的内容呢？IDEs 来来去去。个人笔记？太私人了。

项目`.gitignore`被污染。涵盖第 1 项的规则是可以的，因为那些**为项目贡献团队带来了**价值。

想象一下(真的很容易),你为一个开源项目做贡献，其中`.gitignore`没有列出你最喜欢的排除规则。解决这种情况有两种选择:

1.  添加一个规则，将你最喜欢但不夸张的想法排除到项目的`.gitignore`
2.  在每次提交时做类似于`git add -A && git reset -- MyLovelyIDE/*`的事情

两者都不好，也不方便。

有一个合适的地方可以让您的个人偏好远离 git 轨道，并且不会让您的项目伙伴感到困惑。

**全球`.gitignore`**

1.  在你的主目录`~/`(Windows 下的`C:\Users\your-user-name\`)下创建一个`.gitignore`文件。
2.  用任何项目中的排除规则填充它(开发环境和 IDE 设置，notes 和 scratches 的子目录等。)
3.  运行`git config --global core.excludesfile ~/.gitignore`

你完了！与同事分享你的发现。

* * *

封面图片致谢: [timbercode.pl](https://timbercode.pl/blog/2017/02/19/gitignore-2-sztuczki/)