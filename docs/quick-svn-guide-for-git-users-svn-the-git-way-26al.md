# Git 用户快速 SVN 指南；SVN:Git 之路

> 原文：<https://dev.to/rajbdilip/quick-svn-guide-for-git-users-svn-the-git-way-26al>

这篇文章最初发表在[Git 用户快速 SVN 指南上；SVN:Git 之路](https://diliprajbaral.com/2018/01/13/quick-svn-guide-for-git-users-svn-the-git-way/)。

你可能会问，为什么一个 Git 用户想要切换到 SVN 呢？

嗯，有时候你别无选择。想象一下，在 SVN 从事一个已经维持了十年的项目。"但是将 SVN 代码库迁移到 Git 根本不是什么大事."但是也有像 CI/CD 集成这样的东西需要担心。这也没什么大不了的，但有时人们会说“不要修理没坏的东西。”有点太认真了。

抛开原因不谈，有了一个好的版本控制系统(分布式 VCS)概念，我不想从零开始使用 SVN 指南。虽然网上有大量关于 SVN 到 Git 迁移的资源，但我找不到能帮助我立即使用 SVN 回购的快速简明的指南。如果你和我一样，你会发现这篇文章很有帮助。以下步骤向您展示了如何以 Git 方式使用 SVN。

### 克隆新的回购

签出回购类似于我们在 Git 中的做法。

```
$ svn checkout <path-to-your-repo-branch> <path-to-checkout> 
```

Enter fullscreen mode Exit fullscreen mode

#### 举例

以下代码将您的代码签出到当前工作目录。

```
$ svn checkout https://mysvnrepo.com/myrepo/trunk . 
```

Enter fullscreen mode Exit fullscreen mode

### 创建新的主题分支

在 SVN，分支(和标签)只不过是一个分支的简单拷贝。文件的文字复制粘贴，不像 Git 中的提交指针。这个事实让我花了一段时间去消化和适应。

以下命令是 SVN 等价于 git checkout -b 分支。

```
$ svn copy <path-to-a-branch> <path-for-new-branch> -m "Message" 
```

Enter fullscreen mode Exit fullscreen mode

#### 举例

```
$ svn copy --parents https://mysvnrepo.com/myrepo/trunk https://mysvnrepo.com/myrepo/branches/feature-branch
$ svn switch https://mysvnrepo.com/myrepo/branches/feature-branch 
```

Enter fullscreen mode Exit fullscreen mode

### 从事回购工作

#### 添加新文件

要添加新文件，您可以使用:

```
$ svn add <path-to-file> 
```

Enter fullscreen mode Exit fullscreen mode

至于修改过的文件，我们不需要添加。我们可以直接提交。

```
$ svn commit -m "Commit message" 
```

Enter fullscreen mode Exit fullscreen mode

为了只提交特定的文件，我们需要在提交消息后列出文件。

```
$ svn commit -m "Commit message" <path-to-file-1> <path-to-file-2> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想提交一个文件，我们也可以做以下事情。

```
$ svn commit <path-to-file> -m "Commit message" 
```

Enter fullscreen mode Exit fullscreen mode

### 签出新变更

下面是与 git fetch && git merge 或 git pull 等价的 SVN。

```
$ svn update 
```

Enter fullscreen mode Exit fullscreen mode

### 将特征分支合并到主干

在 SVN 合并一个分支类似于我们在 Git 中的做法。

```
$ svn merge <path-to-branch> 
```

Enter fullscreen mode Exit fullscreen mode

#### 举例

```
$ svn update
$ svn switch https://mysvnrepo.com/myrepo/trunk
$ svn update
$ svn merge https://mysvnrepo.com/myrepo/branches/feature-branch
$ svn commit -m "Merge feature branch to trunk" 
```

Enter fullscreen mode Exit fullscreen mode

### 合并后删除特征分支

要删除一个特征分支(或任何分支)，使用 svn delete。

#### 举例

```
$ svn delete https://mysvnrepo.com/myrepo/branches/feature-branch -m "Delete feature branch after merging" 
```

Enter fullscreen mode Exit fullscreen mode