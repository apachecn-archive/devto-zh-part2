# 软件工程师生产力:编码

> 原文：<https://dev.to/bgadrian/software-engineer-productivity-coding-jkp>

> 这篇文章发表在我的博客[https://coder . today/software-engineer-productivity-coding-75fa 21d 3804](https://coder.today/software-engineer-productivity-coding-75fa21d3804)。这是它的修订版。

**生产力提示**是一系列面向程序员的通用文章。这些技巧应该适用于任何环境、框架、语言或平台。

这个故事属于多流系列:

1.  生产力:编码——本文
2.  [生产力:工作流程——行为&环境提示](https://dev.to/bgadrian/software-engineer-productivity-workflow-5hce)

我很懒，但我在乎我们拥有的最宝贵的货币:时间⏰。也就是说，我想分享一些提高我效率的小技巧。

### TL；TR 🕮

*   自动化一切
*   学习和定制您的工具
*   存储您的片段和其他提示

### 自动执行命令🤖

观察你自己，找出你每天多次输入的**命令并缩短它们，如果需要的话将它们分组。如果您只使用 git 可视客户端，您应该学习底层命令。**

*几乎一切。我通常手动交付重要链中的最后一个命令(例如`test&&build&&copy&&upload`然后手动部署)。*

> 使用别名，你会犯更少的人为错误，并交付更多。从你的日常事务中消除无聊的步骤。

自定义别名的缺点是:**不灵活和认知负荷**。这意味着别名只存在于一个环境中(比如你的工作笔记本电脑)，你必须记住它们。

我也推荐 [git auto complete](https://github.com/git/git/blob/master/contrib/completion/git-completion.bash) 或者像 [bash-it](https://github.com/Bash-it/bash-it) 这样的框架，它们通常是用一些逻辑(在命令命名中)开发的，而且它们更容易记住。搜索为您的语言、框架和平台构建的 bash 助手。

> 不需要输入当前的 git 分支名称为我节省了超过 100 万个字符，使用别名避免了许多可能的错误。

即使你不使用别名，练习使用`HEAD`而不是`master`会使你减少人为错误，例如使用`git pull origin HEAD`而不是`git pull origin master`。

即使你在 Windows 上也可以使用 Bash，我使用的是来自 Git for Windows 的 Git bash。

**Bash 提示:学会使用 [bash 历史，尤其是 CTRL+R](http://lifehacker.com/278888/ctrl%252Br-to-search-and-other-terminal-history-tricks) 。**

### 定制您的工具🌂

您花了大量时间使用 IDE、bash 和其他工具。根据**你的**需求和感受定制**你的**工具。你应该💙利用他们，否则你的工作会很糟糕。如果你讨厌某样东西，现在就改变它(比如缺少键盘快捷键，显示颜色)，继续，修复它，然后返回，我会等你。

> 我喜欢我的狂欢是欢快的，彩色的，我所有的编辑都有黑色的主题。

### 定制您的环境

让你的环境为你服务，而不是与你作对。最基本的例子是在你的全局 git `.gitignore`文件中添加你的 IDE 文件。你将节省**时间**，每台机器只做一次，而不是每个项目。

```
#~/.gitignore
.idea/
.vs/ 
```

观察者自己，以前还有哪些系统设置有问题，防火墙规则？python 版本？Unity3D 本地缓存？一劳永逸地解决它们。

### 学习你的工具🏧

当你学习新的开发技术、新的框架、范例和设计模式时，你应该学习为你设计的工具。

文本编辑器是为编写文本的编辑人员准备的，IDE 是为专业开发人员准备的。为什么？因为我们关心我们的时间，我们是专业的，我们的时间是有报酬的，我们想在这段时间里尽我们所能交付更多。IDE 更重，因为它们有更多为我们设计的内置特性。 *PS:一个有几十个像编译器/git/linter 集成插件的文本编辑器就是一个 IDE。*

> 走出你的舒适区，提高你的技能。

升级您的 IDE 并了解新功能，很可能会满足您的需求。偶尔搜索一下新的 IDE，可能会有惊喜。

通过使用更多的 ***键盘快捷键*** ，你将获得巨大的生产力提升。例如“**扩展选择**”这些年来节省了我很多时间。其他例子有**实时模板和多光标**。

[![ide1](img/2c043a50a2a41baaebf3c12365caf7ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OGdyz9Ov--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fq646knupdm1xg9knxx6.gif)

我经常使用的一个实时模板是调试消息，比如 if `IsDebugBuild {print("msg", var)}`或 try/catch 表达式。

您可能会不时地使用一些正则表达式替换，但有时多游标更简单、更有效。

[![ide2](img/52687aecbf864c34a05d8e43001c4886.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ujM9oo5S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2458q8t3dfwppnbxb5pg.gif)

对多游标来说足够好的场景:硬编码的数据、枚举、模板和一切最无聊的东西。

我看到的一个 [IDE 的最新特性是一个机器学习助手(codata)](https://www.codota.com/)。它知道所有开源代码、设计模式和其他开发者犯的错误。

### 自动化您的开发环境🎰

上次我得到一份新工作，花了几天时间为一个大项目做一个完整的工作开发设置。不一定非要这样，有专门为这类自动化设计的工具。 [Boxstarter](http://boxstarter.org/) (我还没用过)可以在几分钟内从一段代码中构建你的**窗口**开发环境。

我开始用 **Docker** 做这类事情。使用简单的 CLI 指令，您可以安装所需的大多数工具/服务/软件。不需要编译，搜索包或者二进制。

> 示例:具有特定版本和可视化管理工具的持久性 mongo-db 实例。

### 不做一个🔨

> "对一个拿着锤子的人来说，任何东西看起来都像钉子."

如果你需要一个系统脚本，不要用 JavaScript，学一点 bash，问一个同事或者 stack overflow 如何做。如果你需要一个小的浏览器动画，学习 CSS3。你明白了。这将花费你额外的几分钟时间，但它将打开一扇巨大的门🚪在你的未来。

> 大多数工程师都有这个问题。我以前做什么都是 PHP，CS 毕业生卡在 Java 或者 CPP。这是人之常情，设计师在 Photoshop 上犯了同样的错误，QA 通常使用像吉拉这样的 bug 跟踪软件。这个想法是扩展你的工具集，一步一步来，一次一个小脚本。

### *上存💾

在“保存文件”操作中自动执行你能做的一切，我最常激活的是**“保存时自动格式化”**。

> 自动格式化使我能够编写快速而肮脏的代码，我不会在缩进、逗号、分号或其他美化元素上浪费时间，那是 IDE 的工作。

自动格式化还消除了更多的人为错误，比如忘记格式化并产生 git 冲突或事后愚蠢的提交。你和你的同事会更有效率。

> IDE 是来帮助你的，让它完成它的工作吧

你可以根据自己的需要在 save 上创建自己的迷你管道:编译、错误检查、单元测试、热推代码(用于 web dev、js &css)，你可以说出来。对于更耗时的脚本，我使用手动触发的任务。

例如，在 **Go** 中，我激活了**“运行测试&保存覆盖”**，让控制台一直打开提高了我的效率。我可以更快地工作，而不害怕重构，每一分钟我都知道代码是好是坏。

### 待办事宜🔖

在你的代码中使用更多的`//TODO's`。不要停止你当前的流动，不要通过解决邻近的事情来退出“区域”。如果你遇到了一个小问题，转储一个 TODO，然后带着一个更好、更大的视角回来。

### 片段📚

不要像我的几个朋友那样在聊天中储存片段，你可以使用 [Gists](https://gist.github.com/) 或其他类似的服务。你自己的回购，谷歌硬盘里的文件，你说吧。这个想法是存储**证明有用并且很难找到/实现的代码片段**。它将为你节省**时间**，你未来的时间。

### 共享 1-n

我们可以把以前的解决方法推广到其他问题上。如果一个同事有问题，你要帮助他，很可能将来会有其他同事需要这个答案。作为一个经验法则，使用 **1-n 个持久的沟通渠道**，**分享就是关怀**。

保持一个项目最少的文档，写文章/故事，即使它们只是内部的。记录你的内部聚会，
想法是不要用电子邮件/私人聊天来解决问题，**分享知识**，这样其他人也可以访问它。

> 这会节省你的时间，因为你不必解释/解决同样的问题两次。

### 自动化您的 API 调用📝

如果你使用任何类型的 API，我推荐你使用像 [Postman](https://www.getpostman.com/) 这样的工具。如果您将调用放在一个地方，您甚至可以自动化它们(在连续交付管道中)，与您的同事共享它们(作为配置文件)并验证响应(使用 json 模式库)。我曾经把测试和服务器 API 源代码放在同一个 repo 中。

### 谢谢！🤝

请将您的反馈发送给我，以便我可以改进以下帖子。

### 其他资源

[生产力:工作流程——行为&环境提示](https://coder.today/6-workflow-tips-for-a-software-engineer-productivity-ba35d9f7e267)

[乔尔测试:更好编码的 12 个步骤](https://www.joelonsoftware.com/2000/08/09/the-joel-test-12-steps-to-better-code/) 

[![end](img/8afb0c3d200fd0b27f5c99ddf2ad6b76.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yKH_uExg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7m44hjlg0ucepy7sop1u.jpeg)