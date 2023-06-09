# 在 Postgres 上使用 GraphQL 处理无模式数据

> 原文：<https://dev.to/hasurahq/working-with-schemaless-data-using-graphql-on-postgres-54jn>

使用开源的 Hasura GraphQL 引擎在 GraphQL 查询中利用 Postgres 无模式 JSON 类型。

## TL；速度三角形定位法(dead reckoning)

以下是这篇文章内容的摘要

*   无模式数据是不符合严格模式的数据。通常存储为键值对或 JSON。您可以使用`JSON`和`JSONB`列类型在您的表中存储 JSON 数据。
*   使用 [Hasura GraphQL 引擎](https://github.com/hasura/graphql-engine)，您可以在 Postgres 上获得即时 GraphQL APIs，您可以使用它来存储和检索表中的数据。
*   我们使用 Postgres 视图过滤 JSON 数据
*   此时使用 GraphQL 对 JSON 数据进行变异操作是不可能的，因此，要进行更新，您需要用新数据替换整个 JSON。

## 什么是无模式数据？

无模式数据是不符合严格模式的数据。它通常以键值对的形式存储，或者作为 JSON 文档存储。

在 Postgres 这样的关系数据库中，这意味着使用 JSON 数据类型。

### 什么时候使用无模式数据？

关系建模可用于大多数应用程序。但是在一些用例中，将数据存储为 JSON 文档是有意义的:

*   通过将数据存储为一个 JSON 文档，可以避免保存独立数据的表上的复杂连接。
*   如果您依赖于来自外部 API(如 JSON)的数据，可以避免将这些数据规范化到不同的表中的过程。相反，您可以按照接收数据时的格式和结构来存储这些数据。
*   在依赖于模式不固定的数据的情况下，这也很有帮助。

## 在 Postgres 上处理无模式数据

PostgreSQL 提供两种数据类型来存储 JSON 元素:`JSON`和`JSONB`。它们之间的主要区别是效率。`JSON`被存储为文本，而`JSONB`首先被分解为二进制分量，然后被存储。这使得对`JSONB`的插入变慢了，但是对子元素的解析变快了。

## 示例用例

让我们举一个例子，我们有一个第三方 API 为我们提供用户的`name`和`address`。`address`字段可以有多个字段，如`apartment number`、`street name`、`pincode`等。`address`在这种情况下不遵循一种僵化的模式。

### 问题陈述:

*   找到一种方法将 API 提供的完整信息存储到我们数据库的一个表中。
*   提供一个 API，根据用户的地址密码过滤用户。

### 解决方案概述:

*   创建一个包含列`id` *`primary key`* 、`name`和`address`的`user`表。`address`将属于`JSONB`类型。
*   使用 GraphQL insert 突变将数据插入该表。
*   创建一个视图`user_address`,通过从`user`表的`address`列中检索来显示用户的`id`和他们的`pincode`。
*   使用 GraphQL 从`user_address`获取数据。

好了，让我们开始实施我们的解决方案。

## 通过 Postgres 获取 GraphQL APIs

我们将使用 Hasura GraphQL 引擎通过 Postgres 即时获取 GraphQL APIs。点击下面的按钮，将 GraphQL 引擎部署到 Heroku 的自由层。

<figure>[![Hasura on Heroku](img/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)](https://heroku.com/deploy?template=https://github.com/hasura/graphql-engine-heroku) 

<figcaption>点击此按钮将 GraphQL 引擎部署到 Heroku</figcaption>

</figure>

这将把`graphql-engine`部署到 Heroku。如果您没有 Heroku 帐户，您可能需要创建一个。`graphql-engine`将在`https://your-app.herokuapp.com`运行(用你的 heroku 应用名称替换`your-app`)。

## 控制台

Graphql 引擎附带了一个名为`Console`的管理 UI。您可以使用`Console`为您的应用程序构建后端。

它运行在您的`graphql-engine` URL 的`/console`端点，在本例中是`https://your-app.herokuapp.com/console`。`console`的登陆页面看起来是这样的:

[![](img/62274f80491c999fc62d3bb84f7864b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P1CgRPHa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/working-with-schemaless-data-with-graphql-on-postgres-574a1ee2e87f/1-CuBaxCvX-UlNLsSCBRx0uA.png)

## 创建表存储 JSON 数据

在`Console`上，转到`Data`选项卡，点击`Create Table`创建一个新表。

`user`表:

*   `id` _ **整数**主键 _
*   `name` **正文**
*   `address` **JSONB**

[![](img/b9c3e303feb71e411b0c525bac3204a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4NzMdyy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/working-with-schemaless-data-with-graphql-on-postgres-574a1ee2e87f/1-aIDqZHRYSk1Wb5RCXIsjjQ.png)

点击`create`按钮创建表格。

## 将 JSON 数据插入表中

将数据插入到`user`表中的 GraphQL 突变将是

```
mutation addUser($objects: [user_input]) {
 insert_user(objects: $objects) {
    affected_rows
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

变量

```
{
"objects": [
    {
      "id": 1,
      "name": "Jack Smith",
      "address": {
        "house_number": "112",
        "house_name": "XYZ Apartments",
        "street_name": "ABC Street",
        "city": "Bengaluru",
        "pincode": "123456"
      }
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**你可以在`API Console`的`API Explorer`里试用 GraphQL APIs。*

## 使用 GraphQL 获取基于 JSON 属性的数据

目前，我们不能通过存储的 JSON 的不同属性直接过滤数据。但是，我们可以创建一个包含所有这些数据的视图，然后查询该视图。

前往`API Console`上的`Data`选项卡，点击左侧的`SQL`。运行以下 SQL 命令。

```
CREATE VIEW user_address AS
SELECT id as user_id, address->>'pincode' as pincodeFROM "user"; 
```

Enter fullscreen mode Exit fullscreen mode

_ **注意**:确保在运行查询之前选中 **`Track Table`** 复选框，这样您就可以使用数据 API 来查询视图。_

这将创建一个名为 **`user_address`** 的视图，以 **`user_id`** 和 **`pincode`** 为列。

[![](img/5185f76dac06715e0cb3e67b8e0f8d10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--luKHIROM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/working-with-schemaless-data-with-graphql-on-postgres-574a1ee2e87f/1-lDUS_YsqwjlXemkswp1b3Q.png)

我们现在可以[从这个视图中获取数据](https://docs.hasura.io/0.15/manual/data/select.html),就像您从表中获取数据一样。

```
query user_on_pincode{
 user_address(where: {pincode: "123456"}) {
    user_id
    pincode
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 从用户表中过滤

您也可以基于`pincode`直接从`user`表中过滤和提取数据。您需要将`user_address`视图作为`object`关系添加到`user`表中。

为此，前往`API Console`中的`Data`选项卡。点击左边面板上的`user`。点击`Relationship`选项卡并点击`Add a manual relationship`按钮。在出现的表单中，输入以下信息

*   关系类型将是 **`Object Relationship`**
*   关系名称可以是“地址信息”
*   配置:**`id :: user_address -> user_id`T2】**

[![](img/dd4bddb35ab7a7fdcda88a81255933e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V5TJK7rM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/working-with-schemaless-data-with-graphql-on-postgres-574a1ee2e87f/1-Kdf4Bpc7PdpRSnKxGbiotg.png)

您现在可以通过密码过滤 **`user`** 表格。

```
query get_user_by_pincode {
  user(
    where: {
       address_info: { pincode: "123456" }
     }
   ) {
    id
    name
    address_info {
      pincode
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更新 JSON 数据

目前，还不支持在 JSON 内部直接操作数据。要进行更新，您必须使用 an update 变体替换整个 JSON 文档。

```
mutation update_user_address {
update_user(
    where: {id: 1}
    _set: $new_address
  ) {
    returning {
      id
      address
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

变量

```
{
"new_address": {
    "address": {
        "house_number": "120",
        "house_name": "NEW XYZ Apartments",
        "street_name": "NEW ABC Street",
        "city": "Bengaluru",
        "pincode": "456789"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

希望这是在 Hasura 上使用无模式数据的良好开端。要了解更多关于 JSON 和 JSONB 数据类型的信息，您可以在这里查看 Postgres 文档。

如果您有任何问题或建议，不要忘记发表评论！