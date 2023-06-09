# 分解大型拉取请求

> 原文：<https://dev.to/genebean/breaking-up-a-large-pull-request-59aa>

有没有在 GitHub 上完成一个 pull 请求的所有修改，然后意识到它太大了，不容易检查或者推理正在发生什么？我最近遇到了这个问题。解决方案是:创建多个补丁，每个补丁包含一个变更子集，并使用它们来生成更易于管理的拉请求。

对于本指南，让我们做一些假设:

*   git repo 中的默认分支是`master`
*   包含大 PR 的分支称为`my_massive_change`
*   这些更改包含许多不同的文件，其中许多文件位于子文件夹中

为了加快速度，我还使用了 [hub](https://hub.github.com/) 来与 GitHub 交互。

文件结构示例:

```
╔ ☕️ gene:~/Downloads/my_repo (my_massive_change ✔)
╚ᐅ tree
.
├── a
│   ├── a.txt
│   └── another_file_in_a.txt
├── a.pp
├── b
│   ├── another_file_in_b.txt
│   └── b.txt
├── b.pp
├── c
│   ├── another_file_in_c.txt
│   └── c.txt
├── c.pp
├── d
│   ├── another_file_in_d.txt
│   └── d.txt
├── d.pp
├── e
│   ├── another_file_in_e.txt
│   └── e.txt
├── e.pp
├── f
│   ├── another_file_in_f.txt
│   └── f.txt
├── f.pp
├── g
│   ├── another_file_in_g.txt
│   └── g.txt
├── g.pp
├── h
│   ├── another_file_in_h.txt
│   └── h.txt
└── h.pp

8 directories, 24 files 
```

Enter fullscreen mode Exit fullscreen mode

假设您对一些根级文件和一些同名子文件夹中的文件进行了更改，您可以像下面这样分解您的更改。

首先，在你的 repo 之外为所有的补丁准备一个地方:

```
╔ ☕️ gene:~/Downloads/my_repo (my_massive_change ✔)
╚ᐅ mkdir ~/Downloads/patches_for_my_repo/ 
```

Enter fullscreen mode Exit fullscreen mode

接下来，生成相关内容的补丁:

```
╔ ☕️ gene:~/Downloads/my_repo (my_massive_change ✔)
╚ᐅ git diff master a.pp a/ > ~/Downloads/patches_for_my_repo/changes_to_a.patch
╔ ☕️ gene:~/Downloads/my_repo (my_massive_change ✔)
╚ᐅ git diff master b.pp b/ c.pp c/ > ~/Downloads/patches_for_my_repo/changes_to_b_c.patch
╔ ☕️ gene:~/Downloads/my_repo (my_massive_change ✔)
╚ᐅ git diff master e/ g/ > ~/Downloads/patches_for_my_repo/changes_to_e_g.patch 
```

Enter fullscreen mode Exit fullscreen mode

这些命令将列出的文件和文件夹与主分支进行比较，并生成一个包含所有差异的文件。例如，下面是其中一个补丁的样子:

```
╔ ☕️ gene:~/Downloads/my_repo (my_massive_change ✔)
╚ᐅ cat ~/Downloads/patches_for_my_repo/changes_to_b_c.patch
diff --git a/b/another_file_in_b.txt b/b/another_file_in_b.txt
index e69de29..2ef267e 100644
--- a/b/another_file_in_b.txt
+++ b/b/another_file_in_b.txt
@@ -0,0 +1 @@
+some content
diff --git a/c/another_file_in_c.txt b/c/another_file_in_c.txt
index e69de29..2ef267e 100644
--- a/c/another_file_in_c.txt
+++ b/c/another_file_in_c.txt
@@ -0,0 +1 @@
+some content 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了补丁，我们可以使用`git apply`来使用它们。

```
# switch to your starting point:
╔ ☕️ gene:~/Downloads/my_repo (my_massive_change ✔)
╚ᐅ git checkout master
Switched to branch 'master'

# make a branch for the patch's changes:
╔ ☕️ gene:~/Downloads/my_repo (master ✔)
╚ᐅ git checkout -b changes_to_a
Switched to a new branch 'changes_to_a'

# apply the changes:
╔ ☕️ gene:~/Downloads/my_repo (changes_to_a ✔)
╚ᐅ git apply ~/Downloads/patches_for_my_repo/changes_to_a.patch
╔ ☕️ gene:~/Downloads/my_repo (changes_to_a ✘) ✹
╚ᐅ git commit -am 'applying changes to a*'
[changes_to_a 4425166] applying changes to a*
 1 file changed, 1 insertion(+)

# push changes to GitHub
╔ ☕️ gene:~/Downloads/my_repo (changes_to_a ✔)
╚ᐅ git push -u origin changes_to_a

# create a pull request that is just for these changes:
╔ ☕️ gene:~/Downloads/my_repo (changes_to_a ✔)
╚ᐅ hub pull-request -m 'changes to a*' 
```

Enter fullscreen mode Exit fullscreen mode

对每个补丁重复这个过程，瞧，你现在有了一组 PR，它们包含了和以前一样的变化，但是更易于管理。

想要确保你得到了一切，或者只是看看你的 PR 合并后还需要应用哪些补丁？这样做:

```
# get the latest version of master
# that includes the merged changes
$ git checkout master
$ git pull

# Rebase your big change set so that you
# can see how it compares:
$ git checkout my_massive_change
$ git rebase -i master

# Push the updated branch to GitHub so
# you can easily see what's left in the big PR
$ git push -f 
```

Enter fullscreen mode Exit fullscreen mode

一旦你生成并应用了所有的改变，你将会发现`my_massive_change`和`master`没有任何不同。