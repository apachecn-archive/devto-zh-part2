# 教 Git 一些新的技巧

> 原文：<https://dev.to/sublimegeek/teaching-git-some-new-tricks-204j>

第一(第二)篇文章！学会点击保存按钮，乔纳森！

您可以通过添加或省略`--global`标志来创建回购级别和全局的别名。

创建别名很容易。只需键入`git config --global alias.co checkout`为`git checkout`创建一个全局别名。现在，你所要做的就是输入`git co develop`。太棒了，对吧？

有些命令有您可以添加的标志。例如，`checkout`命令允许您有选择地创建和检出一个带有`-b`标志的新分支。Git 只需用引号将命令括起来，就可以轻松处理这些别名。我喜欢用`cob`来表示。

`git config --global alias.cob "checkout -b"`

现在，要创建一个新的分支，我只需输入`git cob new_branch_name`。

以下是我最喜欢的一些别名:

```
st status
co checkout
cob checkout -b
lol log --oneline 
```

Enter fullscreen mode Exit fullscreen mode

如果您想将以`git`开头的命令连接在一起，您必须在前面加上一个 bang(！).

假设我想保存我的更改，并在一个命令中执行拉入操作。我们姑且称这个别名为“搁置”。我会键入以下内容:

```
git config --global alias.shelve "!git stash && git pull" 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我很喜欢 dev.to，我希望能写更多的文章。特别喜欢说 Git。

参考: [git 配置](https://git-scm.com/docs/git-config)