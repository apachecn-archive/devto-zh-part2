# 下一步 Git 为那些已经知道添加和提交

> 原文：<https://dev.to/t4rzsan/next-step-git-for-those-that-already-know-add-and-commit-55ac>

不久前，当我第一次开始使用 Git 时，它看起来非常简单，尽管我不得不习惯 Git 的非连接性，这与 TFS 的工作方式不同。我主要使用 Visual Studio 的 Git 集成，通常它工作得非常好。

但是过了一段时间，事情开始变得复杂起来。我必须使用子模块，更改远程 URL，并处理未被跟踪的文件。那时，我决定离开我在 Visual Studio 中的舒适区，进入 Git CLI。

Git CLI 不容易记住，而且每件事都可以用多种方式完成，所以我开始自己编译有用的命令。下面是编译的结果。我希望它能对那些正在经历同样过程的人有用，如果你有自己的 Git 宝石，请告诉我。

# 列表

没有特定的顺序。

## 编辑配置

在 Windows 上，Git 配置文件通常放在“c:\Users[user]”下。您也可以从命令提示符启动编辑器。

```
git config --global -e 
```

### 为提交消息设置编辑器

要将提交消息的默认编辑器更改为 Notepad++，需要在配置文件中添加一个[core]部分，如下所示。

```
[core]
    editor = 'C:/put-your-folder-here/Notepad++/notepad++.exe' -multiInst -notabbar 
```

从现在开始，只要你不使用-m 开关运行 git commit，Notepad++就会打开。

### 将合并工具设置为 Visual Studio

```
[diff]
    tool = vsdiffmerge
[difftool]
    prompt = true
[difftool "vsdiffmerge"]
    cmd = \"C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Professional\\Common7\\IDE\\CommonExtensions\\Microsoft\\TeamFoundation\\Team Explorer\\vsDiffMerge.exe\" \"$LOCAL\" \"$REMOTE\" //t
    keepbackup = false
    trustexistcode = true
[merge]
    tool = vsdiffmerge
[mergetool]
    prompt = true
[mergetool "vsdiffmerge"]
    cmd = \"C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Professional\\Common7\\IDE\\CommonExtensions\\Microsoft\\TeamFoundation\\Team Explorer\\vsDiffMerge.exe\" \"$REMOTE\" \"$LOCAL\" \"$BASE\" \"$MERGED\" //m
    keepbackup = false
    trustexistcode = true 
```

## 子模块

### 克隆子模块

如果 repo 包含子模块，并且您想要降低子模块中的代码，您将需要递归克隆。

```
git clone --recursive https://github.com/hocuspocus/icsharp.git 
```

### 将子模块改为自己的分支

如果您克隆了一个带有子模块的 repo，并且想要将子模块更改为不同的分支(例如，如果您已经分支了子模块)，您需要编辑文件. gitsubmodule 中的 URL。

```
[submodule "Engine"]
    path = Engine
    url = https://github.com/scriptcs/scriptcs.git 
```

保存后。gitsubmodule，运行命令。

```
git submodule sync 
```

看起来这可能会脱离 HEAD，所以签出可能是必要的(在进行任何本地更改之前)。

```
git checkout 
```

如果下载子模块的代码有问题，请尝试运行以下命令:

```
git submodule update --remote 
```

## 启动合并工具

如果有合并工具，您可以启动您的合并工具(在配置文件中设置)。

```
git mergetool 
```

## 比较远程

首先从远程存储库中提取所有内容:

```
git fetch origin 
```

然后与本地进行比较:

```
git log HEAD..origin/master --oneline 
```

如果您对结果满意，您可以将远程更改与本地回购合并:

```
git merge 
```

## 显示远程网址

显示“原点”的远程 URL:

```
git remote get-url origin 
```

如需更多信息，您可以使用:

```
git remote show origin 
```

如果您的遥控器移动了，您可以使用 *set-url* 更改 URL:

```
git remote set-url origin https://hocuspocus@bitbucket.org/myteam/myproject.git 
```

## 删除分支

删除远程分支:

```
git push -d <remote_name> <branch_name> 
```

例如:

```
git push -d origin my-feature-branch 
```

您也可以使用:

```
git push <remote_name> :<branch_name> 
```

删除本地分支:

```
git branch -d <branch_name> 
```

## 删除局部修改

撤消所有未暂存的本地更改:

```
git checkout . 
```

在单个文件中撤消 git add for:

```
git reset folder/file.cs 
```

Undo `git add .` :

```
git reset . 
```

## 修复未被跟踪的文件

```
git rm . -r --cached
git add .
git commit -m "Fixed untracked files" 
```

## 为命令创建别名

如果你厌倦了输入难以忘记的长命令，你可以创建别名。

```
git config --global alias.a "add ."
git config --global alias.c "commit" 
```

您现在只需键入`git a`即可添加未暂存的文件。

别名也可以直接添加到配置文件中。

```
[alias]
    a = add .
    c = commit 
```