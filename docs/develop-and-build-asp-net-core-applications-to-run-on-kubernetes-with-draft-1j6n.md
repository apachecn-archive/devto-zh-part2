# 开发和构建 ASP.NET 核心应用程序，并使用 Draft 在 Kubernetes 上运行

> 原文：<https://dev.to/cmendibl3/develop-and-build-asp-net-core-applications-to-run-on-kubernetes-with-draft-1j6n>

您开始开发一个 ASP.NET 核心应用程序，在 Kubernetes 上运行，突然您发现自己创建了一个 docker 文件，构建了一个映像，将该映像推送到一个注册中心，为 Kubernetes 创建了一个部署和服务定义，您想知道是否有一个工具可以帮助您简化整个过程。

这是一个工具，它不仅可以帮助您创建在 Kubernetes 中构建和运行应用程序所需的工件，还可以构建和部署容器映像。

## 先决条件:

1.  一个 Kubernetes 集群和一个 kube CTL CLI 工具
2.  在您的 Kubernetes 集群上安装[头盔](https://github.com/Kubernetes/helm)
3.  集装箱登记处

## 安装和配置草稿

为了安装草稿，我运行以下命令:

```
cinst -y draft 
```

Enter fullscreen mode Exit fullscreen mode

然后配置:

```
draft config set registry <registry full name> 
```

Enter fullscreen mode Exit fullscreen mode

请务必登录到容器注册表:

```
docker login <registry full name> 
```

Enter fullscreen mode Exit fullscreen mode

或者如果使用 Azure 容器注册表:

```
az acr login --name <acrName> 
```

Enter fullscreen mode Exit fullscreen mode

## 创建并准备申请

创建一个 ASP.NET 核心 MVC 应用程序，并用 Draft:
创建工件

```
dotnet new mvc
draft create --pack=csharp -a mvcdraft 
```

Enter fullscreen mode Exit fullscreen mode

注意，我已经指定了两个参数:

*   **pack** 告诉 draft 我们使用 C#作为语言。
*   指定我们将在 Kubernetes 上运行的 Helm 版本的名称

## 部署并测试您的应用

运行以下命令来构建容器映像，然后将应用程序部署到 Kubernetes:

```
draft up 
```

Enter fullscreen mode Exit fullscreen mode

第一次运行该命令需要一段时间。输出应该类似于这样:

```
Draft Up Started: 'netcoredraft': 01CWKAZ79WR6W66PHHR2AFRSGC
netcoredraft: Building Docker Image: SUCCESS ⚓ (1.0008s)
netcoredraft: Pushing Docker Image: SUCCESS ⚓ (3.2891s)
netcoredraft: Releasing Application: SUCCESS ⚓ (2.7629s)
Inspect the logs with `draft logs 01CWKAZ79WR6W66PHHR2AFRSGC` 
```

Enter fullscreen mode Exit fullscreen mode

测试应用程序运行:

```
draft connect 
```

Enter fullscreen mode Exit fullscreen mode

输出应该类似于:

```
Connect to netcoredraft:80 on localhost:50998
[netcoredraft]: No XML encryptor configured. Key {ea9dac08-e7af-468c-9e00-cbe38ab40fa8} may be persisted to storage in unencrypted form.
[netcoredraft]: Hosting environment: Production
[netcoredraft]: Content root path: /app
[netcoredraft]: Now listening on: http://[::]:80
[netcoredraft]: Application started. Press Ctrl+C to shut down. 
```

Enter fullscreen mode Exit fullscreen mode

浏览到输出中显示的 url(即 [http://localhost:50998](http://localhost:50998) )，您应该可以开始了！！！

## 打扫卫生

一旦你完成了你的测试，你可以用下面的命令来清理:

```
draft delete 
```

Enter fullscreen mode Exit fullscreen mode

希望有帮助！！！