# 了解更多关于 Git 中如何表示提交的信息

> 原文：<https://dev.to/captainsafia/learning-more-about-how-commits-are-represented-in-git-3dp1>

所以在我的[上一篇阅读代码的博客文章](https://dev.to/captainsafia/reading-code-late-at-night-and-realizing-that-its-not-a-good-idea-2p05)中，我决定重新整理我对 Git 代码库的探索，并试图弄清楚提交是如何进行的。本质上，当我键入`git commit`时，我想知道在引擎盖下发生了什么。

侧栏:我觉得我应该开始把这一系列的帖子叫做“代码阅读彩虹”是的，我正在努力。

好了，回到这篇博文的要点。

我查看了 Git 代码库，发现很多与提交相关的有趣代码位于 [this C 头文件](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/commit.h)和 [this C 源文件](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/commit.c)中。在阅读 Git 代码库时，我喜欢做的第一件事就是探索一些与特定代码相关的数据结构。事实上，当我浏览代码库并挑选出上面链接的头文件中定义的所有`struct`时，我想我可能会用整篇博文来研究这个问题。

我从最重要的结构开始，即`commit`结构。它的定义是这样的。

```
struct commit {

struct object object;

void *util;

unsigned int index;

timestamp_t date;

struct commit_list *parents;

struct tree *tree;

}; 
```

好吧！因此，根据我自己的知识和以前读过的代码，我可以很容易地从中挑选出一些东西。引用的`object`结构是与这个特定提交相关联的对象。我了解了 Git 对象，并在这里和这里写了博客。日期(显然)是某个特定提交的时间。

我不得不进一步挖掘这个结构中的其他属性。最容易解析的似乎是`parents`,因为它引用了同一个头文件中定义的另一个结构。

```
struct commit_list {

struct commit *item;

struct commit_list *next;

}; 
```

好吧！这很容易理解。`parents`属性是在刚刚创建的提交之前进行的所有提交的类似链表的数据结构。

下一个引起我兴趣的东西是`void *util`指针。对于那些不熟悉 C 的人来说，`void*`代表了一个空指针的定义。它本质上是一个指针，可以指向任何类型的对象。在这种情况下，`util`可以是`int`或`short`或`char`。这个属性的不确定性让我很好奇。这个`util`指针可能是什么？我决定跳到源文件，看看在哪里使用了`util`指针，以确定我是否能弄清楚它的用途。

最具启发性的代码片段如下。

```
void set_merge_remote_desc(struct commit *commit,

const char *name, struct object *obj)

{

struct merge_remote_desc *desc;

FLEX_ALLOC_STR(desc, name, name);

desc->obj = obj;

commit->util = desc;

} 
```

因此，`util`指针看起来是一个指向`merge_remote_desc`结构的指针。`merge_remote_desc`结构定义如下。

```
struct merge_remote_desc {

struct object *obj; /* the named object, could be a tag */

char name[FLEX_ARRAY];

}; 
```

看起来,`merge_remote_desc`对象包含了对一个 Git 对象的引用，该对象有一个与之相关联的名称。正如在以前的博客文章中提到的，Git 对象可以是提交、树、blob 或标记。因此，看起来您最终可以将一个提交与一个标签相关联(然后命名该关联)。

引起我注意的`commit`结构中的下一个模糊属性是`unsigned int index`属性。我试图在源文件中找到对`index`属性的引用，但是我找不到任何特别有启发性的东西。所以我可以澄清一下这一点让我困惑的地方，Git 中的“索引”是暂存区，这是在与提交相关联之前添加修改过的文件的地方。我不明白为什么对这个索引的潜在引用会被存储为一个`unsigned int`。

我查看了源文件，看是否能找到任何对`index`属性的引用，这也许能解释为什么它是以这种方式键入的，但是什么也找不到。在这一点上，我认为类型`unsigned int`可能表明尽管它被命名为`index`，但它与 Git-staging-area-index 无关，而是对某种列表中的位置的引用。我的一个想法是，`index`属性可能是对上面定义的提交列表中特定提交位置的引用。我找不到任何明确的证据来证明这一点，所以当我阅读更多的 Git 代码库时，我会将这一特定的问题列为书签，作为进一步研究的内容。

在`commit`结构中最后剩下的属性是`tree`属性。`tree`属性是一个`tree`结构的表示，定义如下。

```
struct tree {

struct object object;

void *buffer;

unsigned long size;

}; 
```

正如我通过前面的代码阅读了解到的，Git 树由对多个 blobs 或其他树的引用组成。这是一种在单个对象中存储相互关联的文件的方式。这类似于把它想象成一个文件系统目录(ish)。因此，该属性存储了对与特定提交相关联的 blobs 集合的引用。

我没有深入研究与 Git 中的提交相关的所有源代码，但是我所深入研究的简短阅读说明了提交对象是如何被操作的，以及数据结构的操作是如何反映在文件系统中存储的数据中的。

我将在下一篇代码阅读中深入探讨这一点。在那之前，我会仔细思考我写这篇文章时的发现。