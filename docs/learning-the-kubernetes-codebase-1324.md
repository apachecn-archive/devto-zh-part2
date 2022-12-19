# 学习 Kubernetes 代码库

> 原文：<https://dev.to/chuck_ha/learning-the-kubernetes-codebase-1324>

```
Date: July 5th, 2018
Most recent Kubernetes release: 1.11.0 
```

Enter fullscreen mode Exit fullscreen mode

Kubernetes 是一个庞大的软件，大约有 200 万行代码。开始可能有点吓人，但是一旦你掌握了窍门，你会惊奇地发现有多少票开始变得有意义，以及你如何快速识别和修复错误！

## 最开始

如果你刚刚开始你的 Kubernetes 之旅，我会鼓励你阅读[这个文档](https://kubernetes.io/docs/concepts/overview/components/)并对主要组件有一个高层次的理解。要称之为 Kubernetes 集群，它需要在至少一个节点上运行这里列出的大多数组件。方便的是，组件的所有代码都存储在 [Kubernetes monorepo](https://github.com/kubernetes/kubernetes) 中。

## 第一步

现在您已经知道了每个组件的作用，您可以选择一个您感兴趣的组件。如果你在挑选一个组件时遇到困难，我喜欢做的一件事就是挑选离我的舒适区最远的组件。例如，如果人际关系是你的弱项，选择 kube-proxy。如果你有网络能力，但并不真正做 web 服务器，选择 kube-apiserver。

## 一脚接着一脚

每个程序员都需要一个好的代码编辑器。阅读代码时对我帮助最大的特性是跳转到定义。我在典型的 Go 插件中使用了 VSCode，这满足了我的要求。您可以使用 vim、Goland 或 emacs，只要您对该工具感到满意，并且可以有效地在函数之间切换，您就可以开始使用了。

## 一小步

库伯内特住在 https://github.com/kubernetes/kubernetes 的 T4。通常，github 上的 Go 代码的导入路径是没有方案的 URL(https://)。所以你可能期望`go get -d github.com/kubernetes/kubernetes`能够工作，但是这会混淆你的工具。包名其实是`k8s.io/kubernetes`。所以继续使用这个命令`go get -d k8s.io/kubernetes`克隆回购协议。如果您没有安装 go，请按照[golang.org](https://golang.org/)上的说明进行操作。

## 又一小步

用可信的代码编辑器打开 Kubernetes 目录。看看里面的`cmd/`。在这里，您可以找到 Kubernetes 内部所有组件的*入口点*。Kubernetes 中的每个组件都作为命令行程序运行，并且[引用](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/)都是在线的。这里是`kube-proxy`的[入口点是](https://github.com/kubernetes/kubernetes/blob/release-1.11/cmd/kube-proxy/proxy.go)的地方。`cmd/<component>/<component>.go`的这种模式在每一个组件中都将非常相似。

## 永远重复

现在开始浏览文件吧！你在`cmd/<component>/<component>.go`下打开的第一个文件可能会非常小。通读每一行。你知道它是做什么的吗？如果你去看看[这一行](https://github.com/kubernetes/kubernetes/blob/release-1.11/cmd/kube-proxy/proxy.go#L38)上函数调用的定义，你会意识到这段代码有多深。你有很多书要读！

现在这样做，直到你对自己的理解满意为止。这需要一段时间，这是完全正常的。这东西不好捡。见鬼，你甚至可以发一两篇帖子，谈谈你发现的有用的、有趣的兔子洞，以及你发现的关于 Kubernetes 代码库的令人惊讶的事情！

如果您试图进入 Kubernetes 开发，没有比阅读大量代码更好的方法了！