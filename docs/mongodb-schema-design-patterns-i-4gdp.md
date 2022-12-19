# MongoDB 模式设计模式(一)

> 原文：<https://dev.to/mrm8488/mongodb-schema-design-patterns-i-4gdp>

[![](img/0ca9faadba4b317fac8c679e3db9085e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u4IDWbLq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q2skcobs31lu94rj80s3.png)

MongoDB 已经成为最流行的 noSQL 数据库之一。它经常被用作均值/MERN 堆栈的一部分，因为它很容易适应 JavaScript 生态系统。
网上有成百上千的教程，成吨的课程，还有一些关于如何成为使用 MongoDB 作为栈中数据库系统的全栈开发人员的书籍(MERN 的 M/MEAN)。
问题是，他们中的大多数人并不关注 MongoDB 模式设计模式。因此，对设计模式的操作/查询具有很差的性能和/或不可伸缩性。

在设计 MongoDB 模式时，您必须面对的一个主要问题是如何建模“一对 N”(一对多)关系。

许多初学者认为在 MongoDB 中建模“一对 N”的唯一方法是将一组子文档嵌入到父文档中，但这并不正确。仅仅因为您可以嵌入文档，并不意味着您应该嵌入文档。事实上，无限增长的阵列会降低性能。此外，最大文件大小为 16MB。

在设计 MongoDB 模式时，您必须从这个问题开始:关系的基数是多少？是**【一对几】****【一对多】**，还是**【一对千万亿】**？根据是哪一种，您可以使用不同的格式来建立关系模型。

## 一对多

“一对几”的一个例子可能是一个人的地址。这是一个很好的嵌入用例——将地址放在 Person 对象的数组中:

```
> db.person.findOne()
{
  name: 'Manuel Romero',
  ssn: '123-456-7890',
  addresses : [
     { street: '123 Sesame St', city: 'Anytown', cc: 'USA' },
     { street: '123 Avenue Q', city: 'New York', cc: 'USA' }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

优点:

*   主要优点是您不必执行单独的查询来获取嵌入的细节。

缺点:

*   主要的缺点是您无法访问作为独立实体的嵌入细节。

## 一对多

“一对多”的一个例子可能是替换零件订购系统中的产品零件。每个产品可能有多达数百个替换零件，但绝不会超过几千个左右。(所有这些不同尺寸的螺栓、垫圈和垫片加在一起。)这是一个很好的引用用例——您可以将部件的 ObjectIDs 放在产品文档的一个数组中。

零件文件:

```
> db.parts.findOne()
{
    _id : ObjectID('AAAA'),
    partno : '123-aff-456',
    name : '#4 grommet',
    qty: 94,
    cost: 0.94,
    price: 3.99
} 
```

Enter fullscreen mode Exit fullscreen mode

产品文档:

```
> db.products.findOne()
{
    name : 'left-handed smoke shifter',
    manufacturer : 'Acme Corp',
    catalog_number: 1234,
    parts : [     // array of references to Part documents
        ObjectID('AAAA...'),    // reference to the #4 grommet above
        ObjectID('F17C...'),    // reference to a different Part
        ObjectID('D2AA...'),
        // etc
    ] 
```

Enter fullscreen mode Exit fullscreen mode

优点:

*   每个部分都是一个独立的文档，因此很容易搜索它们并独立更新它们。

*   这个模式允许多个产品使用不同的部分，所以一对多模式变成了一个 N 对 N 模式，不需要任何连接表！

缺点:

*   必须执行第二次查询来获得产品部件的详细信息。

### 一对多与规格化

假设对我们的产品集合的一个频繁操作是:给定一个部件的名称，查询该部件对于该产品是否存在。使用我们已经实现的方法，我们将有两个执行几个查询。一个获取产品所有部件的 ObjectIDs，另一个获取部件的名称。但是，如果这是我们的应用程序的一个常见数据访问模式，我们可以**将部件的字段*名称*反规格化为产品部件的数组:** 

```
> db.products.findOne()
{
    name : 'left-handed smoke shifter',
    manufacturer : 'Acme Corp',
    catalog_number: 1234,
    parts : [
        {
         ObjectID('AAAA...'),
         name: '#4 grommet'
        },
        {
         ObjectID('F17C...'),    
         name: '#5 another part name'
        },
        {
         ObjectID('D2AA...'),
         name: '#3 another part name 2'
        }
        // etc
    ] 
```

Enter fullscreen mode Exit fullscreen mode

优点:

*   我们可以通过一个查询看到属于一个产品(它的名字)的所有部分。

缺点:

*   当反规格化的字段(在我们的例子中是 *name* 字段)很少更新时，反规格化是有意义的。如果我们对一个频繁更新的字段进行反规范化，那么查找和更新所有实例的额外工作很可能会超过反规范化所节省的成本。一个零件的名字很少会变，所以对我们来说没问题。

## 十亿分之一

“一对一”的一个例子可能是为不同机器收集日志消息的事件记录系统。任何给定的主机都可能生成足够多的消息来溢出 16 MB 的文档大小，即使您在数组中存储的只是 ObjectID。这是“父引用”的经典用例—您有一个主机文档，然后将主机的 ObjectID 存储在日志消息的文档中。

宿主文档:

```
> db.hosts.findOne()
{
    _id : ObjectID('AAA2...'),
    name : 'goofy.example.com',
    ipaddr : '127.66.66.66'
} 
```

Enter fullscreen mode Exit fullscreen mode

消息文档:

```
>db.logmsg.findOne()
{
    time : ISODate("2014-03-28T09:42:41.382Z"),
    message : 'cpu is on fire!',
    host: ObjectID('AAA2...')       // Reference to the Host document
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

基于一对多关系的基数，我们可以从三种基本的一对多模式设计中选择一种:

1.  如果基数是一对几，并且不需要访问父对象上下文之外的嵌入对象，则嵌入 N 端。

2.  如果基数是一对多的，或者如果 N 端对象由于任何原因应该是独立的，则使用对 N 端对象的引用数组。

3.  如果基数是一比十亿，则使用对 N 边对象中的一个边的引用。

请记住:**我们如何对数据建模完全取决于我们特定应用的数据访问模式**。我们希望构建我们的数据，以匹配我们的应用程序查询和更新数据的方式。

[参考](https://www.mongodb.com/blog/post/6-rules-of-thumb-for-mongodb-schema-design-part-1)