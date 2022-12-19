# 如何用 CloudFormation 将一个服务部署到亚马逊弹性容器服务？

> 原文：<https://dev.to/brightdevs/how-to-deploy-a-service-to-amazon-elastic-container-service-with-cloudformation-28dl>

容器正在成为部署软件的标准方式。每个云供应商现在都提供一种或多种方式在他们的平台上运行容器。我们的大多数客户使用 AWS 来托管他们的 SaaS 解决方案。作为我们一个客户的新发展的一部分，我们已经决定放弃[弹性豆茎](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html)而拥抱容器。亚马逊[弹性容器服务](https://aws.amazon.com/ecs/)是一项支持 Docker 容器的编排服务，通常提供一年以上。鉴于我们的开发团队规模较小，这似乎是最佳选择，因为它消除了大多数集群管理难题。在这篇文章中，我将描述我们如何使用 [CloudFormation](https://aws.amazon.com/cloudformation/) 将容器部署到 ECS。

[![containers](../Images/409aa01d84634c620a01d8b36d6e5bb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W3YjWHrK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ww8017v3xvbq9m8l6108.jpeg)

# ECS 集群定义

在 [Bright Inventions](https://brightinventions.pl/) 我们经常使用 CloudFormation 进行基础设施配置，因为它允许我们轻松地版本化和跟踪变化。我们需要的第一个基础架构是 [ECS 集群](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_clusters.html)。集群是在 ECS 内部运行的任务/容器的逻辑组。特别是，由于我们将使用 [EC2 启动类型](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_types.html)，集群也可以被看作一组安装了 ECS 代理的 EC2 实例。

```
"ECSMainCluster":  {  "Type":  "AWS::ECS::Cluster",  "Properties":  {  "ClusterName":  "app-stack-main"  }  },  "ECSAutoScalingGroup":  {  "Type":  "AWS::AutoScaling::AutoScalingGroup",  "Properties":  {  "VPCZoneIdentifier":  [  {  "Ref":  "PrivateASubnet"  },  {  "Ref":  "PrivateBSubnet"  }  ],  "LaunchConfigurationName":  {  "Ref":  "ContainerHostInstances"  },  "MinSize":  "1",  "MaxSize":  "6",  "DesiredCapacity":  "1",  "Tags":  [  {  "Key":  "Name",  "Value":  "app-stack-ecs",  "PropagateAtLaunch":  true  }  ]  },  "CreationPolicy":  {  "ResourceSignal":  {  "Timeout":  "PT5M"  }  },  "UpdatePolicy":  {  "AutoScalingReplacingUpdate":  {  "WillReplace":  "true"  }  }  } 
```

正如您在上面看到的，`ECSMainCluster`主要是一个声明。接下来是一个自动扩展组，它将启动和管理 EC2 实例。`VPCZoneIdentifier`列出了在单独的可用性区域中创建的 2 个 VPC 子网。这对于可用性至关重要，因为它导致 EC2 实例在物理上独立的硬件上运行。为了简洁，我在这篇文章中省略了它们的配置。然而，如果你对这个话题感兴趣，请点击[阅读这篇文章](https://sookocheff.com/post/aws/how-to-create-a-vpc-using-cloudformation/)。指定的名为`ContainerHostInstances`的`LaunchConfigurationName`详细描述了 EC2 实例应该是什么样子。

```
"ContainerHostInstances":  {  "Type":  "AWS::AutoScaling::LaunchConfiguration",  "Properties":  {  "ImageId":  "ami-880d64f1",  "SecurityGroups":  [  {  "Ref":  "ECSSecurityGroup"  }  ],  "InstanceType":  "t2.medium",  "IamInstanceProfile":  {  "Ref":  "ECSHostEC2InstanceProfile"  },  "KeyName":  "private-key-pair",  "UserData":  {  "Fn::Base64":  {  "Fn::Join":  [  "",  [  "#!/bin/bash -xe\n",  "echo ECS_CLUSTER=",  {  "Ref":  "ECSMainCluster"  },  " >> /etc/ecs/ecs.config\n",  "yum install -y aws-cfn-bootstrap\n",  "/opt/aws/bin/cfn-signal -e $? ",  "         --stack ",  {  "Ref":  "AWS::StackName"  },  "         --resource ECSAutoScalingGroup ",  "         --region ",  {  "Ref":  "AWS::Region"  },  "\n"  ]  ]  }  }  }  },  "ECSHostEC2InstanceProfile":  {  "Type":  "AWS::IAM::InstanceProfile",  "Properties":  {  "Path":  "/",  "Roles":  [  {  "Ref":  "ECSHostEC2Role"  }  ]  }  }, 
```

第一个重要的属性是`ImageId`，它使用了 [Amazon ECS 优化的 Linux AMI ID](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) 。接下来，我们有一个安全组，它为应用程序端口上的传入流量添加规则。接下来我们有引用实例概要文件`ECSHostEC2InstanceProfile`的`IamInstanceProfile`，该实例概要文件[又承担 ECS 代理](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html)部署和配置容器所需的角色策略。
在`UserData`中，我们定义了一个 shell 脚本，通知 ECS 代理它正在运行的集群。我将省略`ECSHostEC2Role`的定义，因为[在文档](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html)中有很好的描述。

有了以上内容，我们现在就可以通过 CloudFormation 模板部署 ECS 集群了。然而，没有容器的集群是没有意义的。

# ECS 服务和任务定义

在 AWS 行话[中，ECS 服务](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html)描述了部署和运行任务定义所需的最小配置。一个[任务定义](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)反过来描述了如何配置和运行一组容器，这些容器形成一个单一的逻辑组件。

```
"EmailSenderService":  {  "Type":  "AWS::ECS::Service",  "Properties":  {  "Cluster":  {  "Ref":  "ECSMainCluster"  },  "DesiredCount":  2,  "DeploymentConfiguration":  {  "MinimumHealthyPercent":  50  },  "Role":  {  "Ref":  "ECSServiceRole"  },  "TaskDefinition":  {  "Ref":  "EmailSenderTask"  }  }  },  "EmailSenderTask":  {  "Type":  "AWS::ECS::TaskDefinition",  "Properties":  {  "Family":  "app-stack-email-sender",  "ContainerDefinitions":  [{  "Name":  "app-stack-email-sender",  "Essential":  "true",  "Image":  {  "Ref":  "EmailSenderTaskDockerImage"  },  "LogConfiguration":  {  "LogDriver":  "awslogs",  "Options":  {  "awslogs-group":  {  "Ref":  "EmailSenderLogsGroup"  },  "awslogs-region":  {  "Ref":  "AWS::Region"  },  "awslogs-stream-prefix":  "email-sender",  "awslogs-datetime-format":  "%Y-%m-%d %H:%M:%S.%L"  }  },  "PortMappings":  [{  "ContainerPort":  8080  }],  "Environment":  [{  "Name":  "DEPLOY_ENV",  "Value":  {  "Ref":  "DeployEnv"  }  }]  }]  }  },  "EmailSenderLogsGroup":  {  "Type":  "AWS::Logs::LogGroup",  "Properties":  {  "LogGroupName":  "app-stack-email-sender",  "RetentionInDays":  14  }  } 
```

`EmailSenderService`很容易理解。`EmailSenderTask`定义了一个容器。`app-stack-email-sender`任务定义声明`Image`是在创建或更新堆栈时对传入 CloudFormation 模板的参数的引用。它的值[必须是可以由 ECS 代理提取的 Docker 映像的名称](https://docs.docker.com/engine/reference/commandline/pull/#pull-from-a-different-registry)。存储库可以是公共的或者私有的。当托管您自己的私有 Docker 映像存储库时，您需要确保 ECS 代理[配置了正确的凭证](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/private-auth.html)。值得庆幸的是，有[弹性容器注册中心](https://aws.amazon.com/ecr/)，它提供了使用 ECS 时自动配置的私有存储库，只要`ECSHostEC2Role`策略[允许 ECR 相关动作](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html)。

接下来我们有`LogConfiguration`将容器日志推送到`EmailSenderLogsGroup` CloudWatch 日志组，这样我们可以通过 AWS 控制台检查它们。`PortMappings`列出了正在运行的容器所暴露的端口。请注意，我们尚未定义主机端口，它将被自动分配。当运行同一个容器的多个实例时，这一点很重要。我会在下一篇文章中更详细地描述它。最后但同样重要的是，`Environment`部分列出了启动时传递给容器实例的环境变量。这里我们引用了一个`DeployEnv`堆栈参数，它允许我们通知在容器内运行的应用程序当前的部署环境，例如暂存与生产。

正如您在上面看到的，使用 CloudFormation 将容器部署到 ECS 需要几个步骤。确实比弹性豆茎需要更多的配置。但是，它允许更好地利用 EC2 实例和统一的部署和配置方法，而不考虑容器内部使用的应用程序技术。此外，它更经得起未来的考验，因为只需很少的调整，就可以切换到 [Fargate 发射模式](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_types.html)。使用这种模式将我们从 EC2 ECS 集群管理任务的负担中解放出来。部署更多的服务和任务将需要单独的云形成资源定义。然而，在[云模板](https://brightinventions.pl/blog/introducing-cloudform-tame-aws-cloudformation-templates/)的帮助下，很容易保持云模板干燥。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)