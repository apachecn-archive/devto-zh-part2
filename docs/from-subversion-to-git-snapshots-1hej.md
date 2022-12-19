# 从 Subversion 到 Git:快照

> 原文：<https://dev.to/ronalterde/from-subversion-to-git-snapshots-1hej>

我们谈论 Git 存储库的快照，而在 Subversion 中我们认为是文件变更，这意味着什么？至少对我来说，理解 Git 的关键是每次提交实际上都是整个项目的快照。不是补丁列表。与之前的提交没有区别。只是整个事情的快照。

他们说，Git 会给所有东西拍快照。来自颠覆，这很难相信。如果一个版本控制系统一次又一次地存储整个项目状态，并且每次提交，它将如何扩展？

首先，让我们做一个小实验，看看在不考虑 Git 和 Subversion 的情况下，如何直观地进行版本控制。

## 穷人版控

假设我们有一个名为`myapp`的项目存储在同名目录中。它包含的只是一个 main.c 文件:

```
myapp
  main.c 
```

Enter fullscreen mode Exit fullscreen mode

如果没有版本控制，您将如何跟踪变更，以便能够在以后恢复特定的状态？你可能会说，这很简单:只需创建整个`myapp`目录的副本，并将其命名为类似于`myapp-<version>`的名称。过一会儿，你会得到一堆*备份*目录:

```
myapp-01
  main.c
myapp-<...>
  main.c
myapp-<N>
  main.c 
```

Enter fullscreen mode Exit fullscreen mode

要回到历史上的前一个状态，您可以用之前创建的这些*快照*中的一个来替换整个`myapp`目录。

为了避免保存如此多的冗余信息而浪费空间，您可能会考虑将所有内容放入 gzipp 压缩文件中，并删除所有备份目录:

```
tar -cvzf myapp.tgz myapp-*
rm -r myapp-* 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，这种幼稚的方法与 Git 的实际工作方式并不完全不同。

## Git 是怎么做到的

每次创建提交时，Git 都会获取每个添加或修改的文件的内容，对其进行压缩，并将其存储在内部的*对象数据库*中，同时存储的还有保存一些元信息 <sup id="fnref1">[1](#fn1)</sup> 的*提交*对象。这种方法很容易推理，因为它并不比我们在上面提到的简单尝试中所做的更困难。

您可能会意识到一个很大的缺点:尽管单个文件的内容被压缩了——这很好，但是在提交之间，即使很小的变化*也会导致对象数据库中的大量重复。*

在 Git 中，这个可伸缩性问题在第一阶段被简单地忽略，并在以后得到解决。在一个叫做*打包*的过程中，所有的对象都被 delta 压缩并移动到一个或多个*打包文件*中。这是在几个场合下进行的；您可以使用`git gc --aggressive`来执行它。

下图显示了将压缩文件内容存储到 blob 对象以及生成 packfile 的简化说明。

[![objects](../Images/b74e81ac0b5c3c75475ff773c7c44363.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tLyNAWPE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://steffen.ronalter.de/assets/objects.png)

## 结论

即使对于日常的 Git 使用，理解一点它的内部工作方式也是至关重要的；很高兴看到基本的想法本质上并不复杂，但或多或少与我们可能提出的想法相同。

这种理解让我们有能力掌握所有更高级的特性，如分支、合并和重定基础。

这篇文章最初发表于 [steffen.ronalter.de](http://steffen.ronalter.de)

## 参考文献

*   [Git Packfiles](https://git-scm.com/book/en/v2/Git-Internals-Packfiles)
*   [git 重新打包](https://git-scm.com/docs/git-repack)

* * *

1.  有关所有细节，请参考优秀的 Pro Git 书籍的章节 [Git 对象](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)。 [↩](#fnref1)