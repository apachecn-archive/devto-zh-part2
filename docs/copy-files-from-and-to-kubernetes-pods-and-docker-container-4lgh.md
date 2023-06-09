# 从 Kubernetes pods 和 Docker 容器中复制文件

> 原文：<https://dev.to/mfahlandt/copy-files-from-and-to-kubernetes-pods-and-docker-container-4lgh>

我经常希望在我的本地设置中也有一些来自我的 mongo 的数据库转储。实现这一点非常容易。你可以复制文件或整个文件夹。
我们需要的是 kubectl 命令行工具。

### 将文件从 pod 复制到机器上

```
kubectl cp {{namespace}}/{{podname}}:path/to/directory /local/path
eg:
kubectl cp mongo-0:/dump /local/dump 
```

Enter fullscreen mode Exit fullscreen mode

### 将文件复制到 pod

```
kubectl cp /local/path namespace/podname:path/to/directory 
eg:
kubectl cp /local/dump mongo-0:/dump 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是，这和 docker 一样好用。只需将 kubectl 改为 docker，它也能工作。

### 将文件从 docker 容器复制到您的机器上

```
docker cp containerID:/path/to/directory /local/path
eg:
docker cp mongo-0:/dump /local/dump 
```

Enter fullscreen mode Exit fullscreen mode

### 将文件复制到 docker 容器中

```
docker cp /local/path containerID:path/to/directory
eg:
docker cp /local/dump mongo-0:dump 
```

Enter fullscreen mode Exit fullscreen mode

为什么我写这个明显的帖子？我不时地分层查找正确的语法，只看我的博客文章就能加快速度。