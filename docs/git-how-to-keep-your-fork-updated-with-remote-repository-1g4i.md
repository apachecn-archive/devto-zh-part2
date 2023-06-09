# Git:如何用远程存储库更新您的 fork

> 原文：<https://dev.to/rrampage/git-how-to-keep-your-fork-updated-with-remote-repository-1g4i>

当您已经派生了一个存储库(repo)，将它克隆到您的本地机器上，并希望它与原始的 repo 保持同步时，这是非常有用的。

#### 添加远程回购

我们可以使用 **`git remote -v`** 列出我们的回购的远程存储库，并添加原始回购，如下所示:

```
git remote add upstream LINK_TO_ORIGINAL_REPO 
```

Enter fullscreen mode Exit fullscreen mode

注意，称其为 **`upstream`** 仅仅是一种约定。你可以给任何你想要的名字。

使用 **`git remote -v`** 检查回购是否已添加至您的遥控器。

#### 与遥控器同步

*   使用 **`git fetch upstream`** 检查遥控不上叉有无变化
*   查看您感兴趣的任何分支( **`git checkout $BRANCH`** )
*   与上游合并使用: **`git merge upstream/$BRANCH`**
*   如果需要，将您的更改推送到原点: **`git push origin $BRANCH`**

#### 删除远程回购

如果您不再想从远程回购中获取更改，可以使用 **`git remote remove upstream`** 轻松删除它

#### 参考文献

Github 的[帮助页面](https://help.github.com/articles/syncing-a-fork/)