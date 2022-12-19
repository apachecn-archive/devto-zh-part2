# 部署 usando GIT-FTP

> 原文：<https://dev.to/rafnixg/deploy-usando-git-ftp-of>

[![Deploy usando GIT-FTP](../Images/91adec3ec4d5d905c0a7bf0b9e3211f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NU-dZism--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1554098415-788601c80aef%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D2000%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

很常见的是，当我们进入 web 开发领域，而又没有钱购买优质服务时，我们选择在一个地方购买空间，或者使用功能基本相同的免费托管服务。

导言

当我们希望将 web 项目部署到这些低成本或免费服务中时，我们遇到了一个限制，即我们无法通过 SSH 进行访问，但可以使用 FTP 帐户。

对于许多没有使用或工作过版本控制的人来说，这并不是问题，如果你使用了 git 或者不知道，我邀请你看一下来自的 [GIT/GitHub](https://platzi.com/cursos/git-github/) 的课程，以便你了解为什么一定要使用它。

但是，如果我们要保持良好的工作流程并跟踪我们的项目，则使用 git 是一项关键任务，而且在部署时，它是一种方便我们使用的工具，这与 FTP 不同，FTP 使部署过程变得非常繁琐和耗时，因为我们必须知道文件已被修改以上传到服务器。

**去 FTP**

此工具解决了这样一个问题，即不是上载所有文件或手动选择要上载到服务器的文件，而是检查自上次更新以来文件是否已更改，然后仅上载这些文件。

使用此工具时，我所想的美极了，因为它显然使我们的工作流程比仅使用 FTP 更好，但并不是所有的事情都那么美好。使用此 FTP 客户端时有一个限制，即直接对服务器所做的更改不会从我们的本地区域中看到，因此我建议您记住这一点

**Instalacion de Git-FTP**

对于我们使用 GNU/Linux 的用户来说，安装非常简单。Debian 特别使用 debian 很容易使用终端进行安装，对于使用其他操作系统的用户来说，我将在此向他们提供“T0”官方安装手册，您可以在该手册中找到以下操作系统的安装:

*   Linux/Unix 操作系统和定制
*   Debian、Ubuntu 等使用 apt
*   ArchLinux
*   麦克·OS X
*   Windows 操作系统

对于我们使用 Debian 或 Ubuntu 的人，我们只需在终端上按键

`sudo apt-get install git-ftp`

这样我们就可以安装 Git-FTP 了

配置

从我们的终端设置非常简单我们使用以下命令:

`git config git-ftp.url ftp.example.net`

`git config git-ftp.user ftp-user`

`git config git-ftp.password secr3t`

现在，我们已经设置了通过 FTP 连接到服务器的方式，只需定位到项目文件夹中，并根据以下两个选项运行相应的命令:

*   上传所有文件(初始化)`git ftp init`
*   指示文件已在服务器上。`git ftp catchup`

**庄园部署**

为了使 deploy 遵循我们使用 git 的正常工作流程，当我们需要将修改后的文件上传到服务器时，我们只需在终端上运行以下命令:

`git ftp push`

这样我们就可以在服务器上找到我们的文件了。

建议

> 对于尚未使用 git 的人来说，参观位于[席](https://medium.com/u/5281ef6d82cd)平台上的 Git/GitHub 课程。
> 
> 始终保持相同的工作流，不直接在服务器上修改文件。
> 
> 阅读 git-FTP-[git-FTP 官方网页](https://git-ftp.github.io/)