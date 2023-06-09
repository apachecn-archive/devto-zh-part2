# 通过 Azure DevOps 部署到 AWS 云

> 原文：<https://dev.to/li_chastina/deploying-to-aws-cloud-through-azure-devops-24kf>

[*Azure DevOps*](https://docs.microsoft.com/en-gb/azure/devops/?view=vsts#pivot=index&panel=indexA) 是一个为应用程序运行构建和部署管道的平台。最近[更名为 *Azure DevOps*](https://docs.microsoft.com/en-us/azure/devops/user-guide/what-happened-vsts?view=vsts) ，就是为了赶时髦。它基本上是所有其他构建系统，给你一个 YAML 声明管道 DSL，如果你想通过点击和拖动来构建你的 CICD 管道，还有一个 UI。它没有像 Jenkins 或 CircleCI 等其他大型构建服务器那样受欢迎，因此它在文档、教程和指南方面缺乏很多。

我最近帮助某人使用 Azure DevOps 部署 AWS 基础设施，我知道这很奇怪。我最初对这个选择感到困惑，但在检查了 Azure DevOps 的 [AWS 插件](https://github.com/aws/aws-vsts-tools)后，我有点明白为什么一家小商店会选择它作为它的 CICD 工具。Azure DevOps 追随 Jenkins，因为它只提供了[一些开箱即用的基本部署模块](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks)(在 Azure DevOps 中称为`tasks`)，如 bash 脚本、npm 构建和 maven 构建。其余的功能来自丰富的插件库，比如 AWS 插件。

#### YAML DSL

YAML DSL 配置运行哪些作业以及在哪种服务器上运行。它还公开了一些管道配置，如当作业失败时要做什么，我们是继续还是退出。下面是我为我的项目写的东西，虽然很少，但能完成任务:

```
jobs:
- job: MyJob
  pool:
    vmImage: 'ubuntu-16.04'
  displayName: My First Job
  continueOnError: true
  workspace:
    clean: outputs
  steps:
...... 
```

`steps:`启动要运行的模块列表。

#### AWS 凭证

AWS 凭证可以通过 UI 进行配置(`Project Settings`->-`Service connections`->添加新的 AWS 服务连接)。需要注意的是，服务连接仅在管道启动期间加载，管道创建后添加的任何新连接都不会自动加载。因此，您必须删除并重新创建管道才能使用新的连接。我已经在 [Github 的一期](https://github.com/aws/aws-vsts-tools/issues/109#issuecomment-430479167)中解释过这个警告。看起来有相当多的人遇到了这个问题，所以我只是到处分享我学到的经验。

#### 云形成模块

AWS 插件附带了几个漂亮的[有用的模块](https://github.com/aws/aws-vsts-tools)，比如`CloudFormation Update/Create`、`Lambda Deploy`和`S3 Upload`。不幸的是，我不认为他们发布了它的文档，所以我不得不查看他们的[源代码](https://github.com/aws/aws-vsts-tools)来找到文档。我只用过`CloudFormation Update/Create`和另一个叫做`AWS CLI`的模块。CloudFormation 模块大大节省了我的时间，因为我不必在初始创建后处理多次更新的幂等性，如果 CloudFormation 堆栈已经创建，该模块知道更新而不是创建。

```
 steps:
  - task: CloudFormationCreateOrUpdateStack@1
    inputs:
      awsCredentials: 'aws_tokens'
      regionName: 'us-east-2'
      stackName: 'IAMRoleStack'
      templateFile: templates/iam_role.json
      capabilityIAM: 'true'
      capabilityNamedIAM: 'true' 
```

下面是我用来部署我的 IAM 角色的，所有 AWS 模块都需要`awsCredentials`、`regionName`，所有 CloudFormation 模块都需要`stackName`和`templateFile`。最后两个只是这个模块特有的。

#### AWS CLI 模块(或无)

AWS CLI 模块给了我不切实际的希望，原来你还是要自己安装 AWS CLI 才能使用模块 LOL。我首先必须安装`setuptools`和`wheel`，是的，`pip`依赖项甚至不是免费的。然后，我必须将它安装在我的用户空间中，我花了一些时间才找到 CLI 的安装位置，因为它没有添加到我的路径中。所以我只是使用了 AWS Shell 脚本模块，它似乎和 AWS CLI 模块完全一样，只是更容易编写。

```
- script: |
      python -m pip install --upgrade pip==9.0.3 setuptools wheel
      pip install awscli --user
    displayName: 'Install  tools'
  - task: AWSShellScript@1
    inputs:
      awsCredentials: 'aws_tokens'
      regionName: 'us-east-2'
      scriptType: 'inline'
      inlineScript: |
        eval $(/home/vsts/.local/bin/aws ecr get-login --no-include-email) 
```