# 我的 Git 别名

> 原文：<https://dev.to/nickytonline/my-git-aliases-5dea>

好吧，所以@菲尔纳什 [把我拉进了这个](https://dev.to/philnash/comment/518o)。

这篇文章的诞生来自于[一个要点](https://gist.github.com/nickytonline/128d7d164d1eea5daba6753ed0829f41#file-gitconfig)，本质上是我的 git 别名的复制粘贴。

我将提供我的 git 别名列表，并简单明了地解释每个别名的作用。我们开始吧！🏁对于那些新的 git 别名，请参见别名上的[事实文档](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases)。简而言之，要创建自己的别名，可以使用下面的 git 命令。

```
git config --global alias.somealias some-git-command 
```

Enter fullscreen mode Exit fullscreen mode

在我们开始之前，为什么 git 别名？首先，我不知道你是怎么想的，但是有些 git 命令很难记住，而且，我们是程序员，这意味着我们默认是懒惰的，以保证效率。🐢 —> 🐇

*   `a = add .` —运行`git add`将添加所有已更改为暂存的文件。
*   `b = branch` —列出本地机器上存储库的所有分支。
*   `bi = bisect` —运行`git bi`将运行 git 的[平分](https://git-scm.com/docs/git-bisect)来帮助你找出哪个提交有 bug。
*   `ci = commit -m` —这将使用您指定的消息提交文件，例如`git ci "awesome commit!"`。
*   `co = checkout` —这将检验您指定的分支，例如`git co my-awesome-branch`
*   `colast = checkout -` —运行`git colast`将检查您之前工作的分支。
*   `db = branch -D` —这将删除您指定的分支，例如`git db my-not-so-awesome-branch`。请注意，只有当您要删除的分支不是您当前正在工作的分支时，这才会起作用。
*   `laf = fsck --lost-found` —跑`git laf`会把你带到 [git 的失物招领处](https://git-scm.com/docs/git-lost-found)。我承认我很少使用这个，所以也许它不需要一个别名和一些专业的谷歌搜索。
*   `last = log -1 HEAD` —运行`git last`将显示您最后一次提交的内容。
*   `lc = diff HEAD^ HEAD` -将分支的头与前一次提交进行比较。
*   `pf = push --force-with-lease` —跑`git pf`强制推，但比强制推的破坏力小一点。点击这里了解更多关于[——强制租赁 vs .—强制](https://developer.atlassian.com/blog/2015/04/force-with-lease)的信息。
*   `psu = push --set-upstream` —当您想要第一次将分支推送到远程(通常为`origin`)时运行此程序，例如`git psu origin my-awesome-branch`。
*   `pr = pull --rebase` —这将使用指定的分支，例如`git pr develop`，重新设置当前分支的基础。
*   `ra = rebase --abort` —运行`git ra`将中止[重置](https://git-scm.com/docs/git-rebase)。运行这个，当你想，我的 rebase 目前是一团糟。让我离开这里！
*   `rc = rebase --continue` —运行`git rc`将继续一个[重置](https://git-scm.com/docs/git-rebase)。当您处理了 rebase 中的任何冲突时，通常会运行此命令。
*   `remotes = remote -v` —运行`git remotes`显示当前为存储库配置的所有远程设备。
*   `renb = branch -m` —当您想要重命名分支时，运行例如`git renb my-awesom-branch my-awesome-branch`。
*   `rhh = reset --hard HEAD` —核选项。运行`git rhh`清除你所有的修改，从`HEAD`开始。
*   `rh = reset --hard` —当您指定要重置为什么时，会执行硬重置，例如`git rh HEAD~2`。
*   `sfc = diff-tree --no-commit-id --name-only -r` —显示特定提交的文件(相对文件路径)，例如

```
❯ git sfc HEAD                                
src/posts/any-contribution-to-open-source-is-valuable-57d3.md
src/posts/april-16th-2021-what-did-you-learn-this-week-3e72.md
src/posts/are-there-plans-for-reviewers-of-articles-we-post--42nf.md 
```

Enter fullscreen mode Exit fullscreen mode

*   `s = status -s` —跑步`git s`会给你一个更简洁的[状态](https://git-scm.com/docs/git-status)。而不是这个

```
On branch post/my-git-aliases
Your branch is up to date with 'origin/post/my-git-aliases'.

Changes not staged for commit:
 (use "git add <file>..." to update what will be committed)
 (use "git checkout -- <file>..." to discard changes in working directory)

       modified: src/pages/articles/2018-08-24-my-git-aliases/index.md

no changes added to commit (use "git add" and/or "git commit -a") 
```

Enter fullscreen mode Exit fullscreen mode

你得到了这个

```
M src/pages/articles/2018-08-24-my-git-aliases/index.md 
```

Enter fullscreen mode Exit fullscreen mode

*   `stashes = stash list` —运行`git stashes`会显示您从[储存](https://git-scm.com/book/en/v1/Git-Tools-Stashing)中获得的所有储存。例如

```
stash@{0}: WIP on upgrade: bff6257 Destructuring OCD...
stash@{1}: WIP on upgrade: 3d73199 Fixed LiceCap link.
stash@{2}: WIP on upgrade: c2f78g6 Update default title. 
```

Enter fullscreen mode Exit fullscreen mode

*   `unstash = stash pop` —运行`git unstash`从保存的藏匿点列表中弹出一个藏匿点。
*   `vc = clean -dfx` —运行`git vc`清理你的 git 库，所以任何不在 git 中的东西都会被清除，例如`node_modules`，不应该在 repo 中的设置文件等等。所以在你运行这个之前要小心。
*   `mend = commit --amend` —运行`git mend`允许您修改提交。
*   `trigger = commit --allow-empty -m "Trigger Build"` —创建空提交。当您需要在 CI/CD 管道中远程重启一个构建而不提交更改时，这很方便。
*   `alias = ! git config --get-regexp ^alias\. | sed -e s/^alias\.// -e s/\ /\ =\ /` —运行`git aliases`将显示您在 git 中全局配置的所有别名。

虽然不是 Git 别名，但我也强烈推荐使用 [GitHub CLI](https://cli.github.com) 。

照片由 Flickr 用户[辛迪](https://www.flickr.com/photos/10433743@N06/35124387840/in/photolist-VvPEMN-JwSw3b-p73wLh-eiGWHv-pCT4Hm-e6YNg1-739KFW-ss4Yf-g1pYtw-5igZSR-dtNHYt-66LXG8-FVgnJe-a5N3Cr-boce82-G45Uh-4rZF7i-4fi2L4-WL8Tsp-bTY9DX-6uwDpe-291Xx9r-ooFeXR-dk27Hj-dMENeK-8Jm1oE-6Z7gbC-iZgy6m-a3GKzJ-qHiwTU-x2hdp-jGvoT9-d46TUW-88gpDX-8Jm2cu-NzVwP-aqGuxu-mTpiAH-7kzyHd-73T4C1-bFkSSv-zv4KM-79Cfjz-51Gumc-V78mt2-5vAwY-pWXK6q-8iXFxF-7i5PJA-w3Rw1s)提供