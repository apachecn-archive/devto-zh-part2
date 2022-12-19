# 用于管理 Kubernetes 的一些有用的 CLI 工具

> 原文：<https://dev.to/joehobot/few-helpful-cli-tools-for-managing-kubernetes--372d>

当我第一次使用 Kubernetes 时，我学到的一件事是管理多集群和 kube 配置并不容易。因此，随着时间的推移，我不断寻找 github 和工具，以满足我提高生产率和避免人为错误的需要。

虽然有些人喜欢为多个 kube 配置路径设置他们的环境变量，但我实际上讨厌这样，因为一个小小的错误就可能让你不知不觉地处于不同的环境中。因此，我在一个 kube 配置中拥有我所有的集群信息。

对于这个问题，我一直在使用一个 cli 工具，称为

我可以通过点击标签在集群之间切换，因为我使用 zsh 和 kubernetes 模块，它可以确保我在正确的集群上。

[![alttexthere](../Images/2643a83fb9afff9bd302aa77542c2202.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mAypF5o0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/ahmetb/kubectx/raw/master/img/kubectx-demo.gif)

当处理多集群时，也有许多名称空间。当然，我可以继续做类似
的事情

```
kubectl get pods -n someNamespaces 
```

这有点无聊，而且当你执行下面的命令时，它会咬你一口

```
kubectl create -f /yeya/this/is/for/qa.yaml -n ProdNamespace 
```

因此，我没有附加- namespace SomeNamespace，而是选择始终位于我需要的名称空间中，工具名为

Kubens -由与上述相同的 GH 作者制作，易于 brew 安装。

[![kubens](../Images/cdf0ee45518c8ee7d752c563561e9996.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wYgC1Ry6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/ahmetb/kubectx/raw/master/img/kubens-demo.gif)

日志很重要，要在 Kubernetes 的世界中查看它们，你要么执行下面的命令，查看日志的历史负载，要么使用一个日志聚合器，比如 Graylog、Sumologic 等等。

```
kubectl logs MyPodIsAwesome -n namespace 
```

但是..是的，总是有但是..如果您不知道 pod 的确切名称，或者想查看以名称 nginx 开头的所有 pod 的日志，该怎么办..下一个 CLI 工具是赢家..它跟踪 k8s 日志中想要显示的字符串。这是我的一个同事介绍给我的..

[船尾](https://github.com/wercker/stern)。

顺便说一句，还有一个，它叫做 kube-shell。如果你是 kubernetes 新手，对它不太熟悉，或者只是忘记了命令，但知道它是如何启动的，这是一个非常好的工具。Kube-Shell 是 K8s 的自动完成 CLI 工具。

这就是我目前为你提供的所有内容，请随意分享一些你最喜欢的 kubernetes 工具，顺便说一下，我在 YT 频道上对这些工具做了一个快速演示，我应该很快就会更新。

[https://www.youtube.com/embed/Lb9giCXBVGY](https://www.youtube.com/embed/Lb9giCXBVGY)