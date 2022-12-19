# 我在红帽的 16 个月

> 原文：<https://dev.to/jpinkney/my-16-months-at-red-hat-3kmf>

多伦多大学提供一个名为专业工程实习(PEY)的实习项目。每年学生都会加入一家公司 16 个月，以获得经验(无论是在软件开发还是其他领域)。去年/今年(2017 年 5 月-2018 年 8 月)我在一家名为红帽的软件公司实习。Red Hat 是一家为不同的开源项目贡献了大量资源的公司(你可能会因为 [Fedora](https://getfedora.org) 或 [RHEL](https://en.wikipedia.org/wiki/Red_Hat_Enterprise_Linux) 而知道它们)。所有的实习生都被分配到一个不同的开源项目中工作。在我的例子中，我做了很多不同的事情，主要是 YAML 语言服务器、Eclipse Che 和 Eclipse 忒伊亚。

最初，我开始在 YAML 文件的 YAML 语言服务器上工作。对于那些不熟悉语言服务器的人来说，它基本上是一个语言智能提供者，为编程语言或文件格式集成了自动完成、转到定义、查找所有引用等功能[1]。语言服务器通过语言服务器协议工作，该协议指定在不同事件期间将哪些数据发送到服务器。此外，它还定义了哪些结果将作为响应从服务器发送回客户机。例如，如果您正在编辑一个 YAML 文件，并且希望在特定位置自动完成，客户端将向服务器发送一个完成请求，服务器将使用有效的完成来响应。我在 YAML 语言服务器项目上大部分时间都是独自工作，所以我负责创建所有的功能(悬停、验证、自动完成等)。它花了大约 4 个月才发布，从那以后下载量一直稳步增长。我目前在 Red Hat 兼职，所以我仍然维护它。

在 YAML 语言服务器发布后，我开始在 Eclipse Che 上工作。对于没有体验过 Eclipse Che 的人来说，它与传统的桌面 Eclipse 有很大的不同(它们是两个完全不同的产品，都在 [Eclipse foundation](https://www.eclipse.org/org/foundation/) 下)。首先，Eclipse Che 是一个基于云的工作空间管理工具和 IDE。这意味着您可以随时随地在浏览器中访问您的工作区/代码，并提供 docker 映像和所有已安装的内容，因此您不必为您的 IDE 做任何配置。另一方面，传统的 Eclipse 是一个极其重量级的 IDE。10 多年来，它一直是领先的 Java IDE 之一，具有多种多样的特性，并且只专注于桌面环境。我为 Eclipse Che 所做的大部分工作都集中在语言服务器集成上，并确保预定义的堆栈工作正常。为了确保预定义的堆栈正常工作，我必须学习大量的 docker，并理解每个 docker 映像中需要的需求。例如，对于 java 栈，我们希望确保它使用的映像安装了最新的 Java 工具。在 Eclipse Che 的语言服务器集成方面，我专门从事并维护了 YAML 语言服务器集成，并帮助完成了 Java 语言服务器集成。

现在我正在忒伊亚月食项目上工作。Eclipse 忒伊亚也是一个云 ide，但是在几个不同的方面与 Eclipse Che 不同。Eclipse Che 是一个非常重量级的 IDE，使用 Java 构建，而 Eclipse 忒伊亚是一个轻量级的 IDE，使用 typescript 构建。此外，Eclipse Che 是一个工作区管理+ IDE 组合，而 Eclipse 忒伊亚只是一个 IDE。Eclipse 忒伊亚基于 Monaco(支持 VSCode 的编辑器)，目前正在 Eclipse 忒伊亚内部模拟 VSCode API。这意味着有一天你应该能够在云中运行你的 VSCode 扩展。就我的工作而言，我主要是通过添加一些特性来增加对 Java 的支持，比如查看外部 Java 库、配置类路径、查找接口的实现。最近，我被要求帮助模拟 VSCode 在忒伊亚的一个端点。

好奇者链接:
【1】-[https://langserver.org](https://langserver.org)

[https://microsoft.github.io/language-server-protocol/](https://microsoft.github.io/language-server-protocol/)
T3】https://github.com/redhat-developer/yaml-language-serverT5】T6】https://github.com/eclipse/cheT8】T9】https://github.com/theia-ide/theia