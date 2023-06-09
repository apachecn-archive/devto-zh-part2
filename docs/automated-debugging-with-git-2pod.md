# 使用 git 进行自动化调试

> 原文：<https://dev.to/svettwer/automated-debugging-with-git-2pod>

[![](img/1798b9cc47ddaf12392db21fa16376f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m2w46_Ll--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0zfijln81q2uf624mlz9.png)

想象你正在一个复杂的软件中开发一个更大的特性。您的实现已经完成，范围内的所有测试都变成了绿色，您可以将您的更改进行集成测试了。然后，来自一个完全不同的模块的一些集成测试失败了，并且您不知道是什么变化导致了这种情况。现在你开始分析问题。手动探查您的提交肯定会导致一个非常乏味的过程。谢天谢地，git 可以帮你做所有的工作，而你却可以享受一杯咖啡。

高级命令`git bisect`允许您自动运行指定的测试过程，同时它在您的提交历史中爬行以找到错误的修订。

## 要求

要进行动手操作，需要在本地机器上进行以下设置:

*   饭桶
*   Java 8
*   专家
*   bash(Windows 上的 Cygwin)

## 什么是 git 等分？

对[git-等分文档](https://git-scm.com/docs/git-bisect)的官方描述是:
*使用二分搜索法找到引入 bug 的提交。*

这意味着什么呢？
[![](img/7062cbb4113ee8568ddf0850663d4f4b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--oe7N02g5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r6x5ao18ihip8mhzv9kx.png)

假设您有一堆提交，并且您知道，在某些时候，您的软件是没问题的。现在，你的软件坏了，这意味着，你在开发过程中的某个地方引入了一个错误。`git bisect`所做的是，它通过测试由二分搜索法选择的特定提交，将修订图分为*好的*部分和*坏的*部分。基于测试的结果，git 向中断的提交导航。经过几次迭代后，git 将能够识别引入问题的修订。

### 基本 git 平分工作流程

`git bisect`的工作流程包括两个主要步骤。首先，你必须指定一个修订范围，由一个*好的*和一个*坏的*修订限定。这些修订可以作为修订哈希、标签或任何其他 git 修订选择器来引用。第二步，平分过程开始，git 自动执行第一个修订的检验以进行测试。将测试结果传回 git 后，如果成功，执行`git bisect good`,如果失败，执行`git bisect bad`,将选择下一个版本。将重复第二步，直到找到第一个损坏的修订。
[![](img/7214a7cb07866fac35fcb7d0d5a9367a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FWGnMM4l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o4ts34nkm5blcgg2fbc6.png)

## 手动 git 等分

现在你已经了解了这个想法和工作流程，让我们继续动手操作。首先，从包含示例项目的 GitHub 中签出[存储库。如您所见，存储库包含三个文件夹:](https://github.com/svettwer/git-bisect-examples)

*   cake-factory:一个样例 spring boot web 服务，包含一些需要发现的问题
*   简单-平分:如何手动使用 git-平分 *<的参考-这是我们现在要做的*
*   自动对分:一个如何使用自动对分的参考，包括一个样本脚本

现在让我们通过执行
来尝试构建蛋糕工厂

```
mvn -f ./cake-factory/pom.xml clean install 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/0bd9de30e5b22f8051d5b98ac2e7e2f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Msl3rkKA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ya206dpbq4jxcry18n64.png) 
你会认出来，那次打造并不成功，是因为一次失败的[柑橘](http://citrusframework.org/)整合测试。现在，让我们找出，我们在哪里引入了这个 bug。

如工作流所示，我们必须首先设置平分场景。因此，我们必须找到一个*好的*和一个*坏的*版本来定义分析的边界。这两个版本都很容易找到。*好的*版本很可能是你分支的版本。坏的版本是 HEAD，因为你的测试现在失败了。然而，在我们的例子中，我们将使用两个来自存储库的准备好的标签 *stable* 和 *broken* 。

```
git bisect start # start the bisect procedure
git bisect bad broken # label the *bad* commit
git bisect good stable # label the *good* commit 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你想使用更短的版本:

```
git bisect start broken stable # start the procedure and label the *good* and *bad* commit 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的二等分已经开始，git 已经选择了一个 commit 来测试，并提供了 commit 消息以及对所需迭代的估计。
[![](img/a7b431090b69f6c1787ad9924b5334ae.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--iECy_HYK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4om9i3rtdqhsti0r1d3h.png)

到目前为止，您应该已经想出了一个测试策略来确定问题的根本原因。在我们的例子中，集成测试失败了。因此，我们将调试工作集中在集成测试上。通过设置属性`skip.unit.test=true`，cake-factory 的设置允许我们跳过单元测试，直接开始集成测试。

```
mvn clean verify -f ./cake-factory/pom.xml -Dskip.unit.tests=true 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/10cdc5bb0ef29aaaafd20daa3b1ca26a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BNaDw5xJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4hwha3x7hh5jtl6lloxe.png) 
在这个版本中，一切正常，构建成功。现在我们必须向 git
报告这个结果

```
git bisect good # tells git that the current revision is okay
#If the test would have been failed, you would have used 'git bisect bad' 
```

Enter fullscreen mode Exit fullscreen mode

根据我们的报告，git 选择了下一个版本进行检查。
[![](img/ddb06be59cbe116df70dbb85e8669ff0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--3upKOohN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vp3wwglhk2a6n9vx9gop.png)

现在你必须重复测试和报告程序。经过一些迭代后，您会发现下面的提交导致了问题。
[![](img/c40164067b69449f8da521a40af6b458.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2nKsZ11c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u668g59o1arvqhsphicn.png) 
记下中断提交的修订散列，不要忘记关闭您的等分会话。

```
git bisect reset 
```

Enter fullscreen mode Exit fullscreen mode

如果您查看该版本的代码，您会发现由 cake 服务交付的默认 cake 已经被更改为提交消息中所述的 *vanilla* ，但是集成测试还没有被修改。
T3![](img/99b460e1c039d5e12525d09664696917.png)T5】

## 自动 git 二等分

正如您可能已经认识到的，手动执行二等分步骤会产生一些开销，并且是一项重复的任务。如果您在涉及多个模块的更复杂的项目中工作，这种情况会增加。幸运的是`git bisect`包含一个子命令，允许我们执行一个特定的命令或者一个脚本化的测试程序，并自动报告结果。这不仅减少了所需的时间，还减少了手工重复这些步骤时出错的可能性。

```
git bisect run <command to execute> 
```

Enter fullscreen mode Exit fullscreen mode

为了向 git 报告测试的结果，您的命令或脚本必须满足以下约定:
*如果测试成功，执行的命令将提交返回代码 0，否则将返回除 0 之外的返回代码。*
由于你只要履行这个契约，你还可以用 python，甚至 java 做一些更高级的测试逻辑。尽管如此，我还是建议实用一点，保持测试简单。

### 举例

让我们回到实践中来。如果在上一节中还没有完成，请从包含示例项目的 GitHub 中签出[存储库。在“自动二分”文件夹中，您会发现一个 *findBug.sh* bash 脚本，它包含了来自“手动 git 二分”部分的测试过程代码。](https://github.com/FMAOuroboros/git-bisect-examples) 

```
#!/bin/bash

mvn clean verify -f ./cake-factory/pom.xml -Dskip.unit.tests=true 
```

Enter fullscreen mode Exit fullscreen mode

现在要执行自动化测试，只需准备一个二分环境并将脚本传递给`git bisect run`。

```
git bisect start broken stable
git bisect run sh automated-bisect/findBug.sh 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您有一个单行命令，如 *findBug.sh* 中所示，您也可以将该命令直接传递给`git bisect run`，只要传递的命令符合约定。

```
git bisect start broken stable
git bisect run mvn clean verify -f ./cake-factory/pom.xml -Dskip.unit.tests=true 
```

Enter fullscreen mode Exit fullscreen mode

当这个过程完成后，git 将向您显示第一个错误的提交，这是从手动部分得知的。
[![](img/b2e6aa57cc29e9b432320220d8981c25.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--M_iN_p9o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/brb23h73q7ogaojkpecf.png)

## 提示和窍门

### 关于 git 二等分和 TDD

如果你遵循 TDD 生命周期来开发你的软件，你可能会在使用 git-bisection 时遇到一些麻烦，因为从软件测试的角度来看，你的软件会在错误状态和功能状态之间交替。一旦你指定了一个新的测试，你的软件就会变得不稳定。如果您在这种情况下执行一个简单的`mvn clean install`，那么`git bisect`将不会找到包含问题根本原因的修订，因为它被您的 TDD 测试导致的失败构建所混淆。这也可以用我们的*蛋糕工厂*的例子来说明，从 maven 指令中去掉`skip.unit.tests=true`。

```
git bisect start broken stable
git bisect run mvn clean verify -f ./cake-factory/pom.xml 
```

Enter fullscreen mode Exit fullscreen mode

现在`git bisect`将对每一个失败的单元测试做出反应。这也导致了一个结果，但它与我们要寻找的问题的根本原因没有任何关系。
[![](img/1ced2473829f5369c98ec64802325127.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qzo5Jl_L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eem9nba39j6q64xlorp4.png)

### 明智地设置您的测试场景

正如您在“关于 git 二等分和 TDD”一节中所看到的，一个不够严格的测试场景会返回误导性的结果。因此，创建适合您的情况的测试场景非常重要。这里有一些要点可以帮助你避免陷阱。

*   *保持你的测试规模小*只测试必要的和与问题直接相关的内容。这将导致正确的结果，并减少测试运行的时间。
*   *了解你的依赖关系*如果失败的测试依赖于一个不同的模块，这个模块在开发过程中已经改变了，请记住，在开始测试之前，你必须在每次迭代中重建依赖关系。
*   自动化你的测试如果你不止一次地做一件事，那就自动化它们！不要手动执行测试。这是一项耗费大量时间的重复性工作。编写您要测试的脚本，让`git bisect run`完成剩下的工作。
*   *使用常见的“坏”和“好”候选人*不要浪费时间去思考你的分析的最有效边界。因为`git bisect run`将为您完成这项工作，一次迭代或多或少根本不重要。
    *   *不好的*人选:多半是*的头头*
    *   *好的*候选人:
    *   您从中分支的修订版
    *   您的软件的最新发布版本
    *   成功构建的分支的最后一个版本。(例如，昨晚)

## 总结

正如您所看到的，git 二等分是一个强大的工具，可以在您的 git 历史中找到中断的提交。您可以定义自动检查的修订范围。如果您想了解更多关于 *git 等分*的信息，请访问以下链接:

*   [git-平分文档](https://git-scm.com/docs/git-bisect)
*   [用 git 调试](https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git)

本文原载于 2018-01-12[labs . consol . de](https://labs.consol.de/development/git/2018/01/12/automated-debugging-with-git.html)