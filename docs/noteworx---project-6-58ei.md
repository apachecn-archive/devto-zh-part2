# 注 Worx -项目 6

> 原文：<https://dev.to/drminnaar/noteworx---project-6-58ei>

## 概述

在过去的几个月里，我用 6 种不同的方式构建了一个简单的笔记应用程序。这个项目的目标是建造一些简单的东西，但是用不同的方式，使用不同的技术来建造。到目前为止，前 5 个 NoteWorx 项目都是使用 Javascript 和 Javascript 框架以及 MongoDB 数据库构建的。对于第六个 NoteWorx 项目，我决定使用跨平台的 dotnetcore 框架、C#和 PostgreSQL 数据库。

该项目是一个参考项目，将阐明以下概念:

*   使用 C#、ASP.NET 核心身份和实体框架核心构建端到端 ASP.NET 核心 web 应用程序。
*   创建一个蛋糕构建脚本来管理项目构建。
*   使用 Docker 和 Docker-Compose 来管理用于托管 web 应用程序和 postgres 数据库的容器。
*   如何使用 NPM 和 Gulp 来管理客户端资产(css，javascript 等)。
*   使用 PostgreSQL 作为所有注释和身份数据的数据库。

NoteWorx(项目 6)的源代码可以在[这里](https://github.com/drminnaar/noteworx-aspnetcore)找到

之前的 NoteWorx 项目如下:

*   注 worx-cli-fs

一个基本的笔记应用程序，它使用 CLI(命令行界面)前端来捕获和管理笔记，并使用文件系统来存储笔记

*   noteworx-cli-mongodb

一个基本的 notes 应用程序，使用 CLI(命令行界面)前端来捕获和管理 notes，使用 mongodb 来存储 notes。

*   noteworx-CLI-mongose

一个基本的 notes 应用程序，它使用 CLI(命令行界面)前端来捕获和管理 notes，使用 Mongoose ODM 来管理 mongodb 交互，使用 MongoDB 来存储 notes。

*   noteworx-cli-couchbase

一个基本的 notes 应用程序，使用 CLI(命令行界面)前端来捕获和管理 notes，并将 couchbase 作为数据存储。

*   noteworx-CLI-express-MongoDB

一个基本的 notes 应用程序，它使用 CLI(命令行界面)前端来捕获和管理 notes，使用 express 构建 express note 管理 API，使用 Mongodb 来存储 notes。

*   [noteworx-expressui-MongoDB](https://github.com/drminnaar/noteworx-expressui-mongodb)

一个基本的 notes 应用程序，使用 Express 前端来捕获和管理 notes，使用 mongodb 来存储 notes。

*   [noteworx-react-mongodb](https://github.com/drminnaar/noteworx-react-mongodb)

一个基本的 notes 应用程序，使用 React frontend 来捕获和管理 notes，使用 ExpressJS 编写的 api，使用 mongodb 来存储 notes。

## 什么是 NoteWorx？

NoteWorx 是一个基本的笔记管理 web 应用程序。这个版本的 NoteWorx(项目 6)，已经使用最新的[ASP.NET 核心 2.1](https://www.asp.net/) 、[ASP.NET 核心身份](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity)、[实体框架核心](https://docs.microsoft.com/en-us/ef/core/)和 [Postgresql](https://www.postgresql.org/) 构建。

NoteWorx 提供以下功能:

*   添加注释
*   编辑便笺
*   删除便笺
*   列出所有笔记
*   按标题或描述查找笔记

## 发达了

这个版本的 NoteWorx 的目标是演示如何完全在 Linux 操作系统上构建端到端的 dotnetcore 应用程序。因此，整个应用程序是在 [Ubuntu 18.04](https://www.ubuntu.com/) 上使用 [Visual Studio 代码](https://code.visualstudio.com/)构建的。下表总结了用于构建应用程序的主要工具、语言和框架:

*   [。网芯](https://www.microsoft.com/net/download) -。NET Core 是一个适用于 Linux、Windows 和 macOS 的免费开源托管软件框架。

*   C# -一种多范例编程语言，包含强类型、命令式、声明式、函数式、泛型、面向对象(基于类)和面向组件的编程原则。

*   ASP.NET 核心是一个免费的，跨平台的，开源的网络框架

*   [ASP.NET 核心身份](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity)-ASP.NET 核心身份是一个会员系统，为 ASP.NET 核心应用增加了登录功能。

*   [实体框架核心](https://docs.microsoft.com/en-us/ef/core/) - EF 核心是一个对象关系映射器(O/RM)，支持。NET 开发人员使用。NET 对象。它消除了开发人员通常需要编写的大部分数据访问代码。

*   Postgresql 是一个对象关系数据库管理系统，强调可扩展性和符合标准

*   [Docker](https://www.docker.com) -用于托管 Postgresql 数据库

*   Docker-Compose 是一个定义和运行多容器 Docker 应用程序的工具。

*   Cake (C# Make)是一个跨平台的构建自动化系统，使用 C# DSL 来完成诸如编译代码、复制文件和文件夹、运行单元测试、压缩文件和构建 NuGet 包等任务。

*   [Bootstrap 4](https://getbootstrap.com) -构建响应迅速、移动优先的项目

*   Gulp 是一个工具包，用于自动化开发工作流程中痛苦或耗时的任务，因此你可以停止浪费时间去构建一些东西。

### 值得注意的 Nuget 包

除了前面提到的工具，我想特别提一下我发现非常有用的两个 Nuget 包。

*   [OdeToCode。UseNodeModules](https://github.com/OdeToCode/UseNodeModules)——ASP.NET 核心中间件，用于服务项目根目录下 node_modules 目录中的文件。这个包使我能够从我的 Razor 视图中引用*‘node _ modules’*文件夹。这对发展非常有利。我有*【吞咽】*任务，将所需的包从*【node _ modules】*文件夹复制到*【wwwroot】*文件夹中进行生产。

*   [人性化。核心](https://github.com/Humanizr/Humanizer) -人性化满足你所有的。NET 需要操纵和显示字符串，枚举，日期，时间，时间跨度，数字和数量。这个包使我能够以一种更“人性化”的方式显示日期和时间。例如，Humanizer 不是以日期格式显示一周前的日期，而是获取日期并返回“一周前”。人性化可以用得更多。

## 入门

这些说明将为您提供一个项目的副本，并在您的本地机器上运行，用于开发和测试目的。

### 先决条件

您的系统需要安装以下软件:

*   点网核心

需要版本 2.1.402。

从终端运行以下命令来验证 dotnet 的版本:

```
 dotnet --version 
```

Enter fullscreen mode Exit fullscreen mode

*   NodeJS

需要以下版本的节点和 Npm:

*   节点 8.x
*   Npm 3.x

在终端中键入以下命令，以验证您的节点和 npm 版本

```
 node -v
  npm -v 
```

Enter fullscreen mode Exit fullscreen mode

*   码头工人

需要版本 18.06。

从终端运行以下命令来验证 Docker 的版本:

```
 docker -v 
```

Enter fullscreen mode Exit fullscreen mode

### 安装

按照以下步骤运行开发环境。

1.  从 GitHub 克隆“noteworx-aspnetcore”存储库

```
 git clone https://github.com/drminnaar/noteworx-aspnetcore.git 
```

Enter fullscreen mode Exit fullscreen mode

*或者使用 ssh*

```
 git clone git@github.com:drminnaar/noteworx-aspnetcore.git 
```

Enter fullscreen mode Exit fullscreen mode

1.  安装节点模块

```
 cd noteworx-aspnetcore/src/NoteWorx.Web
   npm install 
```

Enter fullscreen mode Exit fullscreen mode

### 打造

有两种构建选项:

*   使用 dotnet CLI 工具构建

```
 cd noteworx-aspnetcore
  dotnet build 
```

Enter fullscreen mode Exit fullscreen mode

*   使用 Cake 构建脚本构建

```
 # linux terminal
  cd noteworx-aspnetcore
  ./build.sh 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下， *Gulp* 处理*“Gulp file . js”*文件。gulp 进程准备要包含在 wwwroot 文件夹中的客户端资产。

### 运行

NoteWorx 应用程序被配置为用测试数据作为其数据库的种子。因此，要登录 NoteWorx 应用程序，可以使用在 *'/NoteWorx 中指定的任何用户。web/infra structure/seed files/users . JSON '*文件。所有用户的密码是*‘P @ ss word 123！’*。

#### 使用 Docker 在容器中运行 NoteWorx 演示

如果您只想看到应用程序在演示模式下运行，请在解决方案根目录下的终端中键入以下命令。

```
cd noteworx-aspnetcore
docker-compose -f ./docker-compose-demo.yml build
docker-compose -f ./docker-compose-demo.yml up 
```

Enter fullscreen mode Exit fullscreen mode

上述命令将基于 Dockerfile 构建所需的映像，在容器中设置 postgres 数据库，并在容器中设置 pgAdmin (postges db tool)。

要观看演示，请在浏览器中键入以下内容:

```
# To view the NoteWorx demo application
http://localhost:8080

# To open the database admin tool (pgAdmin)
http://localhost:8081 
```

Enter fullscreen mode Exit fullscreen mode

运行完演示程序后，键入以下命令来停止并清理容器。

```
docker-compose -f ./docker-compose-demo.yml down 
```

Enter fullscreen mode Exit fullscreen mode

#### 从源代码运行 NoteWorx

在从源代码运行 NoteWorx 之前，需要有一个正在运行的 Postgres 数据库实例。为此，您可以键入以下命令来启动 Postgres 容器和 pgAdmin 容器。

```
cd noteworx-aspnetcore
docker-compose -f ./docker-compose-data.yml -up 
```

Enter fullscreen mode Exit fullscreen mode

接下来，键入以下命令来验证 Postgres 数据库已经在容器
中启动

```
# You should see a container having the name 'db-dev' by typing the following
docker container ls 
```

Enter fullscreen mode Exit fullscreen mode

您需要确保在 *'appsettings 的连接字符串设置中配置了正确的 IP 地址。Development.json'* 文件。因此，键入以下命令来确定 postgres 容器(db-dev)的 IP 地址。

```
# use the following putput IP address in connection string
docker container inspect | grep -w IPAddress 
```

Enter fullscreen mode Exit fullscreen mode

接下来，运行 NoteWorx web 应用程序

```
cd noteworx-aspnetcore/src/NoteWorx.Web
dotnet run 
```

Enter fullscreen mode Exit fullscreen mode

应用程序启动后，打开 web 浏览器并键入以下地址:

```
http://localhost:5000 
```

Enter fullscreen mode Exit fullscreen mode

运行完应用程序后，键入以下命令停止并清理容器。

```
docker-compose -f ./docker-compose-data.yml down 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

在线 JSON 数据生成器[www.json-generator.com](https://www.json-generator.com/)用于生成所有用于播种 NoteWorx 应用程序的数据。

### 模板

以下模板用于生成数据。

#### 用户模板

```
[
  '{{repeat(10)}}',
  {
    picture: 'http://placehold.it/32x32',
    age: '{{integer(20, 40)}}',
    name: '{{firstName()}} {{surname()}}',
    gender: '{{gender()}}',
    company: '{{company().toUpperCase()}}',
    email: '{{email()}}',
    phone: '+1 {{phone()}}',
    address: '{{integer(100, 999)}} {{street()}}, {{city()}}, {{state()}}, {{integer(100, 10000)}}',
    about: '{{lorem(1, "paragraphs")}}'
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

#### 标签模板

```
[
  '{{repeat(100)}}',
  {
    value: '{{lorem(1, "words")}}'
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

#### 备注模板

```
[
  '{{repeat(50)}}',
  {
    title: '{{lorem(random(3,4,5), "words")}}',
    description: '{{lorem(1, "paragraph")}}',
    createdAt: '{{date(new Date(2018, 0, 1), new Date(), "ISODateTimeTZ")}}',
    modifiedAt: function() {
      var createdDate = new Date(this.createdAt);
      var modifiedDate = new Date(createdDate);
      modifiedDate.setDate(createdDate.getDate() + Math.floor(Math.random() * Math.floor(10)));
      return modifiedDate;
    }
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

接下来，我将使用一个有角度的前端构建 NoteWorx，该前端使用 JWT 令牌连接到 ASP.NET 核心 API。