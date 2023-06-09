# 如何:使用 alphie.io 通过 5 个步骤创建微服务 MVC 应用程序

> 原文：<https://dev.to/alphie/how-to-create-a-microservice-mvc-application-in-5-steps-with-alphieio-4h6f>

开始总是最难的部分，事实上，知道从哪里开始可以让人停下脚步。

为了帮助您，我们有 5 个步骤来创建您的第一个微服务 MVC 应用程序，您可以将其作为模板进行编辑，根据需要进行扩展，并使用其他服务进行扩展。

**第一步:登录或注册**

转到[https://platform . alphie . io](https://platform.alphie.io)

**步骤 2:创建新服务**

使用 alphie，您可以从预定义的模板结构中进行选择，以帮助您快速入门。在侧边菜单上选择`"build from template"`，然后选择`"c# MVC Web Application"`。给它起一个名字，比如“webportal ”,然后单击 build

**第三步:创建环境**

一旦你的新服务建立起来，你需要一个地方来发布它。选择“环境”菜单下的“添加和管理”。

给你的环境一个名字和一个你想让它运行的 URL。Alphie 为每个服务运行一个标准的方法`{service_name}-{environment}-{username}.alphie.io`,这样所有的服务都是唯一的。

别忘了点击“保存”。

**第四步:启动**

现在，转到服务菜单下的`"launch business function"`，从下拉菜单中选择您的新服务和您的环境。继续点击`"launch"`。

**第五步:测试&管理**

现在，服务已经启动并开始运行，是时候进行测试和管理了。点击服务菜单下的`"manage"`项。在这里，您可以看到您的服务的唯一 URL。继续点击它，这会打开一个新的窗口，很快就会有一个漂亮的新的 MVC web 应用程序。

从这里您可以开始编辑`"source code"`下的代码。这将为您提供惟一的 GIT 实例的登录细节和 URL。一旦您编辑了代码并提交，Alphie 将重新编译并创建一个新版本，您可以在准备就绪时启动它。

现在去抓住他们！