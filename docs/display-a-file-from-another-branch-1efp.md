# 显示来自另一个分支的文件

> 原文：<https://dev.to/math2001/display-a-file-from-another-branch-1efp>

想看看不同分支的文件吗？嗯，不用`stash` & & `checkout`，用`git show`就行了。

```
dev $ git show master:path/to/file 
```

Enter fullscreen mode Exit fullscreen mode

PS:这适用于任何引用(不确定这是不是正确的术语，如果我错了请纠正我)，比如提交和标签:

```
master $ git log --oneline
d529def Some more new stuff
4598db3 Some new stuff
e8dd680 init
master $ git show e8dd680:path/to/file
<The content of the file at commit e8dd680> 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！