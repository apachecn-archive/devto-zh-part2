# 运行在 OpenShift 上的 MongoDB Enterprise

> 原文：<https://dev.to/mongodb/mongodb-enterprise-running-on-openshift-76k>

为了参与竞争并将产品快速推向市场，如今的企业利用云就绪和云支持技术。平台即服务(PaaS)提供开箱即用的功能，使应用程序开发人员能够专注于他们的业务逻辑和用户，而不是基础设施和互操作性。这一关键能力将成功的项目与那些淹没在永不停止的无关紧要的工作中的项目区分开来。

在这篇博客文章中，我们将介绍 MongoDB 的通用 PaaS 和云支持策略，以及 Red Hat 的 OpenShift 的一些新特性，这些特性使您能够运行生产就绪的 MongoDB 集群。我们也很兴奋地宣布在 OpenShift 上运行的 MongoDB Enterprise Server 的开发者预览版。这个预览版允许您测试您的应用程序将如何与运行在 OpenShift 上的 MongoDB 交互。

### MongoDB 和 PaaS 的集成方法

平台即服务越来越受欢迎，尤其是对于那些负责构建“支持云”或“云就绪”应用程序，但目前需要使用私有数据中心部署的人。需要适当地将数据库与 PaaS 集成，以确保数据库实例可以被正确地部署、配置和管理。

任何生产就绪的云数据库部署都有两个常见组件:

*   一个现代化的、坚如磐石的数据库(比如 MongoDB)。
*   支持遥测、访问和授权以及备份的工具(更不用说与您选择的问题跟踪系统集成的主动警报、用于自动化的完整的基于 REST 的 API，以及到托管服务的无缝过渡)。)对于 MongoDB，这是 [MongoDB 运营经理](https://www.mongodb.com/download-center?jmp=partners_OpenShift#ops-manager)。【T2![](img/c5c6cfb82d636d8e013a2579b866b7d6.png)

MongoDB Ops Manager 的深度集成是我们将 MongoDB 与流行的 PaaS 产品集成的核心。一般的设计方法是使用“关注点分离”的设计原则。所选择的 PaaS 处理物理或虚拟机、CPU 和 RAM 分配、持久存储需求和机器级访问控制，而 MongoDB Ops Manager 控制运行时数据库部署的所有方面

该策略使系统管理员能够在自己的数据中心内快速部署“MongoDB 作为解决方案”产品。反过来，企业开发人员可以轻松地自助服务他们自己的数据库需求。

如果您还没有，下载 [MongoDB Ops Manager](https://www.mongodb.com/download-center?jmp=partners_OpenShift#ops-manager) 以获得运行 MongoDB 的最佳方式。

### MongoDB 企业服务器 OpenShift 开发者预览版

我们在 OpenShift 上的 MongoDB“开发者预览”可以在这里找到:[https://github . com/jasonmimick/MongoDB-open shift-dev-preview](https://github.com/jasonmimick/mongodb-openshift-dev-preview)。预览版允许直接通过 OpenShift 提供 MongoDB 副本集和“仅代理”节点(便于以后作为 MongoDB 实例使用)。部署自动向 [MongoDB Ops Manager](https://www.mongodb.com/download-center?jmp=partners_OpenShift#ops-manager) 的实例注册自己。所有的技术细节和入门说明都可以在回购中找到。这里我们将只描述一些功能和使用的技术。

预览版需要访问运行版本 3.7 或更高版本的 OpenShift 集群，并利用新的 Kubernetes 服务目录特性。具体来说，我们正在使用 [Ansible Service Broker](https://github.com/openshift/ansible-service-broker) 并且已经构建了一个 [Ansible Playbook Bundle](https://github.com/ansibleplaybookbundle/ansible-playbook-bundle) ，它将一个图标安装到你的 OpenShift 控制台中。预览版还包含一个支持副本集和类似功能的 OpenShift 模板示例。

### 巡视并部署您的第一个集群:

一旦您准备好了开发环境(参见开发者预览版 Github 资源库中的注释)并配置了一个 [MongoDB Ops Manager](https://www.mongodb.com/download-center?jmp=partners_OpenShift#ops-manager) 实例，您就可以开始部署 MongoDB Enterprise Server 了。

<center>![](img/d284938a325dc181d44f909d8e0c5ba8.png)</center>

可以通过 OpenShift web 控制台或命令行来提供集群。web 控制台提供了一个直观的“类似向导”的界面，用户可以在其中指定各种参数的值，例如 MongoDB 版本、存储大小分配和 MongoDB Ops Manager 组织/项目等等。

命令行安装也是可用的，其中参数值可以是脚本化的或预定义的。这种可扩展性允许自动化以及与各种持续集成和持续交付技术的集成。

未来的帖子将详细介绍集群配置和各种管理场景，如升级、性能调优和故障排除连接，敬请关注。

我们很高兴推出简单高效的方法，通过 OpenShift 和 Kubernetes 等工具来管理您的 MongoDB 部署。请试用开发者预览版，并在 Twitter #mongodb-openshift 或电子邮件[bd@mongodb.com](//bd@mongodb.com)上给我们留言了解更多信息。