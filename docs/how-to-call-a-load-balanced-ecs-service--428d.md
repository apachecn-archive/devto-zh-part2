# 如何调用负载平衡的 ECS 服务？

> 原文：<https://dev.to/brightdevs/how-to-call-a-load-balanced-ecs-service--428d>

运行 [ECS](https://aws.amazon.com/ecs/) 的服务可以调用过多的 AWS APIs。它可以从队列中读取消息，向 [SNS](https://aws.amazon.com/sns/) 主题发布消息，查询数据库。这些都是与服务通信的有效方式。然而，通常最合适的方式是通过 HTTP API 调用服务。在这篇文章中，我将描述如何配置在 VPC 内部运行的 ECS 服务，以便其他服务可以调用它的 API。

[![containers](../Images/1c0d232024a621ea100aa4db49c57bc1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0voUqGXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/318v5l57vo401x02ko0n.jpeg)

# 将一个 ECS 服务部署到多台主机

每当我们关心在 AWS 内部运行的服务的可用性时，我们需要让它在至少两个可用性区域中运行。因此，在定义 ECS 群集自动扩展组时，我们需要指定至少 2 个在不同可用性分区中运行的 VPC 子网。

```
"ECSMainCluster":  {  "Type":  "AWS::ECS::Cluster",  "Properties":  {  "ClusterName":  "Main Cluster"  }  },  "ECSAutoScalingGroup":  {  "Type":  "AWS::AutoScaling::AutoScalingGroup",  "Properties":  {  "VPCZoneIdentifier":  [  {  "Ref":  "PrivateASubnet"  },  {  "Ref":  "PrivateBSubnet"  }  ],  "LaunchConfigurationName":  {  "Ref":  "ContainerHostInstances"  },  "MinSize":  "2",  "MaxSize":  "6",  "DesiredCapacity":  "2"  }  },  "ServiceA":  {  "Type":  "AWS::ECS::Service",  "Properties":  {  "Cluster":  {  "Ref":  "ECSMainCluster"  },  "DesiredCount":  2,  "LoadBalancers":  [{  "ContainerName":  "serviceA",  "ContainerPort":  8080,  "TargetGroupArn":  {  "Ref":  "ServiceAAlbTargetGroup"  }  }],  "DeploymentConfiguration":  {  "MinimumHealthyPercent":  50  },  "Role":  {  "Ref":  "ECSServiceRole"  },  "TaskDefinition":  {  "Ref":  "ServiceATask"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们如何使用 2 个私有子网作为`VPCZoneIdentifier`。`MinSize`也被设置为 2，这将导致两个可用性区域至少有 1 个实例在运行。为简洁起见，子网和 VPC 定义未包括在内。你可以在我之前的文章中找到更多关于如何在 ECS 集群[中配置 EC2 实例的细节。](https://dev.to%20post_url%202018-02-19-how-to-deploy-a-service-to-amazon-elastic-container-service-with-cloud-formation%20)

部署在`ECSMainCluster`中的`ServiceA`还指定 at 的 [`DesiredCount`](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html) 为 2，这指示 ECS 运行至少 2 个服务实例。负载平衡设置需要 [`LoadBalancers`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ecs-service.html#cfn-ecs-service-loadbalancers) 和 [`Role`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ecs-service.html#cfn-ecs-service-role) 属性。`ECSServiceRole`必须允许 ECS 代理调用负载均衡器 API。`LoadBalancers`引用[一个 ALB 目标组](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)，正在运行的 ECS 任务应该被添加到该目标组中。

# 私有负载均衡器

为了让我们能够调用运行在多个实例上的 ECS 服务所公开的 API，我们将使用内部应用程序负载平衡器。我所说的内部是指负载平衡器在 VPC 之外是不可访问的。

```
"PrivateApiLoadBalancer":  {  "Type":  "AWS::ElasticLoadBalancingV2::LoadBalancer",  "Properties":  {  "Subnets":  [  {  "Ref":  "PrivateASubnet"  },  {  "Ref":  "PrivateBSubnet"  }  ],  "Name":  "PrivateApiLoadBalancer",  "Scheme":  "internal",  "SecurityGroups":  [{  "Ref":  "HttpHttpsProxySecurityGroup"  }]  }  },  "HttpHttpsProxySecurityGroup":  {  "Type":  "AWS::EC2::SecurityGroup",  "Properties":  {  "GroupDescription":  "Enable http and https access",  "VpcId":  {  "Ref":  "VPC"  },  "SecurityGroupIngress":  [{  "IpProtocol":  "tcp",  "FromPort":  "80",  "ToPort":  "80",  "CidrIp":  {  "Ref":  "VPCCidr"  }  },  {  "IpProtocol":  "tcp",  "FromPort":  "443",  "ToPort":  "443",  "CidrIp":  {  "Ref":  "VPCCidr"  }  }],  "SecurityGroupEgress":  [{  "IpProtocol":  "-1",  "CidrIp":  {  "Ref":  "VPCCidr"  }  }]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`PrivateApiLoadBalancer`只有两个重要方面。首先，它连接到与我们的 ECS 任务定义相同的子网。其次，它配置了一个安全组，允许传入的 HTTP/HTTPS 流量和传出的流量到达 VPC CIDR 地址中的任何 IP。负载平衡器需要出口规则才能检查其目标的健康状况。

为了让负载平衡器执行一些操作，我们需要配置侦听器来定义它响应传入流量的行为。

```
"PrivateApiLoadBalancerHttpListener":  {  "Type":  "AWS::ElasticLoadBalancingV2::Listener",  "Properties":  {  "Port":  80,  "Protocol":  "HTTP",  "LoadBalancerArn":  {  "Ref":  "PrivateApiLoadBalancer"  },  "DefaultActions":  [{  "Type":  "forward",  "TargetGroupArn":  {  "Ref":  "PrivateApiLoadBalancerInvalidHostGroup"  }  }]  }  },  "PrivateApiLoadBalancerInvalidHostGroup":  {  "Type":  "AWS::ElasticLoadBalancingV2::TargetGroup",  "Properties":  {  "Protocol":  "HTTP",  "Port":  80,  "VpcId":  {  "Ref":  "VPC"  },  "Name":  "invalid-target-group"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`PrivateApiLoadBalancerHttpListener`指定端口 80 上对`PrivateApiLoadBalancer`的 HTTP 请求应该被路由到`PrivateApiLoadBalancerInvalidHostGroup`。`AWS::ElasticLoadBalancingV2::Listener`要求我们设置`DefaultActions`。上述设置允许我们以一致的方式失败。除非有`AWS::ElasticLoadBalancingV2::ListenerRule`匹配传入的 HTTP 请求，否则它将被路由到空的`AWS::ElasticLoadBalancingV2::TargetGroup`，这又将导致 502 坏网关。这允许我们有效地将负载平衡器监听器的`DefaultActions`从特定的 ECS 服务实例中分离出来。

# 将 ECS 服务附加到应用程序负载平衡器

现在是将请求从应用程序负载平衡器路由到 ECS 服务实例的时候了。 [`AWS::ElasticLoadBalancingV2::ListenerRule`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-listenerrule.html) 允许我们配置这样的行为:

```
"ServiceAAlbHttpListenerRule":  {  "Type":  "AWS::ElasticLoadBalancingV2::ListenerRule",  "Properties":  {  "Actions":  [{  "Type":  "forward",  "TargetGroupArn":  {  "Ref":  "ServiceAAlbTargetGroup"  }  }],  "Priority":  1,  "Conditions":  [{  "Field":  "host-header",  "Values":  [  "service-a.in.example.com"]  }],  "ListenerArn":  {  "Ref":  "PrivateApiLoadBalancerHttpListener"  }  }  },  "ServiceAAlbTargetGroup":  {  "Type":  "AWS::ElasticLoadBalancingV2::TargetGroup",  "Properties":  {  "Protocol":  "HTTP",  "Port":  8080,  "HealthCheckPath":  "/application-status/health",  "HealthyThresholdCount":  2,  "UnhealthyThresholdCount":  10,  "HealthCheckIntervalSeconds":  5,  "HealthCheckTimeoutSeconds":  4,  "VpcId":  {  "Ref":  "VPC"  },  "Name":  "service-a-target-group"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`ServiceAAlbHttpListenerRule`声明任何一个`Host`等于`service-a.in.example.com`的请求都应该从`ServiceAAlbTargetGroup`路由到一个实例。`ServiceAAlbTargetGroup`组引用自`ServiceA`定义，包含我们任务的所有运行和健康的实例。这意味着为了调用由`ServiceA`公开的 HTTP API，我们将简单地使用`service-a.in.example.com`域名。

# 一条私有 DNS 记录

最后一部分是定义一个[私有托管区域](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zone-private-creating.html)和[一个 DNS 记录集](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-route53-recordset.html)，以便在 VPC 内部对`service-a.in.example.com`进行 DNS 查找，得到`PrivateApiLoadBalancer`的 IP 地址。

```
"PrivateHostedZone":  {  "Type":  "AWS::Route53::HostedZone",  "Properties":  {  "VPCs":  [{  "VPCId":  {  "Ref":  "VPC"  },  "VPCRegion":  {  "Ref":  "AWS::Region"  }  }],  "Name":  "in.example.com"  }  },  "ServiceALoadBalancerDNSRecord":  {  "Type":  "AWS::Route53::RecordSet",  "Properties":  {  "Name":  "service-a.in.example.com",  "HostedZoneId":  {  "Ref":  "PrivateHostedZone"  },  "ResourceRecords":  [{  "Fn::GetAtt":  ["PrivateApiLoadBalancer",  "DNSName"]  }],  "TTL":  60,  "Type":  "CNAME"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`PrivateHostedZone`仅仅定义了域名，我们将使用该域名来寻址在我们的 VPC 内运行的 ECS 服务。建议使用您控制的域名，以便 DNS 配置中的错误不会导致您的服务调用不属于您的地址。`ServiceALoadBalancerDNSRecord`定义了一个使用`PrivateApiLoadBalancer` AWS 分配的 DNS 名称的 CNAME。这样，`service-a.in.example.com`的查找将有效地解析不同可用性区域中的多个 IP。

有了上面的配置，我们可以使用最自然的方式(即 DNS 名称)调用由 ECS 集群上的 VPC 内部运行的负载平衡服务私有公开的 HTTP API。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)