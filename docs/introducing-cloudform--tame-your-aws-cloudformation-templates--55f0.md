# 引入 cloud form——驯服您的 AWS CloudFormation 模板

> 原文：<https://dev.to/brightdevs/introducing-cloudform--tame-your-aws-cloudformation-templates--55f0>

无论我们在[光明发明](https://brightinventions.pl/)做什么，我们都非常关心自动化、可追溯性和可重复性。这就是我们采用 DevOps 相关实践的原因，如[连续交付](https://brightinventions.pl/blog/teamcity-for-ios-project/)或[集装箱化](https://brightinventions.pl/blog/dockerizing-android-builds/)，我们小心翼翼地正确设置[日志记录](https://brightinventions.pl/blog/http-request-logging-in-node/)和监控，当涉及到[可靠性和弹性](https://brightinventions.pl/blog/the-importance-of-timeouts/)时，我们知道自己的东西。这也是为什么每当我们在后端做任何事情时，我们都在 [AWS CloudFormation](https://aws.amazon.com/cloudformation/) 的巨大帮助下将我们的[基础设施定义为代码](https://en.wikipedia.org/wiki/Infrastructure_as_Code)。

## 我为什么要在乎？

在 AWS CloudFormation 中，运行应用程序所需的所有云基础设施——从硬件设置到网络层再到实际的应用程序环境——都是在 JSON(或 YAML) [模板文件](https://aws.amazon.com/cloudformation/aws-cloudformation-templates/)中定义的。这很方便，因为我们现在可以将文件保存在 GIT 中，并将其用作部署脚本的输入。

然而，问题是，除非我们的项目非常简单，否则我们的模板文件增长很快。我们基于[弹性容器服务](https://aws.amazon.com/ecs/)的典型环境设置，包括 VPC、故障转移、防火墙、负载平衡、自动伸缩、日志记录和监控，在我们的 JSON 模板文件中需要大约 2.5k 行。其中的一些构造——比如将 RDS 数据库连接字符串传递给包含多个串联参数的容器实例，其中一些参数是秘密的，另一些是由其他资源生成的——非常冗长和复杂。

[![Is this how your AWS CloudFormation templates looks like?](img/daf4b762dfdeeadf87b74e16bcf6cbd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LUQ9fv1c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8y6ldtzgnupsz5wlidr.jpg)

## 进入 cloudform

我们典型的模板文件太大了。它太冗长和重复了。这很容易出错，也很难理解。很乱。但是并没有全部丢失——模板只是一个普通的旧 JSON。 [`cloudform`](https://www.npmjs.com/package/cloudform) 就是这样诞生的。这是一种基于类型脚本的定义 AWS CloudFormation 模板的强制性方法。使用`cloudform`,您可以使用 TypeScript 的所有功能来定义您的基础设施的元素，并让库负责从中生成巨大的 JSON 文件。

因此，例如，要定义一个 [VPC](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html) ，而不是编写这段冗长而令人毛骨悚然的 JSON:

```
"VPC":  {  "Type":  "AWS::EC2::VPC",  "Properties":  {  "CidrBlock":  {  "Fn::FindInMap":  [  "NetworkingConfig",  "VPC",  "CIDR"  ]  },  "EnableDnsHostnames":  true,  "Tags":  [  {  "Key":  "Application",  "Value":  {  "Ref":  "AWS::StackName"  }  },  {  "Key":  "Network",  "Value":  "Public"  },  {  "Key":  "Name",  "Value":  {  "Fn::Join":  [  "-",  [  {  "Ref":  "AWS::StackId"  },  "VPC"  ]  ]  }  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

你可以有一个完全等价的`EC2.VPC`对象定义:

```
VPC: new EC2.VPC({
    CidrBlock: NetworkingConfig.VPC.CIDR,
    EnableDnsHostnames: true,
    Tags: [
        new ResourceTag('Application', Refs.StackName),
        new ResourceTag('Network', 'Public'),
        new ResourceTag('Name', Fn.Join('-', [Refs.StackId, 'VPC']))
    ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意我在这里做了什么:

1.  没有更明确的`Type`，没有`Properties`包装样板。TypeScript 的类型就足够搞清楚了。更重要的是，它给我们一个编译时检查结构的有效性。
2.  不再有用于`AWS::StackName`或`AWS::StackId`引用的神奇字符串。编译时常量。
3.  我直接使用 TypeScript 级别的对象，而不是复杂的`Fn::FindInMap`对象遍历。<small>是的，它会在模板中产生一个不同的条目，但在功能上是等效的。</small>
4.  我还简化了在`Tags`中使用的键值对的构造。强类型的一行代码，而不是冗长的 4 行代码。

但这并不是结束。使用`cloudform`(或者实际上使用 TypeScript)我们可以走得更远:

1.  没有什么能阻止我们使用 TypeScript 级别的变量、函数或条件从可重用、可配置的部分构建我们的对象。
2.  我们可以将模板定义分成多个文件。
3.  我们可以应用所有可用的重构技术来保持定义的整洁和易用。

使用`cloudform`的语法，我们大约 2.5k 行的 JSON 被减少到大约 1k 行的 TypeScript 对象定义。

## 入门&用法

您可能已经迫不及待地想测试它了，所以让我们直接进入问题的关键。显然，您可以在`npm`获得该套餐。键入以下内容:

`npm install --save-dev cloudform`

现在我们需要定义我们的模板。让我们为它建立一个单独的目录，这样它就不会与我们的生产或测试代码混淆。这也意味着我们可以将文件分成更小的块，并使用标准的[导入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)将它们链接在一起。也许`cloudformation/index.ts`？

这个文件需要以`cloudform`函数调用结束，这样工具才能正确地选择模板。它接受一个包含 CloudFormation 模板可能包含的所有内容的对象。

```
import cloudform, {Fn, Refs, EC2, StringParameter, ResourceTag} from "cloudform"

cloudform({
    Description: 'My template',
    Parameters: {
        DeployEnv: new StringParameter({
            Description: 'Deploy environment name',
            AllowedValues: ['dev', 'stage', 'production']
        })
    },

    Resources: {
        VPC: new EC2.VPC({
            CidrBlock: Fn.FindInMap('SubnetConfig', 'VPC', 'CIDR'),
            EnableDnsHostnames: true,
            Tags: [
                new ResourceTag('Application', Refs.StackName),
                new ResourceTag('Network', 'Public'),
                new ResourceTag('Name', Fn.Join('-', [Refs.StackId, 'VPC']))
            ]
        })
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里使用了简单的约定——所有 AWS 类型的名称空间都可以从`cloudform`包中直接导出。这个包里的所有资源都在里面。通过这种方式，我们示例中的`EC2.VPC`对象转化为`AWS::EC2::VPC`类型，我们可以在 CloudFormation 文档中找到[。所有属性也一一匹配，包括大小写。这种 API 级别的符合性保证成立，因为`cloudform`的类型是从](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html) [AWS 提供的模式定义文件](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-resource-specification.html)中生成的。

另请参见[储存库](https://github.com/bright/cloudform/blob/master/example/example.ts)中包含的示例。

现在，运行为您安装的二进制文件，指定模板源根目录的路径。这个库将把 JSON 打印到 stdout，所以您可能想把它通过管道传输到文件的某个地方:

`cloudform cloudformation/index.ts > template.out`

如果没有编译错误发生，`template.out`现在就是一个 JSON 文件，可以在 CloudFormation 中使用了。

## cloud form 不是什么

我已经能感觉到你的兴奋了😆，但是让我们明确一下`cloudform`做什么，什么不在它的范围之内。基本上，它将类型脚本代码按原样翻译成 JSON 对象。无论您在 TypeScript 中放入什么，只要它能够编译，就会包含在生成的文件中。没有执行任何验证或域级检查——如果您引用不存在的资源、参数或条件，或者为参数使用无意义的值，`cloudform`今天不会警告您(CloudFormation 很可能会这样做)。

这个包的目标不是为 CloudFormation 创建一个成熟的 DSL。而是从 TypeScript 特性中获得最大价值，所以如果要实现任何更复杂的检查，最有可能的是 TypeScript 可以在编译时验证的东西。如果你需要一个完整的验证，已经有一些专门的工具了(例如见 [`cfn-lint`](https://www.npmjs.com/package/cfn-lint) 或 [`cfn-check`](https://www.npmjs.com/package/cfn-check) )，所以把它放在`cloudform`之后可能是有意义的。

还要注意的是，`cloudform`不会试图以任何方式与您的 AWS 堆栈进行交互，也不会读写——不需要凭证。这使得实验 100%安全。所有的栈交互(即部署)都不在这个工具的范围之内。

## 接下来是什么？

这个包的源代码是 MIT 授权的，[可以在 GitHub](https://github.com/bright/cloudform) 上获得。如果您发现`cloudform`中缺少或无效的东西，可以在 GitHub 上自由发表，或者更好的是，贡献您自己！

在后续的帖子中，我将分享[更多的例子和方法](https://brightinventions.pl/blog/aws-cloudformation-patterns-practices-cloudform/)如何将模板生成构建到您的持续交付工作流中。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)