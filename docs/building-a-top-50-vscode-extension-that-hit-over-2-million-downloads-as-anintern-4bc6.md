# 作为实习生，构建了下载量超过 200 万次的前 50 名 VSCode 扩展

> 原文：<https://dev.to/jpinkney/building-a-top-50-vscode-extension-that-hit-over-2-million-downloads-as-anintern-4bc6>

自从我开始从事 [YAML 语言服务器](https://github.com/redhat-developer/yaml-language-server)和 [VSCode-YAML](https://github.com/redhat-developer/vscode-yaml) 的工作已经有一年多了，我认为写一写它是如何完全靠运气和一点毅力从每个版本几百次下载增长到每个版本几十万次下载的会很有趣。

### 背景

在我开始在 Red Hat 实习时，我被要求专门为 Kubernetes 开发 YAML 工具。有人告诉我，该工具将利用语言服务器协议，我们将为几个代码编辑器编写集成，从 VSCode 开始。

最初的想法是创建一个 Kubernetes 语言服务器，消除编写 Kubernetes YAML 文件的一些痛苦。我们将通过提供诸如验证、自动完成、文档符号等特性来做到这一点。随着开发的深入，决定转向通用语言服务器，以支持整个 YAML，而不仅仅是 Kubernetes 的 YAML。因此，我们能够引入另一个新特性:通过 JSON 模式进行验证。这意味着用户能够插入他们自己的定制 JSON 模式，并根据它来验证他们的 YAML。换句话说，您可以使用语言服务器来帮助编写 Ansible、Kubernetes 和 Travis CI YAML 文件。

既然我们已经构建了语言服务器，我们需要将它集成到代码编辑器中。我们从 VSCode-YAML 开始，这是一个 VSCode 扩展，它启动 YAML 语言服务器并在 VSCode 中向用户公开首选项。

经过几个月的开发，发布的时候到了。

### 释放；排放；发布

在发布后的几周内，有数百名新开发人员下载了该扩展，并向 GitHub repo 提交了一些 bug 和功能请求。随着下载量的增加，我的责任也越来越大。我不仅要维护现有的功能，还要创建用户要求的任何增强功能，并创建新的版本。

最初，VSCode-YAML 并不流行，因为当时 VSCode 市场上有一个竞争扩展。与之竞争的扩展安装了大约 2 万个，看起来我们无法与之竞争。幸运的是，通过开源的力量，我最终联系上了那个扩展创建者。简而言之，他告诉我，他想从事其他项目，不想继续他的工作，如果我们的 YAML 语言服务器支持文档格式，那么他将关闭他的扩展。在我们开始支持文档格式化后，他取消了发布。完全出于运气，它给了我们足够的动力成为推荐安装在 VSCode 上的 YAML 扩展。

大约一周之内，我们的下载量从每天数百次增加到数千次。首先，我们的扩展出现在今天的市场趋势上，然后是本周的市场趋势，最后是每月的市场趋势。在那之后，扩展开始起飞，完成了大约 155，000 总下载量的版本。非常幸运的是，我们能够从少数人使用的东西扩展到成千上万人使用的东西。

目前，YAML 语言服务器集成到一些支持语言服务器协议的不同客户端中。它首先通过扩展集成到 VSCode 中，然后是 Eclipse Che、Atom、Neo-Vim，最近是忒伊亚。

[在市场上查看 vs code-YAML](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml)

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

VSCode-YAML 和 YAML 语言服务器仍在积极开发中。如果你对这个项目感兴趣，并愿意作出贡献，让我知道！

另外，如果你对这种类型的内容感兴趣，请给这个帖子一颗心:)