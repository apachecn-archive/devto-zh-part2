# 使用 applescript 来自动化项目

> 原文：<https://dev.to/teej/use-applescript-to-automate-with-iterm-1fpl>

有时当你在做一个项目时，无论是工作还是个人，你可能只会在终端中运行一个命令，比如`npm run dev`。其他时候可能会更复杂一些。也许您有一个本地服务器在一个选项卡中运行，另一个选项卡运行 webpack，最后还有一个打开的选项卡供您点击 git 提交。

如果你经常这样做，这给了我们一个自动化的机会。让我们想象一下上面的场景，我们想要运行一个服务器，webpack，并为其他我们可能想要像 git 一样运行的一次性命令保留一个标签。

首先，我们需要从应用程序>实用程序>脚本编辑器打开脚本编辑器。我们将使用这块空白画布来排列一些说明。*注意:*这里假设 iTerm 已经在运行。

```
tell  application  "iTerm"  tell  the  current  window  end  tell  end  tell 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就可以获取当前运行的 iTerm 实例，并聚焦于当前正在运行的窗口。

接下来，我们将创建一个新选项卡并运行一些命令。这将为选项卡设置一个变量，我们可以与它交互并使用默认概要文件运行。如果您用 iTerm 设置了其他概要文件，您可以在这里指定它。

同样好的是，我们可以告诉 iTerm 分成多个窗格，这样我们就不会被选项卡淹没。

```
tell  application  "iTerm"  tell  the  current  window  set  appTasks  to  create  tab  with  profile  "Default"  tell  the  appTasks  tell  the  current  session  set  name  to  "App"  write  text  "cd ~/Code/my-app"  write  text  "node index.js"  set  webpack  to  split  horizontally  with  default  profile  tell  webpack  write  text  "cd ~/Code/my-app"  write  text  "npm run dev"  end  tell  end  tell  end  tell  set  freeTab  to  create  tab  with  profile  "Default"  tell  freeTab  tell  the  current  session  set  name  to  "Free"  write  text  "cd ~/Code/my-app"  end  tell  end  tell  end  tell  end  tell 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用它来启动您选择的代码编辑器，因此如果您正在运行 Visual Studio 代码，您可以将最后一个代码块更新为:

```
tell  freeTab  tell  the  current  session  set  name  to  "Free"  write  text  "cd ~/Code/my-app"  write  text  "code ."  end  tell  end  tell 
```

Enter fullscreen mode Exit fullscreen mode

一旦你准备好了，你可以点按脚本编辑器工具栏中的播放按钮来开始一切。当你对结果满意时，你可以保存脚本，下次启动脚本编辑器时，你可以选择它并再次运行它。

我发现这对于可能需要并行运行多个应用和服务的工作非常有用。

这最初是[发布在我的博客](https://tj.ie/use-applescript-to-automate-with-iterm)上。