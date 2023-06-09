# 合并两个 git 仓库

> 原文：<https://dev.to/foursixnine/merge-two-git-repositories-1mj>

我只是关注了这篇[中帖](https://medium.com/@spences10/git-allow-unrelated-histories-a39a3814b981)，长话短说:

检查 origin/master 分支，确保我的本地 master 等于 origin，然后将它推到我的 fork(我很少在 master 上做 git pull)。

```
git fetch origin
 git checkout master
 git reset --hard origin/master
 git push foursixnine 
```

我需要添加属于一个完全不同的存储库的遥控器:

```
git remote add acarvajal gitlab@myhost:acarvajal/openqa-monitoring.git
 git fetch acarvajal
 git checkout -b merge-monitoring
 git merge acarvajal/master --allow-unrelated-histories 
```

塔达姆。我的改变在那里！

因为我想要特定目录中的所有内容:

```
mkdir openqa/monitoring
 git mv ${allmyfiles} openqa/monitoring/ 
```

由于不相关的存储库有一个 README.md 文件，我需要确保这两个文件都在切割中，一个来自我的原始回购，另一个来自新的不相关回购:

以下命令表示:从 acarvajal/master 中检查 README.md。

```
git checkout acarvajal/master -- README.md
 git mv README.md openqa/monitoring/ 
```

以下命令表示:从 origin/master 签出 README.md。

```
git checkout origin/master -- README.md 
```

提交更改、推送到我的分支并打开拉请求:)

```
git commit
 git push foursixnine 
```