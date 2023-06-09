# 在 Azure 上运行 Java

> 原文：<https://dev.to/yuriburger/running-java-on-azure-1jn0>

Azure 是微软的云平台。它是服务应用、逻辑应用、云存储、Kubernetes 服务的家园，并为 VSTS(现在的 Azure DevOps)、Office 365 和其他服务和工具的负载提供基础。

[![](img/e8b89d08e364ac1aae7e06e24869b695.png)T2】](https://www.yuriburger.net/2018/11/05/running-java-on-azuimg/javalovesazure1.png)

但不仅仅是为了。基于. NET 的服务和应用程序。今天的微软为 Linux 开发者、OSX 团队、Docker 容器、Python 代码、Node.js 和许多不同的工作负载提供了选择。在这个系列文章中，我将探索一些 Azure 服务和基于 Java 的解决方案选项。

### 运行 Java 应用程序

Azure(或任何其他云平台)最好的部分是运行工作负载的能力。出于许多不同的原因，云已经将公司从运行自己的数据中心推向云。那么你在微软 Azure 上运行 Java 应用有什么选择呢？

#### Azure Web Apps

它的全称是 Azure App Service Web Apps，是一个托管 Web 应用程序、REST APIs 和移动后端的服务。你可以用你最喜欢的语言来开发。网，。NET Core、Java、Ruby、Node.js、PHP 或者 Python。这个平台有两种风格:基于 Windows 的和基于 Linux 的。您可以使用 Linux 上的 App Service 在 Linux for Java(当前为 8.0)、基于 Spring 和 Apache Tomcat 应用程序上本地托管 web 应用程序。

[![](img/445c0ab79dbb9933a4e5df905dc76979.png)T2】](https://www.yuriburger.net/2018/11/05/running-java-on-azuimg/java-hello-world-in-browser.png)

这方面的信息很多，微软甚至为 [Eclipse](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-get-started-java) 和 [IntelliJ](https://docs.microsoft.com/en-us/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app?view=azure-java-stable) 创建了不同的演练！

#### Docker 与 Web 应用程序的容器

如果 Linux 的默认 App Service 不支持您的应用程序运行时依赖项，您也可以使用您定制的 Docker 映像来部署/创建一个。你可以从 Docker Hub 或一个私有的 Azure Container Registry 中提取容器映像，Web App for Containers 会在几秒钟内将带有你的首选依赖项的容器化应用部署到生产中。该平台会自动处理操作系统修补、容量供应和负载平衡。如需更多信息，请尝试此【Java 快速入门

#### Azure 容器实例

Azure 容器实例提供了一种在 Azure 中运行单个容器的快速而简单的方法。没有虚拟机的麻烦，没有要管理的操作系统，也没有流程编排。这项服务最适合小型应用程序。您可以使用 bash (CLI)、Azure 门户或 PowerShell 来部署您的容器。更多信息，请尝试这篇关于创建部署容器的教程。

#### 蓝色立方服务

成熟的托管 Kubernetes。作为一个托管的 Kubernetes 服务，Azure 为你处理健康监控和维护等关键任务。Kubernetes 大师赛由 Azure 管理。您只需管理和维护代理节点。该选项适用于所有类型的应用程序:单节点、多节点、集群、关键任务、基于微服务、自动扩展等。关于[产品页面](https://docs.microsoft.com/en-us/azure/aks/)的更多信息

#### 蔚蓝市场

对通过 [Azure Market Place](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=java&page=1) 可获得的基于 Java 的产品进行快速搜索，发现了许多在 Azure 上运行 Java 的选项:

[![](img/31f4d6cc9b578c514cd331e65f1ad223.png)T2】](https://www.yuriburger.net/2018/11/05/running-java-on-azuimg/javaazuremarketplace.png)

#### 使用数据工厂的 Azure 数据块

好吧，这是完全不同的东西。Azure Data Factory 是微软用于编排数据管道的云解决方案。把它当作你的数据 ETL 服务:)Azure Databricks 是微软基于 Apache Spark 的分析服务。 [![azure-databricks-overview](img/e446a578d8d3a949e795c8f9f348a829.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b5bhZG5n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://allthingssharepoint.files.wordpress.com/2018/11/azure-databricks-overview.png) 所以，假设你有一个编译成. jar 的 Java 代码，用于数据管道中的某些任务(例如数据分类)，你可以在 Azure Databricks 上使用 Azure Data Factory 来部署它。如果您对大规模数据处理感兴趣，这是对您的工具集的一个很好的补充。更多信息:[https://docs . Microsoft . com/en-us/azure/azure-data bricks/what-is-azure-data bricks](https://docs.microsoft.com/en-us/azure/azure-databricks/what-is-azure-databricks)

#### 但是等等，还有！

微软在 Azure 上运行基于 Java 的工作负载有很多选择，但这肯定不是全部。在接下来的两篇文章中，我将回顾在 Azure 上开发和运行 Java 解决方案的选项。

/Y。