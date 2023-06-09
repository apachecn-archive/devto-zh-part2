# 今天我学到了一些东西:使用“git diff”和“git log -u”来查看提交之间文件的变化

> 原文：<https://dev.to/tomatohammado/something-i-learned-today-using-git-diff-and-git-log--u-to-see-changes-in-a-file-between-commit-1lg4>

## 问题

我必须对一个模板文件进行修改，但是在我当前的分支上，我正在处理数据库。所以我想创建一个新的分支，只是为了模板更新，但是需要仔细检查我没有在单独分支的提交历史中对该文件进行更改

## 资源

*   [堆栈溢出:使用`git diff`](https://stackoverflow.com/a/3338145)
*   [堆栈溢出:使用`git log -u`](https://stackoverflow.com/a/3338901)

## 调查结果

*   `<commit_0>..<commit_1>`是检查两次提交之间差异的简写，不要与混淆...
*   `<commit_0>...<commit_1>`——用三个点——这也涵盖了两次提交之间的所有提交*。两个点只是简单的相互比较。*
*   您还可以排除其中一个提交引用，它将退回，就像使用`HEAD`作为值一样。哦，你可以用`HEAD`

## 进一步研究

*   看起来你可以做类似于`HEAD^^`和`HEAD~2`的事情，但是我还不知道那是什么意思😬