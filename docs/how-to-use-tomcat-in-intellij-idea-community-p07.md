# 如何:在 Intellij IDEA 社区中使用 Tomcat

> 原文：<https://dev.to/stefanbc/how-to-use-tomcat-in-intellij-idea-community-p07>

最近，我开始从事一个项目，该项目要求我在本地安装后端部分，而不是使用远程后端。该项目使用 Maven 作为构建工具，并将代码部署到 Tomcat 服务器上。

我开始在我的本地机器上配置这个项目。我注意到的第一件事是 Intellij IDEA 社区没有 Tomcat 插件，不像终极版那样有现成的插件。

后来，我在谷歌上搜索了很多次，找到了一些插件，但当我尝试使用它们时，著名的 NullPointerException 发生了，我开始寻找一个替代品。我在 Github 上找到了一些灵感，但不是我想要的形式，但它足以让我开始。

以下是一个简短的教程，介绍如何在 Intellij IDEA 社区中设置 Tomcat 来运行/调试，以及如何使用 Maven 来构建您的项目，然后将其部署到 Tomcat。

## 先决条件

*   安装 Intellij IDEA 社区。
*   安装 Tomcat——在我的项目中，我使用了 Tomcat 8。你可以从[这里](https://tomcat.apache.org/download-80.cgi)得到。
*   安装 Maven。

## Tomcat 配置

确保将 Tomcat 安装在不需要管理员访问的地方，例如项目旁边。

之后，打开`File -> Settings`展开`Tools`选择`External Tools`。

您可以使用任何名称添加新工具。我给我的取名为，雄猫。对于工具设置，选择`catalina.bat`文件，通常位于 Tomcat 安装的`bin`文件夹中。比如我的是`C:\apache-tomcat-8.5.34\bin\catalina.bat`。最后但同样重要的是，用命令`jpda run`设置一个参数。应该自动设置工作目录。

<figure>[![](img/1e2c73095813f66d460990b92b8870c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mArrPcpx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stefancosma.xyz/conteimg/2018/10/n90GnDc.png) 

<figcaption>Tomcat 外部工具</figcaption>

</figure>

## 启动 Tomcat

如果您转到`Tools -> External Tools`并点击 Tomcat，它应该会启动 Tomcat 服务器。

## Maven 构建并部署到 Tomcat

现在您已经完成了 Tomcat 配置，您希望能够使用 Maven 构建并部署到现在工作的 Tomcat 服务器上。

要做到这一点，你需要前往`Run -> Edit Configurations`，点击“+”并选择`Maven`。不要忘记命名您的新配置。选择您的项目位置并添加以下命令行。

```
war:war org.codehaus.mojo:wagon-maven-plugin:upload-single -Dwagon.fromFile=D:\work\project\backend.war -Dwagon.url=file://C:\apache-tomcat-8.5.34\webapps\ 
```

Enter fullscreen mode Exit fullscreen mode

这行代码使用 Maven 构建项目，然后从指定的路径(通常在项目中配置)获取文件，并将其复制到 Tomcat 中的`webapps`文件夹。然后 Tomcat 知道它有了这个文件的新版本，并重新部署它。

<figure>[![](img/15be0be59b9fb828b623ee17ec5d7eda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iMMXgfKR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stefancosma.xyz/conteimg/2018/10/p1AH4PH.png) 

<figcaption>构建并部署到 Tomcat</figcaption>

</figure>

## Tomcat 远程调试

像所有应用程序一样，您希望能够调试后端。为此，您需要像上一步那样添加一个新配置。这次唯一的不同是，当你点击“+”时，你需要选择`Remote`。

为您的配置命名。您需要将调试器模式设置为`Attach to remote JVM`，将传输设置为`Socket`，将主机设置为`localhost`，最后将端口设置为`8000`。这样，当您开始配置时，Intellij 将通过套接字连接到 Tomcat 服务器，该服务器通过端口`8000`进行发送。您可以在 config 文件夹中的 Tomcat `server.xml`文件中将 Tomcat 的端口更改为其他端口。

<figure>[![](img/3eba113b67544148e5c58c80062a6ba0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M4U_1qon--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stefancosma.xyz/conteimg/2018/10/0sqI0hD.png) 

<figcaption>Tomcat 远程调试</figcaption>

</figure>

一旦 Tomcat 服务器启动，您就可以运行这个配置。只需点击`Run -> Debug`，然后选择新创建的配置。

如果您想在调试时保留所有日志的记录，可以通过在新创建的远程配置中使用 logs 选项卡，并将保存控制台输出到文件设置到 Tomcat 安装的`logs`文件夹中来实现。对我来说是`C:\apache-tomcat-8.5.34\logs*.*`。

<figure>[![](img/cea21c1c3896c6687a340b129099cde8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BBU1QQYo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stefancosma.xyz/conteimg/2018/10/BSxR11Q.png) 

<figcaption>Tomcat 远程调试日志</figcaption>

</figure>

就是这样。@ me on Twitter 如果你认为我错过了什么，或者有另外一种方法可以做到这一点，而不需要安装 Eclipse 或购买 Intellij IDEA 的终极版。

直到下一次，代码长，繁荣！