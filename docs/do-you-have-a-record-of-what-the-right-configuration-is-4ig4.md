# 您是否有正确配置的记录？

> 原文：<https://dev.to/thaiwood/do-you-have-a-record-of-what-the-right-configuration-is-4ig4>

除非您刚刚起步，否则您已经有服务器在做它们需要做的事情。它们在数据中心进行配置和构建，或者在云中启动。无论哪种方式，都有一定数量的配置有助于塑造它如何工作。仅仅因为你或你的团队配置了一次，并不意味着它被完全记录或者甚至是正确的。向您自己和您的团队提出的核心问题是:“如果需要，您是否拥有在最短的时间内创建该服务器的相同版本所需的所有信息？”这可能是灾难恢复的一部分。灾难性的事情发生了，你需要做一个新的服务器。你知道那意味着什么吗？或者这可能是好事的一部分，也许你正在成长，想要一个新的应用服务器，或者更强大的东西。您如何确保新服务器拥有旧服务器的所有特性、配置和功能？

如果你没有这些问题的答案，不要担心，现在收集它们还为时不晚。

在恢复或重新创建这些信息时，需要问一些问题:机器上必须运行什么软件才能完成工作？要让该软件按照您需要的方式工作，需要提供什么配置？是否需要在机器上创建特殊用户？例如，对于自动化或支持，机器上是否有需要设置或取消的限制或配额？它需要什么操作系统？有具体的版本或者补丁级别吗？应用程序需要存在或不存在哪种网络连接和防火墙规则？

一旦掌握了这些信息，您不仅可以拥有更强大的灾难恢复流程，还可以开始为拥有代码形式的基础架构和自动化铺平道路。

有了这些信息，您将能够更快地应对灾难，减少停机时间和恢复时间。您将为基础架构本身的自动化打开大门。

此外，您将能够使用这些信息来回答问题和解决问题。也许你需要遵守审计标准。有了记录和访问的所有配置，您就可以开始回答诸如“我们使用的是安全版本的操作系统吗？”，“我们的防火墙规则是过于宽松还是过于严格？”

已经这么做了？太好了！下一步是使用[基础设施作为代码](https://dev.to/thaiwoodhere/what-is-infrastructure-as-code-and-why-should-you-use-it-51k7-temp-slug-8181978)，自动化这种配置和供应。

你怎么想呢?留下评论。想看更多这样的点击这里:[https://thaiwood.io/DevTo](https://thaiwood.io/DevTo)