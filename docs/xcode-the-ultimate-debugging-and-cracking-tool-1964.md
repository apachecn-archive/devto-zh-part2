# Xcode，终极调试和破解工具

> 原文：<https://dev.to/nakiostudio/xcode-the-ultimate-debugging-and-cracking-tool-1964>

调试一直是我使用计算机经验的一部分，甚至在学习如何编码之前。然而，展开二进制文件和修改程序行为的满足感从来都不是没有争议的。

通常，调试包括在十六进制编辑器和调试器、应用程序、终端、计算器之间来回切换，更不用说程序退出和你必须遵循的很长的配方，直到你的调试会话突然结束。*不好……*

但是几个月前，以在 macOS 上破解 Twitter 为借口，我决定最小化这个问题，优化这个流程。这时，Xcode 来了。

### 设置

首先，我们需要创建一个 Xcode 项目，以 Cocoa 框架为目标。

[![setup](../Images/ec88c49d3bffc5bcdbf6725798c72945.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jL23nCjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/550y57wtuby89996b3bs.png)

### 调试

我们将使用嵌入式 lldb 控制台进行动态调试。

想象一下，macOS 的 Twitter 就是我们要调试的 app。通常，我们需要运行 lldb 并从调试器中启动进程，相反，Xcode 可以在我们每次单击 run 按钮时为您完成这项工作。

为此，选择 Twitter 应用程序作为我们框架方案的 **Run** 配置中的可执行程序。

[![debugging_1](../Images/537e664961a33cbd9a5e41328c698bbe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--isGKMrGd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7tuv6yjg5adn9fculwiq.png)

通过这种方式，我们的框架将被编译，Twitter 应用程序将与附加到流程的 lldb 一起启动。

[![debugging_2](../Images/2d39ccb0bea82af4816eac540525540f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---L2Gq3gv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2sd2sgbkpstq6f0l6ujb.png)

### 注入代码

到目前为止，我们所做的可能是一个足够的成就，我们可以轻松地创建一个针对应用程序的调试会话，并且还可以利用 autocomplete Xcode 提供的命令。但是注入代码呢？

代码注入通常包括编译一个动态库并修补二进制文件，使其链接到我们的库。尽管如此，调试我们注入的代码是一项单调乏味的任务，过程缓慢且重复，尽管我们总是可以自动化其中的一部分。

现在，由于 Xcode 灵活的配置方案和一点魔法，我们可以轻松地修补我们正在检查的应用程序的运行时，而无需修改其二进制文件(双赢)。

作为一个例子，让我们注入一些记录对`viewDidLoad`的每次调用的代码。
Cocoa Framework 项目模板创建了一个名为`nameOfTheProject.h`的头文件。我们将创建各自的`.m`文件，并在其中放置 swizzling 逻辑。

[![injection_1](../Images/1800c4b313f8e591460c51c82cb33abb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ybenPeqg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hvpgxj5uor6eboso70ls.png)

实际上，Xcode 将编译我们的框架，并启动 Twitter 应用程序，调试器附加到它的进程中。将这两者联系起来就像用一个环境变量运行流程一样简单，这个环境变量强制加载位于给定路径的库。这个环境变量是`DYLD_INSERT_LIBRARIES`和我们库的路径`${TARGET_BUILD_DIR}/${TARGET_NAME}.framework/Versions/A/${TARGET_NAME}`。

[![injection_2](../Images/18425b36d1b840ac6790d49a831f7955.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pzlD1FtB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8bfnlyiapoyyf6fl1htj.png)

在我们的方案的 **Run** 配置的 **Arguments** 选项卡中设置环境变量后，单击 Run 按钮。我们的代码应该被正确地注入到 Twitter 进程中。

[![injection_3](../Images/3602a1c51dd777cc2f81b77873af92ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cQ2a9c92--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/112anpji9b3rzr84jjzf.png)

不够好？Xcode 提供的所有其他调试工具也是可用的，从视图层次结构到内存图调试器。

[![injection_4](../Images/cab1a0fd2c77600ba2f5a98de9c26c9e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_plDm9n6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7t8mh752oqtvehntdtat.png)

### 总结归纳

无论你是否对逆向工程感兴趣，我都鼓励你尝试一下，并使用 [TwitterX](https://github.com/nakiostudio/TwitterX) 来了解这个设置的更多功能。