# AWS CloudFormation 模式和 cloudform 实践

> 原文：<https://dev.to/brightdevs/aws-cloudformation-patterns--practices-with-cloudform-26dj>

最近我们推出了[`cloudform`](https://brightinventions.pl/blog/introducing-cloudform-tame-aws-cloudformation-templates/)——我们的[开源库](https://www.npmjs.com/package/cloudform)，允许通过 TypeScript 管理 [AWS CloudFormation](https://aws.amazon.com/cloudformation/) 模板文件。它的核心价值主张是以理智和熟悉的方式处理大量的 JSON 文件——把它当作我们项目中的任何其他类型的脚本代码。但这实际上意味着什么呢？让我们看一些例子，看看这条路能把我们引向何方。

简单回顾一下，`cloudform`的基本用法允许我们替换 AWS CloudFormation 资源冗长的 JSON 定义，如下:

```
{  "VPC":  {  "Type":  "AWS::EC2::VPC",  "Properties":  {  "CidrBlock":  {  "Fn::FindInMap":  [  "NetworkingConfig",  "VPC",  "CIDR"  ]  },  "EnableDnsHostnames":  true,  "Tags":  [  {  "Key":  "Application",  "Value":  {  "Ref":  "AWS::StackName"  }  },  {  "Key":  "Network",  "Value":  "Public"  },  {  "Key":  "Name",  "Value":  {  "Fn::Join":  [  "-",  [  {  "Ref":  "AWS::StackId"  },  "VPC"  ]  ]  }  }  ]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

转换成完全等价的一段打字稿:

```
{
  VPC: new EC2.VPC({
      CidrBlock: Fn.FindInMap('NetworkingConfig', 'VPC', 'CIDR'),
      EnableDnsHostnames: true,
      Tags: [
          new ResourceTag('Application', Refs.StackName),
          new ResourceTag('Network', 'Public'),
          new ResourceTag('Name', Fn.Join('-', [Refs.StackId, 'VPC']))
      ]
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们尝试更多的东西。

## 去掉重复的值

我们模板中的一些引用或其他变量重复了很多次。使用`cloudform`比在纯 JSON 中更简洁一点——比较一下`Refs.StackName`和`{"Ref": "AWS::StackName"}`。我们也经常引用更复杂的结构，比如连接——在纯 JSON 中，它很快变得复杂:

```
{  "Fn::Join":  [  "-",  [  "app-name-",  {  "Ref":  "DeployEnv"  }  ]  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

与`cloudform`相同，表示为

```
Fn.Join('-', ['app-name-', Fn.Ref('DeployEnv')]) 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们多次引用这种值，我们可以将它保存为一个 TypeScript 级别的常量，并在 TypeScript:
中引用它

```
const envAppName = Fn.Join('-', ['app-name-', Fn.Ref('DeployEnv')])

cloudform({
    // ...
    ParameterValueSomewhereInResources: envAppName
}) 
```

Enter fullscreen mode Exit fullscreen mode

[![Structure and order for our AWS CloudFormation templates](img/32852cb153004dacdc428dcc7b8e413f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SWn6790I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1eqic0u231bvsxhowbf.jpg)

## 去掉重复的资源

同样的概念也适用于类似的资源定义。假设我们需要在基础设施中定义 10 个 SQS 队列。在纯 JSON 中，这意味着我们必须一遍又一遍地重复相同的队列定义，改变的可能只是一个名称参数。

```
{  "NthQueue":  {  "Type":  "AWS::SQS::Queue",  "Properties":  {  "QueueName":  {  "Fn::Join":  [  "-",  [  {  "Ref":  "DeployEnv"  },  "nth-queue"  ]  ]  },  "RedrivePolicy":  {  "maxReceiveCount":  4,  "deadLetterTargetArn":  {  "Fn::GetAtt":  [  "DefaultDeadLetterQueue",  "Arn"  ]  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

更进一步，我们可以应用非常标准的重构——提取一个方法。因此，我们可以定义生成队列资源对象的那段 TypeScript，并在我们的模板中为每个要定义的队列调用它:

```
const defineQueue = (name: string) => {
    return new SQS.Queue({
        QueueName: Fn.Join('-', [Fn.Ref('DeployEnv'), name, 'queue']),
        RedrivePolicy: {
            maxReceiveCount: 4,
            deadLetterTargetArn: Fn.GetAtt('DefaultDeadLetterQueue', 'Arn')
        }
    })
}

cloudform({
    Resources: {
        Queue1: defineQueue("first"),
        Queue2: defineQueue("second"),
        // ...
        QueueN: defineQueue("nth")
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 去掉静态 FindInMap

将一些常见的配置值组合在一起通常是有意义的，并且根据某些参数可能会有所不同，例如部署环境，如试运行与生产。在一个纯 JSON 模板中，我们可以使用 [`Fn::FindInMap`](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html) 在需要的地方达到期望值。我们需要将所有的键作为路径传递给我们的值，可能使用参数引用(`Ref`)或其他变量。但是如果我们的路径是静态的，使用`cloudform`，我们可能会去掉冗长的映射访问，代之以本地的类型脚本对象。

所以与其:

```
{  "Mappings":  {  "NetworkingConfig":  {  "VPC":  {  "CIDR":  "0.0.0.0/16"  }  }  },  "Resources":  {  "VPC":  {  "Type":  "AWS::EC2::VPC",  "Properties":  {  "CidrBlock":  {  "Fn::FindInMap":  [  "NetworkingConfig",  "VPC",  "CIDR"  ]  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以直接在 TypeScript 中创建这个映射:

```
const NetworkingConfig = {
    VPC: {
        CIDR: "0.0.0.0/16"
    }
}

cloudform({
    Resources: new EC2.VPC({
        CidrBlock: NetworkingConfig.VPC.CIDR
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 分割成多个文件

没有人喜欢大的源文件。复杂环境的定义也越来越多。在纯 JSON 中，我们不能轻易地分割文件。有一个选项[导入外部代码片段](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/create-reusable-transform-function-snippets-and-add-to-your-template-with-aws-include-transform.html)，但是它要求代码片段位于 S3。如果我们想适当地保持我们的基础设施在源代码控制中，并认真对待我们的[基础设施即代码方法](https://www.infoq.com/code-infrastructure)，这有点麻烦。

有了`cloudform`,我们就进入了 TypeScript 世界，没有什么可以阻止我们将模板的一部分作为可以导入最终模板的 TypeScript 模块。将我们的网络堆栈模块与实例模块和数据库模块等分开可能是有意义的。–每个模块可能将所有资源逻辑绑定在一起–与数据库访问相关的安全组可能与数据库一起定义，但与 ECS 相关的安全组可能与容器一起定义。

让我们看看数据库模块的例子，`database.ts` :

```
export default {
  DatabaseInstanceParameters: new RDS.DBParameterGroup({
    Family: "postgres9.6"
  }),
  DatabaseInstance: new RDS.DBInstance({
    DBName: Fn.Join('-', ['db', Fn.Ref('DeployEnv')]),
    DBParameterGroupName: Fn.Ref('DatabaseInstanceParameters'),
    VPCSecurityGroups: [
        Fn.GetAtt('DatabaseSecurityGroup', 'GroupId')
    ]
    // ...
  }),
  DatabaseSecurityGroup: new EC2.SecurityGroup({
    // ...
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

及其在实际模板中的用法:

```
import databaseResources from './database'

cloudform({
  Resources: Object.assign({}, databaseResources, /* and possibly more */)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 完全忘记实际的 JSON

有了这些好东西，我们可能会忘记 JSON 文件在我们的源代码中的存在。作为我们构建或部署过程的一部分，动态生成它可能是有意义的。将模板生成任务添加到我们的 NPM 脚本中怎么样:

```
{  "generate-aws-template":  "cloudform cloudformation/index.ts > .build/template.out",  "deploy":  "npm run generate-aws-template && <proceed with deployment using .build/template.out>"  } 
```

Enter fullscreen mode Exit fullscreen mode

使用`cloudform`来简化和管理我们的 AWS CloudFormation 模板，可能还有很多其他创造性的方法。欢迎分享你的想法！

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)