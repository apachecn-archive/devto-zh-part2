# GitHub 中 Mono-Repos 的 Azure 部署

> 原文：<https://dev.to/sebnilsson/azure-deployment-of-mono-repos-in-github-27jc>

随着[微服务](https://en.wikipedia.org/wiki/Microservices)越来越多地被采用，有时[讨论](https://hackernoon.com/one-vs-many-why-we-moved-from-multiple-git-repos-to-a-monorepo-and-how-we-set-it-up-f4abb0cfe469)的[问题](https://www.quora.com/What-companies-use-a-monorepo-but-deploy-as-microservices)之一是 [*单回收*与*多回收*](http://blog.shippable.com/our-journey-to-microservices-and-a-mono-repository) ，它试图回答你的代码结构是让**一个大型、单一的存储库**用于所有/多个微服务，还是让**单个、小型的存储库**用于每个微服务。

如果你在使用*单一回购*的场景中，你在一个单一回购中有多个项目，并且你想**在 Azure** 中将这个单一代码库部署到多个应用和服务，有一个简单的方法可以做到这一点。

Azure 应用服务中 git 部署背后的引擎 [Kudu](https://github.com/projectkudu/kudu) ，有关于如何[定制部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)的[文档](https://github.com/projectkudu/kudu/wiki)，但是**这些方法中只有一种能很好地与*单一回购*-场景**一起工作。不幸的是，使用`.deployment`-文件的方法将指定项目的配置锁定在您的源代码中，因此**对于*单重存储*来说**是行不通的。

相反，[使用所有](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) [Azure 应用服务](https://docs.microsoft.com/en-us/azure/app-service/web-sites-configure)中可用的*应用设置* ，你使用键`PROJECT`到**设置你想要部署到你的不同 Azure 应用服务的项目**的路径值。因此，该解决方案适用于功能应用、Web 应用、API 应用等等。

## 举例

如果您的单个存储库中有以下代码结构:

*   `/src/ExampleProject/ExampleProject.csproj`
*   `/src/ExampleProject.FunctionsApp/ExampleProject.FunctionsApp.csproj`
*   `/src/ExampleProject.WebApiApp/ExampleProject.WebApiApp.csproj`
*   `/src/ExampleProject.WebApp/ExampleProject.WebApp.csproj`
*   `/test/ExampleProject.Tests/ExampleProject.Tests.csproj`

然后你可以在*应用程序设置*中为不同的应用程序服务设置你的`PROJECT`路径，如下所示:

*   **功能 App** : `"src/ExampleProject.FunctionsApp"`
*   **网络应用** : `"src/ExampleProject.WebApp"`
*   **API App** : `"src/ExampleProject.WebApiApp"`

用相对于 Git-repository 的根路径的**路径。如果在同一个文件夹中有歧义的风险，你也可以直接指向`.csproj`-文件。**

完成后，你可以[使用相同的库，在 GitHub、Bitbucket、本地 Git 或 Azure 支持的任何其他源中，配置你的 Azure 应用服务用于连续部署](https://docs.microsoft.com/en-us/azure/app-service/app-service-continuous-deployment) **。**