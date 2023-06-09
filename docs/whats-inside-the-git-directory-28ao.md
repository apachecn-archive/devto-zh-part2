# 里面是什么。git 目录？

> 原文：<https://dev.to/captainsafia/whats-inside-the-git-directory-28ao>

所以，在[我的上一篇博文](https://dev.to/captainsafia/getting-into-git-init-42nk)中，我花了一点时间弄清楚`git init`是如何工作的。与`git init`相关的函数的中心点是创建存储在`.git`目录中的文件。我认为合乎逻辑的下一步是查看一下`.git`目录中有哪些内容，并找出它们的用途。为此，我首先在一个空目录中初始化 Git。

```
$ ls -lFh .git/
total 24
-rw-r--r-- 1 captainsafia staff 23B Mar 4 20:30 HEAD
drwxr-xr-x 2 captainsafia staff 64B Mar 4 20:30 branches/
-rw-r--r-- 1 captainsafia staff 137B Mar 4 20:30 config
-rw-r--r-- 1 captainsafia staff 73B Mar 4 20:30 description
drwxr-xr-x 12 captainsafia staff 384B Mar 4 20:30 hooks/
drwxr-xr-x 3 captainsafia staff 96B Mar 4 20:30 info/
drwxr-xr-x 4 captainsafia staff 128B Mar 4 20:30 objects/
drwxr-xr-x 4 captainsafia staff 128B Mar 4 20:30 refs/ 
```

我设置了上面的`ls`语句，这样您就可以很容易地看出哪些对象是目录，哪些是文件。我们从头开始，好吗？脑袋里有什么？

```
$ cat .git/HEAD
ref: refs/heads/master 
```

哦，有意思！所以看起来它存储了对我们当前所在的分支的引用。假设这是真的，如果我签出一个新的分支，HEAD 的内容应该会改变。

```
$ git checkout -b new-branch
$ cat .git/HEAD
ref: refs/heads/new-branch 
```

所以接下来要看的是`branches/`目录，但是我发现自己对`refs/`目录更感兴趣，因为它在上面的头文件中被引用过。我们能在里面找到什么？

```
$ ls .git/refs
heads tags
$ ls .git/refs/heads 
```

因此，`refs/`目录包含了`heads/`和`tags/`目录。然而，`refs/heads`目录不包含任何内容。我想这是因为我还没有提交任何东西，所以这里没有真正的提交来引用。所以，我假设如果我创建一个提交，那么`refs/heads/master`文件应该被填充。

```
$ touch test.txt
$ git add test.txt
$ git commit -m "Initial commit"
[new-thing (root-commit) 38eea52] Initial commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.txt
$ ls .git/refs/heads
new-thing
$ cat .git/refs/heads/new-thing
38eea52e57f993d5c594aa7091cc9377b6063f5c 
```

好吧！有道理。我创建了一个文件并提交到`new-thing`分支。此后不久，找到了一个`refs/heads/new-thing`文件，它的内容由我刚才提交的提交散列组成。这是有道理的。如果我再次提交，会发生什么？

```
$ echo "Some content." >> test.txt
$ git add test.txt
$ git commit -m "Change #1"
[new-thing 63916e1] Change #1
 1 file changed, 1 insertion(+)
$ cat .git/refs/heads/new-thing
63916e1e1856e84d1f26dc9e0c26ec5b2344b991 
```

如果我回到以前的提交，会发生什么？

```
$ git checkout 38eea52
$ cat .git/refs/heads/new-thing 
63916e1e1856e84d1f26dc9e0c26ec5b2344b991
$ cat .git/HEAD 
38eea52e57f993d5c594aa7091cc9377b6063f5c 
```

因此，当我检查旧的提交时，我进入分离头模式。在这种状态下，`new-thing`分支上的 head 引用仍然指向最近的提交，但是头文件指向与我们的初始提交相关联的提交散列。这是有意义的，因为当我们处于分离头模式时，我们在技术上不在`new-thing`分支中，所以让头引用它是没有意义的。

说到这里，`branches/`目录里面有什么？

```
$ ls -lFh .git/branches/ 
```

嗯。没什么。那让我大吃一惊！我希望会有对`new-thing`或`master`分支的某种引用。一些谷歌搜索[揭示了](http://schacon.github.io/git/gitrepository-layout.html)这些`branches/`目录实际上是一种目前已被废弃的方式，用来存储在`git pull`和`git push`等命令中使用的 URL 的引用。

列表上的下一个东西是`config`文件。我以前曾经摆弄过这个配置文件，所以我熟悉它的内部结构。对于那些不熟悉的人来说，这个文件看起来有点像这样。

```
$ cat .git/config 
[core]
        repositoryformatversion = 0
        filemode = true bare = false logallrefupdates = true ignorecase = true precomposeunicode = true 
```

我想我可能会写另一篇博文，在这篇博文中，我会更详细地介绍配置这个文件的所有可能的方法。现在，我会尽力把注意力放在`.git`目录上。哈哈哈！

列表中的下一个对象是`description`文件。里面有什么？

```
$ cat .git/description 
Unnamed repository; edit this file 'description' to name the repository. 
```

嗯。有意思。它声明存储库是未命名的。我希望存储库的名称是我正在工作的目录的名称(`git-test`)，但事实并非如此。我做了一些谷歌搜索，结果是这个特定的文件被 GitWeb 程序使用(它像 GitHub，除了它不是 GitHub)。此 web 应用程序使用存储在“描述”文件中的数据。

我也有点熟悉列表中下一个对象的用途，即`hooks/`目录。通常，我会用它来配置一个提交后挂钩，它将运行更漂亮的代码格式化程序。

```
$ ls .git/hooks/
applypatch-msg.sample post-update.sample pre-commit.sample pre-rebase.sample prepare-commit-msg.sample
commit-msg.sample pre-applypatch.sample pre-push.sample pre-receive.sample update.sample 
```

默认情况下，该目录包含一些展示如何使用钩子的示例文件。钩子基本上就是 shell 脚本，所以你可以像编写其他 shell 脚本一样编写它们。

列表中的下一个目录是`info/`目录。那个目录里有什么？

```
$ ls .git/info/
exclude 
```

有意思。`exclude`文件里有什么？

```
$ cat .git/info/exclude 
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
# For a project mostly in C, the following would be a good set of
# exclude patterns (uncomment them if you want to use them):
# *.[oa]
# *~ 
```

因此，看起来这个名副其实的 exclude 文件包含了在运行某些 Git 命令时可能想要排除的一些文件的列表。

列表中的最后一个目录是`objects`目录。我在上一篇博文中发现了这个目录是如何初始化的，但是现在让我们来看看其中有什么。

```
$ ls .git/objects/
38 5e 63 e6 f9 info pack 
```

因此，通过查看`objects/`文件中的一些子目录，我可以看到它们显然是对所做提交的提交散列的引用。在我看来，这提出了一个非常重要的问题。对象到底是什么？我在这里会有点懒，只是[将](https://matthew-brett.github.io/curious-git/git_object_types.html)链接到我发现的一篇很好的文章，这篇文章完整地解释了`.git/objects`目录的内容。

所以那次探索相当有趣。我对`.git`目录的内容更有信心了。我还有几个问题要回答。

*   `.git/config`文件中有哪些选项？
*   `.git/objects`中子目录的层次结构是如何创建的？
*   `hooks`目录中的脚本是如何集成到 Git 的命令中的？

下次见！