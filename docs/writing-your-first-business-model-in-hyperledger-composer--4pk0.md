# 在 Hyperledger Composer 中编写您的第一个业务模型

> 原文：<https://dev.to/skcript/writing-your-first-business-model-in-hyperledger-composer--4pk0>

本文着重于创建一个简单的业务模型，帮助您可视化 Hyperledger Composer 语言的抽象本质。

如果您不知道从哪里开始，或者在开始使用 Hyperledger Composer 时浪费了大量时间，您应该阅读这篇文章。我的同事，凡荣已经用一种简单的方式解释了它，任何人都可以理解。

## 什么是商业网络定义？

业务网络定义是保存 Hyperledger Composer 编程模型的核心定义。简而言之，它控制模型定义、它们之间的关系、对它们的访问控制以及可以对它们执行的操作。

BND 有三个核心组成部分:

1.  模型写在。cto 文件
2.  编写的业务逻辑。js 文件
3.  写入的访问控制逻辑。acl 文件

(可选)有一个用编写的查询文件。帮助我们查询持久数据库的 qry 文件。

这三个组件易于维护，它们管理我们在 Composer 上的整个业务应用程序。

让我们看看如何编写一个可管理的简单模型文件，帮助任何人理解我们的大部分业务逻辑。我们将在业务逻辑文件中实现这一点。

## 理解模型的关键概念

在编写我们自己的模型之前，让我们理解它的关键概念。在模型文件中，您将定义以下内容，

1.  命名空间命名空间 org . skscript . SVR
2.  资源，包括
    1.  参与者
    2.  资产
    3.  处理
    4.  事件
3.  用于继承或导入其他模型文件的可选导入语句。

## 资源是 CTO 文件中的一切

“参与者”表示采取行动的人(简单来说)。这是向其发放 ID 的人，也是执行交易的人。

例如:考虑这个例子

```
participant Author identified by id {
    o Integer id
    o String name
    o String email
    o String specialized_in 
    o Double age
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们看到有一个名为`Author`的参与者，关键字`identified by`用于选择主键。在这里，充当主键的是`id`字段。我们将会知道在右边区域有这个`identified by`的广泛应用，以便对模型有一个容易和可读的访问。

圆圈只不过是小写的“O”(O，耶！).Composer 的模型文件有一种独特的方式来表示对象中的新键。但是你会习惯的。

资产是我们商业模式所拥有的资源。考虑到我们上面的例子，`Author`是参与者，那么`Post`将是他持有的资产。我们知道每一篇文章都属于一个作者。永远不会有不属于作者的资产。这可以通过在我们的参与者和资产之间建立关系来实现。这很容易，因为 CTO 的建模语言。

另外，你应该小心关系，因为它是单向的，你不能像大多数应用程序框架那样反过来。

示例:

```
/**
* An enumerated type
*/
enum Category {
  o AI
  o BLOCKCHAIN
  o WEB-DEVELOPMENT
  o BACKEND
  o DESIGN
  o FUN
}

/**
 * A post asset.
 */
asset Post identified by id {
  o String id
  o String title
  o Category category
  o DateTime timestamp
  --> Author author
  --> Author coAuthor optional
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，正如我们之前了解到的，`identified by`与`id`一起作为主键使用，这是有意义的。对吗？现在你可以理解了。

但是新的东西在哪里出现呢？不急。让我们慢慢来。让我们看看`Line 21`——我们看到一个箭头。好吧。我没有写它，因为它在代码上看起来很好，它也不是一个注释。这又是我们的作曲家的建模语言书写关系的怪异方式。所以我们应该把它当作文章属于作者来读。这很简单。完成了。
在`Line 22`中，我们可以看到有一个新的关键词`optional`。这有助于我们避免创建资产的强制输入。这被定义为可选的，因为考虑到特定的帖子可能有也可能没有合著者。

接下来，为什么是`enum`？这是为了管理整洁的代码。因为我们文章的目标不仅仅是学习如何写一个模型，而且还要写一个好的模型，易于阅读和更新。
所以 enum 保存了我们的字段可以取的可能值。所以我们定义了一个叫做`Category`的`enum`，并赋予它所有可能的值。现在在`Line 19`中，我们使用它并创建了一个`enum Category`的类别。

交易是我们的参与者对资产进行操作的过程。考虑到我们的场景，作者创建帖子也是一个事务。
举例:

```
transaction createPost {
  --> Author owner
  o String title
  o Category category
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的事务定义通过指出帖子的作者强调了我们正在创建一个帖子。我们还提供标题和类别，同时创建职位。

页（page 的缩写）在我们的下一篇文章中，我们将学习更多关于事务和事务处理器的功能。

事件用于触发一些其他第三方应用程序。您可以为事件编写逻辑，这将有助于您发送关于作者新帖的电子邮件。这些事件可以用来帮助您发送电子邮件，当且仅当您订阅了特定的作者，这有助于避免垃圾邮件和提高完整性。事件的定义也与事务相同。目的只是不同。与`event`关键字一起用于声明。

## CTO 档案的无限可能

您仍然可以通过关键字`abstract`分离模型声明，该关键字不会在注册表中创建参与者或资产，而是用于从其他资源继承。

示例:考虑相同的场景，试着理解下面的代码

```
abstract Participant UserInformation identified by name {
  o String name
  o String address
  o String email
  o Integer age
}

participant Author identified by id {
    o Integer id
    o String specialized_in 
    o UserInformation user_information
} 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了一个`abstract`参与者，它保存了用户的大部分信息，我们在`Author`参与者中继承并使用了这些信息。这有助于我们有一个干净的模型，仍然保持单一责任原则。我们也可以对资产和交易进行抽象。

## 结论

我们可以用 CTO 文件做更多像`extends`这样神奇的事情。但是让我们干净利落地结束它。在您编写了第一个模型之后，您将会明白为了拥有一个可维护的代码，您可能需要遵循的大多数约定。祝你好运，读者们。