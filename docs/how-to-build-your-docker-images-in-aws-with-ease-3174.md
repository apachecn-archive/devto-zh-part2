# 如何在 AWS 中轻松构建 Docker 图像

> 原文：<https://dev.to/kylegalbraith/how-to-build-your-docker-images-in-aws-with-ease-3174>

继续我最近的主题，在 AWS 中实现尽可能多的自动化。今天，我将分享我们如何在 AWS 的 CI/CD 管道中构建 Docker 映像。具体来说，我们将探索:

*   扩展提供 CI/CD 管道的 Terraform 模板，以提供 AWS 弹性容器注册中心(ECR)。
*   为准系统 ExpressJS API 创建一个简单的 docker 文件。
*   使用我们的`buildspec.yml`文件中的 docker `build`、`tag`和`push`将我们的最新图像发布到 ECR。
*   从我们的注册表中取出最新的图像并在本地运行。

现在我们已经了解了情况，让我们来谈谈如何扩展我们常用的 CI/CD 地形模板来支持构建 Docker 映像。

### 将 ECR 纳入我们的 CI/CD 渠道

首先，我们需要创建一个 Terraform 模板，用于配置 CI/CD 模板。我们可以使用我们在之前的文章中看到的`terraform-aws-codecommit-cicd`模块来实现。

*完整的模板可以在[这里](https://github.com/kylegalbraith/aws-ecr-codepipeline-demo/blob/master/infrastructure/deployment-pipeline/cicd-pipeline-with-ecr.tf)找到。*

```
variable "image_name" {
  type = "string"
}

module "codecommit-cicd" {
  source                    = "git::https://github.com/slalompdx/terraform-aws-codecommit-cicd.git?ref=master"
  repo_name                 = "docker-image-build"                                                             # Required
  organization_name         = "kylegalbraith"                                                                  # Required
  repo_default_branch       = "master"                                                                         # Default value
  aws_region                = "us-west-2"                                                                      # Default value
  char_delimiter            = "-"                                                                              # Default value
  environment               = "dev"                                                                            # Default value
  build_timeout             = "5"                                                                              # Default value
  build_compute_type        = "BUILD_GENERAL1_SMALL"                                                           # Default value
  build_image               = "aws/codebuild/docker:17.09.0"                                                   # Default value
  build_privileged_override = "true"                                                                           # Default value
  test_buildspec            = "buildspec_test.yml"                                                             # Default value
  package_buildspec         = "buildspec.yml"                                                                  # Default value
  force_artifact_destroy    = "true"                                                                           # Default value
} 
```

Enter fullscreen mode Exit fullscreen mode

在顶部，我们看到我们已经声明了一个变量`image_name`，它将被传递到模板中。接下来，我们看到我们创建了我们的`codecommit-cicd`模块。这与我们过去看到的略有不同。

1.  首先，将`build_image`属性设置为`aws/codebuild/docker:17.09.0`。这是 AWS 提供的代码构建映像，允许我们构建自己的 Docker 映像。
2.  第二，`build_privileged_override`属性是新的。这个属性告诉 CodeBuild 我们将要构建 Docker 图像，所以授予我们访问它的权限。

为了支持在 AWS CodeBuild 中构建 Docker 映像，我们只需要对 CI/CD 管道做这两件事。让我们看看下面定义的两个资源。

```
resource "aws_ecr_repository" "image_repository" {
  name = "${var.image_name}"
}

resource "aws_iam_role_policy" "codebuild_policy" {
  name = "serverless-codebuild-automation-policy"
  role = "${module.codecommit-cicd.codebuild_role_name}"

  policy = <<POLICY {
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ecr:BatchCheckLayerAvailability",
        "ecr:CompleteLayerUpload",
        "ecr:GetAuthorizationToken",
        "ecr:InitiateLayerUpload",
        "ecr:PutImage",
        "ecr:UploadLayerPart"
      ],
      "Resource": "*",
      "Effect": "Allow"
    }
  ]
} POLICY } 
```

Enter fullscreen mode Exit fullscreen mode

我们从定义 AWS 弹性容器注册中心(ECR)开始。这是我们 AWS 帐户内的一个完全受管理的 Docker 容器注册表。我们可以使用 ECR 存储、管理和部署我们的容器映像。注意，这里我们使用传入模板的`image_name`变量作为 ECR 存储库的名称。

我们在这里看到的最后一部分是附加的 IAM 策略，它附加到我们的 CodeBuild 项目所承担的角色上。该策略授予我们的 CodeBuild 项目将图像推送到我们的图像存储库的权限。

现在我们知道了将要创建什么资源，让我们继续使用 Terraform 实际创建它们。

首先，我们用`init`命令初始化我们的提供者和模板。

```
deployment-pipeline$ terraform init
Initializing modules...
- module.codecommit-cicd
- module.codecommit-cicd.unique_label

Initializing provider plugins... 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们的模板被初始化，我们可以运行一个快速的`plan`命令来确认所有将要被创建的资源。

```
deployment-pipeline$ terraform plan -var image_name=sample-express-app
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + aws_ecr_repository.image_repository

....
......
........

Plan: 13 to add, 0 to change, 0 to destroy.

----------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

我们知道这 13 个资源将被创建。让我们继续运行我们的`apply`命令，在我们的 AWS 帐户中创建所有这些。

```
deployment-pipeline$ terraform apply -auto-approve -var image_name=sample-express-app
data.aws_iam_policy_document.codepipeline_assume_policy: Refreshing state...
module.codecommit-cicd.module.unique_label.null_resource.default: Creating...

....
......
........

module.codecommit-cicd.aws_iam_role_policy.codebuild_policy: Creation complete after 1s (ID: docker-image-build-codebuild-role:docker-image-build-codebuild-policy)
module.codecommit-cicd.aws_codepipeline.codepipeline: Creation complete after 1s (ID: docker-image-build)

Apply complete! Resources: 13 added, 0 changed, 0 destroyed.

Outputs:

codebuild_role = arn:aws:iam::<account-id>:role/docker-image-build-codebuild-role
codepipeline_role = arn:aws:iam::<account-id>:role/docker-image-build-codepipeline-role
ecr_image_respository_url = <account-id>.dkr.ecr.us-west-2.amazonaws.com/sample-express-app
repo_url = https://git-codecommit.us-west-2.amazonaws.com/v1/repos/docker-image-build 
```

Enter fullscreen mode Exit fullscreen mode

我们看到已经创建了 13 个资源，并且已经输出了我们的 Git repo url 和 ECR repo url。将 ECR url 暂时复制到某个地方，一旦我们需要配置 CodeBuild 将要使用的`buildspec.yml`文件，就需要它。

让我们快速概述一下我们将要构建的 Docker 映像，并将其推送到我们新的 ECR 存储库中。

### 我们的示例应用程序和 Docker 图像

对于我们的演示，我已经创建了一个 [GitHub 库](https://github.com/kylegalbraith/aws-ecr-codepipeline-demo)，其中配置了一个样例 Express API。在其中，我们看到了包含应用程序逻辑的`api.js`文件。

```
const express = require('express');

// Constants
const PORT = 8080;
const HOST = '0.0.0.0';

const app = express();
app.get('/health', (req, res) => {
  res.send('The API is healthy, thanks for checking!\n');
});

app.listen(PORT, HOST);
console.log(`Running API on port ${PORT}`); 
```

Enter fullscreen mode Exit fullscreen mode

这并没有做什么神奇的事情，但是它完美地展示了我们的 Docker 图像构造。我们正在设置`express`监听端口`8080`，并设置路由`/health`，以返回一个简单的响应。

为了配合我们的示例应用程序，我们还有一个示例`Dockerfile`。

```
FROM node:8
WORKDIR /src/app

# Install app dependencies
COPY package*.json ./
RUN npm install

# Copy app contents
COPY . .

# App runs on port 8080
EXPOSE 8080

# Start the app
CMD [ "npm", "start"] 
```

Enter fullscreen mode Exit fullscreen mode

我们的 Dockerfile 正在做的事情的快速概要。

*   `FROM`指定**基础映像**我们的映像将从其开始构建。在我们的例子中，我们使用的是来自 [Docker Hub](https://hub.docker.com/_/node/) 的节点 8 映像。
*   正在为之后出现的任何命令设置我们的工作目录。
*   `COPY`只是将我们的`package.json`文件复制到我们的工作目录中。
*   `RUN`用于运行命令，这里我们运行的是`npm install`。
*   正在告诉 Docker 我们的集装箱计划在 8080 端口监听。
*   `CMD`正在为我们的容器指定默认行为。在我们的例子中，我们在我们的`package.json`中调用一个脚本`start`，然后在`api.js`中启动我们的 Express 服务器。

看到没坏对吧？您可以在 Dockerfile 文件中配置很多东西。这对于获得正确的图像是很棒的，并且允许你的容器启动并做它们需要做的事情，不需要进一步的配置。

### 在 CI/CD 渠道中建立 Docker 形象

我们为 CI/CD 渠道调配了基础 AWS 资源。我们有一个样例应用程序，它有一个关联的 Dockerfile。现在剩下的就是在 AWS 的部署管道中构建 Docker 映像。

为了开始在 AWS CodePipeline 和 CodeBuild 中构建 Docker 映像，我们需要做的最后一件事是配置我们的`buildspec.yml`文件。

再次，查看我们的[样本库](https://github.com/kylegalbraith/aws-ecr-codepipeline-demo)，我们看到我们的`buildspec.yml`文件是我们回购的根。看一看它，我们看到下面的命令。

```
version: 0.2
phases:
  install:
    commands:
      - echo install step...
  pre_build:
    commands:
      - echo logging in to AWS ECR...
      - $(aws ecr get-login --no-include-email --region us-west-2)
  build:
    commands:
      - echo build Docker image on `date`
      - cd src
      - docker build -t sample-express-app:latest .
      - docker tag sample-express-app:latest <your-ecr-url>/sample-express-app:latest
  post_build:
    commands:
      - echo build Docker image complete `date`
      - echo push latest Docker images to ECR...
      - docker push <your-ecr-url>/sample-express-app:latest 
```

Enter fullscreen mode Exit fullscreen mode

在`pre_build`步骤中，我们通过 [AWS CLI](https://aws.amazon.com/cli/) 向 ECR 发出`get-login`调用。这个调用的结果被立即执行，但是这里是这个调用返回的内容，以供参考。

```
docker login -u AWS -p <complex-password> https://<AWS-accound-id>.dkr.ecr.us-west-2.amazonaws.com 
```

Enter fullscreen mode Exit fullscreen mode

该调用返回一个 Docker `login`命令，以便访问我们的 ECR 存储库。

接下来，在`build`命令中，我们从我们的`src`目录中运行`docker build`，因为那是我们的`Dockerfile`所在的位置。构建命令将从该文件构建一个图像，并用`sample-express-app:latest`标记它。

然后，我们获取带标记的源图像，并添加一个带标记的目标图像，该图像使用我们的 ECR 存储库 url。

完成所有这些工作后，我们运行一个`docker push`命令将我们的目标映像推送到 ECR 存储库。

酷吧？现在，随着我们的存储库中每个提交到 master 的操作，我们的 CI/CD 管道被触发。然后，我们的构建流程可以使用我们的代码和 Dockerfile 来生成一个新的容器映像，该映像被直接推送到 ECR 中的私有映像存储库中。

### 测试我们的管道

我们在 AWS 中建立了基础架构。当一个新的提交进入 master 时，一个新的容器映像从我们的 Dockerfile 中构建出来。我们将新图像直接推送到 ECR 中的私有图像存储库中。

测试很简单。我们可以从我们的 ECR 存储库中取出最新的图像。

```
kyleg$ $(aws ecr get-login --no-include-email --region us-west-2)
Login succeeded
kyleg$ docker pull <your-ECR-url>/sample-express-app:latest
latest: Pulling from sample-express-app
kyleg$ docker run -p 8080:8080 -i <your-ECR-url>/sample-express-app:latest
> docker_app_express@1.0.0 start /src/app
> node api.js

Running API on port 8080 
```

Enter fullscreen mode Exit fullscreen mode

现在可以在我们的浏览器中打开`localhost:8080/health`或者在我们的命令行上运行 cURL 请求。

```
kyleg$ curl localhost:8080/health
The API is healthy, thanks for checking! 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们已经成功地使用我们的 ECR 映像创建了一个可以在本地运行的容器。

### 结论

在本文中，我们深入探讨了如何在 AWS 中创建 CI/CD 管道，以便为我们的示例应用程序持续构建 Docker 映像。我们还演示了可以使用弹性容器注册将这些图像发布到我们自己的私有图像存储库中。

只需对我们的 Terraform 模块进行一些小的调整，我们就能在几分钟内建立起这条管道。有了 Docker 的基础知识，我们可以开始构建更复杂的图像。

我们可以探索如何将这些图像推送到 DockerHub 这样的公共存储库。或者如何通过 EKS 或 ECS 使用这些映像部署容器。可能性几乎是无穷无尽的。

如果你对这篇文章有任何问题，请在下面留言，我很乐意帮助你。

### 你是否渴望了解更多关于亚马逊网络服务的信息？

想了解更多关于 AWS 的知识？我最近发布了一个电子书和视频课程，穿越了信息的海洋。它着重于在 AWS 上托管、保护和部署静态网站。目标是在您使用服务时了解与此问题相关的服务。如果你一直想学习 AWS，但不知道从哪里开始，那么看看我的课程。