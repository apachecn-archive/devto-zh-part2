# Hasura GraphQL 引擎上的 GraphQL 和带 Postgres 的树数据结构

> 原文：<https://dev.to/hasurahq/graphql-and-tree-data-structures-with-postgres-on-hasura-graphql-engine-580o>

在 postgres 上建模一个树数据结构，并使用 GraphQL 从树中添加和获取数据。

[![](img/df9ab21d1b82db20885ddbccbbb8e995.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pQrMAa5J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/graphql-and-tree-data-structures-with-postgres-on-hasura-dfa13c0d9b5f/1-hMpPgGrfcSTl8Y5IhbQPgQ.png)

## 什么时候使用树形数据结构？

递归或树数据结构通常用于对模式进行建模，以存储具有层次结构的数据。

树形数据结构的常见用例包括:

*   为像 Google Drive 这样的应用程序存储一个目录结构，在这个目录结构中你会有一个根目录。在这个根目录中，您可能有其他目录或文件。任何嵌套级别的每个目录都可以包含任意数量的文件或目录。
*   博客或论坛的评论线程，其中每个帖子或主题都可以有评论，每个评论又可以有评论(或回复)。
*   为 LinkedIn 或脸书这样的网络应用程序寻找共同的关系或朋友。

在这篇博文中，我们将看看如何对数据库建模，为应用程序构建评论线程特性。此外，我们还将使用 GraphQL 来添加和删除评论。

让我们以一个非常简单的博客为例，为每篇文章添加评论。详细说明我们需要什么

*   每个帖子都可以有评论
*   每个评论都可以有回复(子评论)
*   我们希望按照创建时间排序的方式列出这些评论。

我们将使用 Hasura GraphQL 引擎，因为它为 Postgres 数据库提供了即时 GraphQL 查询。

## 入门

我们将使用 Hasura GraphQL 引擎通过 Postgres 即时获取 GraphQL APIs。点击下面的按钮，将 GraphQL 引擎部署到 Heroku 的自由层。

<figure>[![Hasura on Heroku](img/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)](https://heroku.com/deploy?template=https://github.com/hasura/graphql-engine-heroku) 

<figcaption>点击此按钮将 GraphQL 引擎部署到 Heroku</figcaption>

</figure>

这将把`graphql-engine`部署到 Heroku。如果您没有 Heroku 帐户，您可能需要创建一个。`graphql-engine`将在`https://your-app.herokuapp.com`运行(用你的 heroku 应用名称替换`your-app`)。

## *T3】API 控制台 T5】*

每个 Hasura 集群都有一个`API Console`,您可以用它来构建应用程序的后端。

API 控制台位于 [`https://your-app.herokuapp.com`](https://your-app.herokuapp.com) `/console`

[![](img/9ec395eb3a5910ae5517767613b11a01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VPod5MCu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/graphql-and-tree-data-structures-with-postgres-on-hasura-dfa13c0d9b5f/1-0QX6Ejk4A3swZD6u9OZNZg.png)

## 创建表格

转到`Data`选项卡，点击`Create Table`创建一个新表

让我们调用我们的表`post_comments`来存储每个帖子的评论。该表将包含以下列

*   **`id`** 整数(自动递增)*主键*
*   **`parent_id`** 整数*可空*
*   **`comment`** 正文
*   **`created_at`** 时间戳*默认现在()*
*   **`post_id`** 整数
*   **`user_id`** 整数

[![](img/52509222054a998d5a2ccfd4d3aff8df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7JKHZRkz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/graphql-and-tree-data-structures-with-postgres-on-hasura-dfa13c0d9b5f/1-_JaBTlZ35nWZyeaE3nHcCA.png)

点击`create`按钮创建表格。

这是我们将存储每个博客帖子的所有评论的表。

### 创建自我引用

接下来，让我们定义一个从`parent_id`列到`id`列的外键约束。

为此，转到`Modify`选项卡，点击`parent_id`旁边的`Edit`按钮。勾选`Foreign Key`复选框，选择`post_comments`作为参考表，选择`id`作为参考列。

[![](img/3b20fef385b35907551f1174f4779fd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2IP2GdeJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/graphql-and-tree-data-structures-with-postgres-on-hasura-dfa13c0d9b5f/1-K_FWIG1pSefcVQ0tkuAiJg.png)

### 添加一个[关系](https://docs.hasura.io/0.15/manual/data/relationships/index.html)来获取子注释

接下来，点击`Relationship`选项卡，并点击`Suggested Array Relationship`栏下的`Add`按钮。命名关系`children_comments`

[![](img/005204fe6d4ab102e2943a8a7dfd2247.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iLKNzJ1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/graphql-and-tree-data-structures-with-postgres-on-hasura-dfa13c0d9b5f/1-INIXEHedYj2jo7wxgoy4bQ.png)

点击`Save`添加此关系。

## 获取和添加注释

对**插入一个注释**的突变将会是

```
mutation add_comment {
  insert_post_comments(objects: $objects) {
    returning{
      id
      parent_id
      comment
      created_at
      user_id
      post_id
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`$objects`变量将是

```
{
  "objects": [
    {
      "user_id": 1,
      "post_id": 1,
      "comment": "First comment on post 1",
      "parent_id": null
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

类似地，`$objects`变量添加对评论
的回复

```
{
 "objects": [
    {
      "user_id": 1,
      "post_id": 1,
      "comment": "First comment on post 1",
      "parent_id": 1 //Or id of the comment to which this comment is a reply to
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 抓取评论

如果我们知道评论中的嵌套层次，那么获取帖子的评论和所有子评论的 GraphQL 查询应该是

```
query get_comments_for_post {
  post_comments(
    where: {
      post_id: 1
      parent_id: null
    }
     order_by: ["+created_at"]
  ) {
    id
    parent_id
    comment
    created_at
    user_id
    post_id
    children_comments (
      order_by: ["+created_at"]
    ){
      id
      parent_id
      comment
      created_at
      user_id
      post_id
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们获取一篇帖子的所有评论，该帖子的`id`值为`1`，其`parent_id`为`null`。然后我们获取所有的回复(`children_comments`)作为关系。`order_by`字段中的`+created_at`表示注释应以升序提取(基于`created_at`的值)。或者，`-`表示下降。如果查询中没有指定符号，则默认采用`+`。

类似地，如果有另一层嵌套，查询将是

```
query get_comments_for_post {
 post_comments(
    where: {
      post_id: 2
      parent_id: null
    }
     order_by: ["+created_at"]
  ) {
    id
    parent_id
    comment
    created_at
    user_id
    post_id
    children_comments (
      order_by: ["+created_at"]
    ){
      id
      parent_id
      comment
      created_at
      user_id
      post_id
      children_comments(
        order_by: ["+created_at"]
      ) {
        id
        parent_id
        comment
        created_at
        user_id
        post_id
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理未知层次的嵌套

像 https://news.ycombinator.com/这样的网站允许任何层次的嵌套。这意味着每个注释可以有一个以上的子注释。在这种情况下，像上面那样获取注释不起作用，因为我们不知道需要获取多少级别。

处理这种情况的方法之一是获取特定主题的完整评论列表(在这种情况下是一篇博客文章),然后在客户机的内存中对其进行排列。

```
query get_comments_for_post {
  post_comments(
    where: {
      post_id: 1
    }
     order_by: ["created_at"]
  ) {
    id
    parent_id
    comment
    created_at
    user_id
    post_id
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以拥有另一个记录祖先的表，类似于`post_comment_ancestry`

*   **T2`comment_id`**
*   **T2`ancestor_id`**

在这里，您将为每个评论存储其所有祖先的列表。例如:如果评论`A`有两个子评论`B`和`C`，而评论 C 有一个子评论`D`、

```
A
| - B
| - C
    | - D 
```

Enter fullscreen mode Exit fullscreen mode

`post_comment_ancestry`表中的条目将是

```
+------------------------+
|comment_id | ancestor_id|
+------------------------+
|     B     |      A     |
|     C     |      A     |
|     D     |      C     |
|     D     |      A     |
+------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，注释`D`有两个条目，分别对应`A`和`C`。使用这个表，您可以为一个特定的注释获取任意数量嵌套的所有子注释的列表。

## 结论

在这篇博文中，我们看了一下在 Postgres 上使用树形数据结构的一种方法。

如果你想看任何其他的用例或者对上面提到的想法提出改进建议，请在评论中告诉我。

[***Hasura***](https://goo.gl/QfCiQV)*让你在任何 Postgres 数据库上即时实时 GraphQL APIs，而无需编写任何后端代码。*

*对于那些刚接触 Hasura GraphQL 引擎的人来说，* [***这个***](https://docs.hasura.io/1.0/graphql/manual/index.html) *是一个入门的好地方。*

* * *