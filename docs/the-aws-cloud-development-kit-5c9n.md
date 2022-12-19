# AWS 云开发工具包

> 原文：<https://dev.to/kayis/the-aws-cloud-development-kit-5c9n>

不久前 AWS 发布了[云开发套件](https://github.com/awslabs/aws-cdk) ( **CDK** )的*开发者预览*。管理 AWS 基础设施的工具...用 JavaScript！猜猜 JavaScript 真的吃掉了世界:D

## 什么

CDK 是云形成的必要替代物。

虽然 CF 使用 JSON 或 YAML 来定义你的服务，但 CDK 现在允许你用 JavaScript、TypeScript 和 Java 来定义服务。

NPM 上可用的版本是在 TypeScript 中实现的。

### 概念

CDK 使用名为 **construct** 的概念来定义基础设施。一个构造可以有子构造，所以它们形成一棵树。

构造要么是低级的 **CF 资源**，要么是高级的 **AWS 构造库**。

[CloudFormation 资源](https://awslabs.github.io/aws-cdk/cloudformation.html)被用作 AWS 构造库尚未提供的高级配置的后备。

AWS 构造库是用 TypeScript 编写的 NPM 包。基本上都是预先配置好的 CF 资源。

## 为什么

这个想法似乎是与一些开发人员已经用来实现他们的系统的工具更紧密地集成。如果你已经知道 JavaScript，你不必学习 CF 的自定义 YAML/JSON 方言

此外，CDK 中使用的资源供应方法比 CF 更高级。AWS 添加了一些预配置，因此定义可以更简洁。

也就是说，这并没有让我们不去学习 CF 是如何工作的。

## 如何

让我们以这个简单的 DynamoDB 为例:

```
const { Stack, App } = require("@aws-cdk/cdk");
const dynamodb = require("@aws-cdk/aws-dynamodb");

class MyStack extends Stack {
  constructor(parent, name, props) {
    super(parent, name, props);

    const table = new dynamodb.Table(this, "Table", {
      tableName: "MyAppTable",
      readCapacity: 5,
      writeCapacity: 5
    });

    table.addPartitionKey("Alias", dynamodb.KeyAttributeType.String);
    table.addSortKey("Timestamp", dynamodb.KeyAttributeType.String);
  }
}

const app = new App(process.argv);

new MyStack(app, "MyStack");

process.stdout.write(app.run()); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，CDK 库可以像其他节点包一样包含在内。

核心包定义了基本的结构。

*   `App`构造是我们应用程序的根，而`Stack` s 是它的直接子代，其他所有构造都是它的后代。
*   `Stack`结构是`App`的直接子结构，并且将所有资源作为子结构。

因为资源也是包，我们也可以简单地包含它们。

一个`Stack`必须在它的构造函数中定义它的资源。

资源定义是通过从资源类创建对象来完成的。

`dynamodb`包定义了一个`Table`类，它引用了一个`MyStack`、`name`和一个 config 对象，DynamoDB 用户应该对此很熟悉。

类似于`Stack`和`App`的`Table`对象也有添加*可选配置*的方法。

在定义了`Stack`之后，创建了一个由`App`构造和`Stack`构造组成的对象。`App`构造的对象也被作为`parent`传递给`Stack`对象。

最后，可以执行`App`构造来创建已定义的基础设施。

## 结论

AWS 云开发工具包带来了一种用 JavaScript 和 TypeScript 创建基础设施的新方法。

我认为这是对 AWS 工具领域的一个很好的补充。

这是开发中的*，所以我不应该要求太多，但对我来说，这有点像一个 C#开发人员创建了一个 JavaScript 库。整个传递`this`、扩展类和覆盖构造函数的过程让人感觉笨拙而不习惯。嵌套函数，比如 Reacts 无状态组件，可能是一个更简洁的想法。*

 *反正试试吧，在评论里说说你对它的看法！*