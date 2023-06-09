# 如何在 Xcode 中制作命令行工具

> 原文：<https://dev.to/ceri_anne_dev/how-to-make-a-command-line-tool-in-xcode-2f81>

MacOS 命令行工具可以方便地实现任务自动化，也是享受 Swift 更多乐趣的好方法，以下是设置方法:

**创建项目**

1)新建一个 Xcode 项目，选择 MacOS 和命令行

[![Create an Xcode project](img/698d59039a7e4f33e5344994bf6e1111.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3E9VgJjn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://ceri-anne.co.uk/wp-content/uploads/2018/08/Picture1-300x216.png)

2)为您的应用命名，并确保选择 Swift 作为语言

[![Name the app](img/586d2c7769bd1bb4c8348fe9a4793009.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oepqtrz4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://ceri-anne.co.uk/wp-content/uploads/2018/08/Screen-Shot-2018-08-31-at-15.59.05-300x216.png)

3)单击“下一步”并选择保存项目的位置。

您现在有一个 Xcode 命令行项目了！`main.swift`文件是 Xcode 运行代码的地方，类似于 Java 或 C#中的 main 函数或 iOS 中的 AppDelegate。那里已经有一个`“Hello World”`打印声明了。

在产品下有一个名为`CommandLineTool`的红色可执行文件。这是我们构建项目时创建的可执行文件。从命令行。它是红色的，因为我们还没有建造它

[![Folder in Finder](img/ed026effc1f91acc195832523a4e97cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--slYCDSGe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://ceri-anne.co.uk/wp-content/uploads/2018/08/Screen-Shot-2018-08-31-at-16.00.10-300x159.png)

4)构建并运行项目。命令行工具产品现在将变为黑色，输出窗口中将显示以下输出

[![Output window](img/8ca36b1887614b2785abcb9682c6556e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--96B3ea3X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://ceri-anne.co.uk/wp-content/uploads/2018/08/Picture3-300x97.png)

**从命令行运行**

1)在文件检查器(⌥⌘1)中，检查 CommandLineTool 产品的保存位置。这将是一个很长的位置，以`/Build/Products/Debug/CommandLineTool`结尾

2)导航到这个`Debug`文件夹，通过键入`./CommandLineTool`
运行命令行工具

```
$ ./CommandLineTool
Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

3)尝试进入 Xcode 并稍微更改程序，例如: