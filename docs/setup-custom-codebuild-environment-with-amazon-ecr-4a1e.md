# ☁️用 Amazon ECR 设置定制代码构建环境

> 原文：<https://dev.to/jpomykala/setup-custom-codebuild-environment-with-amazon-ecr-4a1e>

由于 Oracle 的 Java 开发速度更快，我们现在使用的每个工具都需要跟进每个 Java 版本，或者只支持像 Amazon 这样的 LTS 版本。

CodeBuild 是 AWS 托管的用于构建软件应用程序的工具，在稳定的 Java 11 发布 3 个月后，它仍然不支持任何比 Java 9 更新的东西。时间到了，现在我们将构建我们自己的构建环境，它将在稳定发布后支持 Java 11 和 Java 12。

## 要求

*   AWS CLI
*   码头工人
*   亚马逊 ECR

我们不需要什么特别的东西。AWS CLI 与 AWS 帐户完全不同。在我们的本地机器上安装 docker 来构建映像，以及一些关于 docker 如何工作和正确的 docker 文件应该是什么样子的基本知识。基本上，我们将创建构建环境，该环境将在使用 docker 映像的代码构建过程中使用。之后，我们将推动亚马逊 ECR 创建的图像。ECR 是类似 docker hub 的容器注册中心，但由 Amazon 管理。最终，我们将使用我们的图像作为构建环境。现在是时候开始“修复”代码构建了。

## 构建环境

首先我们需要应该创造我们自己的`Dockerfile`与环境。我用了 Docker Hub 的官方图片，我们可以选择 maven 和 Java 版本。[https://hub.docker.com/_/maven/](https://hub.docker.com/_/maven/)我添加了几个环境变量，并拥有`CMD`来在启动时显示`mvn --version`，而不是只运行`mvn`，因为这会导致代码构建过程失败。

```
FROM maven:3.6-jdk-11
MAINTAINER Jakub Pomykała <hello@jpomykala.me>

ENV PATH $PATH:$JAVA_HOME/bin
ENV JAVA_OPTS "-server -XX:+UseG1GC -XX:+UseStringDeduplication -XX:+OptimizeStringConcat -Dsun.net.inetaddr.ttl=60"
ENV HEAP_SPACE "-Xms512m -Xmx2g"

CMD ["mvn", "--version"] 
```

Enter fullscreen mode Exit fullscreen mode

## 推送到集控室

现在我们可以在[亚马逊 ECR](https://eu-west-1.console.aws.amazon.com/ecr/repositories?region=eu-west-1) 上创建名为`java11-codebuild-environemnt`的新存储库。

用
登录到 ECR

```
$(aws ecr get-login --no-include-email --<YOUR REGION>) 
```

Enter fullscreen mode Exit fullscreen mode

使用
构建 docker 图像

```
docker build -t java11-codebuild-environemnt . 
```

Enter fullscreen mode Exit fullscreen mode

并尝试使用:
运行它

```
docker run java11-codebuild-environemnt:latest 
```

Enter fullscreen mode Exit fullscreen mode

并确保 maven 显示安装的版本。

[![mvn version](../Images/b8bf1a7a7dede4166b4b62c0a1b56590.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fVH0Smvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpomykala.com/assets/2018-11-10/docker-run-mvn.png)

如果一切按预期工作，我们可以标记并把我们的图像推送到 ECR 服务。

```
docker tag java11-codebuild-environment:latest <YOUR_ECR_URL>/java11-codebuild-environment:latest
docker push <YOUR_ECR_URL>/java11-codebuild-environment:latest 
```

Enter fullscreen mode Exit fullscreen mode

[![pushing to ecr](../Images/7a2008b17670d9388e10376c38a43e58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--363Y2eoU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpomykala.com/assets/2018-11-10/pushing-to-ecr.png)

在开始使用我们的容器之前，我们需要为 CodeBuild 服务设置适当的访问策略。这需要使用内部 ECR 政策管理来完成(不是标准的 AWS 角色/政策！)`Amazon ECR > Repositories > Permissions > Edit policy JSON`

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "CodeBuildAccess",
      "Effect": "Allow",
      "Principal": {
        "Service": "codebuild.amazonaws.com"
      },
      "Action": [
        "ecr:BatchCheckLayerAvailability",
        "ecr:BatchGetImage",
        "ecr:GetDownloadUrlForLayer"
      ]
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

[![pushing to ecr](../Images/095d624c836fab2344f94d620abf0913.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cDmyTROM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpomykala.com/assets/2018-11-10/ecr-permissions.png)

我们允许 CodeBuild 服务获取 docker 图像，下载它们并检查它们的可用性。

## CodeBuild 配置

最后一步是配置 CodeBuild 环境，如下图。

[![pushing to ecr](../Images/2ed1f600029eb133097c274f750f4f9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DI-KlDkW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpomykala.com/assets/2018-11-10/codebuild-environment.png)

开始了。我们终于可以使用最新的 Java 特性，并马上将其推向生产了！下一个版本定于 2019 年 3 月，所以我们将再次经历这些步骤，但在`Dockerfile`我们将把`maven:3.6-jdk-11`改为`maven:3.6-jdk-12`。即使现在我们也可以这样做，因为我们可以在 Java 12 的测试版中找到 docker 映像。