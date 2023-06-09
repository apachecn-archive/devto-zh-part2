# 使用 AWS Lambda 和无服务器框架部署. NET 核心 Web API

> 原文：<https://dev.to/schwamster/deploy-a-net-core-web-api-with-aws-lambda-and-the-serverless-framework-3762>

使用 API Gateway 触发的 lambdas 可以做的一件很酷的事情是，您可以简单地让您的代码处理路由。这应该适用于所有的无服务器堆栈，我已经在 nodejs 中使用过了(查看[这篇](https://dev.to/adnanrahic/how-to-deploy-a-nodejs-application-to-aws-lambda-using-serverless-2nc7) dev.to 文章解释了这如何适用于 nodejs)。

当然，你也可以去定义许多独立的函数，并为每个函数创建一个新的 API 网关资源/方法。我真正喜欢的是运行你的整个应用程序，而不是一个功能一个功能地运行，因为你可以首先运行现有的应用程序，而不用做很大的改变。实际上，你可以在 lambda 运行时，在一个容器中，或者通过按 F5 键来运行同一个应用程序...

第二，当涉及到你已经建立的标准时，你不必重新发明轮子。net 核心 Web APIs，例如健康检查端点、日志记录标准和所有其他您真正欣赏的中间件。

已经有很多教程可以帮助您设置。net 核心 Web API 与 lambda。但是它们使用 Visual Studio 模板或。net 核心 cli 模板。对我来说，这个模板的问题是它们不依赖于一个工具，而这个工具对于我来说是无服务器的必备工具——无服务器框架。所以在这篇文章中，我想解释如何用无服务器框架在 lambda 上建立一个. net 核心 Web API。这不是很复杂，但我不会详细介绍其他教程中涉及的内容。

最终的结果会是这样的:

[![api_gw_lambda_aspnetcore](img/28773925d5f128de588fbac0f2483294.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0cg0HsCj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1be13u90bvutctdgh7ow.png)

## 先决条件

你应该已经准备好了一些东西

*   [AWS 账户](https://aws.amazon.com/console/)

*   [Node.js 和 npm 已安装](https://nodejs.org/en/download/)

*   [。安装了 net Core SDK 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1)

*   [安装无服务器框架](https://serverless.com/framework/docs/providers/aws/guide/installation/)

## 创建 API

创建一个希望代码驻留并运行的新文件夹:

```
dotnet new webapi 
```

您可以通过运行以下命令，然后打开[http://localhost:5000/API/values:](http://localhost:5000/api/values:)来测试它是否在本地工作

```
dotnet restore
dotnet run 
```

## 向其中添加无服务器框架

添加名为 serverless.yml 的文件，其内容如下:

```
service: exampleservice

provider:
  name: aws
  runtime: dotnetcore2.1
  region: eu-west-1

package:
  artifact: bin/release/netcoreapp2.1/deploy-package.zip

functions:
  api:
    handler: exampleservice::exampleservice.LambdaEntryPoint::FunctionHandlerAsync
    events:
     - http:
         path: /{proxy+}
         method: ANY 
```

如果你已经在使用无服务器框架，你应该熟悉文件的大部分内容。我们正在添加一个 API 网关，它有一个通配符 path = > {proxy+}，并且处理程序指向一个尚不存在的函数。
(把地区换成对自己有意义的)。

## 添加附加依赖项

打开您的 csproj 文件，并将其更改为如下内容(不要忘记之后运行 dotnet restore):

```
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" Version="2.1.0" />
    <PackageReference Include="Amazon.Lambda.Core" Version="1.0.0" />
    <PackageReference Include="Amazon.Lambda.Serialization.Json" Version="1.4.0" />
    <PackageReference Include="Amazon.Lambda.AspNetCoreServer" Version="2.1.0" />
  </ItemGroup>

</Project> 
```

！显式指向特定版本的 Microsoft.AspNetCore.App 非常重要。默认情况下，dotnet new webapi 命令不会这样做。

## 添加 lambda 入口点(处理程序)

除了在本地启动时处理应用程序初始化的 Program.cs 文件之外，我们还需要为 lambda 添加一个处理程序。检查我们之前创建的 serverless.yml 文件以供参考。serverless.yml 中的处理程序必须完全正确-> {assemblyname}::{namespace}。{class}::FunctionHandlerAsync。创建一个名为 LambdaEntryPoint.cs 的文件:

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using System.IO;

namespace exampleservice
{
    public class LambdaEntryPoint : Amazon.Lambda.AspNetCoreServer.APIGatewayProxyFunction
    {
        protected override void Init(IWebHostBuilder builder)
        {
            builder
                .UseStartup<Startup>();
        }
    }
} 
```

## 为您的应用程序添加构建/打包脚本

在打包 lambda 之前，您必须安装 dotnet-lambda cli 工具:

```
dotnet tool install --global Amazon.Lambda.Tools --version 3.0.1 
```

对于 windows 系统，添加一个名为 build.ps1 的文件:

```
dotnet restore
dotnet lambda package --configuration release --framework netcoreapp2.1 --output-package bin/release/netcoreapp2.1/deploy-package.zip 
```

如果你在 debian(比如 docker image microsoft/dotnet)中运行这个，添加 build.sh:

```
#!/bin/bash

#install zip on debian OS, since microsoft/dotnet container doesn't have zip by default
if [ -f /etc/debian_version ]
then apt -qq update
  apt -qq -y install zip
fi dotnet restore
dotnet lambda package --configuration release --framework netcoreapp2.1 --output-package bin/release/netcoreapp2.1/deploy-package.zip 
```

通过为您的环境运行构建脚本来测试构建，例如

```
.\build.ps1 
```

！每次代码发生变化时，您都必须运行它。当您将它作为 CI/CD 管道的一部分运行时完全没问题，因为无论如何您都必须执行该脚本，但是如果您只是随意使用它并从您的机器上进行部署，就很容易忘记。

## 发布您的应用程序！

应该就是这个了！是时候部署应用程序了。这里假设您已经按照无服务器安装说明进行了安装，并设置了您的 AWS 帐户(这可能意味着您必须调整您的 serverless.yml 文件，以便为您工作):

```
serverless deploy -v 
```

这应该会产生类似这样的结果:

[![serverless-log](img/8d44dc2457ab69f280c30c0ec500e8bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gvb26h1L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xfq6xey3mdxyxntget6v.png)

复制突出显示的链接，用 api/values 替换{proxy+}，您应该会得到和以前一样的结果！恭喜你。

这是一篇写得很快的文章！如果您遗漏了某些步骤的信息，请添加评论，这样我会更清楚。

范例知识库可以在这里找到:[https://github.com/schwamster/lambda_webapi_serverless](https://github.com/schwamster/lambda_webapi_serverless)