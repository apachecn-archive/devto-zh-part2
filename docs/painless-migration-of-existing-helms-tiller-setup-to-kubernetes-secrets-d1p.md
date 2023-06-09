# 现有舵柄设置无痛迁移到 Kubernetes 秘密

> 原文：<https://dev.to/evilmartians/painless-migration-of-existing-helms-tiller-setup-to-kubernetes-secrets-d1p>

通过用行业标准的 [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 替换[舵柄](https://github.com/kubernetes/helm/blob/master/docs/architecture.md#components)的未加密配置图，了解解决[舵柄](https://github.com/kubernetes/helm)安装安全问题的命令行工具。

## 舵及其安全选项

如果你是一个头盔用户，你可能会在官方知识库中偶然发现一篇优秀的[“安全头盔安装”](https://github.com/kubernetes/helm/blob/master/docs/securing_installation.md)文章。如果您不知道这份文件，请花时间阅读它，这是一个很好的起点。

总而言之，在保护您的安装时，有四个要点需要考虑:

1.  基于角色的访问控制，或 RBAC
2.  Tiller 的 gRPC 端点及其 Helm 的使用
3.  分蘖释放信息
4.  舵图

分蘖释放信息是我想进一步谈论的东西。

## 分蘖释放有什么问题？

历史上，对于每个“发布”，基本上是应用程序的任何配置或版本更新，Tiller 会创建一个包含所有操作数据的[配置图](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)。

除了应用程序经常需要敏感数据来操作(密码、令牌)以及配置映射不是存储它们的最佳格式之外，它基本上是可以的:它们按原样保存在 Kubernetes 运行时数据库(Etcd)中，没有任何加密。任何能接触到 Etcd 甚至它的垃圾场的人都可以很容易地窥视里面。这是不允许的。

## Kubernetes 秘密营救

Kubernetes `1.7`中引入了加密存储的 Kubernetes 秘密的选项。Tiller 从版本`2.7.0`开始支持秘密作为后端存储。

如果你正在安装一个新的舵，使用[这里描述的`helm init --override=...`选项](https://github.com/kubernetes/helm/blob/master/docs/install.md#using---override)。

例如:

```
helm init --override 'spec.template.spec.containers[0].command'='{/tiller,--storage=secret}' 
```

Enter fullscreen mode Exit fullscreen mode

*为了简洁起见，我省略了你可能需要的其他`helm init`选项*

## 但是如果你没有那个闲心从头开始呢？

Secrets 对于新的安装来说非常好并且易于使用，但是如果您曾经想要将管理您的一组生产版本的现有集群从 ConfigMaps 迁移到 Secrets 并避免停机，那么您就不走运了。不再是了。

我写了一个小工具，它自动化了令人生畏的手工过程。遇见[舵柄-释放-转换器](https://github.com/dragonsmith/tiller-releases-converter)！

## 使用舵柄-释放器-转换器

在你[安装了应用程序](https://github.com/dragonsmith/tiller-releases-converter#installation)之后，升级你的舵柄设置到秘密存储后端(零停机！)就是按以下顺序执行三个 shell 命令:

```
tiller-releases-converter convert # Will create a Secret for every ConfigMap release
tiller-releases-converter secure-tiller # Will update tiller deployment with "--storate=secret"
tiller-releases-converter cleanup # Deletes tiller's ConfigMaps 
```

Enter fullscreen mode Exit fullscreen mode

有第四个命令叫做`list`——它只是打印 Tiller 的当前配置图:

```
tiller-releases-converter list 
```

Enter fullscreen mode Exit fullscreen mode

输出示例:

```
I've found these Tiller's ConfigMap releases for you:

kube-state-metrics.v1
kube-state-metrics.v2 
```

Enter fullscreen mode Exit fullscreen mode

为了方便起见，这里有一些全局命令行参数:

```
Flags:
      --context string      kube config context
  -h, --help                help for tiller-releases-converter
  -c, --kubeconfig string   config file (default is $HOME/.kube/config)
  -n, --namespace string    tiller namespace (default is kube-system) 
```

Enter fullscreen mode Exit fullscreen mode

## 广而告之

感谢您的阅读！我希望[舵柄释放转换器](https://github.com/dragonsmith/tiller-releases-converter)能为你节省一些宝贵的时间，让你放心。如果您遇到了任何麻烦或者对如何改进该工具有任何建议，请随时提出来！

附言

请记住，一旦[头盔 3](https://github.com/helm/community/blob/master/helm-v3/000-helm-v3.md) 到来，机密迁移的配置图将不再是一个问题:该工具的下一个主要版本将不会包括蒂勒。然而，发布日期尚未公布。跟随官方的[里程碑](https://github.com/kubernetes/helm/milestones)让自己保持在循环中！

在[开发网站](https://dev.to/evilmartians)和[火星编年史](https://evilmartians.com/chronicles)上阅读更多由[邪恶火星人](https://evilmartians.com/)撰写的开发者文章