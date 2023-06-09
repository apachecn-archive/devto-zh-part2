# 快速浏览一下 git 对象存储

> 原文：<https://dev.to/imjacobclark/a-quick-look-at-the-git-object-store-290j>

让我们讨论一下 git 的一些内部机制，以及它如何在`.git`目录中存储和跟踪对象。

如果您不知道`.git`目录是什么，它只是 git 用来存储您的存储库数据的一个空间，该目录是在您运行`git init`时创建的。诸如提交和提交数据的二进制对象和纯文本文件、远程服务器信息以及关于分支位置的信息都存储在。

贯穿整篇文章的关键概念非常简单——几乎你在 git 中做的每个操作都用一堆元数据创建对象，这些对象用一堆元数据指向更多的对象，如此等等。差不多就是这样。

### Git 管道

首先，创建一个新目录，并将其初始化为一个新的 git 项目。

```
$ mkdir git && cd git
$ git init 
```

我们现在可以看一下`.git`目录及其内容。

```
$ tree .git
.git
|___branches
|___config
|___description
___HEAD
|___hooks
| |___applypatch-msg.sample
| |___commit-msg.sample
| |___post-update.sample
| |___pre-applypatch.sample
| |___pre-commit.sample
| |___pre-push.sample
| |___pre-rebase.sample
| |___prepare-commit-msg.sample
| |___update.sample
|___info
| |___exclude
|___objects
| |___info
| |___pack
|___refs
| |___heads
| |___tags 
```

目前，git 创建的大多数目录都是空的。这是因为我们还没有开始跟踪任何文件或目录。

在底层，git 只不过是一个键值存储，它获取一个文件并对其进行压缩，然后将其作为二进制对象存储在计算出的 SHA1 中。

Git 有一个名为`hash-object`的底层管道命令。

[hash-object](https://git-scm.com/docs/git-hash-object) 将获取一些东西并计算它的对象 ID。例如，我们可以计算字符串“Hello World”的对象 ID。

```
$ echo "Hello World" | git hash-object --stdin
557db03de997c86a4a028e1ebd3a1ceb225be238 
```

你可以在你的机器上试试，你应该会看到和上面完全一样的输出。

当我们让 git 跟踪一个文件时，这正是为了计算一个 ID 来存储信息而发生的事情。

我们可以进一步使用这个命令，要求 git 获取字符串“Hello World ”,并将其作为对象存储在其对象存储中。

```
$ echo "Hello World" | git hash-object --stdin -w
557db03de997c86a4a028e1ebd3a1ceb225be238 
```

现在如果我们看看我们的。git 目录树再次，我们将看到一些额外的文件和目录。

```
$ tree .git
.git
|___branches
|___config
|___description
|___HEAD
|___hooks
| |___applypatch-msg.sample
| |___commit-msg.sample
| |___post-update.sample
| |___pre-applypatch.sample
| |___pre-commit.sample
| |___pre-push.sample
| |___pre-rebase.sample
| |___prepare-commit-msg.sample
| |___update.sample
|___info
| |___exclude
|___objects
| |___55
| | |___7db03de997c86a4a028e1ebd3a1ceb225be238
| |___info
| |___pack
|___refs
| |___heads
| |___tags 
```

我们可以看到，在`objects`目录下创建了一个新的子目录:`55`，其中存储了一个 id 为`7db03de997c86a4a028e1ebd3a1ceb225be238`的对象，注意，对象文件名中省略了 SHA1 的前两个字符。

如前所述，git 根据计算出的 SHA1 存储每个对象，为了使目录结构更简单，git 采用 SHA1 的前两个字符，并将其用作对象的目录。

如果我们回想一下，我们的“Hello World”字符串计算的 SHA1 是`557db03de997c86a4a028e1ebd3a1ceb225be238`，这意味着名为`55`的目录应该是我们的字符串“Hello World”。我们可以使用`cat-file`命令来验证这一点。

[cat-file](https://git-scm.com/docs/git-cat-file) 是 git plumming 命令，可以显示 gits 对象存储中对象的内容、类型和大小。

```
$ git cat-file 557db03de997c86a4a028e1ebd3a1ceb225be238 -p
Hello World 
```

这是 git 的最低级别，在 git 的日常使用中，您几乎不会直接使用这些命令。

### 提交

让我们创建一些文件和目录。

```
$ mkdir docs
$ touch README.md 
$ echo "Hello World" >> README.md 
```

如果我们现在执行`git status`操作，我们应该会看到几个未被跟踪的文件。

```
$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README.md
    docs/

nothing added to commit but untracked files present (use "git add" to track) 
```

让我们通过暂存和检入新文件来解决这个问题。

```
$ git add .
$ git commit -m 'Add README and Docs' 
```

如果我们运行一个`git log`,我们应该看到我们的新提交用一个 SHA1 来表示。

```
$ git log
commit b82828ed05ef076db09118994c7c036708973b40
Author: Jacob Clark <jacob.clark@>
Date:   Sat Mar 19 00:40:04 2016 +0000

    Add README and Docs 
```

上面的 SHA1 只是一个指针，指向 git 存储提交信息的对象。

如果我们现在查看`.git`目录的内容，我们应该看到为我们刚刚提交的文件存储了几个额外的对象。

```
$ tree .git
.git
|___branches
|___COMMIT_EDITMSG
|___config
|___description
|___HEAD
|___hooks
| |___applypatch-msg.sample
| |___commit-msg.sample
| |___post-update.sample
| |___pre-applypatch.sample
| |___pre-commit.sample
| |___pre-push.sample
| |___pre-rebase.sample
| |___prepare-commit-msg.sample
| |___update.sample
|___index
|___info
| |___exclude
|___logs
| |___HEAD
| |___refs
| | |___heads
| | | |___master
|___objects
| |___55
| | |___7db03de997c86a4a028e1ebd3a1ceb225be238
| |___66
| | |___801fba9ba0350874eb1f64b87b6cdb609da859
| |___76
| | |___ebb0f0fd6dfc00be4631fd7019d55536f38ea8
| |___b8
| | |___2828ed05ef076db09118994c7c036708973b40
| |___info
| |___pack
|___refs
| |___heads
| | |___master
| |___tags 
```

我们可以看到现在存储了四个对象。有人可能会认为这有点奇怪。我们已经存储了我们的“Hello World”对象，我们知道 git 将我们的提交存储为一个对象，我们刚刚签入了三个新对象(两个文本文件和一个目录)，所以*应该*在存储中留给我们 5 个对象，让我们看看为什么不是这样。

通过运行`git cat-file b82828ed05ef076db09118994c7c036708973b40 -p`来看看我们的提交对象。

您提交的 SHA1 将与我的不同，因为时间戳用于计算它，所以只需用上面的那个替换您的`git log`中的那个。

```
$ git cat-file b82828ed05ef076db09118994c7c036708973b40 -p
tree 66801fba9ba0350874eb1f64b87b6cdb609da859
author Jacob Clark <jacob.clark@> 1458348004 +0000
committer Jacob Clark <jacob.clark@> 1458348004 +0000

Add README and Docs 
```

这里我们可以看到 git 已经存储了一堆关于我们刚刚执行的提交的信息，树 SHA1、作者、提交者和消息。

让我们来看看树对象，同样你的 SHA1 将是不同的。

```
$ git cat-file 66801fba9ba0350874eb1f64b87b6cdb609da859 -p
100644 blob 557db03de997c86a4a028e1ebd3a1ceb225be238    README.md
040000 tree 76ebb0f0fd6dfc00be4631fd7019d55536f38ea8    docs 
```

一棵树就像一个目录，它包含几个对象，在`.git`中，你要么在看一棵树，要么在看一个对象。文件系统中的目录与 git 中的树是一对一的映射。

正如我在本文开头所说的，git 中的几乎所有东西都是指针。在这个提交中，我们可以看到两个指针，一个指向对象，一个指向树。我们可以看到这两个指针都与我们之前签入的内容相关——一个`README.md`文件(现在是一个对象)和一个名为`docs`的目录(现在是一棵树)。

我们的`README.md`文件中的文本只是“Hello World”，当我们签入我们的`README.md`文件时，这个对象已经存储在 gits 对象存储中，如果我们查看上面列出的 SHA1，我们可以看到它与我们之前手动放入 gits 对象存储的“Hello World”字符串中的 SHA1 完全相同。

```
$ git cat-file 557db03de997c86a4a028e1ebd3a1ceb225be238 -p
Hello World 
```

Git 也不会跟踪任何它不需要的东西。Git 很聪明，知道它跟踪了什么，没有跟踪什么，两个相同字符串的 SHA1 永远不会改变。git 没有理由在存储中为我们之前签入的完全相同的字符串和 SHA1 创建第二个对象。相反，git 只是从我们前面看到的提交元数据中创建了一个指向它的指针。

这些类型的优化保持了 git 的高性能，并减少了不必要的对象存储，这些对象占用了不需要的磁盘空间，这就是为什么我们只看到了 4 个新对象，而不是之前的 5 个。

### 树枝

分支与提交没有什么不同——它只是一个指向存储库中提交的指针(头部)。

Git 将分支信息存储在`.git/refs/heads`目录的纯文本文件中，默认情况下，git 会创建一个名为`master`的分支，由该目录中名为`master`的文件表示。

```
$ cat .git/refs/heads/master
b82828ed05ef076db09118994c7c036708973b40 
```

这个 SHA1 是指向分支当前位置(头部)的指针，如果我们运行一个`git log`，我们可以看到上面的 SHA1 与我们刚刚创建的单个提交完全相同，因为那是我们在该分支中的当前位置。

```
$ git log
commit b82828ed05ef076db09118994c7c036708973b40
Author: Jacob Clark <jacob.clark@g>
Date:   Sat Mar 19 00:40:04 2016 +0000

    Add README and Docs 
```

如果我们创建一个名为`hello-moon`的新分支，git 将开始跟踪该分支在`.git/refs/heads/hello-moon`中的位置。

```
$ git checkout -b hello-moon
Switched to a new branch 'hello-moon'
$ cat .git/refs/heads/hello-moon
b82828ed05ef076db09118994c7c036708973b40 
```

上面的 SHA1 仍然和`master` SHA1 一样，因为我们还没有改变分支机构的位置。

```
$ touch hello-moon.txt
$ git add .
$ git commit -m 'Add hello moon'
[hello-moon 683d9df] Add hello moon
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 hello-moon.txt 
```

现在 git 已经跟踪了新的提交和新文件，我们现在将有一个新的分支位置`hello-moon`。

```
$ cat .git/refs/heads/hello-moon
683d9df7e5c7247c4606c86e21c84f89e9a575bb 
```

我们可以在这个 SHA1 上做一个`git cat-file`来看看这个分支现在也指向哪里。

```
$ git cat-file 683d9df7e5c7247c4606c86e21c84f89e9a575bb -p                                                                                (hello-moon) 
tree 0a1b6bbf04e5d9448994cae4b5a2cb7eff14302a
parent b82828ed05ef076db09118994c7c036708973b40
author Jacob Clark <jacob.jh.clark@googlemail.com> 1458386031 +0000
committer Jacob Clark <jacob.jh.clark@googlemail.com> 1458386031 +0000

Add hello moon 
```

正如我们所料，它指向我们的“Add hello moon”提交，增加了一个新的内容，这个提交有一个父级，git 中的每个提交都有一个父级(除非它是分支上的第一个提交)，父级是 Git 跟踪其对象历史的方式，如果我们`git cat-file`父级，我们可以看到它是我们在主分支上进行的初始提交。

```
$ git cat-file b82828ed05ef076db09118994c7c036708973b40 -p                                                                                (hello-moon) 
tree 66801fba9ba0350874eb1f64b87b6cdb609da859
author Jacob Clark <jacob.jh.clark@googlemail.com> 1458348004 +0000
committer Jacob Clark <jacob.jh.clark@googlemail.com> 1458348004 +0000

Add README and Docs 
```

如果我们决定在这个分支上不再需要“添加你好月亮”提交，我们可以要求 git 将分支重置回一个特定的提交。

```
$ git reset --hard b82828ed05ef076db09118994c7c036708973b40
HEAD is now at b82828e Add README and Docs 
```

现在，如果我们最后一次抓取分支文件，我们将看到我们回到了最初开始的地方。

```
$ cat .git/refs/heads/hello-moon                                                                                                          (hello-moon) 
b82828ed05ef076db09118994c7c036708973b40 
```

总之，一个分支*字面上的*只是一个指向特定提交 SHA1 的文件，仅此而已。

### 结论

有一些我在这篇文章中没有涉及的主题，比如合并、标签、远程以及 HEAD 实际上是什么。我将在以后的文章中更详细地介绍这些概念和上述内容。

git 文档非常棒，我强烈建议你阅读“Git 内部——管道和瓷器”部分。

访问我的[网站](https://www.jacobclark.xyz)，在 [Twitter](https://twitter.com/imjacobclark) 和 [GitHub](https://github.com/imjacobclark) 上关注我，或者在 [LinkedIn](https://uk.linkedin.com/in/imjacobclark) 上查看我的职业背景。

最初发布于 [blog.jacobclark.xyz](https://blog.jacobclark.xyz/a-quick-look-at-the-git-object-store)