# 使用 Docker 的命令中心生成器移动到容器

> 原文：<https://dev.to/techcommunity/moving-to-containers-with-command-central-builder-for-docker-3jf8>

# Command Central 的“基础设施即代码”功能如何帮助您完成容器和云之旅

**了解如何使用 Docker 的 Command Central Builder 和 GitHub 的默认模板库为 Software AG 数字业务平台产品构建定制的 Docker 映像。**

| 2018 年第四期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue4/SAG_Moving_To_Containers_TECHniques_Oct18_WEB.pdf) |
| --- | --- |

## 容器、云和命令中枢

Command Central 是一款 DevOps 工具，用于软件 AG 数字业务平台的自动化供应、迁移、配置、管理、资产部署和监控。传统上，它用于物理硬件和虚拟机的内部部署，但并不止于此。Command Central 基于模板的供应和“基础设施即代码”的基本功能适用于容器和不可变基础设施的世界。Command Central Builder for Docker 本机集成到标准的基于 Dockerfile 的构建过程中，并帮助您为运行在 Software AG 产品之上的应用程序采用容器技术。

## 构建过程如何工作

要使用 Docker builder 构建 Docker 映像，您需要:

1.  Dockerfile
2.  命令中心复合模板. yaml 文件
3.  适用于 Linux、Mac 或 Windows 10 的 Docker 引擎
4.  访问 Empower SDC 上或网络中的 Command Central 服务器上托管的 Software AG 产品、修复和许可存储库

**Dockerfile**

builder Dockerfile 是一个标准的 Docker 构建文件，它包括四个控制构建过程的关键指令。让我们逐一回顾一下。

```
FROM softwareag/commandcentral-builder:10.3 as builder RUN $CC\_HOME/provision.sh myTemplate && $CC\_HOME/cleanup.sh FROM centos:7 as base COPY –from=builder /opt/softwareag /opt/softwareag EXPOSE 1234 ENTRYPOINT /opt/softwareag/some/bin/runme.sh<font face="Arial, Open Sans, PT Sans, Lato"><span style="white-space: normal;">
</span></font> 
```

第一条指令(来自……as builder)引入了 Docker builder 工具，它本身就是一个 Docker 映像。该映像是定制构建的，包括产品、修复和许可证密钥存储库的配置，这些存储库在构建过程中由配置操作使用。

第二条指令(RUN provision.sh)运行在 template.yaml 文件中声明的基于模板的配置操作，该文件可以在 Docker 文件旁边提供，也可以预先打包在 Docker 映像的命令中心生成器中。当运行指令完成时，新的 Docker 映像层具有/ opt/softwareag 目录，其中填充了已安装的产品、修复程序和产品运行时的已配置实例，就像针对由 Command Central server 管理的远程虚拟机的传统配置一样。cleanup.sh 脚本删除容器运行时不需要的文件，例如 Update Manager 及其元数据，从而减小图像大小。这条指令是构建过程中最重要的一步。

第三个指令(FROM … as base)控制目标图像的基础层。通常，它是一个标准化的基本操作系统映像或带有 Java 运行时的映像。跨多个映像使用相同的基础层可以提高下载性能并优化存储。

第四条指令(COPY–from = builder)将/opt/software EAG 安装文件夹从构建阶段复制到最终映像中。该指令可以是单个复制命令，也可以是单个子文件夹和文件的副本列表，以实现更小、高度优化的图像大小。

Docker 文件可以包括其他指令，如暴露运行时端口、定义入口点脚本或 Docker 引擎支持的任何其他有效指令。

**Template.yaml**

Docker 命令中心生成器使用的 template.yaml 文件通常包含产品和修复安装、运行时实例创建及其配置的说明。下面是一个带有行内注释的 template.yaml 文件的通用示例。

```
alias: myTemplate  templates:   myContainer:      fixes: ALL             # install all fixes      products:           myProduct:          # install myProduct            myInstance:      # create myInstance of myProduct               my.port: 1234                   # port configuration               configuration:                 configurationType:                    configurationInstance:                     property1: value1         # configuration property1                     property2: value2         # configuration property2 layers:    runtimes:       productRepo: ${repo.product}            # product repository       fixRepo:     ${repo.fix}                # fix repository       templates:   myContainer  provision:    default:       runtime: ${nodes} 
```

关键点:

1.  模板应该只定义一个运行时实例。不同的产品运行时应该作为不同的 Docker 容器运行，因此应该构建为独立的 Docker 映像
2.  将尽可能多的配置放入 Docker 映像可以实现(几乎)不变的基础设施，这为运行容器化的应用程序带来了很多好处。可变配置方面可以通过利用产品特定的功能来解决，例如使用环境变量的动态重新配置。
3.  用于构建 Docker 映像的模板与可用于传统配置的模板相同。这允许在 Docker 构建过程之外迭代地开发和测试模板，然后将它们用于传统的和容器化的部署。

**构建过程**

构建过程非常简单，只需在包含 Dockerfile:
的文件夹中运行 **docker build** 命令

```
docker build -t myimage . 
```

## 使用默认模板构建 Docker 图像

命令中心模板和 Docker 构建文件的默认库可从[https://github.com/SoftwareAG/sagdevops-templates](https://github.com/SoftwareAG/sagdevops-templates)获得，用户指南可从[https://github . com/software ag/sagdevops-templates/wiki/Building-default-Docker-images](https://github.com/SoftwareAG/sagdevops-templates/wiki/Building-default-Docker-images)获得

首先，将带有 10 . x release Software AG Linux 许可证密钥的 licenses.zip 文件复制到 sagdevops-templates/ licenses 文件夹中，并使用您的 Empower SDC 凭据从基础架构文件夹:
中使用 **docker-compose build** 命令为 Docker 映像构建您的自定义命令中心构建器

```
cp saglicenses.zip sagdevops-templates/licenses/licenses.zip cd sagdevops-templates/infrastructure export EMPOWER\_USR=you@company.com export EMPOWER\_PSW=\*\*\*\*\* docker-compose build 
```

在您成功地为 Docker 映像构建了一个命令中心构建器之后，您可以通过从 containers 文件夹中执行 **docker-compose up** 命令来构建产品映像并测试运行容器。例如，要构建并运行一个通用消息容器，执行以下命令:

```
cd sagdevops-templates/containers docker-compose up -d universal-messaging docker-compose ps 
```

一分钟后，您可以使用由 **docker-compose ps** 命令显示的自动分配的端口来访问 Command Central 和 Universal Messaging containers。

## 总结

您可以将 Command Central DevOps 工具用于传统的内部配置和容器化部署。您可以为 10.x 版本构建托管的(通过 Command Central)和非托管的 Docker 映像，从而更好地控制映像的具体内容，以满足您的安全要求或流程编排引擎的要求。

**重要提示:**有关最新的详细说明，请访问 https://github.com/SoftwareAG/sagdevops-templates