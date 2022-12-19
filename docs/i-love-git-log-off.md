# I love git log

> 原文：<https://dev.to/drews256/i-love-git-log-off>

#### 这个帖子的标题应该是 IFLG(像 IFLS，但丫知道用 Git)。

但是用 f***这个词来命名一篇文章可能不太好。你知道的。

## Git 最好

真的，我爱 git。我认为它非常有用，如果使用得当，它可以极大地加快团队的发展。当我第一次学习 git 时，我总是因为不能*看到*git 发生了什么而感到沮丧，比如我的队友在做什么，我的工作如何与他们的工作相适应？

我尝试了几个使用 git 的 UI，但是从来都不满意，所有的东西看起来都很笨拙，你必须离开终端去看 UI，然后再回来弄清楚你在做什么。

当我使用 git repo 时，我最喜欢的命令之一是`git log`。

这显示了所有提交的日志，基本命令输出如下内容:

```
commit 8c2ca7c38f50e1d837485edd946916bfdciii20a1 (HEAD -> master, origin/master)
Author: Andrew Stuntz <andrewbstuntz@gmail.com>
Date:   Mon Aug 20 10:20:27 2018 -0700

    Fixes the resetting of form fields on the settings form

commit cc309a7cb45bfcfd7e94a49387086266dff54c05
Author: Andrew Stuntz <andrewbstuntz@gmail.com>
Date:   Mon Aug 20 10:03:46 2018 -0700

    Updates some styling issues and copy

commit 2d848cec12038475b9f12070547653ab212e4e6
Author: Andrew Stuntz <andrewbstuntz@gmail.com>
Date:   Mon Aug 20 08:17:13 2018 -0700

    Add config and format correctly

commit deec64859bc9b2ef74a987437b7864f2183940e
Author: Andrew Stuntz <andrewbstuntz@gmail.com>
Date:   Thu Aug 16 11:11:42 2018 -0700

    First commit, I never know what to put here? 
```

那么我们在这里做什么呢？

```
commit 8c2ca7c38f50e1d837485edd946916bfdciii20a1 (HEAD -> master, origin/master)
Author: Andrew Stuntz <andrewbstuntz@gmail.com>
Date:   Mon Aug 20 10:20:27 2018 -0700

    Fixes the resetting of form fields on the settings form 
```

总的来说，很容易看出提交的顺序，从上到下，从第一个提交到最后一个提交。您将获得唯一标识单个版本的版本号。接下来，您会在括号中看到关于您的分支的更多信息，这些信息引用了您的`HEAD`(这是您现在已经检出的)。你也是`origin/master`，这意味着`origin/master`也在这个提交中。所以你的`HEAD`和`origin/master`是同一个版本。然后你得到一个作者和一个日期。最后，您会收到提交消息。到目前为止一切顺利。没什么太复杂的。

## 潜入`git log`命令

如果你做了一个`git log --help`你会得到大量的输出，事实上我喜欢 git 的一个原因是大量的文档让你可以真正地深入和定制你想要的东西。我将介绍几个我最喜欢的选项，让您真正了解存储库中的情况。

第一个是`git log --graph`

它可以让你看到分支的图形。

```
* commit 8c2ca7c38f50e1d837485edd946916bfdciii20a1 (HEAD -> master, origin/master)
| Author: Andrew Stuntz <andrewbstuntz@gmail.com>
| Date:   Mon Aug 20 10:20:27 2018 -0700
|
|     Fix the resetting of form fields on the settings form
|
* commit cc309a7cb45bfcfd7e94a49387086266dff54c05
| Author: Andrew Stuntz <andrewbstuntz@gmail.com>
| Date:   Mon Aug 20 10:03:46 2018 -0700
|
|     Update some styling issues and copy
|
*   commit a9b263cacd3240f6d00e7ce401fc6f6c23988cee
|\  Merge: 2d848cec deec4187
| | Author: Andrew Stuntz <andrewbstuntz@gmail.com>
| | Date:   Mon Aug 20 08:19:44 2018 -0700
| |
| |     Merge branch 'master' of some/private/repo
| |
| *   commit deec398745bc9b2ef74a418287b7864f2183940e 
```

我们已经开始变得更加直观了，你可以很容易地看到从一个分支到主分支的提交。您还可以在底部看到在合并到主分支之前对该分支所做的提交。

下一个我很喜欢的命令是`git log --oneline`

这将获取输出并使每个提交成为一行，因此它极大地增加了您在任何给定时间可以看到的提交数量。

```
8c2ca7c3 (HEAD -> master, origin/master) Fix the resetting of form fields on the settings form
cc309a7c Update some styling issues and copy
a9b263ca Merge branch 'master' of https://github.com/drews256/some/private/repo
2d848cec Add config and format correctly
deec4187 Merge pull request #1303940 from drews256/some-other-branch 
```

现在我们有了相同数量的信息，但是简洁地显示在 5 行中。

你甚至可以把两者结合起来，得到`git log --oneline --graph`。

```
* 8c2ca7c3 (HEAD -> master, origin/master) Fix the resetting of form fields on the settings form
* cc309a7c Update some styling issues and copy
*   a9b263ca Merge branch 'master' of https://github.com/drews256/some/private/repo
|\
| *   deec4187 Merge pull request #1303940 from drews256/some-other-branch 
```

你得到了两个世界的最好的东西，你看到了图形和分支加上提交，现在它简洁地显示出来了。

如果你已经为你的 git repos 打开了`color`,这将以全彩色显示，每个分支都有一个独特的颜色，这使得它非常容易跟随。如果不是这样，你想给你的输出添加一些颜色，你可以运行`git log --oneline --graph --color`,你会看到每个分支都有很好的颜色供你探索。

现在你可以很容易地看到你的分支是什么和在哪里，并确保你没有在你不应该在的地方工作。

你最喜欢 git 的哪些部分？

感谢 [atlassian](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) 的封面照片。