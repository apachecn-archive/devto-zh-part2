# Git 如何存储工作树状态？

> 原文：<https://dev.to/captainsafia/how-does-git-store-working-tree-state-5a5l>

在[我的上一篇博文](https://dev.to/captainsafia/trying-tofigure-out-how-git-status-works-a-saga-72a)中，我开始探究`git-status`是如何工作的。我最后走进了一个小兔子洞。事实证明，`git-status`命令与 Git different 以及当前工作树的状态管理方式有交集。我最终搁置了两个具体的问题来回答我的博文。

*   Git 如何执行 diffs，如何管理 diffed 状态？
*   Git 如何管理工作树的状态？

在今天的博文中，我想尝试更彻底地回答第二个问题。我已经想到 Git 使用一个`wt_status_state`结构(C 中的一个数据结构)来存储关于工作树是否处于合并、恢复或其他事情的信息。更具体地说，下面是结构中定义的不同字段。

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

整洁，对不对？所以我研究了代码，试图找出 wt_status_state 对象在代码库中的位置。这个逻辑大部分存储在 [`wt_status_get_state`](https://github.com/git/git/blob/d0db9edba0050ada6f6eac68061599690d2a4333/wt-status.c#L1541) 函数中。下面是该函数的标题。

```
void wt_status_get_state(struct wt_status_state *state,
             int get_detached_from) 
```

像大多数 C 函数一样，您给它一个指向一个对象的指针，它应该用信息(`*state`)和一些附加信息填充这个对象。那么如何填充`wt_status_state`结构呢？函数中第一行有意义的代码就是这样做的。

```
if (!stat(git_path_merge_head(), &st)) {
    state->merge_in_progress = 1;
} 
```

有意思！所以`stat`函数是一个返回文件状态信息的函数。你可以在这里了解更多关于[的信息。因此，无论`git_path_merge_head`函数返回什么，它都必须是某种文件路径。我决定看看这个函数是在哪里以及如何定义的。事实证明，该函数没有明确定义。相反，该函数是使用宏定义调用的，如下所示。](http://man7.org/linux/man-pages/man2/stat.2.html) 

```
#define GIT_PATH_FUNC(func, filename) \
    const char *func(void) \
    { \
        static char *ret; \
        if (!ret) \
            ret = git_pathdup(filename); \
        return ret; \
    } 
```

所以，基本上，每当我们调用`git_path_merge_head`，真正被调用的是下面这段代码。

```
const char *git_path_merge_head(void) {
    static char *ret;
    if (!ret)
        ret = git_pathdup("MERGE_HEAD");
       return ret;
} 
```

在这种情况下，`git_pathdup`函数返回我们所在的存储库的`.git`目录的位置(类似于`~/dev/my-git-repo/.git`)，并与定义的文件名连接在一起(因此在示例路径上调用上面的函数的结果将是`~/dev/my-git-repo/.git/MERGE_HEAD`)。

好吧！现在我们知道了`git_path_merge_head`函数的作用，我们可以回过头来看看`wt_status_get_state`中的代码在做什么。

```
if (!stat(git_path_merge_head(), &st)) {
    state->merge_in_progress = 1;
} 
```

因此，`stat`如果找不到位于由`git_path_merge_head`返回的路径中的文件，则返回 false-y 值。本质上，这段代码检查在`.git`目录中是否存在一个`MERGE_HEAD`文件，如果存在，则将`merge_in_progress`的状态设置为真值。

检查用户当前是否正在挑选工作树的代码是相同的。

```
else if (!stat(git_path_cherry_pick_head(), &st) &&
         !get_oid("CHERRY_PICK_HEAD", &oid)) {
    state->cherry_pick_in_progress = 1;
    hashcpy(state->cherry_pick_head_sha1, oid.hash);
} 
```

用于检查工作树当前是否被还原的代码也是如此。

```
if (!stat(git_path_revert_head(), &st) &&
    !get_oid("REVERT_HEAD", &oid)) {
    state->revert_in_progress = 1;
    hashcpy(state->revert_head_sha1, oid.hash);
} 
```

啊哈！因此，在底层，工作树的状态实际上是通过一组特殊命名的文件存储在文件系统中的`.git`目录中。不过，有一种情况不同。

```
} else if (wt_status_check_rebase(NULL, state)) {
    ; /* all set */
} 
```

嗯。为什么 rebase 中间的工作树检查看起来像这样？事实证明，在 [`wt_status_check_rebase`](https://github.com/git/git/blob/d0db9edba0050ada6f6eac68061599690d2a4333/wt-status.c#L1501) 函数的定义中，Git 检查工作目录中是否存在某些与 rebase 相关的文件(`rebase-apply`)。因此，尽管检查重定基础是否正在进行的函数调用看起来不同，但实际上，它做的是同样的事情。它检查工作树中是否存在反映存储库状态的特定文件。

嗯！我很高兴我得到了答案。老实说，我以前在我的 Git 存储库中见过这些`MERGE_HEAD`和`REVERT_HEAD`文件。我认为 Git 使用它们来存储一些关于合并状态的信息，但是从一个更具体的角度来看这些文件的使用方式也很酷。

好吧。下次再见！