# 如何移除所有不是“主”的本地 Git 分支

> 原文：<https://dev.to/nickymarino/how-to-remove-all-local-git-branches-that-aren-t-master-4p8n>

*本文最初发表于[我的网站](https://nickymarino.com)。查看更多精彩内容！*

有时，我会想删除一个存储库的所有本地分支，而不是`master`分支。一个简单的命令是:

```
$ git branch | grep -v "master" | xargs git branch -d 
```

Enter fullscreen mode Exit fullscreen mode

(如果您想保留多个分支，比如`master`和`develop`，您可以使用`grep -v "master\|develop"`将它们链接在一起)

`git branch`列出回购的所有本地分支，`grep -v`打印上一个命令中与“master”不匹配的所有行，`xargs`从上一个输出中取出每一行并运行`git branch -d <output_line>`。

我建议使用`-d`而不是`-D`，以防 git 建议不删除分支。