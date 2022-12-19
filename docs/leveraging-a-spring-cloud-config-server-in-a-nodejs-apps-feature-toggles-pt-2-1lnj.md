# 在 Node.js 应用程序的特性中利用 Spring 云配置服务器切换(第 2 部分)

> 原文：<https://dev.to/paigen11/leveraging-a-spring-cloud-config-server-in-a-nodejs-apps-feature-toggles-pt-2-1lnj>

[![Laptop screen filled with minified source code](../Images/718db8c3e806f6b2c184596103c611d5.png "Laptop screen filled with minified source code")T2】](///static/1bc85da9de192091881a510c69f4a2e3/eea4a/more-code.jpg)

## 简介

正如我在关于 Spring Cloud Config server 的第一篇文章中所展示的，为一个 Java Spring Boot 应用程序设置一个配置服务器，然后利用这个服务器为现有的 Spring Boot 项目提供环境变量，这真的很方便。

下一个技巧是弄清楚是否有可能在一个节点 js 项目中使用完全相同的配置服务器。

简单介绍一下，我的团队致力于基于微服务的应用架构，这意味着我们有多达 13 个单独的 Java 服务和一个节点服务在运行，为我们的应用提供支持。

使用基于 Java 的配置服务器是完全有意义的，因为:十三个 Spring Boot 微服务，但是当我们的业务合作伙伴决定他们想要实现功能切换时，让配置服务器在节点项目中工作也成为了优先事项。

* * *

## 什么是特性切换，我为什么需要它？

让我给你一个快速的介绍什么是特性切换，为什么它对开发者、商业伙伴和客户如此有益。

> **特性切换**是[软件开发](https://en.wikipedia.org/wiki/Software_development)中的一种技术，它试图提供一种维护多个[源代码](https://en.wikipedia.org/wiki/Source_code)分支(称为特性分支)的替代方法，这样一个特性甚至可以在完成并准备发布之前就被测试。功能切换用于在运行时隐藏、启用或禁用该功能。例如，在开发过程中，开发人员可以为测试启用该特性，并为其他用户禁用它。
> 
> [—维基百科，功能切换](https://en.wikipedia.org/wiki/Feature_toggle)

基本上，特性切换允许开发人员以更敏捷的开发方式向用户引入新特性。

有了功能切换，开发人员可以继续只维护一个代码库，但在生产中向用户显示不同的 UI 视图，而在生命周期的较低阶段，根据切换是设置为“开”还是“关”，向开发团队显示不同的 UI 视图。

它们还允许我们的业务和 UX 合作伙伴尽早验证新功能是否对我们的用户有益，或者他们是否需要重新评估我们如何实现解决方案。

让我把一件事说清楚:**特性切换并不是永久的解决方案。**它们是临时的，仅在功能完成并被验证有用或满足其他成功或失败的标准之前保留。然后，应该删除特性切换周围的代码，以保持代码库的整洁，并尽可能避免技术债务。

对于我的具体情况，我们的功能切换需要隐藏一个按钮，因为支持它的后端服务还没有完成，还没有完整的功能，并对用户隐藏单选按钮。在按钮和服务完成之前，它们是不需要的。

[![The UI a typical user sees (feature toggle hides buttons and checkboxes)](../Images/2b8b0c660683a2d10b96d7d77b063fa3.png "The UI a typical user sees (feature toggle hides buttons and checkboxes)")T2】](///static/097dd5b077051b3987a8884e99515af7/2cefc/feature-toggle-users.png)

用户的功能切换视图:没有按钮和复选框。

上面是用户在产品中看到的 UI 视图:没有按钮，没有复选框。下面是开发人员需要看到的 UI 视图:按钮和复选框。

[![The UI a developer sees (feature toggle shows buttons and checkboxes)](../Images/1ed647385a0ae3b6361146b2e412ed04.png "The UI a developer sees (feature toggle shows buttons and checkboxes)")T2】](///static/cd8585f87c6ca9fa4882efabf18b3f41/2cefc/feature-toggle-devs.png)

功能切换视图为开发者:按钮和复选框。

让我们看看我们是如何同时获得这两个视图的。

* * *

## 设置您的配置服务器属性

在我们到达节点配置服务器之前，让我们首先在我们的配置服务器属性报告中使用以下命名约定来设置新文件:

```
[application name]-[life cycle profile].yml
ex. my-ui-app-to-config-QA.yml 
```

Enter fullscreen mode Exit fullscreen mode

在这个 YAML 文件中，您将能够为您的 UI 特性切换添加配置属性。以下是我必须为我的功能切换包括的所有内容。

```
modifyDatesToggle: true 
```

Enter fullscreen mode Exit fullscreen mode

把这个提交给 Github repo，我们就可以了。你可以在 Github 中看到一个配置属性 repo [的例子。](https://github.com/paigen11/spring-cloud-config-properties-sample)

### node cloud-config-client node . js 设置

为了利用我们的节点前端应用程序设置的现有 Spring Cloud Config server，我选择了 npm 上的 [`nodecloud-config-client`](https://www.npmjs.com/package/nodecloud-config-client) ，因为它是一个为 JavaScript 编写的相当完善的 Spring Cloud Config Server 客户端。

> 我应该提到，这不要与`node-cloud-config-client`或`cloud-config-client`混淆——实际上，为 Node.js 编写的配置客户端包有一百万个版本，但是上面和参考资料链接中列出的是我使用的版本。

因此，言归正传，在将 npm 包保存到我们的 UI 的`package.json`依赖项之后，将其添加到`server.js`文件中(或者您的节点服务器配置所在的任何位置)。

[![Code set up in app.js file showing the cloud-config-client requirement](../Images/9c69fd421d6920af9fd0f1d55717e39a.png "Code set up in app.js file showing the cloud-config-client requirement")T2】](///static/3e4d6fedebe868cce1a95acbd2414d9e/1e043/node-cloud-config.png)

*查看底部的`client`变量——这就是配置客户端发挥作用的地方。*

如果你像我的团队一样在多种环境中开发(本地、QA、Q1、生产等)。)，添加一个变量来访问开发过程所有阶段的 Spring Cloud config server(这些[环境变量服务](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES)是我们在 Pivotal Cloud Foundry 中连接到它的方式)。如果你想了解更多信息，我在我的 **[前一篇](https://www.paigeniedringhaus.com/blog/why-a-spring-cloud-config-server-is-crucial-to-a-good-ci-cd-pipeline-pt-1)** 中提到了这些。

[![Logic in Node server determining how to fetch feature toggles from the Spring Cloud Config server](../Images/b8643e29576d95428286dc1345e0db2f.png "Logic in Node server determining how to fetch feature toggles from the Spring Cloud Config server")T2】](///static/8b2f4460d32b995fd95fc968a5a96c72/610c0/config-logic.png)

*`configServerUrl`对环境变量进行一系列检查，如果没有发现任何环境变量(比如在本地开发期间)，它默认使用硬编码的 URL。我对`configServicesProfile`和`featureToggle`使用了相同的环境变量和逻辑。*

接下来，添加一个变量来说明配置服务概要文件的位置(对于本地开发，我将它默认为 QA)。我添加了一个变量来检查启用/禁用特性的切换。

下面的代码连接到配置服务器(当启用特性切换时)，并将找到的任何特性切换映射到一个对象。

```
{ modifyDatesFeature : true } 
```

Enter fullscreen mode Exit fullscreen mode

然后，该对象被发送到 UI 的其余部分，端点`app.get`被简单地称为`"/featureToggles"`。

[![Node code connecting to config server and checking for feature toggles](../Images/7eda21ee0b71227ce587d59349fe1811.png "Node code connecting to config server and checking for feature toggles")T2】](///static/ec2063dabcc7ff699489c64a0bf017ca/62a6a/node-feature-toggle.png)

*特性标志变量，调用客户端访问配置服务器的正确环境(在我的例子中是 QA)，检查`featureToggle`是否为`‘on’`，映射可用的属性。下面是调用特性的端点。*

太好了。节点服务器已经设置好了，只需更改几个变量，就可以切换特性(或存储在配置服务器中的任何东西)。

### 将客户端 UI 框架连接到节点服务器

您在前端需要做的第一件事是对节点服务器进行 AJAX 调用，以检查任何现有的特性切换。实现这一点的应用程序框架是 JavaScript MVC(一个多年前流行的旧框架，我在使用它之前也从未听说过它)，下面是一个调用的例子。

[![JavaScript client-side code calling for any feature toggles from Node server](../Images/bf8b126ca0c7620f84a02fc6b972e946.png "JavaScript client-side code calling for any feature toggles from Node server")T2】](///static/08fa392466f831d1e687036a78b88e80/000c7/javascript-mvc.png)

在与特性标志相关的 JavaScript 文件中(对我来说，它是一个`dates.js`文件)，我导入了特性切换 AJAX 调用，并创建了一个函数来检查与该功能相关的特定特性标志:`modifyDatesToggle`。

[![Feature toggle code for showing / hiding particular date / time feature](../Images/f544e20ebb9e103928d95aea9b8981c6.png "Feature toggle code for showing / hiding particular date / time feature")T2】](///static/c9c250b288bb39d8f8c12f1643ce353f/5f6dd/js-feature-toggle-code.png)

特性标志被导入到文件的顶部，然后我调用特性标志端点来查看文件是否需要注意什么。如果特性标志与我命名为“modifyDatesToggle”的变量相匹配，它将被提取并应用到文件中。

最后，在使用特性切换的 JavaScript 文件中，根据特性切换是否启用，尽可能对代码进行分组，如下所示。

[![Code modifications that happen when feature toggle is enabled](../Images/79b4f7a20de6580b2f8ee1275d4acfa2.png "Code modifications that happen when feature toggle is enabled")T2】](///static/f74ee8cfe78d761e09949ec07632fb67/3c492/feature-toggle-grouped-code.png)

*例 1:如果`modifyDatesToggle`为`“on.”`* ，此检查显示复选框

思路是，通过将所有启用特性的代码包装在特性切换检查中，即使对配置服务器的调用由于某种原因失败，用户也只能看到应该部署到生产环境中的代码。这给了我们额外的保护，因为我们不需要依赖过多的变量。

* * *

## 但是等等，还有更多——支持端到端测试和特性切换

我还想在这篇博客中介绍一件事:我如何使用相同的功能切换逻辑来运行或跳过一些量角器端到端测试。

如果你正在使用量角器的配置文件，我不知道为什么没有人会这样做，有一个很酷的功能，可以让你制作自己的[自定义参数](https://moduscreate.com/blog/protractor_parameters_adding_flexibility_automation_tests/)。我从自己的书中抽出一页，在`server.js`文件中设置了特性切换，并在量角器配置文件中做了类似的设置:如果为特性切换传入了环境变量，则与该特性相关的测试将会运行，如果没有传入，则它们将被忽略——我稍后将向您展示测试语法，因为这有点不合常规。

[![Feature toggle setup in e2e testing configuration](../Images/b8a18c40dcf2d8d72d28c7c83c93e430.png "Feature toggle setup in e2e testing configuration")T2】](///static/16ebbe609d61f226bf2d751080c234ba/63ec5/feature-toggle-e2e-config.png)

这里是所有量角器测试使用的配置。Params 接受 modifyDatesToggle 对象，如果它存在，就设置 params，如果不存在，就设置为 null。

当您在本地运行测试时，您实际上可以通过命令行传递参数，如下所示:

`bash
$ protractor e2e/conf.js browser.params.modifyDatesToggle=‘true’` 

为了在实际的端到端测试中实现特性切换，在每个需要特性切换检查的测试中，在`‘it’`声明之后添加一个 if 语句，检查特性切换是否为空。正如我所说的，这看起来有点奇怪，但它确实有效。

[![Automated test utilizing feature toggled code](../Images/b2c3b23ca45af454fbe53f006925c3ca.png "Automated test utilizing feature toggled code")T2】](///static/5b10677f0d6acfc3724bddbf96a836d1/a3767/feature-toggle-test.png)

*一个量角器端到端测试的例子，带有一个功能切换检查。并不是所有的测试都需要这种检查——只是那些需要将特性切换为开或关的测试。*

如果没有启用特性切换，测试将被跳过，但是在控制台中显示为通过(排除失败的测试，因为没有合适的环境供测试使用)。

如果启用了特性切换，测试将正常运行。这避免了我们不得不一个接一个地检查测试，或者`x`忽略它们，或者删除`x`以便它们根据期望的功能运行。一旦到了移除特性切换的时候，很容易从`spec`文件中移除不必要的测试，或者仅仅是`x`它们，这取决于您的团队偏好保留测试，即使在功能已经改变之后。(就我个人而言，我会删除它。)

上面，我展示了如何从命令行运行特性切换测试，但是它也可以在部署应用程序时以编程方式运行。

Segue:对我的团队个人来说，我们使用 [Jenkins](https://jenkins.io/) 来做我们所有的构建。这是另一套完整的博客帖子，但可以说，当构建运行时，所有的单元测试都在运行，所有的端到端测试都在运行，在声明一个功能之前，我们已经做好的所有其他检查都已测试，并准备好让我们的业务团队接受。

回到这个问题:由于 Jenkins 是我们的作业运行者，我们可以通过一个`Jenkinsfile`将特性切换测试的环境变量传递给 Jenkins。

[![Enabling feature toggle in Jenkinsfile](../Images/241ea130a56c496625dadb5395c7f1cc.png "Enabling feature toggle in Jenkinsfile")T2】](///static/2aa4b21d04f265f1c5152f0faf43d15f/8cdda/feature-toggles-jenkinsfile.png)

*所需要的只是底部简单的一行:`env.MODIFY_DATES_TOGGLE=true`*

这应该就是您运行端到端测试所需要的一切——特性的开启或关闭。

### 自动化特征切换

正如我之前所说，我的团队在 Pivotal Cloud Foundry 上托管我们的应用程序，在 PCF 中，我们使用称为 VCAP 服务的东西来保存我们的环境变量。通过清单文件，我们可以将这些服务绑定到我们的应用程序，并传入环境变量——如功能切换是否应该打开或关闭的变量。明白我的意思了吗？

对 feature toggle 环境变量的检查使得以自动化方式部署应用程序变得更加容易。通过在每个不同的开发阶段部署的清单中包含一个简单的环境变量(或者不包含),可以很容易地告诉应用程序是否检查配置服务器中的设置(如果需要的话使用它们)。

[![QA manifest that includes feature toggles](../Images/52b5e58686b3796817c866e92a37b0b6.png "QA manifest that includes feature toggles")T2】](///static/1353543796a724400b25393482e96286/3ddad/qa-manifest.png)

*QA 清单截图。*

QA 清单:注意`SPRING_PROFILES_ACTIVE`指向`QA`，`FEATURE_TOGGLE`带有`‘on’`的环境变量。

[![Production manifest that does not include feature toggles](../Images/80162c3fa2c99637be80fe1272d0a951.png "Production manifest that does not include feature toggles")T2】](///static/e043e464058470192b22f20b6cbdea92/e9131/prod-manifest.png)

*生产清单截图。*

生产清单:看不到特性切换，并且`SPRING_PROFILES_ACTIVE`被设置为`production`。

* * *

## 结论

这就是了。现在，您已经看到了如何利用使用 Node.js 应用程序构建的 Spring Cloud 配置服务器。不仅如此，您还可以将端到端测试配置为在打开或关闭特性切换的情况下运行。

如果您在自己的 JavaScript 项目中发现了实现特性切换或其他类似的漂亮东西的其他方法，我很想听听它们。

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢阅读！

* * *

## 进一步引用&资源

*   [维基百科，功能切换](https://en.wikipedia.org/wiki/Feature_toggle)
*   [节点云配置客户端](https://www.npmjs.com/package/nodecloud-config-client)
*   [示例云配置属性报告](https://github.com/paigen11/spring-cloud-config-properties-sample)
*   [围绕环境参数的量角器文件](https://moduscreate.com/blog/protractor_parameters_adding_flexibility_automation_tests/)