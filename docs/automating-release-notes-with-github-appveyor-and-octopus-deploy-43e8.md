# 使用 GitHub、AppVeyor 和 Octopus Deploy 实现发行说明自动化

> 原文：<https://dev.to/michaeljolley/automating-release-notes-with-github-appveyor-and-octopus-deploy-43e8>

随着多个客户、项目、截止日期、发布时间表同时运行，很难跟上在一个版本中发布的特性。为了帮助管理构建发布说明，我们的团队构建了一个 PowerShell 脚本，它访问 GitHub & Octopus Deploy API 来确定在最新部署的发布和当前构建之间发生了什么提交。

## 背景

在大多数情况下，我们对客户使用相同的 CI 流程。

*   每个提交都内置于[应用程序](https://www.appveyor.com)
*   根据分支/标签，构建的工件被加载到 [Octopus Deploy](https://octopus.com/)
*   章鱼将所有信息发送到合适的环境中

## 设置

为了让所有的东西都协同工作，我们必须设置每个系统协同工作。

#### 章鱼调遣

我假设您已经在 Octopus Deploy 中设置了环境，所以我就不赘述了。您从 Octopus 中真正需要的唯一东西是我们稍后将在 PowerShell 脚本中使用的一些数据。你需要:

*   你的八达通服务器的网址
*   用户名和密码
*   API 密钥
*   我们将用于部署的项目的名称
*   用于生产的环境的名称

#### GitHub

像 Octopus 一样，我们只需要一些信息，就可以通过 PowerShell 脚本访问 GitHub API。你需要:

*   存储库所有者的姓名
*   存储库的名称
*   个人访问令牌([来自 GitHub](https://blog.github.com/2013-05-16-personal-api-tokens/) 的 How-to)

#### 储存库

我们的存储库通常会有一个包含 release-gen.ps1 文件的 *build* 文件夹。然后，在我们的 appveyor.yml 中，我们将在每个构建过程中添加以下内容:

```
install:
  - ps: Invoke-Expression ./build/release-gen.ps1; 
```

在 release-gen.ps1 文件的顶部是您需要根据我们上面提到的项目进行设置的变量。

```
$global:github_owner = "GitHub Owner Name Here"
$global:github_repo = "GitHub Repo Name Here"
$global:github_token = "GitHub Personal Access Token"

$global:octopus_url = 'Url to Octopus Deploy'
$global:octopus_username = "Octopus Deploy Username"
$global:octopus_password = ConvertTo-SecureString "Octopus Deploy Password" -AsPlainText -Force
$global:octopus_apikey = "Octopus Deploy API Key"
$global:octopus_projectName = "Octopus Deploy Project Name"
$global:octopus_productionEnvironment = "Name of Production Environment in Octopus Deploy" 
```

## 汇集一切

现在一切都设置好了，让我们来谈谈脚本和它的作用。这其实是一个简单的过程，可以节省我们很多时间。

在 AppVeyor 中开始构建时，脚本调用您的 Octopus Deploy API，并确定将哪个版本(应该对应于 GitHub 中的一个标签)部署到生产环境中。然后，它调用 GitHub API，并获得当前提交或标签与生产环境中的当前版本之间的所有提交的列表。

然后，有了提交列表，我们就可以构建发布说明的 HTML 和 markdown 版本。HTML 版本与构建工件一起传递给 Octopus。降价版本与构建工件一起被发送到 GitHub。

<figure>[![](../Images/95f8b153894926dc3492e633f0f18f3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IELcVxdm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1228996/48307475-14befb80-e513-11e8-85fb-b50ec28751b2.png) 

<figcaption>降价版</figcaption>

</figure>

<figure>[![](../Images/55bb3588b84675f3e586426370f60799.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ifZ3Tvpd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1228996/48307489-69fb0d00-e513-11e8-8f8c-a86359d90494.png) 

<figcaption>HTML 版</figcaption>

</figure>

## 重要提示

#### 跳过提交消息

脚本会查找某些提交消息，但不会添加到您的发行说明中。任何以“merge”、“merging”或“private”开头的提交都不包括在内。这允许我们的开发人员在任何没有完成问题的提交前加上“Private:”前缀，以确保它们不会包含在发行说明中。

当最终提交时，开发人员将输入一个质量提交消息，准确描述他们的工作已经完成了什么。

#### 发布数字

我们的团队致力于在我们的最终提交中添加关闭消息，当他们关闭一个问题时。(即做了某种修复。关闭#430)

该脚本识别关闭# {问题编号}或修复# {问题编号}的模式，并修改它以使问题编号成为 GitHub 中该问题的链接。因此发行说明中的行既有指向提交的链接，也有指向提交消息中指出的任何问题的链接。

### 下载完整脚本

你可以在 https://github.com/MichaelJolley/release-notes-script 查看完整的剧本。请随意发布你看到的任何可以帮助他人的改进。