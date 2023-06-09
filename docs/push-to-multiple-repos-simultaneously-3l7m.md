# 同时推送::多个回购

> 原文：<https://dev.to/g33kzone/push-to-multiple-repos-simultaneously-3l7m>

本教程帮助演示将源代码推送到多个存储库的过程配置。

### 背景

如果需要有多个回购，那么它们最好是同步的。幸运的是，Git 允许一次将源代码推送到多个仓库。

[![Git Multi Repo](img/e8526c5dcbf86d0f03d0acc0f618bb8c.png)T2】](https://2.bp.blogspot.com/-EYBE6CHprjg/W4O3ZR-1uhI/AAAAAAAAz6E/iQ-1-NXU_hIbECnTSEnIXT19AX9HHocbACLcBGAs/s1600/Multiple%2BRepo.PNG)

### 远程网址

下面我们来考虑一下要推送的源代码的 repo urls。你可以观察到，除了域名之外，这些 URL 几乎是相同的。

```
git@github.com:g33kzone/multi-repo.git
git@gitlab.com:g33kzone/multi-repo.git 
```

Enter fullscreen mode Exit fullscreen mode

*注意:为了防止在推送源代码时被要求输入密码，请确保为每个服务设置了 SSH 访问。*

让我们为您的主远程存储库(即 Github)
创建一个名为 origin 的别名

```
git remote add origin git@github.com:g33kzone/multi-repo.git 
```

Enter fullscreen mode Exit fullscreen mode

### 配置推送远程网址

下面的命令(通过命令行)将完成这个任务。

```
git remote set-url --add --push origin git@github.com:g33kzone/multi-repo.git

git remote set-url --add --push origin git@gitlab.com:g33kzone/multi-repo.git 
```

Enter fullscreen mode Exit fullscreen mode

这可以通过运行以下命令
来确认

```
git remote show origin 
```

Enter fullscreen mode Exit fullscreen mode

### 推送多个回购

使用以下命令(通过命令行-一次)设置上游

```
git push --set-upstream origin master 
```

Enter fullscreen mode Exit fullscreen mode

后来只有“git push”也应该工作。