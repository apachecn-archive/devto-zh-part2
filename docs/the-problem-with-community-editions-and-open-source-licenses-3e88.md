# 社区版和开源许可的问题是

> 原文：<https://dev.to/glind/the-problem-with-community-editions-and-open-source-licenses-3e88>

专有 SaaS 项目的社区版、开源版类似于打折仓里过期的科技书籍。一个亏本的领导带你去商店，在后面买贵的东西。是的，从历史的角度来看，它们可能是有趣的，也是值得学习的，但它们很少能帮助你或你的客户完成任何真正的工作。问题很多，解决方案却很少，而且复杂性或开源许可很难提供喘息的机会。

问题
首先，开发者很少有时间或者合适的策略来维护社区版。UI 和 UX 是后来才想到的，库也已经非常过时了。最重要的是，你永远找不到一个托管和支持的社区版实例。所有关于开源软件即服务的坏的刻板印象都来自于社区版；慢，复杂，难学，过时。

解决方案
那么，我们如何摆脱社区版的问题，专注于开源的好处。我们看到的一种方式是通过为不同的平台即服务用例开发开源许可证。在 [Humanitec](https://humanitec.com) ,我们与其他开发人员和 IT 团队共享我们的平台，以便他们可以部署和管理代码，重用我们的核心服务，并免费构建和增强现有的互联微服务。所有开发人员需要做的就是同意继续在我们的平台上构建，在我们的市场上分享和销售，并以开源的方式将这些工作回馈给社区。我们跟踪每一个代码分支，当它在我们的服务中使用时，他们会得到 API 调用收入的一部分。我们的开源版本已经成为我们构建的每个服务的默认版本，我们可以通过基于社区的方法以完全透明的方式进行开发，并且仍然可以找到一种方法来保护原始版本代码的所有权，同时获得收入。

开源的定义对我们来说，剩下的最大问题是技术上不允许我们称之为开源，因为我们需要共享代码但仍然保护原创的许可证类型不符合 opensource.org OSI 规定的“开源”的传统名称。为什么？主要是因为这个项目:

1.  许可证不得专用于某一产品。程序附带的权利不得依赖于程序是否属于某一特定软件分发的一部分。如果程序是从该分发中提取出来的，并在程序许可证的条款范围内使用或分发，那么程序被再分发到的所有各方都应该拥有与最初的软件分发所授予的权利相同的权利。在我们的案例中,“软件分发”是我们构建的平台即服务，我们希望将我们的 Affero GPL 许可证版本绑定到这个平台上。本质上，它与当前的 AGPL 相同，但有一个简单的序言，说明如果你想在我们的平台上构建一些东西，只要保持开源，只要我们的平台存在，就让它与我们的服务和核心一起留在我们的市场中，我们专门为重用微服务而构建。

我们希望这样做，主要是为了让我们的客户、投资者和自己感到高兴，因为他们所做的或支付的工作将在平台上得到公平的再分配。他们会因此获得荣誉，当作品在我们的平台上被重用时，他们会因此获得报酬，但其他人拿走并在他们的平台上分发它的可能性要小得多。他们仍然可以以同样的方式从开源中受益，但是如果他们愿意，他们的工作仍然是他们的，这增加了一点安慰。

**一个新的旧定义**
从某种意义上说，开源的初衷是让开发者分享和相互学习，并在需要时轻松获得帮助，我们认为我们的 AGPL 许可证版本符合真正的意义和意图。我们理解通过限制被认可为“开源”的许可证的数量和类型来避免混淆的必要性。然而，不允许像我们这样的公司分享创新和进步，同时保护足够谋生的知识产权，会使他们无法获得可信的开源许可。最终，当我们试图采用开源的新定义时，这只会带来更多的分裂和混乱。

参考资料:

[https://www . IDG connect . com/abstract/29497/alfresco-founder-commercial-open-source-old-stuff-free](https://www.idgconnect.com/abstract/29497/alfresco-founder-commercial-open-source-old-stuff-free)
T3】https://www . wired . com/2016/03/former-open-sources-ask-companies-pay-fair-share/