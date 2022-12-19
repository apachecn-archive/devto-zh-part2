# 博客文章:查看基于 RDBMS 的合同

> 原文：<https://dev.to/jonasbn/blog-post-view-based-contracts-for-your-rdbms-5g7n>

我们在一个相当老的学校标准堆栈上构建我们的应用程序。它由一个基于 RDBMS 的 web 应用程序组成。

昨天我开始修复一个 bug，在*长时间追逐*应用层的 bug 后，我最终在数据库中修复了它，这相当容易，不需要任何部署，从数据库的改变来看，这样做的好处是:它马上就能工作，并且很容易测试。

这让我想到是时候写一篇关于这种*架构*模式的文章了，我们在这方面取得了很大的成功。

我们的 RDBMS 由您的标准 RDBMS 数据模型组成。范式。在此基础上，我们将数据库封装在视图中。

差不多就是这样，但是让我用一个基本的例子来演示一下。

[![model](../Images/88902cd73fbfa18e5377a13c45b65aa0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MT-PFCmN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lastmover.files.wordpress.com/2018/07/model.png%3Fw%3D277%26h%3D300)

这里我们有一个由两个表组成的基本数据库模型，一个表包含地址数据，另一个表包含邮政编码数据。

```
CREATE TABLE address ( street TEXT NOT NULL, no TEXT NOT NULL, floor TEXT NOTNULL, door TEXT NOT NULL, zipcode TEXT NOT NULL, country TEXT NOT NULL, FOREIGN KEY (zipcode) REFERENCES zipcode (zipcode));CREATE TABLE zipcode ( zipcode TEXT PRIMARY KEY, city TEXT NOT NULL); 
```

Enter fullscreen mode Exit fullscreen mode

如果您想尝试所提供的示例，这个示例是在一个 **SQLite** 数据库上实现的。

基本概念是直接使用数据模型，但是只能使用视图访问。所以我们添加了第一个视图。

```
CREATE VIEW zipcode\_v1 AS SELECT \* FROM zipcode; 
```

Enter fullscreen mode Exit fullscreen mode

请注意命名约定，即保留封装表的名称，并附加一个:" _v1 "，稍后会详细介绍。

[![zipcode_v1](../Images/017908f838dd16e2923e05cbe847dc04.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--olmM4Xy8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lastmover.files.wordpress.com/2018/07/zipcode_v1.png%3Fw%3D660)

这是其中一个简单的方法，目前它看起来很傻，需要大量的工作，我听到的对这种方法的一个担忧是:

> “但是我们的数据库将充满对象”

是的，这至少会使数据库中的对象数量翻倍，但是这种方法的好处超过了维护。这些物品是有用途的。

这个视图的用例是什么。它可以用来在 web 应用程序或类似程序中填充带有邮政编码的下拉列表。

一对一视图实现的下一个自然步骤是更复杂的数据类型和查询。所以你想查询包括城市在内的完整地址，你就必须这样做:

```
SELECT a.street AS street, a.no AS no, a.floor AS floor, a.door AS door, a.country AS country, z.zipcode AS zipcode, z.city AS cityFROM zipcode z, address aWHERE z.zipcode = a.zipcode; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们可以做:

```
SELECT street, no, floor, door, country, zipcode, cityFROM address\_v1; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以，只是创建一个视图来帮助我们。

```
CREATE VIEW fulladdress\_v1 AS SELECT a.street AS street, a.no AS no, a.floor AS floor, a.door AS door, a.country AS country, z.zipcode AS zipcode, z.city AS cityFROM zipcode z, address aWHERE z.zipcode = a.zipcode; 
```

Enter fullscreen mode Exit fullscreen mode

我们的数据库现在看起来像这样:

[![address_v1](../Images/4e37b0962cef17b3e62e3935b640dc84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sGyD683c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lastmover.files.wordpress.com/2018/07/address_v1.png%3Fw%3D660)

我们正在慢慢地用我们的 RDBMS 组装合同。

正如您所看到的视图的命名一样，*一对一的*实现重用了封装的表的名称，而封装了多个视图的视图是通过意图或目的来命名的，表明它们的目标是什么。

> 但是这样够快吗？

在现代的 RDBMS 中，我希望*的性能影响*是微不足道的，但是我没有足够的数据来支持这一点，也许另一篇博客文章应该对此有所启发。请注意，这是一种架构方法，它还有其他好处，其目标不是高性能，而是可维护性和简单性。

所以现在我们已经完全封装了我们的模型。

1.  只要合同保持不变，我们可以更换型号

接下来是一个有点糟糕的例子，但是想象一下 T2 有人想要改变模型。我们不想听起来太美国化，所以**邮政编码**必须换成**邮政编码**。

```
PRAGMA foreign\_keys=off;BEGIN TRANSACTION;DROP VIEW fulladdress\_v1;ALTER TABLE address RENAME TO \_address\_old;CREATE TABLE address ( street TEXT NOT NULL, no TEXT NOT NULL, floor TEXT NOTNULL, door TEXT NOT NULL, postal\_code TEXT NOT NULL, country TEXT NOT NULL, FOREIGN KEY (postal\_code) REFERENCES zipcode (zipcode));INSERT INTO address (street, no, floor, door, postal\_code, country) SELECT (street, no, floor, door, zipcode, country) FROM \_address\_old;CREATE VIEW fulladdress\_v1 AS SELECT a.street AS street, a.no AS no, a.floor AS floor, a.door AS door, a.country AS country, z.zipcode AS zipcode, z.city AS cityFROM zipcode z, address aWHERE z.zipcode = a.postal\_code;COMMIT;PRAGMA foreign\_keys=on; 
```

Enter fullscreen mode Exit fullscreen mode

在 **SQLite** 中重命名一个列有点麻烦，请原谅，选择另一个实现而不是 **SQLite** 会使上面的例子更短。

1.  首先我们放弃这个观点
2.  我们重命名旧表
3.  我们用所需的列名更改创建新表
4.  我们将旧表中的数据复制到新表中
5.  我们重新创建视图，封装变更并保持我们的契约

[![address_v1-2](../Images/3c59ef0673a1720a244130197176b8d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VYagSXXw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lastmover.files.wordpress.com/2018/07/address_v1-2.png%3Fw%3D660)

如果我们继续进行重命名，那么通过外键涉及的 zipcode 表也必须被重命名，我没有包括这个例子，但是模式是相同的，好处也是相同的，它可以被重命名，但是封装保持了契约的完整性，并且我们使用数据库的应用程序不必被改变。

这在另一个用例中非常有用，考虑到您有一个现有的模型，您想要将它暴露给某个领域特定的区域。然后，您可以保留您的原始模型，并在视图中公开数据，在视图中，数据按照领域特定区域的命名来呈现。

总的来说，一切看起来都不怎么样。但也有一些陷阱，以整个*观点为基础的契约*方法，举几个最突出的例子:

1.  命名
2.  透明度
3.  维护
4.  信息泄露

所以让我们来看看这些。

命名是困难的，对于一对一的视图，您受模型的支配，这没什么，但是如果您的模型中已经有了不好的命名，这将反映在视图的命名中，因此可以决定在封装层中消除不好的命名，这将我们带到透明性。

为了透明，建议保留原始模型的名称，因为数据模型通常会嵌入到数据库的用户中。请注意，我们是在现有数据模型的基础上实现视图的，所以人们经常求助于与实际模型相关联，而不是抽象/封装——如果我们在一些讨论中能够坚持抽象而不是实现就好了 [![🙂](../Images/2ea3454d48c385c4b31b2c45b77b4229.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zCyXRrdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f642.png)

为意图命名更难，但更像是一种恰当的抽象，而不是一对一的映射。我们开始为我们的服务使用视图，但是它确实传播到了我们的批处理组件中，在那里它被证明是非常有用的。

我们会观察到批处理组件变得越来越小，因为决策逻辑被移到了数据库契约中。用于删除记录的脚本将简单地只在视图上工作，其中所有符合删除条件的记录将可用于处理，并且不符合删除条件的记录将永远不会由视图呈现给执行应用程序。

当涉及到维护和生命周期时，当视图过时时，我们就删除它们。这尤其适用于基于意图的视图，我们可以看到我们有几个修订版:“_v1”、“_v2”和“_v3”。当我们看到没有应用程序再使用“_v1”时，我们简单地删除它。

删除的另一个例子是当一个视图实现业务规则时，这些业务规则不再适用，因此不应该可用。

如本文开头所述，我们可以创建如下视图:

```
CREATE VIEW zipcode\_v1 AS SELECT \* FROM zipcode; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这种方法为通过合同公开的模型扩展打开了大门，如果您不想自动公开，您的视图应限制在仅提供您在合同中同意的字段的实现中。

我在文章开头提到的 bug 在某种程度上与这种信息泄漏有关，一个字段，没有被封装很好地处理，因此暴露了出来。

一个简单的例子是，如果我们的数据库扩展了关于谁插入了一个实际记录的信息，可以说是由字段创建的，而不是由:

```
CREATE VIEW zipcode\_v1 AS SELECT \* FROM zipcode; 
```

Enter fullscreen mode Exit fullscreen mode

我们应该做:

```
CREATE VIEW public\_zipcode\_v1 AS SELECT zipcode, city FROM zipcode; 
```

Enter fullscreen mode Exit fullscreen mode

您可以为您的内部应用程序定义一个类似的视图，如下所示:

```
CREATE VIEW public\_zipcode\_v1 AS SELECT zipcode, city FROM zipcode; 
```

Enter fullscreen mode Exit fullscreen mode

所以你现在有两个视图命名，意图和不泄漏我们的合同以外的信息。当然还有对象引用的问题，因为我们的视图本身并没有限制跨对象所有权/关系的访问，这是另一篇博客文章的主题，但是这种方法确实只公开了您有兴趣通过应用程序提供的数据，而不一定是您的完整模型和数据集。

> 但是我们用的是 ORM？

我们将您的 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) 模式/代码生成器指向视图，而不是实际的模型。这也有一些陷阱。因为不是所有的 RDBMS 都支持可写视图，所以如果您是一个大量使用 ORM 的用户，并且数据库不支持可写视图，那么您可能不太喜欢这种契约方法。与存储过程或类似方法的结合可能是可行的，这提醒了我，我必须提到我的前任经理 Haktan Bulut，他在一个架构设计规范中向我介绍了这种方法。

这个概念很简单，但看起来需要做很多工作，然而我得出的结论是，当它建立起来时，它为我们节省了很多工作，只要我们的合同是合理的，我们的应用程序就可以减少:

*   使用契约方法更容易理解组件之间的交互
*   我们不会向应用程序公开不必要的数据

它需要时间来封装一切，并且需要一些努力来维护，但是在我看来，将数据公开的更改置于控制之下是一件好事，因为我们总是必须考虑当我们延长或限制合同时我们做了什么，最后但同样重要的是，我们可以在不中断应用程序的情况下优化模型。