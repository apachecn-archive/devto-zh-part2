# 精通 git:平分

> 原文：<https://dev.to/orangejellyfish/get-good-with-git-bisect-20l1>

您是否曾经遇到过这样的情况:您需要找出 git 存储库历史上哪个提交导致了行为上的一些变化？这是一种相当常见的调试技术，尤其是在较大、较旧的代码库中。如果您知道在哪里可以找到可能引入变更的代码，那么您可以使用像`git blame`或`git log`这样的命令在历史中搜索违规提交，但通常情况并非如此，即使是这样，通常也有更简单的方法。输入`git bisect`，这是一个有用的 git 命令，它执行历史二分搜索法来识别引入变更的提交。

> 更多的实践方法会让你学得更好吗？
> 
> 如果面对面的学习方法对你或你的团队更有效，orangejellyfish 会举办一个很受欢迎的 git 研讨会,可以远程进行，也可以与你一起现场进行，让你有机会更深入地了解一些更高级的 git 概念。

## 二分搜索法

如果这样的描述足以让你不想继续读下去，不要绝望，跟着这一节去更好地理解“二分搜索法”是什么。如果你已经记下了这个概念，那就继续吧。

“二分搜索法”是一种算法，用于在排序后的值列表中查找目标值。它的工作原理是将目标值与列表的中间元素进行比较。如果它们不匹配，则列表的一半已经被消除(因为列表已经被排序)，并且通过将目标值与新的中间元素进行比较来重复该过程，等等，直到找到目标值。

视觉表现可能会使它更容易理解。这是我们排序后的数字列表或数组。由于这个数组包含数字，我们可以看到它处于排序状态，因为每个元素保存的值都大于前一个元素。假设我们的目标值是 **42** 。

我们首先将目标值与中间元素进行比较。我们可以看到中间的元素**比我们的目标**大:

[![Comparing to the target value](img/cf0419bbfd84ebb7bb4b7d7de50bc975.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oDHThj18--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rfog7e8fdqq3fke30x7j.png)

因为中间的元素大于目标元素，所以我们可以丢弃数组的后半部分，重新进行测试。这次我们可以看到中间的元素比我们的目标小:

[![Discarding half of the array](img/ccd01498c0d53cd19c3d2336e4699ae2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cuSVMpvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/po6mqufjd5ofqozgsck1.png)

因为中间的元素比目标小，所以我们可以丢弃数组的前半部分(记住，我们现在正在处理一个新数组，已经丢弃了原始数组的一半)并再次执行测试。注意，这一次，因为我们在数组中有偶数个元素，所以由算法决定是向上取整还是向下取整来确定中点。在这个例子中，为了简洁起见，我们进行了四舍五入。这一次我们可以看到中间元素**与目标**匹配:

[![Discarding another half of the array](img/b9c1fcb024b68afe600cfed792bceabe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JRrcJEER--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yskmltksq6kpbpru61dx.png)

因为中间的元素与目标匹配，所以二分搜索法是完整的，目标在数组中的位置就是结果。在我们的例子中，目标位于位置 **4** 。但是这些如何应用于 git 呢？

## `git bisect`基础知识

`git bisect`命令允许我们对存储库中的一系列提交执行二分搜索法。试着将一系列提交看作一个数组，其中每个元素都是一个单独的提交。这个数组是按照时间顺序(提交的时间)排序的，而不是按照上面例子中的数字顺序，但是因为它是排序的，所以我们能够对它执行二分搜索法。搜索的目标是引入给定变更的提交。

为了确定提交的相关范围，我们需要知道我们试图寻找的变化还没有发生的起点和已经发生的终点。端点通常是当前分支的`HEAD`。起点可能更难确定，但一个好的策略可能是检查一个旧的但可能稳定的标签，并从那里开始。

让我们假设我们知道在`v1.2.0`标签和现在(主分支的`HEAD`)之间的某个时间，一个 bug 被引入到我们的代码库中。有了这些信息，我们可以开始二等分，并告诉它我们正在处理哪个范围:

```
$ git bisect start          # Start the binary search process
$ git bisect bad            # Identify the end of the range
$ git bisect good v1.2.0    # Identify the start of the range 
```

Git 现在识别提交范围的中间点，检查它并暂停，让您有机会判断您试图定位的更改是否在那个时间点被引入。您可以通过运行自动化测试套件、检查文件，或者运行您的应用程序并手动测试相关功能来实现。Git 通过打印如下内容告诉您发生了什么:

```
Bisecting: 64 revisions left to test after this (roughly 6 steps)
[bf96dbd8768d94e935e0bb874b319ae9b65a9aaf] The mid-point commit message 
```

确定中点是否包含您正在搜索的变化后，您可以告诉`git bisect`，它将通过丢弃一半范围、检查新的中点并再次暂停来继续该过程。让我们假设我们想要找到的变化在这个时间点已经存在，这意味着当前的中点是“坏的”:

```
$ git bisect bad    # Mark the current mid-point as bad, discard later commits
Bisecting: 32 revisions left to test after this (roughly 5 steps)
[e68d1eed5051744ad21bf7391b582939b5c996fc] New mid-point commit message 
```

Git 在新的中点再次暂停，因此我们有机会重复我们的测试。让我们这次假设在这个时间点**没有**引入变更，这意味着这个中间点是“好的”:

```
$ git bisect good    # Mark the current mid-point as good, discard earlier commits
Bisecting: 12 revisions left to test after this (roughly 4 steps)
[1ee2cc616963f57900d3bc2b635d9efbd1cd168d] New mid-point commit message 
```

继续这个过程，在每一步运行您的测试，并将中间点标记为“好”或“坏”,直到 git 能够最终确定引入变更的提交。到那时，它会告诉你:

```
ebd6cd99667703a856b20c87c2307427006fcd2f is the first bad commit
commit ebd6cd99667703a856b20c87c2307427006fcd2f
Author: First Last <first.last@example.com>
Date:   Thu Aug 23 13:58:20 2018 +0000

    This is the commit that introduced the issue 
```

现在剩下的就是通过告诉 git 您已经完成了来返回到正常状态:

```
$ git bisect reset 
```

## 自动化`git bisect`

到目前为止，我们所介绍的是对 git 调试工具箱的强大补充，但是我们可以更进一步，减少一些手工工作。

上一个例子中会降低我们速度的部分是确定每个中点提交是否表现出我们试图定位的行为的测试。如果可以编写测试脚本，git 会非常乐意为我们减轻负担。我们需要的是一个脚本，如果当前提交是“好”的，它会以代码`0`退出，如果是“坏”的，它会以`1`(Unix 总括错误代码)退出。大多数自动化测试运行人员已经这样做了，所以在许多情况下，您将能够使用现有的脚本，而不是编写一个新的脚本。

让我们假设您正试图追踪一个代码库中的 bug，该代码库使用 [Jest](https://jestjs.io/) 作为单元测试运行程序。Jest CLI 工具带有有用的代码，因此我们能够使用它来自动执行 git 二等分:

```
$ git bisect start          # Start the binary search process
$ git bisect bad            # Identify the end of the range
$ git bisect good v1.2.0    # Identify the start of the range
$ git bisect run jest       # Run unit tests against each mid-point 
```

您可以在测试脚本中使用一些额外的技巧。例如，用退出代码`125`退出脚本将告诉`git bisect`提交不能被测试，应该被跳过。在这种情况下，命令将为您选择另一个提交并自动继续。

## 接下来呢？

此时，您应该已经对`git bisect`有了深入的了解，并且能够手动或自动地使用它来发现由未知提交引起的问题。如果你想更进一步，一个好的起点是[官方 git 文档](https://git-scm.com/docs/git-bisect)。

如果更实际的方法对你或你的团队更有效， [orangejellyfish](https://www.orangejellyfish.com/) 举办一个受欢迎的 [git 研讨会](https://www.orangejellyfish.com/workshops/get-good-with-git/)，它可以远程或现场与你一起进行，让你有机会更深入地了解一些更高级的 git 概念。