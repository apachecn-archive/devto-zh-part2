# Git 如何知道你在工作树中是否有未提交的变更？(第一部分)

> 原文：<https://dev.to/captainsafia/how-does-git-know-if-you-haveuncommitted-changes-in-the-working-tree-part-1-5h4n>

在我最近的一篇博文中，我开始探究`git-status`是如何工作的。我最后走进了一个小兔子洞。事实证明，`git-status`命令与 Git different 以及当前工作树的状态管理方式有交集。我最终搁置了两个具体的问题来回答我的博文。

*   Git 如何执行 diffs，如何管理 diffed 状态？
*   Git 如何管理工作树的状态？

在我的上一篇博文中，我非常完整地回答了第二个问题。在这篇博文中，我想回答第一个问题。所以让我们开始吧！

作为补充，我正在研究的相关代码块在负责检查存储库中当前是否有未提交的变更的代码库中。这个函数看起来像这样。

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

侧边栏:我不知道为什么我说“类似这样的东西。”以上是我一直在阅读的代码库的固定版本的函数的确切定义。你明白我的意思…

我得到的暗示是，这将是我留下的问题多于答案的那些代码阅读中的一个。正如我以前多次说过的，这就是生活。

我想深入研究的第一件事是上面代码片段中使用的`rev_info`结构。那么`rev_info`结构的定义是什么样的呢？好吧，让我告诉你，有很多事要做。[这里](https://github.com/git/git/blob/c6284da4ff4afbde8211efe5d03f3604b1c6b9d6/revision.h#L55)是勇敢者定义的链接。

如果我包含了`rev_info`结构的代码，那就太多了。相反，我将尽力总结上述代码片段中使用的结构部分。

`diffopt`变量代表一个`diff_options`结构。`diff_options`结构，包含与文件间差异生成相关的选项。

`ignore_submodules`标志表示是否应该考虑子模块的变化。

花了一段时间才弄明白`quick`旗做了什么。我不完全确定，但是通过阅读该标志在整个代码库中是如何使用的，我认为它的使用方式与 Unix 中的`--brief`标志为`diff`命令工作的方式相同。总之，它只在文件不同时显示输出。提示我这一点的代码行是这里的。总之，当`quick`标志被设置为真时，`REV_LIST_QUIET`标志被切换。

好的。我希望这有意义。很难表达这些结构代表什么，因为这涉及到读取不同位置的代码块，然后试图拼凑出可能发生的事情。这很像解决一个难题。有些事情不能很好地解释，除非你有解决这个难题的经验。

不管怎样，我想研究的下一件事是`init_revisions`函数。这个名字很好地暗示了这个函数的作用。它用一些默认配置初始化`rev_info`结构。关于 [`init_grep_defaults`](https://github.com/git/git/blob/d0db9edba0050ada6f6eac68061599690d2a4333/revision.c#L1440) 还有一些其他的事情在进行，但是我会在其他的时候进入这个话题。专心点，萨菲娅！

下一个要研究的函数是`add_head_to_pending`函数。这个函数是那种只有几行代码，但是下面有很多东西的函数。

```
void add_head_to_pending(struct rev_info *revs)
{
    struct object_id oid;
    struct object *obj;
    if (get_oid("HEAD", &oid))
        return;
    obj = parse_object(&oid);
    if (!obj)
        return;
    add_pending_object(revs, obj, "HEAD");
} 
```

好吧！所以看起来我们在这里乱搞东西。如果你已经浏览这个博客有一段时间了，我在第一篇博文中对此做了一些探究，在那篇博文中我探索了 Git 目录的内容。

我认为这是一个很好的切入点，可以停下来，提出一些问题来指导未来的探索。我想弄清楚的一些事情是:

*   `init_grep_defaults`是做什么的？
*   从文件系统的角度和数据结构的角度来看，什么是对象？
*   给 HEAD 加个对象是什么意思？
*   为什么`rev_info`结构如此密集？

也就是说，我认为我澄清了什么是 Git 上下文中的`rev_info`是非常好的。就像我之前说的，我知道我会留下更多的问题而不是答案。

下次见！我要去睡觉了…