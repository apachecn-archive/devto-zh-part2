# 深夜阅读代码并意识到这不是一个好主意

> 原文：<https://dev.to/captainsafia/reading-code-late-at-night-and-realizing-that-its-not-a-good-idea-2p05>

现在是周日晚上 11 点 09 分。在过去的几个小时里，我一直在用 C 语言为我的一门网络课程实现一个路由表。这是我大学生涯的最后一次编程作业，这让我感到某种形式的解脱。

幸运的是，这篇博文不是关于路由表的！我想我应该让自己休息一下，去看看别的东西。

在这篇博客文章中，我将继续我一直在做的关于 Git 内部的系列文章。在过去的几篇帖子中，我一直试图回答这个问题:Git 如何知道您在存储库中有未提交的更改？

我阅读的相关代码如下。

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

在以前的文章中，我研究了`rev_info`结构以及 Git 如何维护关于特定存储库选项的信息。然后我开始深入到看起来像这样的`add_head_to_pending`函数中。

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

上面的函数让我看到了 Git 中使用的对象。我研究了它们在文件系统中是如何表示的。在这篇博客文章中，我想研究对象是如何在 Git 源代码中被表达为数据结构的。从上面的代码中，我可以看到两个相关的 struct 定义值得探究`object_id`和`object`。

从我在上一篇博文中对对象的文件系统表示的深入研究中，我对这两个结构可能表示的内容有了一点概念。我认为`object_id`是与对象相关联的散列的表示，`object`是与对象本身相关联的元数据(如以前的帖子中所定义的)。

但是足够的假设，时间来调查！我在这里找到了`object_id` [的定义](https://github.com/git/git/blob/c6284da4ff4afbde8211efe5d03f3604b1c6b9d6/cache.h#L52-L54)。

```
struct object_id {
    unsigned char hash[GIT_MAX_RAWSZ];
}; 
```

啊哈！正如我所怀疑的,`object_id`结构保存了一个与对象相关联的散列的引用。

好的。接下来我想研究的是`object`结构的定义，我在这里找到了[。](https://github.com/git/git/blob/7fb6aefd2aaffe66e614f7f7b83e5b7ab16d4806/object.h#L53-L58) 

```
struct object {
    unsigned parsed : 1;
    unsigned type : TYPE_BITS;
    unsigned flags : FLAG_BITS;
    struct object_id oid;
}; 
```

`oid`位有意义。每个`object`都应该有一个对其 ID 的引用。我很好奇`parsed`是做什么用的。经过一番挖掘，我[发现了](https://github.com/git/git/blob/e83352ef23cca2701953ed3c915f1db49b255a7d/blob.h)下面的代码注释。

```
/**
 * Blobs do not contain references to other objects and do not have
 * structured data that needs parsing. However, code may use the
 * "parsed" bit in the struct object for a blob to determine whether
 * its content has been found to actually be available, so
 * parse_blob_buffer() is used (by object.c) to flag that the object
 * has been read successfully from the database.
 **/ 
```

好吧！因此，似乎可以使用`parsed`位的一种情况是确定 blob 对象是否已经正确解析了其文件内容。

侧栏:对于那些可能不熟悉 C 的人来说，`unsigned parsed: 1`语法意味着`parsed`是一个由 1 位组成的无符号值。类似地，`unsigned type : TYPE_BITS`意味着`type`是一个由 3 位组成的无符号值。

通过阅读代码注释，我推断出`type`字段表明了对象的类型(树、提交、blob 等)。).要弄清楚`flag`字段有点复杂。它由 27 位组成。从下面的代码注释中，我可以看出这些标志被用于各种用例的代码库的不同部分。

```
/*
 * object flag allocation:
 * revision.h: 0---------10 26
 * fetch-pack.c: 0----5
 * walker.c: 0-2
 * upload-pack.c: 4 11----------------19
 * builtin/blame.c: 12-13
 * bisect.c: 16
 * bundle.c: 16
 * http-push.c: 16-----19
 * commit.c: 16-----19
 * sha1_name.c: 20
 * list-objects-filter.c: 21
 * builtin/fsck.c: 0--3
 * builtin/index-pack.c: 2021
 * builtin/pack-objects.c: 20
 * builtin/reflog.c: 10--12
 * builtin/unpack-objects.c: 2021
 */ 
```

没错。那确实是很多。看起来这个`flags`字段用于从提交到推送再到二分的所有事情。

好吧！所以现在我觉得从文件系统和数据结构的角度来看，我对对象有了一个坚实的认识。是时候回头看看这段代码了。

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

好吧！因此,`get_oid("HEAD", &oid)`获得与头部提交相关联的对象的 ID。这相当于在命令行中做这件事。

```
$ git cat-file -t HEAD
commit
$ git cat-file -p HEAD
tree f9505fb80cdbdb1081735bf9a824c6bc67081447
parent 38eea52e57f993d5c594aa7091cc9377b6063f5c
author Safia Abdalla <safia@safia.rocks> 1520217895 -0600
committer Safia Abdalla <safia@safia.rocks> 1520217895 -0600

Change #1 
```

所以本质上，函数中的前几行获取与当前 HEAD(或最近的提交)相关联的对象。

好了，接下来我想弄清楚的是`add_pending_object`函数在做什么。此时已经快到午夜了，我正在考虑是否真的要深入阅读另一个函数的代码。我确实找到了一份解释这个函数的文档。

```
`add_pending_object`::

    This function can be used if you want to add commit objects as revision information. You can use the `UNINTERESTING` object flag to indicate if you want to include or exclude the given commit (and commits reachable from the given commit) from the revision list. 
```

我明白这句话的意思，但我也不明白。你感觉到我了吗？我想这可能与我已经连续工作了 18 个小时，其中 75%的时间都在读/写 C 代码有关。

我想说的是，我要在这里结束这篇博文。我确实学到了一些有趣的东西。

*   Git 对象由它们的散列唯一地标识。
*   类型(blob、提交、树等。)存储在一个 3 位无符号整数中。
*   每个对象都有一个标志，在整个代码库中用于特定于函数的细节。

老实说，我开始对这种调查失去兴趣了。我想我可能也迷失了自我，偏离了我最初的探究点。我会试着向自己提出一个新的问题，从不同的角度构建我的探索，希望能让我再次对它充满好奇。具体来说，在以后的文章中，我想弄清楚提交是如何进行的。

好了，我要去睡觉了…