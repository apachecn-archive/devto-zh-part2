# 试图弄清楚 git-status 是如何工作的:传奇

> 原文：<https://dev.to/captainsafia/trying-tofigure-out-how-git-status-works-a-saga-72a>

饭桶继续！

我想弄清楚的下一件事是当我执行`git status`时会发生什么。特别是，我很想知道 Git 是如何检测到工作目录的内容发生了变化的。

所以我开始研究代码，看看我能找到哪些与工作目录及其状态相关的代码。我发现[一个头文件](https://github.com/git/git/blob/d0db9edba0050ada6f6eac68061599690d2a4333/wt-status.h)定义了一些与工作树状态功能相关的枚举、结构和函数。

补充说明:作为题外话，我对“工作树”和“工作目录”之间的区别很好奇。许多代码库称它为“工作树”，但我一直称它为“工作目录”，并在过去几年中阅读了其他几个教程，这些教程称它为“工作目录”事实证明，最近在代码库和文档中有一个变化，将术语从“工作目录”转移到了“工作树”我发现一个 [StackOverflow 问题](https://stackoverflow.com/questions/39128500/working-tree-vs-working-directory)提出了一个类似的问题。对于那些没有点击过这个链接的人来说，基本上，它是从“工作目录”变成了“工作树”，以避免人们把“工作目录”和“当前目录”混淆。“工作目录”或“工作树”是包含在[这篇博文](https://dev.to/captainsafia/whats-inside-the-git-directory-28ao)中讨论的`.git`文件夹的目录，而“当前工作目录”可以是该目录中的任何子目录。如果这是令人困惑的，不要担心。基本上，开发人员正在改变用什么词来描述事物，以避免混淆。

好吧！侧边栏结束。回到代码。我之前链接的那个头文件包含一些基本的定义。看看这个。

```
struct wt_status_change_data {
    int worktree_status;
    int index_status;
    int stagemask;
    int mode_head, mode_index, mode_worktree;
    struct object_id oid_head, oid_index;
    int rename_status;
    int rename_score;
    char *rename_source;
    unsigned dirty_submodule : 2;
    unsigned new_submodule_commits : 1;
};

enum wt_status_format {
    STATUS_FORMAT_NONE = 0,
    STATUS_FORMAT_LONG,
    STATUS_FORMAT_SHORT,
    STATUS_FORMAT_PORCELAIN,
    STATUS_FORMAT_PORCELAIN_V2,

    STATUS_FORMAT_UNSPECIFIED
}; 
```

上述结构和枚举存储了目录“状态”的不同方面。在这里，我注意到了可以与来自`git status`命令的输出相联系的东西，例如，`rename_status`和`index_status`。我做的下一件事是找到包含这个`wt-status.h`头文件的所有文件。两个 C 文件需要它。`worktree.c`和`wt-status.c`。在查看了每个文件的 includes 之后，我发现`wt-status`包含了打印目录状态的代码，而`worktree.c`包含了通过检查工作树来检查状态的代码。总而言之，`wt-status.h`是一个头文件，它包含了`worktree.c`和`wt-status.c`所使用的定义。`wt-status.c`依赖于`worktree.c`中为评估工作树定义的功能。

唷！这句话里 c 太多了。

不管怎样，我开始研究 Git 如何表示工作树以及它是如何被操纵的。首先，有一个相关的 [`worktree.h`](https://github.com/git/git/blob/7668cbc60578f99a4c048f8f8f38787930b8147b/worktree.h) 文件，它包含了代表工作树的结构的定义。

```
struct worktree {
    char *path;
    char *id;
    char *head_ref; /* NULL if HEAD is broken or detached */
    char *lock_reason; /* internal use */
    struct object_id head_oid;
    int is_detached;
    int is_bare;
    int is_current;
    int lock_reason_valid;
}; 
```

因此，工作树是一条路径和一些关于它的引用和状态的细节。接下来，我开始研究`wt-status.c`中定义的一些函数，看看它们如何评估工作树状态的不同方面。我好奇的第一件事是弄清楚 Git 如何确定工作树中何时有未提交的变更。事实证明，这是在名副其实的`has_uncommitted_changes`函数中定义的。

```
int has_uncommitted_changes(int ignore_submodules)
{
    struct rev_info rev_info;
    int result;

    if (is_cache_unborn())
        return 0;

    init_revisions(&rev_info, NULL);
    if (ignore_submodules)
        rev_info.diffopt.flags.ignore_submodules = 1;
    rev_info.diffopt.flags.quick = 1;
    add_head_to_pending(&rev_info);
    diff_setup_done(&rev_info.diffopt);
    result = run_diff_index(&rev_info, 1);
    return diff_result_code(&rev_info.diffopt, result);
} 
```

看起来，确定存储库是否有未提交的变更的核心功能是通过在代码基础上运行 diff 来完成的。我已经在心里记下了这些不同的函数。我通读了源文件中定义的一些其他函数，比如`has_unstaged_changes`依赖于使用 diffs 来确定工作目录中是否有变化。

还有另一类与状态相关的函数，这些函数确定是否有正在进行的合并或恢复。

```
struct wt_status_state {
    int merge_in_progress;
    int am_in_progress;
    int am_empty_patch;
    int rebase_in_progress;
    int rebase_interactive_in_progress;
    int cherry_pick_in_progress;
    int bisect_in_progress;
    int revert_in_progress;
    int detached_at;
    char *branch;
    char *onto;
    char *detached_from;
    unsigned char detached_sha1[20];
    unsigned char revert_head_sha1[20];
    unsigned char cherry_pick_head_sha1[20];
}; 
```

这个结构包含几个二进制指示符来指示工作目录的当前状态。我很好奇这个状态是如何在整个文件中更新的。

所以我带着更多的问题离开了这次探索，但这就是生活。我确实对工作树的结构和它的状态是如何确定的有了更多的了解。具体来说，我带着另外两个问题离开这次探索:

*   Git 如何执行 diffs，如何管理 diffed 状态？
*   Git 如何管理工作树的状态？

敬请关注更多内容！

我去健身房…