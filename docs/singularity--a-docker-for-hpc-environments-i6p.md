# singularity:HPC 环境的“码头”

> 原文：<https://dev.to/grokcode/singularity--a-docker-for-hpc-environments-i6p>

[![](img/a5eb51d8b765f5f310f8f3f71aed6380.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3PtcI0Cv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yzafdxak1nf3fh9q1jvw.png)

如今，企业面临着应用数字化转型的压力，但受到现有应用和基础架构的限制，同时还要对多样化的云、数据中心和应用架构组合进行合理化。

市场上已经发布了许多不同的容器平台，总体而言最著名的是 Docker，这是一个非常强大的容器平台，可以完美地用于“几乎”所有应用程序用例。我说“差不多”，是因为 Docker 面向微服务虚拟化。

然而，在 HPC(高性能计算)环境中，调度资源是一个基本特性，它可以在很大程度上决定系统的性能。这种类型的应用程序在各种领域运行广泛的计算密集型任务，一些示例包括量子力学、天气预报、气候研究、石油和天然气勘探、分子建模、生物大分子和物理模拟。使用这类应用程序的中心和公司不能冒险在他们的环境中运行 Docker，因为它根本不适合他们的用例。这种不兼容性的一些主要原因如下:

**安全问题** : HPC 环境是多用户系统，用户只能访问自己的数据。虽然 Docker 赋予了超级用户权限，但它并没有避免访问其他用户数据的能力，并因此获得了对集群和计算资源的控制。Singularity 通过允许不同的特权机制来避免这个安全问题:

**Root 拥有的守护进程**:这种机制最类似于 Docker，因为有一个 Root 拥有的后台进程允许管理容器并在容器内产生作业。允许用户通过 IPC 控制套接字访问守护程序，以便与 root 拥有的守护程序进程进行通信。

**对根用户**的有限使用:这种机制只允许根用户运行容器。

**用户名称空间**:这种机制允许一个用户虚拟地成为另一个用户，运行一组有限的特权系统功能。Singularity 原生支持用户名称空间，可以完全“无根”或无特权运行，当然这些特性受到严重限制。

SetUID : Singularity 支持“老派”的 UNIX 方法来运行具有升级权限的特定程序。这种机制在支持的特性和传统兼容性方面提供了很大的灵活性。

**功能集**:使用功能集，您可以按进程和每个文件设置权限。这是 SetUID 的替代方案，因为它允许更细粒度的功能控制。

便携性 :
奇点的另一个令人惊叹的特性是便携性。您可以将您的容器映像带到您的环境中，其中包含您需要的所有科学应用程序和工具，并将其从一个系统带到另一个系统。Docker 在这个问题上使用了图层，使得图像更难共享。

在支持之前所有需求的必要性中，奇点诞生了。它是由 Sylabs 和一个在这个平台上持续工作的社区开发的。第一次公开发布是在 2016 年 4 月，它得到了世界各地不同公司和研究小组的大量采纳。事实上，这个强大的工具得到了 HPC Wire 团队的关注，也被评为 2017 年最值得关注的技术之一。

我们刚刚开始触及深度学习和人工智能的皮毛。这仍然是一个新的未开发的领域，预计至少在未来 20 年内会迅速发展。Singularity 已经是学术界运行与 ML、DL 和 AI 相关的 HPC 工作负载的标准事实，它部署在全球数百个超级计算机研究中心。奇点是希望进入这些新兴领域的企业的最佳选择，因为它在研究世界和企业世界之间建立了一座桥梁，并允许最新的研究进展自由流入企业产品。新的颠覆性技术要求企业的 IT 基础设施快速适应，但这与 IT 部门保持稳定性、安全性和性能的需求相冲突。Singularity 通过在应用程序和底层操作系统之间引入一个抽象层来解决这个问题，同时提供一个安全的通道来直接访问 ML、DL 和 AI 所需的计算资源，没有性能损失，也不损害安全性。