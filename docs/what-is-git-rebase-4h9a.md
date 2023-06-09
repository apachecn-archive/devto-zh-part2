# 什么是 git rebase？

> 原文：<https://dev.to/borrrden/what-is-git-rebase-4h9a>

您可以将 git 提交想象成一个长链。每次提交时，都会在链中添加一个链接。问题是，有时这条链会分裂成两条链，就像这样:

```
D
|
C   C2
|   |
B --
|
A 
```

Enter fullscreen mode Exit fullscreen mode

当大多数人想要将 C2 移回原始链时，他们所做的就是所谓的合并，它看起来像这样:

```
E
|  \
D   |
|   |
C   C2
|   |
B --
|
A 
```

Enter fullscreen mode Exit fullscreen mode

其中 E 是将 C、D 和 C2 的改变组合成新提交的提交。然而，随着分支，然后分支分支，这可以很快导致一个意大利面条式的历史。相反，让我们看看当您使用提交 C2(姑且称之为“dev”)切换到分支并在主线上运行 rebase(姑且称之为“master”)时会发生什么。所以

```
git checkout dev
git rebase master 
```

Enter fullscreen mode Exit fullscreen mode

```
C2
|
D
|
C
|
B
|
A 
```

Enter fullscreen mode Exit fullscreen mode

这段历史现在清晰多了，因为 C2 被移到了这条线的末端。这就是 rebase 所做的。请注意，master 仍然指向“D ”, dev 指向新的“C2”位置。