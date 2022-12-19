# 来自构建农民的提示——第 1 部分:CI 脚本很可怕

> 原文：<https://dev.to/dmerejkowsky/tips-from-a-build-farmer---part-1-ci-scripts-are-scary-5cfg>

*最初发表于[我的博客](https://dmerej.info/blog/post/tips-from-a-build-farmer-part-1-ci-scripts-are-scary/)。*

*注意:这是“打造农民系列[技巧的第一部分。](https://dmerej.info/blog/post/introducing-tips-from-a-build-farmer/)*

Bash 中有一个简单但有点现实的 CI 脚本:

```
echo "fetching latest changes"
git fetch
git reset --hard @{u}
echo "compiling" ...
make
echo "running tests" ...
make test
echo "generating archive" ...
DESTDIR=/tmp/foo make install
cd /tmp
tar cvfz foo.tar.gz foo 
```

Enter fullscreen mode Exit fullscreen mode

这段代码获取最新的更改，编译代码，运行测试，最后生成一个归档文件。

那几句台词有什么可怕的？这不就是*无聊*吗？

好吧，让我们来看看，请注意，我将要告诉你的一切都是基于真实的故事。 <sup id="fnref1">[1](#fn1)</sup>

在我们开始之前，最后一件事:让我们假设编译代码需要 5 分钟，运行测试需要 10 分钟。

# 缓慢发展的陷阱

就在第一次公开发布之前，您发现归档文件中没有任何自述文件。然后添加一行代码，将 README 文件从源文件复制到用于生成归档文件的目录:

```
 ...
  echo "generating archive" ...
  DESTDIR=/tmp/foo make install
+ cp README /tmp/foo
  cd /tmp
  tar cvfz foo.tar.gz foo 
```

Enter fullscreen mode Exit fullscreen mode

然后，为了查看您的代码是否工作，您必须*等待整个构建的持续时间*，然后才能知道您的代码是否正确。

因此，您等待 20 分钟，CI 脚本就在生成归档文件之前终止:

```
fetching latest changes
compiling ...
runinng tests ...
generting archive ...
cp: cannot stat 'README': No such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

哎呀，原来自述文件叫做`README.md`，而不仅仅是`README`。

所以你做了另一个改变:

```
- cp README /tmp/foo + cp README.md /tmp/foo 
```

Enter fullscreen mode Exit fullscreen mode

您再等 20 分钟，构建再次通过。

恭喜你，你刚刚花了 40 分钟做了一个单行补丁(！)。

这就是我所说的“缓慢发展陷阱”。

但是等等，还有更糟的。

让我们保留这个简单的例子，研究一些您可能会遇到的错误。

# CI bug 又在哪里找到它们

## 致命的错字

[![All the automated tests have crashed - it's normal](../Images/f1d0d86d9a6d63ba17f364e6785c5b30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BcC164G_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/geek-and-poke-its-normal.png)

第一个例子:脚本总是在运行任何测试之前崩溃:

```
echo "compiling" ...
make
echo "running tests" ...
make tests
...

make: *** No rule to make target 'tests'. Stop. 
```

Enter fullscreen mode Exit fullscreen mode

明白了吗？Makefile 目标叫做`test`，单数，不是`tests`，复数。

如果你幸运的话，你的团队成员会一直等到你解决问题，但是有些人可能有权完全绕过 CI，因为“我们在运行`git push`之前已经在我们的机器上运行了所有的测试，就像我们一直做的那样”。

提示:他们没有。没有人会一直这样做，这也是我们首先使用 CI 的原因。所以最好在主分支充满失败的测试之前抓紧时间。并且记住缓慢发展陷阱。

# 忘记重要的事情

第二个例子:

```
echo "fetching latest changes"
git reset --hard @{u}
echo "compiling" ...
make
echo "running tests" ...
make test 
```

Enter fullscreen mode Exit fullscreen mode

你忘了打电话给`git fetch`。日志看起来像是代码被更新了，但实际上您只是将工作树重置为相同的提交。因为这是一个好的提交，所以所有的测试总是通过的。

一周后，您发现引入了几个 bug，因为开发人员错误地认为测试是在他们刚刚推出的代码版本上运行的(谁能责怪他们呢？).此外，团队开始再次信任 CI 脚本还需要相当长的时间。 <sup id="fnref2">[2](#fn2)</sup>

# 你只缺一个人……

第三个例子:您正在构建一个 Qt 应用程序，在您用来安装的 Makefile 中，缺少复制`libQt5Widgets.so`的规则。

主程序在安装后第一次运行时就崩溃了。

```
error while loading shared libraries: libQt5Widgets.so.5:
cannot open shared object file: No such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

这意味着你不能再发货了，你不能再做 QA 了，每个人都被卡住了，直到你修复了 bug(我们又回到了‘开发缓慢’的状态)

现在你应该已经意识到**你不允许在你的 CI 脚本**中有 bug。

害怕吗？让我们换换口味，谈谈时间旅行吧。

# 回到未来

你的团队正在开发一个全新的产品，他们打算在两个月后发布。你马上开始写 CI 脚本。但是你仍然必须穿越到未来，确保你今天写的脚本仍然有效。或者至少，你可以让它们快速工作。记住:“不允许有虫子”。

所以，除了不允许有任何错误，你*也*必须**实现所有需要的特性**并且你**不能错过最后期限**。如果晚了，就不能发货了。或者更糟的是，您将通过手工完成所需的步骤来发货，然后您将遇到一系列全新的问题。

# 从过去向前

两年前，你们发布了旗舰产品 2.5.2 版本，这是 2.x 系列的最后一款产品。

从那时起，你做了一些相当大的重新设计，并发布了 3.0 和 3.1 版本。您的大多数客户已经完成了转换，开发人员很高兴不再需要在古老的代码基础上工作，一切看起来都很好。

但是有人给你发邮件说他们在 2.5.2 版本中发现了一个安全漏洞，无论如何他们都会在下周公布。

您的团队花了 1 天时间讨论和计划解决方案。然后，他们需要 3 天时间来实际实现它，因为他们必须在一个旧的代码库中找到自己的方法，没有人再非常了解它。现在是时候对 2.5.3 版本进行 QA 了。

最好确保你两年前写的脚本仍然有效，否则每个人都将过得很艰难！

好了，恐怖故事讲够了，来说说写 CI 代码的手艺吧。恐怕我也有一些坏消息。

# 你的标准方法行不通

[![my normal approach is useless here](../Images/55d5e8dba6e542aa127f491c04972e73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ngKnJyLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xkcd-normal-approach.jpg)

编写 CI 脚本与编写生产代码或测试代码非常不同。这是一个完全不同的世界。我来详细说明一下。

## 测试很难

为 CI 脚本编写自动化测试是很困难的，而且几乎从来没有发现任何错误(至少，根据我的经验)。检查 CI 脚本是否有效的最佳方法是完整运行它。然后你又一次陷入了缓慢发展的陷阱。

## 调试很难

CI 脚本很难调试，因为它们通常不在开发人员的机器上运行，而是在很少找到调试工具的运行程序上运行。

如果出现最坏的情况(这种情况经常发生)，您唯一的选择将是添加调试日志，这意味着再次陷入缓慢开发的陷阱。

# 消除恐惧

现在你应该开始紧张了。

TDD 帮助我摆脱了对改变产品代码的恐惧，它教会了我一种在一个漂亮的循环中编写测试和产品代码的方法，这通常会产生高质量和很少缺陷的代码。

在这个系列中，我将尝试做同样的事情。我会告诉你如何抵制缓慢开发陷阱，以及如何避免我们讨论过的 bug(以及其他许多 bug)。希望下一次你不得不改变一个 CI 脚本，它会不那么可怕。

下次再见，我们将介绍一些有用的概念。

*感谢您阅读到目前为止:)*

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。

* * *

1.  我不会给你这些事件的证据，因为它们让所有相关的人都很尴尬。 [↩](#fnref1)

2.  顺便说一句，我就是那个忘记取东西的人。 [↩](#fnref2)