# Python 和 GraphQL 入门——第 1 部分

> 原文：<https://dev.to/xngwng/getting-started-with-python-and-graphql---part-1-4gnj>

注意:这篇文章是由我的同事 Keyur 写的，但是我们在同一个地方写博客。

自从脸书提出 GraphQL 以来，它就一直被认为是 REST APIs 的替代品。最近 [Github](https://githubengineering.com/the-github-graphql-api/) 也宣布了对他们的一个 API 的 GraphQL 支持。因为 GraphQL 不仅仅是 REST 的进化替代品，所以这篇博客将帮助您了解 GraphQL 的基础知识，并使用 Python Django 开发 GraphQL APIs。

#### graph QL 简介

GraphQL 是什么？

> GraphQL 是一种强类型查询语言，描述了如何请求数据。GraphQL 将所有东西都声明为图。你要求你想要的，然后你会得到你期望的。不多不少。

考虑具有两个表`projects`和`user`数据库。项目包含字段`title`和`project_name`，而`user`包含字段`user_name`和`email`。

现在，如果您想知道与该项目相关的项目标题和用户电子邮件？你的第一反应是什么？加入两个表格，获取项目标题和用户电子邮件。当然，它将提供预期的结果，但是 GraphQL 将能够检索相同的信息，而无需在服务器端实现联接或接收额外的数据。

[![graphqlVsql.png](img/aac8ccab8785a11fd6ded2943ab9e2e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J0LktpsA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.moesif.com/blimg/posts/technical/graphql/graphqlVsql.png)

> 作为一种强类型查询语言，它允许您确定需要什么数据，从而避免与未充分利用的数据相关的问题。

GraphQL 有时被视为 API 理解的革命性方式。与使用严格定义的端点(如 REST API 的情况)不同，必要的数据通过单个数据库往返来接收，这确保了前端和后端团队之间的平稳工作流。

听起来很刺激，不是吗？如果您目前正在使用 REST API，并且想知道迁移到 GraphQL 是否能解决您的业务问题，请查看这篇对 REST 与 Graph 架构的深入分析。

#### 入门

继续之前，请确保您安装了 Python 3.6。

##### 我们要建造什么？

在这篇博客中，我们将使用 Django 和 Graphene 创建一个示例项目。 [Graphene](http://graphene-python.org/) 是 Python 的 GraphQL 框架。该项目将具有以下特点:

*   `Events`有`name`和`url`字段的创作
*   搜索和过滤`Events`数据

##### 创建您的本地环境

在使用 Python 时，我们建议使用虚拟环境来将所有项目的依赖项与其他项目隔离开来。

```
conda create -n graphql python=3.6 anaconda # Create the environment
source activate graphql # Activate the environment 
```

Enter fullscreen mode Exit fullscreen mode

##### 安装依赖项

```
pip install django==2.0.2 graphene==2.0.1 graphene-django==2.0.0 django-filter==1.1.0 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置石墨烯 Django

在`{django_project_name}/settings.py`上，添加以下内容:

```
INSTALLED_APPS = (
    # At the end of all the default packages
    'graphene_django',
) 
```

Enter fullscreen mode Exit fullscreen mode

在文件底部添加:

```
GRAPHENE = {
    'SCHEMA': '{django_project_name}.schema.schema',
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建一个`Event`应用

在`{django_project}`根上，创建`events`应用

```
python manage.py startapp events 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要定义数据库和 Django 之间的层。

```
# Defining Event model
class Event(models.Model):
    name = models.TextField(blank=True)
    url = models.URLField() 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要配置 Django 在`{django_project_name/settings.py}`文件上使用新的`events`应用程序:

```
INSTALLED_APPS = (
    # After the graphene_django app
    'events',
) 
```

Enter fullscreen mode Exit fullscreen mode

##### 创建数据库表

```
python manage.py makemigrations
python manage.py migrate 
```

Enter fullscreen mode Exit fullscreen mode

打开 Django shell `python manage.py shell`并创建一些数据。

```
from events.models import Event
Event.objects.create(name='API Analytics', url='https://www.moesif.com/')
Event.objects.create(name='Trove Marketplace', url='https://www.trove.com/') 
```

Enter fullscreen mode Exit fullscreen mode

#### 介绍失眠/GraphiQL

我们可以使用 REST 客户端或浏览器中的图形交互界面来运行查询。

#### 创建您的第一个模式

当开始使用 GraphQL 时，我们的第一个问题是如何构建我们的 GraphQL 服务器？由于 GraphQL 已经作为[规范](http://facebook.github.io/graphql/October2016/)发布，我们可以用任何编程语言构建服务器。

模式是可能包含多个字段的对象的集合。每个字段都通过返回值的解析器进行计算。字段的解析函数将访问数据库并返回一个对象。

模式是严格类型化的，描述了所有可能接收到的数据。GraphQL 查询模式和您的数据库结构没有连接。

为模式创建文件`{django_project_name}/schema.py`。

```
import graphene
import events.schema

# Query for getting the data from the server.
class Query(events.schema.Query, graphene.ObjectType):
    pass

# Create schema
schema = graphene.Schema(query=Query) 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建您的第一个查询

> GraphQL 查询语言完全是关于选择对象上的字段。

我们从“根”对象开始，并在其上选择`events`字段。从事件返回的对象中，我们将选择`id`、`name`和`url`字段。

[![grapql_query.png](img/82c29182060203175fbd0296f5d1c6bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lSmFOg1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.moesif.com/blimg/posts/technical/graphql/graphql_query.png)

#### 创造你的第一次突变

> 向服务器发送数据的过程称为变异。

定义突变类似于我们定义查询的方式。我们可以定义您可以发送到服务器的数据。突变方法将使用用户发送的数据在数据库上创建一个事件。

[![graphql_mutation.png](img/4bb93733926b038605639782330702df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0wOcaHbB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.moesif.com/blimg/posts/technical/graphql/graphql_mutation.png)

再次尝试查询数据，您将看到新事件被创建。

> 查询和变异定义了 GraphQL API 的入口点。每个 GraphQL 服务都有一个查询类型，可能有也可能没有突变类型。

#### 搜索和过滤

一旦我们有了数据，我们就可以根据需求进行搜索和过滤。我们可以搜索第一/最后“n”个记录，跳过“n”个记录或搜索任何术语。

只有事件 Id 和名称字段的最后 n 条记录:

[![query_first_n.png](img/7bb2d805bf08516e864d76a4bb089ed7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8FjCmpEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.moesif.com/blimg/posts/technical/graphql/query_first_n.png)

在名称或 url 字段中搜索特定术语:

[![query_search.png](img/1ca5bafd7e88211e68fc28257e602c60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vy8rKf7R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.moesif.com/blimg/posts/technical/graphql/query_search.png)

我们还可以一起搜索和过滤:

[![query_filter_search.png](img/e0174cb2c07aa01e680792fa09c98fbc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TLT5bxrz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.moesif.com/blimg/posts/technical/graphql/query_filter_search.png)

好吧，就让我玩玩吧！要查看运行中的 GraphQL，您可以从[这里](https://github.com/Moesif/moesif-graphene-django-example)运行这个示例应用程序

在下一个教程中，我们将讨论更多关于认证和分页的内容。同时，如果您有任何问题，请联系 [Moesif 团队](//mailto:team@moesif.com)。