# 运行“git commit”时会发生什么的完整故事

> 原文：<https://dev.to/captainsafia/a-complete-story-of-what-happens-when-you-run-git-commit-36f8>

因此，在我的上一篇博文中，我研究了 Git 代码库中用于表示提交的数据结构。在这篇博文中，我想更深入地了解一下当您在 Git 存储库中运行`git commit`时会发生什么。

正如我在之前的博文中提到的，大多数关于提交的繁重工作都位于名副其实的 [commit.c](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/commit.c) 源文件中。

我开始研究代码，试图找到`git commit`命令的入口点。我发现，尽管许多用于操作提交的核心逻辑存储在 commit.c 文件中，但是运行`git commit`时执行的许多代码存储在 [builtin/commit.c](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/commit.c) 源文件中。具体来说，`commit`命令的代码位于这些线的[处。它总共有 200 多行代码，所以我决定按照时间顺序(基于它们在函数中被调用的时间)提取从命令行可执行程序到支持库的调用。调用的与提交相关的函数如下。](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/commit.c#L1404-L1632)

1.  `lookup_commit_or_die`
2.  `parse_commit`
3.  `prepare_to_commit`(这实际上没有在`commit.c`中定义，但它很重要，所以我在这里包括它。)
4.  `copy_commit_list`
5.  `commit_list_append`
6.  `commit_list_insert`
7.  `commit_tree_extended`

这个清单很简短，但它讲述的故事大体如下。首先，我们查找当前分支上的现有提交。这个提交将成为我们新提交的父提交。然后，我们通过检查新提交作者的信息和提示用户提交消息来准备提交。然后，通过`commit_tree_extended`函数将提交对象实际写入`.git/objects`目录。如果正在执行的提交是合并提交，则调用`commit_list`相关的函数调用，在这种情况下，提交列表被自动更新，以使合并提交位于提交列表的头部。

记住这一点，我现在知道上面代码中调用的两个最重要的函数是`prepare_to_commit`和`commit_tree_extended`函数。我决定进一步研究这些问题。首先浏览一下`prepare_to_commit`函数是有意义的，所以我将从这里开始。如果你愿意，你可以在[跟随这个函数声明](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/commit.c#L627)。

我开始浏览`prepare_to_commit`函数中的代码。它从核实作者的信息开始。

```
/* This checks and barfs if author is badly specified */
determine_author_info(author_ident); 
```

并运行 Git 存储库中包含的任何预提交挂钩。

```
if (!no_verify && run_commit_hook(use_editor, index_file, "pre-commit", NULL))
    return 0; 
```

接下来的几行，远远不止几行，与请求用户提交消息和创建一个完整的提交消息有关，其中包含提交日期和提交作者。

看到与提交消息相关的代码如此复杂，我真的很惊讶。事实证明，它处理了相当多情况下的代码。例如，您必须根据是进行合并提交还是精选提交来区别对待提交消息。

接下来我想深入研究的是`commit_tree_extended`函数，这里的[定义了](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/commit.c#L1510)。

我通读了为这个函数定义的代码，并意识到它主要负责处理存储在提交消息中的信息，并将其写入提交对象，这是我在后面的几篇博客文章中了解到的。那个物体看起来像这样。

```
$ git cat-file -p 38eea52e57f993d5c594aa7091cc9377b6063f5c
tree 5efb9bc29c482e023e40e0a2b3b7e49cec842034
author Safia Abdalla <safia@safia.rocks> 1520217723 -0600
committer Safia Abdalla <safia@safia.rocks> 1520217723 -0600

Initial commit 
```

所以，总而言之，根据我对刚刚阅读的代码的理解，这就是我在 Git 存储库中运行`git commit`时所看到的情况。

1.  调用`builtin/commit.c`中的`cmd_commit`功能。
2.  `cmd_commit`函数调用`prepare_to_commit`,后者向用户请求提交消息，并准备与提交相关的数据(如作者信息和当前时间)。
3.  用上一步中收集的提交消息数据调用`commit_tree_extended`函数。这些数据被写入缓冲区，然后缓冲区作为提交对象被写入`.git/objects`目录。

这是正在发生的事情的粗略概述。还有更多的细节，主要与处理“边缘情况”有关，比如合并提交。我对 Git 代码库中使用的提交数据结构、持久化到文件系统的提交对象以及从用户那里收集的提交消息信息之间的联系感觉很好。

本质上，每当要进行新的提交时，都会发生以下情况。

1.  提交对象是在内存中创建的。这被表示为一个`commit`结构。
2.  提交结构填充有关于提交时间和提交作者的信息。
3.  从用户处收集提交消息。
4.  来自提交消息的数据和存储在提交结构中的数据被写入存储在文件系统中的提交对象。

和往常一样，根据我从代码中读到的内容，这是我认为正在发生的事情。我可能在某些地方有点不正常。如果是这样，请给我发电子邮件，地址是:safia [at] safia [dot] rocks。

下次见！