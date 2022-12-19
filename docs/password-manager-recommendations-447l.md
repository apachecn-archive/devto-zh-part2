# 密码管理器推荐？

> 原文：<https://dev.to/jess/password-manager-recommendations-447l>

密码管理器是人们不愿意随意更换的工具之一，因为这真的很痛苦。我有*数百*的凭证，我没有时间去经历设置新密码的过程，只有在我的密码管理器停机时才会考虑。就像今天。

我们几年前就设置了密码管理器，现在我想知道是不是该换成别的东西了。你有什么建议？

下面是我正在研究的两个开源密码管理器:

## ![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png) [毛茛](https://github.com/buttercup) / [毛茛-桌面](https://github.com/buttercup/buttercup-desktop)

### 🔑跨平台密码和机密库

<article class="markdown-body entry-content container-lg" itemprop="text">

# 金凤花桌面

> 桌面版金凤花- Mac，Linux 和 Windows

[![Buttercup](../Images/60f780841601793e7521e3da59beff16.png)](https://buttercup.pw)[![Latest version](../Images/0e467796b6309bb67a7f4cde3c98d58c.png)](https://camo.githubusercontent.com/f72de2b3548642367b560636214c6ef3dd0ac4c1c4ef7c76782effc6835a15ad/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f7461672f6275747465726375702f6275747465726375702d6465736b746f702e7376673f6c6162656c3d6c6174657374)[![Chat securely on Keybase](../Images/5affe31dc7d1f2fd08b14bc71c02b7fc.png)](https://keybase.io/team/bcup)[![Discuss on Reddit](../Images/b792d23a78c45435a3cab795e506af97.png)T11】](https://www.reddit.com/r/bcup/)

[![Buttercup Desktop](../Images/26b17353abfbe143b779f57c0fbd3815.png)T2】](https://github.com/buttercup/buttercup-desktoppreview.png)

## 关于

Buttercup 是一个免费的、开源的、跨平台的**密码管理器**，建立在 NodeJS 上，带有 Typescript。它使用强大的行业标准加密来保护您的密码和凭证(以及您存储在金凤花保管库中的其他数据)，在保管库文件(`.bcup`)中处于静止状态。可以从许多来源加载和保存 Vaults，例如**本地文件系统**、 **Dropbox** 、 **Google Drive** 或任何支持 **WebDAV** 的服务(如 *ownCloud* 或 *Nextcloud* )。

### 为什么你需要密码管理器

当你在线时，密码管理是一个重要的工具。至关重要的是，你所有的在线账户都要使用强而唯一的密码，这样它们就更难被攻破。即使您的一个帐户被攻破，拥有唯一的密码意味着攻击者获得进一步访问您的…

</article>

[View on GitHub](https://github.com/buttercup/buttercup-desktop)

## ![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png) [ bitwarden ](https://github.com/bitwarden) / [服务器](https://github.com/bitwarden/server)

### 核心基础设施后端(API、数据库、Docker 等)。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Bitwarden](../Images/f9000eec1d8f74722e58c1a6086065b9.png)T2】](https://github.com/bitwarden/brand/blob/master/screenshots/apps-combo-logo.png)

[![Github Workflow build on master](../Images/e95706459117e85ceb192fcc3b92d1e5.png)](https://github.com/bitwarden/server/actions/workflows/build.yml?query=branch:master)[![DockerHub](../Images/77d4a37b5187f1e6331379409a38b081.png)](https://hub.docker.com/u/bitwarden/)[![gitter chat](../Images/bdce587f624da682601b7dae5687e465.png)](https://gitter.im/bitwarden/Lobby)

* * *

Bitwarden 服务器项目包含所有 bitwarden 客户端应用程序的“后端”所需的 API、数据库和其他核心基础设施项目。

服务器项目是使用 C#编写的。网芯与 ASP.NET 芯。数据库是用 T-SQL/SQL Server 写的。代码库可以在 Windows、macOS 和 Linux 发行版上跨平台开发、构建、运行和部署。

## 构建/运行

请阅读[设置指南](https://github.com/bitwarden/server/blob/master/SETUP.md)以获得设置您自己的本地开发服务器的逐步指南。

### 要求

*   [。NET 5.0 SDK](https://dotnet.microsoft.com/download)
*   [SQL Server 2017](https://docs.microsoft.com/en-us/sql/index)

这些依赖项可以免费使用。

### 推荐的开发工具

*   [Visual Studio](https://www.visualstudio.com/vs/) (Windows 和 macOS)
*   [Visual Studio 代码](https://code.visualstudio.com/)(其他)

这些工具可以免费使用。

### 应用程序接口

```
cd src/Api
dotnet restore
dotnet build
dotnet run 
```

访问[http://localhost:4000/alive](http://localhost:4000/alive)

### 身份

```
cd src/Identity
dotnet restore
dotnet build
dotnet run 
```

访问 [http://localhost:33657/。知名/openid 配置](http://localhost:33657/.well-known/openid-configuration)

## 部署

[![docker](../Images/6a1e326a1f2a59f29086bba068a78a5c.png)T2】](https://hub.docker.com/u/bitwarden/)

您可以在 Windows、macOS 和 Linux 发行版上使用 Docker 容器部署 Bitwarden。使用提供的 PowerShell 和 Bash 脚本…

</article>

[View on GitHub](https://github.com/bitwarden/server)