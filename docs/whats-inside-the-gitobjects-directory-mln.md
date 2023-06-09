# ` . git/objects `目录中有什么？

> 原文：<https://dev.to/captainsafia/whats-inside-the-gitobjects-directory-mln>

好吧！所以我继续这个 Git 系列。在这一点上，我可能应该为它做一个标签或什么的，但是你可以通过浏览我的档案看到与这个主题相关的其他帖子。

在我的上一篇博客文章中，我开始研究 Git 如何决定你是否在工作树中有未提交的变更。代码阅读并没有最终回答大量的问题，但它最终提出了更集中、更精炼的问题，太棒了！我带着以下问题离开了上一篇帖子。

*   `init_grep_defaults`是做什么的？
*   从文件系统的角度和数据结构的角度来看，什么是对象？
*   给 HEAD 加个对象是什么意思？
*   为什么`rev_info`结构如此密集？

所以，我想开始回答这篇博文中的第二个问题。如果你想知道这个问题的背景，你可以回头看看上面链接的最后一篇文章。

不管怎样，继续这篇文章的主题:对象。

所以在[我之前的一篇博文](https://dev.to/captainsafia/whats-inside-the-git-directory-28ao)(关于`.git`目录的那篇)中，我提到了对象，作为与 Git 相关的文件系统工件。我想深入探讨一下这个问题，以及它在 Git 代码库中是如何使用的。因此，从文件系统的角度来看，对象存储在`.git`目录中的以下位置。

```
$ ls .git/objects/
38 5e 63 e6 f9 info pack 
```

当我第一次开始探索这个目录时，我对这个目录中的文件夹和对象与项目中的提交散列之间的关联非常感兴趣。所以，如果我们看看这个项目的提交。

```
$ git log
commit 63916e1e1856e84d1f26dc9e0c26ec5b2344b991 (HEAD -> new-thing)
Author: Safia Abdalla <safia@safia.rocks>
Date: Sun Mar 4 20:44:55 2018 -0600

    Change #1

commit 38eea52e57f993d5c594aa7091cc9377b6063f5c
Author: Safia Abdalla <safia@safia.rocks>
Date: Sun Mar 4 20:42:03 2018 -0600

    Initial commit 
```

您会注意到 objects 目录中的一些文件夹对应于每个日志的提交散列的前两位数字。好的。目前所有这些想法都有点不正式。是时候对此更加严格了。

今天，我要做一些我在代码阅读中不常做的事情。在读代码之前，我要先读文档。这主要是因为我想读一些能给我知识来构建未来探索的东西。这让博客读起来没什么意思，但这就是生活。

所以，我去了我能找到的关于 Git 对象的最权威的指南，来自官方 Git 书籍的[文档](https://git-scm.com/book/id/v2/Git-Internals-Git-Objects)。你可以现在就去读，也可以读我下面做的这个方便的总结。

*   整个对象目录是基于文件系统的键值存储的表示。关键是散列(最终形成目录的东西)，根据对象的类型，下面的要点描述了内容。
*   对象可以与提交、blob、标记或树相关联。
*   提交对象包含您可能期望与提交相关联的所有信息(散列、作者、时间戳)。
*   blob 对象包含文件内容在某一点的散列。
*   标签对象通常包含与提交对象相同的信息(散列、作者等)。)因为标签通常是对特定提交的引用。
*   树是存储对 blobs 的引用的对象。附录:我刚刚了解到它们也可以存储树的引用。
*   有一组 git 命令可以用来读取`.git/objects`目录中的对象，比如`git cat-file`和`git hash-object`。

所以我决定尝试这些命令来弄清楚这里发生了什么。我可以使用`git cat-file`命令来获取与我在上面的初始提交中所做的更改相关联的文件的 blob 吗？

```
$ git cat-file -p 38eea52e57f993d5c594aa7091cc9377b6063f5c
tree 5efb9bc29c482e023e40e0a2b3b7e49cec842034
author Safia Abdalla <safia@safia.rocks> 1520217723 -0600
committer Safia Abdalla <safia@safia.rocks> 1520217723 -0600

Initial commit 
```

太棒了。因此，该特定提交的内容包含了我期望看到的信息。它是提交者、时间戳和提交消息。从上面可以看出，似乎有一个树对象与这个提交对象相关联。

```
$ git cat-file -p 5efb9bc29c482e023e40e0a2b3b7e49cec842034
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 test.txt 
```

好吧！因此，我刚刚读取了与单次提交的提交对象和树对象相关联的数据。但是我如何获得上面输出中引用的实际 blob 呢？

```
$ git cat-file -p e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 
```

嗯。没什么。这不是我所期待的。我结束了这一段时间的折腾，直到我意识到一些完全愚蠢的事情。在我最初的提交中，我提交了一个空文件。所以，当然，博客的内容是空的。

要真正看到这一切，我需要从上面的提交日志中调查带有消息“Change #1”的提交。

```
$ git cat-file -p 63916e1e1856e84d1f26dc9e0c26ec5b2344b991
tree f9505fb80cdbdb1081735bf9a824c6bc67081447
parent 38eea52e57f993d5c594aa7091cc9377b6063f5c
author Safia Abdalla <safia@safia.rocks> 1520217895 -0600
committer Safia Abdalla <safia@safia.rocks> 1520217895 -0600

Change #1
$ git cat-file -p f9505fb80cdbdb1081735bf9a824c6bc67081447
100644 blob 637d210528e696380c88c3beae2a695f574c3c74 test.txt
$ git cat-file -p 637d210528e696380c88c3beae2a695f574c3c74
Some content. 
```

这还差不多！很抱歉怀疑你，伟大的电脑！我只是一个愚蠢的人类…

好吧！所以这很好。能够查看`.git/directory`中的不同对象并跟踪提交的不同方面是如何表示的，这有点酷。

我觉得我应该在这里结束这篇博文。我想我已经从文件系统的角度对 Git 对象的用途有了一个非常明确的想法，并且已经准备好在将来的某篇博客文章中从代码库/数据结构的角度来研究它们。

回头见！