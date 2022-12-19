# Outthentic 简化了命令行应用程序测试

> 原文：<https://dev.to/melezhik/command-line-application-tests-made-easy-with-outthentic-j49>

编写测试是软件开发的重要部分。有很多框架可以做到这一点。在这篇文章中，我想介绍一下[outhentic](https://github.com/melezhik/outthentic)——一个带有*嵌入式*测试工具的通用脚本引擎，允许人们轻松地为命令行应用程序创建 BDD/集成测试。

# 安装

我们将需要 Outthentic 的前沿来查看一些专门为测试开发而创建的最新功能。

```
$ cpanm https://github.com/melezhik/outthentic.git 
```

Enter fullscreen mode Exit fullscreen mode

# 应用于测试

比方说，我们有一个应用程序脚本- **application.bash** -它完成所有有用的工作。

我们正确测试这个脚本的目标:

```
$ nano /usr/local/bin/application.bash

#!bash

echo "Hello world" 
```

Enter fullscreen mode Exit fullscreen mode

让我们概述一下简单的测试计划:

*   确保脚本退出代码为`0`
*   确保脚本将特定字符串输出到 STDOUT
*   跳过特定环境的测试
*   如果系统中没有安装脚本，则中止测试

## 确保脚本退出代码为`0`

默认情况下，这个退出代码被检查*当外部脚本通过 Outthentic 时，我们只需要添加一个薄层来启用这个测试:* 

```
$ mkdir check-exit-code

$ nano check-exit-code/story.bash

#!/bash

application.bash 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们运行第一个测试，看看输出:

```
$ strun --story check-exit-code

2018-09-26 16:35:47 :  [path] /check-exit-code/
/root/projects/outthentic-dev.to//check-exit-code/story.bash: line 1: /usr/local/bin/application.bash: Permission denied
not ok  scenario succeeded
STATUS  FAILED (2) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，测试失败了。我们看到了原因。我们忘记设置执行位，很容易修复并重新运行测试。这就是我们编写测试的目的——看到不工作的东西，而不是看到好的东西。

```
$ chmod a+x /usr/local/bin/application.bash

2018-09-26 20:07:27 :  [path] /check-exit-code/
Hello world
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

关于 Outthentic 发出的状态代码的几句话。您可能已经注意到，第一个失败的测试会产生状态代码`2`，这通常意味着*测试失败*，Outthentic 提供了 3 个退出代码:

*   测试通过，一切正常
*   测试失败，肯定是出了问题
*   `1`有些测试通过了，有些没有，有*出了问题*或者有一些*警告*

最后一种情况使得使用输出测试作为[咨询检查脚本](https://www.consul.io/docs/agent/checks.html#check-scripts)成为可能

# 确保脚本向 STDOUT 输出一些字符串

在进入细节之前，让我们想想*为什么*我们会需要它？

几个原因(我想知道读者是否会提供更多) :

*   一些外部脚本没有提供合理的退出代码，我们可以测试的唯一合理的东西是输出

*   非零退出代码*可能不*意味着程序意外地*工作*。同样，我们可以检查输出，而不是退出代码

*   程序发出零退出代码(成功完成)，但是当用不同的参数调用时，产生不同的输出消息。因此，为了检查各种测试用例，我们需要不同地运行相同的程序，并检查不同的输出

因为我们的应用程序只是一个例子，所以对“Hello world”字符串的检查很简单:

```
$ nano check-exit-code/story.check

Hello World 
```

Enter fullscreen mode Exit fullscreen mode

现在运行:

```
$ strun --story check-exit-code

2018-09-26 17:23:29 :  [path] /check-exit-code/
Hello world
ok      scenario succeeded
ok      text has 'Hello world'
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

你可以通过 Outthentic 查看更多的信息。假设我们想检查由`sequential numbers from 1 to 10` :
组成的程序的输出

```
$ nano check-exit-code/story.check

begin:
generator: <<CODE
[ map { "regexp: ^\\d+$_" } (1..10) ]
CODE
end: 
```

Enter fullscreen mode Exit fullscreen mode

但是让我们更进一步。

# 针对不同情况进行拆分测试

在真实的项目中，我们最终可以将许多测试用例映射到许多测试场景。Outthentic 足够灵活，可以采用这样的方案，将多个测试作为一个套件运行。

让我们稍微重组一下我们的测试结构:

```
$ mkdir check-exit-code

$ nano check-exit-code/story.bash

#!/bash

application.bash

$ mkdir check-stdout 

$ nano check-exit-code/story.bash

#!/bash

application.bash

$ nano check-stdout/story.check

Hello World 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有两个测试场景。检查退出代码并检查返回的输出。

这些测试重叠的事实是它们都运行`application.bash`然而对于我们的目的来说这并不重要，我们只是创建不同的测试来强调我们要测试什么。

```
$ tree 

.
├── check-exit-code
│   └── story.bash
└── check-stdout
    ├── story.bash
    └── story.check 
```

Enter fullscreen mode Exit fullscreen mode

最后，Outthentic 允许我们递归地运行所有的测试*:* 

```
 $ strun --recurse

2018-09-26 17:34:52 :  [path] /check-exit-code/
Hello world
ok      scenario succeeded
STATUS  SUCCEED
2018-09-26 17:34:52 :  [path] /check-stdout/
Hello world
ok      scenario succeeded
ok      text has 'Hello world'
STATUS  SUCCEED
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

随着我们的测试越来越多，我们可能需要缩小输出，这可以通过 Outthentic test runner 的`--format`选项实现:

```
$ strun --recurse --format=production
2018-09-26 17:36:14 :  [path] /check-exit-code/
2018-09-26 17:36:14 :  [path] /check-stdout/ 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来看看测试计划的最后两点。

有时，我们出于某些原因需要跳过我们的测试，甚至在*某些*初步条件不满足时抛出异常，这样我们就不会浪费时间运行测试，让*不必要的*消息污染控制台，因为我们已经知道*可能不会*运行测试套件。

# 退出对某些环境的测试

比方说，我们不希望通过传递`environment`变量:
来为*定义的*生产环境运行测试

```
$ export environment=production 
```

Enter fullscreen mode Exit fullscreen mode

Outthentic 允许通过使用`quit`函数*立即*退出测试执行阶段，让我们来看一个例子:

```
 $ nano check-exit-code/hook.bash

#!bash
if test "$environment" = "production"; then
  quite "production tests are disabled, please use dev environment"
fi 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun --story check-exit-code

2018-09-26 18:10:54 :  [path] /check-exit-code/
? forcefully exit:  production tests are disabled, please use dev environment
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

相反的想法是让你的测试在特定条件下立即失败。您应该为此选择`outthentic_die`功能:

```
$ nano check-exit-code/hook.bash

#!bash

which application.bash 2>/dev/null || \
  outthentic_die "application.bash is not installed. You should install it to run tests" 
```

Enter fullscreen mode Exit fullscreen mode

```
$ unlink /usr/local/bin/application.bash
$ strun --story check-exit-code

2018-09-26 18:16:49 :  [path] /check-exit-code/
!! forcefully die:  application.bash is not installed. You should install it to run tests
STATUS  FAILED (2) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的项目结构是这样的:

```
.
├── check-exit-code
│   ├── hook.bash
│   └── story.bash
└── check-stdout
    ├── story.bash
    └── story.check 
```

Enter fullscreen mode Exit fullscreen mode

`Hook.bash`是一个 outhentic 钩子的例子——小脚本在主测试运行之前*运行，你可以在 outhentic 文档页面上阅读更多关于[钩子](https://github.com/melezhik/outthentic#hooks)的内容。*

## 分散或集中测试模式

您可能已经注意到了，我们必须在每个测试中添加 hook.bash，以确保满足初步条件。在这个方案中，每个测试都被视为独立的单元，这遵循了我们在许多测试框架中看到的模式。虽然它看起来更容易实现，但也导致了代码的重复。现在我们有 hook.bash 脚本为每个故事做同样的工作。

或者，在我们运行*任何*测试之前，我们可以检查那些初步条件(比如环境和正在安装的应用程序)**一次**、*在最开始*。

这种方法将我们引向相反的方案，在那里所有的测试在一些“主要”入口点上变得更深入，在那里我们可以:

*   执行初始化步骤(初步条件检查)

*   按顺序调用测试作为函数

在 Outthentic 中，这种类型的设计可以通过所谓的“故事模块”轻松实现。当你把测试作为函数调用时。

让我们稍微重构一下我们的项目来实现这个想法:

```
 $ nano hook.bash

#!bash

# this a main entrypoint 

if test "$environment" = "production"; then
  quite "production tests are disabled, please use dev environment"
fi

which application.bash 2>/dev/null || \
  outthentic_die "application.bash is not installed. You should install it to run tests"

run_story "check-exit-code"
run_story "check-stdout"

$ # we don't need hook.bash per story anymore

$ rm check-exit-code/hook.bash

# we make our tests - modules or functions, just when we copy those ones to modules/ folder

$ mkdir modules

$ mv check-exit-code check-stdout modules/ 
```

Enter fullscreen mode Exit fullscreen mode

所以我们最终得到了这个结构，它有一个主入口点(hook.bash)和两个可靠的测试(check-exit-code/story.bash，check-stdout/story.bash)，还要注意我们在主入口点内部运行初步条件检查，并控制*如何运行*和*运行什么*测试。

```
.
├── hook.bash
└── modules
    ├── check-exit-code
    │   └── story.bash
    └── check-stdout
        ├── story.bash
        └── story.check 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备运行测试，注意我们不再需要`--recurse`选项，因为所有的测试序列都是通过 hook.bash 文件定义的。

```
$ strun

2018-09-26 20:24:20 :  [path] /modules/check-exit-code/
Hello world
ok      scenario succeeded
2018-09-26 20:24:20 :  [path] /modules/check-stdout/
Hello world
ok      scenario succeeded
ok      text has 'Hello world'
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# 结束

这只是对 Outthentic framework 提供的测试功能的一个简单介绍。如果你喜欢——去 [GH 页](https://github.com/melezhik/outthentic)看所有的细节。

欢迎在评论中分享你的观点。**