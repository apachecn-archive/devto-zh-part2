# “Git”不止一个分支

> 原文：<https://dev.to/kyleparisi/git-more-than-one-branch-25o3>

在前一份工作中，我不得不审查同事的代码，并继续开发一个功能。为此，我曾经在一个`review/`文件夹中签出项目的一个全新克隆。从那里，我可以安全地在本地审查代码。

现在我正在进行一次大规模的重写。这一次我需要回顾一下当前的系统是如何运作的。以下是另一种方法:

```
# git worktree add <path> [<branch>]
git worktree add legacy develop 
```

如果您`cd`到 legacy，您的 git 命令将在那个分支的上下文中。一个不错的专业建议，放在你的后口袋里！