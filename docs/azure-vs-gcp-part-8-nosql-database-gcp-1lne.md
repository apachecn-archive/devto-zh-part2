# Azure vs GCP 第 8 部分:NoSQL 数据库(GCP)

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-8-nosql-database-gcp-1lne>

在[之前的文章](https://dev.to/kenakamu/azure-vs-gcp-part-7-nosql-database-azure-17d)中，我简单解释了一下 Cosmos DB，Azure NoSQL 服务。在这篇文章中，我研究了 GCP。

# GCP 数据库

和 Azure 一样，GCP 提供了很多数据库服务，比如 SQL、NoSQL、缓存等。请参见 [GCP 存储选项](https://cloud.google.com/storage-options/)了解比较服务。它也有很好的流程来根据场景找到我应该使用的服务。

### SQL 数据库

可以用云 SQL，就是 MySQL 和 PostgreSQL 的云版本，还有 Cloiud Spanner。如果您对 RDBMS 感兴趣，请查看这些服务。

### NoSQL

数据存储和 Bigtable 是 NoSQL 服务的 GCP 版本。

### 仓库

BigQuery 是一个用于分析目的的仓库解决方案。

# Bigtable 和云数据存储

尽管两者都被标记为 NoSQL 服务，但 BigTable 是为了更好地进行分析。因此，在本文中，我主要关注数据存储。

请参考[本文档](https://cloud.google.com/datastore/docs/)了解关于数据存储的详细信息，但简单来说，它是:

*   多单据交易
*   次级和综合指数
*   跨区域自动复制
*   低成本且安全

听起来不错。

### 结构

Datastore 与 Azure Cosmos DB 非常不同，我只是简单地存储 JSON 对象。虽然它是基于文档存储，但它有自己的结构。

*   种类:相当于桌子。
*   实体:它相当于行。实物有实体。
*   属性:实体具有存储字段(列)值的属性。
*   Keys:它是字段的主键类型。
*   索引:它和 RDBMS 不是同一个索引。数据存储需要索引来运行查询。请观看参考视频了解更多细节，因为用一句话来解释有点困难。

关于数据存储，还有很多东西需要学习。我把有用的链接放在最后。

### SDK

Datastore 为多种语言和 REST 端点提供 SDK。因此，如果 SDK 不适用于您的首选语言，您仍然可以调用 REST endpoint。

SDK 可用于 C#、GO、Java、Node.js、PHP、Python 和 Ruby。

# 咱们码！

我在想，从开发者体验的角度来看，什么是与 NoSQL 的 Azure 和 GCP 进行“比较”的最佳方式。这可能对 GCP 不公平，但我决定修改我在[上一篇文章](https://dev.to/kenakamu/azure-vs-gcp-part-7-nosql-database-azure-17d)中使用的应用程序，使用 Datastore 来看看我能转换到多平滑。

我的策略是:

*   尽可能多地保留代码
*   尝试一般化数据存储服务

好了，我们走吧。

1.打开解决方案并管理 NuGet。删除“微软。Azure.DocumentDB”，并安装“Google。Cloud.Datastore.V1 "。

[![app](img/e53a6c881076dddf733da89b87cd9d44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tEtr-Q4F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5oglound8rdv4ryrzjkm.PNG)

2.将 DocumentDBRepository.cs 中的代码替换为以下内容。将 projectId 更新为您自己的。

```
namespace todo
{
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Linq.Expressions;
    using System.Threading.Tasks;
    using Google.Cloud.Datastore.V1;
    using System.Reflection;

    public static class DocumentDBRepository<T> where T : class
    {
        private static string projectId = "cloud-compare-20180306";
        private static DatastoreDb datastoreDb;
        private static KeyFactory keyFactory;

        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                var entity = await datastoreDb.LookupAsync(keyFactory.CreateKey(long.Parse(id)));
                return ConvertToItem(entity);
            }
            catch (Exception e)
            {
                return default(T);
            }
        }

        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            Query query = new Query(typeof(T).Name)
            {
                Filter = Filter.And(Filter.Equal("Completed", false))
            };
            var entities = (await datastoreDb.RunQueryAsync(query)).Entities;
            List<T> items = new List<T>();
            foreach (var entity in entities)
            {
                items.Add(ConvertToItem(entity));
            }

            return items;
        }

        public static async Task<Key> CreateItemAsync(T item)
        {
            return await datastoreDb.InsertAsync(ConvertToEntity(item));
        }

        public static async Task<bool> UpdateItemAsync(string id, T item)
        {
            using (var transaction = await datastoreDb.BeginTransactionAsync())
            {
                Entity entity = transaction.Lookup(keyFactory.CreateKey(long.Parse(id)));
                if (entity != null)
                    transaction.Update(ConvertToEntity(item, entity));
                transaction.Commit();
                return entity != null;
            }
        }

        public static async Task DeleteItemAsync(string id)
        {
            await datastoreDb.DeleteAsync(keyFactory.CreateKey(long.Parse(id)));
        }

        public static void Initialize()
        {
            datastoreDb = DatastoreDb.Create(projectId);
            keyFactory = datastoreDb.CreateKeyFactory(typeof(T).Name);
        }

        private static Entity ConvertToEntity(T item, Entity entity = null)
        {
            if (entity == null)
            {
                entity = new Entity()
                {
                    Key = keyFactory.CreateIncompleteKey()
                };
            }

            foreach (var property in item.GetType().GetRuntimeProperties())
            {
                if (property.GetValue(item) is string)
                    entity[property.Name] = new Value() { StringValue = property.GetValue(item).ToString() };
                else if (property.GetValue(item) is bool)
                    entity[property.Name] = (bool)property.GetValue(item);
            }

            return entity;
        }

        private static T ConvertToItem(Entity entity)
        {
            var item = Activator.CreateInstance<T>();

            foreach (var property in entity.Properties)
            {
                object value = null;
                if (property.Value.ValueTypeCase == Value.ValueTypeOneofCase.StringValue)
                    value = property.Value.StringValue;
                else if (property.Value.ValueTypeCase == Value.ValueTypeOneofCase.BooleanValue)
                    value = property.Value.BooleanValue;

                item.GetType().GetProperty(property.Key).SetValue(item, value);
            }

            if (typeof(T) == typeof(todo.Models.Item))
                item.GetType().GetProperty("Id").SetValue(item, entity.Key.Path.First().Id.ToString());

            return item;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

3.打开 _Layout.cshtml 并将“Azure DocumentDB”替换为“GCP 数据存储”。

4.现在我需要向服务部门认证。与[存储文章](https://dev.to/kenakamu/azure-vs-gcp-part-6-storage-gcp-4f50)相同，所以我直接在本地运行以下命令进行认证。如果你还没有 gcloud，就从这里安装吧。

它启动浏览器，所以只需认证。

```
gcloud auth application-default login 
```

Enter fullscreen mode Exit fullscreen mode

[![portal](img/43ef66ada5cf2309993303686625a1ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qg40nozA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l5apccjuzek0bae25rzq.PNG)

就是这样。够简单吗？嗯，事实上并不尽然...

## 有什么不同

Azure Cosmos DB 和 Datastore 非常不同，因为它们以不同的方式存储对象。

#### 普通类 vs 实体类

Azure Cosmos DB C# SDK 让我使用普通 C#类来呈现文档，因为它将文档存储为 JSON，而 GCP 让我使用“实体”类，我甚至不能继承它。当我重用代码时，这有几个问题。

*   实体不为字段提供强类型体验。
*   实体有“Key”字段，我需要将它映射到我的类中的一个字段。
*   GCP 数据存储 C# SDK 不支持 LINQ，它有自己的查询类。

当我决定尽可能多地重用代码时，我尝试将所有内容封装在 DocumentDBRepository.cs 中

##### 初始化

使用** typeof(T)。Name **获取类型名作为实体名。

```
public static void Initialize()
{
    datastoreDb = DatastoreDb.Create(projectId);
    keyFactory = datastoreDb.CreateKeyFactory(typeof(T).Name);
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 从普通类转换为实体

```
I only implemented string and boolean type to simplify code, and use reflection. This affect performance.

private static Entity ConvertToEntity(T item, Entity entity = null)
{
    if (entity == null)
    {
        entity = new Entity()
        {
            Key = keyFactory.CreateIncompleteKey()
        };
    }

    foreach (var property in item.GetType().GetRuntimeProperties())
    {
        if (property.GetValue(item) is string)
            entity[property.Name] = new Value() { StringValue = property.GetValue(item).ToString() };
        else if (property.GetValue(item) is bool)
            entity[property.Name] = (bool)property.GetValue(item);
    }

    return entity;
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 从实体转换为普通类

因为我必须在类实例中为一个字段设置键值，所以我必须添加特定于项目类的代码，这破坏了通用目的。我可以改变项目类，但我想保持其他代码完整。

```
private static T ConvertToItem(Entity entity)
{
    var item = Activator.CreateInstance<T>();

    foreach (var property in entity.Properties)
    {
        object value = null;
        if (property.Value.ValueTypeCase == Value.ValueTypeOneofCase.StringValue)
            value = property.Value.StringValue;
        else if (property.Value.ValueTypeCase == Value.ValueTypeOneofCase.BooleanValue)
            value = property.Value.BooleanValue;

        item.GetType().GetProperty(property.Key).SetValue(item, value);
    }

    if (typeof(T) == typeof(todo.Models.Item))
        item.GetType().GetProperty("Id").SetValue(item, entity.Key.Path.First().Id.ToString());

    return item;
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 将 Id 字段作为关键字段处理

项目的 Id 字段是字符串类型，而键中的 Id 是长的。所以我必须为许多操作解析它。

```
public static async Task DeleteItemAsync(string id)
{
    await datastoreDb.DeleteAsync(keyFactory.CreateKey(long.Parse(id)));
} 
```

Enter fullscreen mode Exit fullscreen mode

##### LINQ 查询到查询类

我不想手动转换一切，所以我放弃了，只是简单地硬编码预期的查询。

```
public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
{
    Query query = new Query(typeof(T).Name)
    {
        Filter = Filter.And(Filter.Equal("Completed", false))
    };
    var entities = (await datastoreDb.RunQueryAsync(query)).Entities;
    List<T> items = new List<T>();
    foreach (var entity in entities)
    {
        items.Add(ConvertToItem(entity));
    }

    return items;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 测试一下

现在代码已经完成，所以先尝试本地调试。

1.按 F5 键开始调试。

2.添加新项目。

[![test](img/9a480b73845f1c0b987dd5ebe9568139.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C9ba232V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tf72q7n68vdqvmbh68d9.PNG)

3.转到[谷歌云控制台](https://console.cloud.google.com)并导航到数据存储。您可以看到创建了一个记录。

[![portal](img/203dbfb4511b1dbd329700ca936e9645.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FoIB5Ki1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m1haybpb5ojx0p3a2y8z.PNG)

4.尝试更新和删除。

# 部署到蔚蓝和 GCP

尝试部署两个平台。它在 GCP 工作没有任何问题，但它在 Azure 失败了，原因和存储产品一样，安全。

### 添加认证

目前，我只是用自己的凭证来测试应用程序。这在测试时是可以的，但是当我部署应用程序时，我需要启用身份验证。

1.运行以下命令创建服务帐户。我给了项目一个答案，但是您可以根据场景进一步限制它。

```
serviceName=datastoreserviceaccount
projectId=<your project id>
gcloud iam service-accounts create ${serviceName}
gcloud projects add-iam-policy-binding ${projectId} --member "serviceAccount:${serviceName}@${projectId}.iam.gserviceaccount.com" --role "roles/owner" 
```

Enter fullscreen mode Exit fullscreen mode

2.然后运行以下命令来生成密钥。

```
gcloud iam service-accounts keys create keys.json --iam-account ${serviceName}@${projectId}.iam.gserviceaccount.com 
```

Enter fullscreen mode Exit fullscreen mode

3.文件生成后，复制到本地，添加到 Visual Studio 2017 项目根目录。

4.将初始化方法改为使用 keys.json.

```
public static void Initialize()
{
    var credential = GoogleCredential.FromFile("keys.json");
    DatastoreClient.Create(new Channel(DatastoreClient.DefaultEndpoint.ToString(), credential.ToChannelCredentials()));
    datastoreDb = DatastoreDb.Create(projectId);
    keyFactory = datastoreDb.CreateKeyFactory(typeof(T).Name);
} 
```

Enter fullscreen mode Exit fullscreen mode

5.还要添加 using 语句。

```
using Google.Apis.Auth.OAuth2;
using Grpc.Core;
using Grpc.Auth; 
```

Enter fullscreen mode Exit fullscreen mode

6.部署到 Azure 来测试应用程序。

7.顺便说一下，如果您在发布时看到“bower '未被识别为内部或外部命令”错误，请通过 npm 安装它。

```
npm install -g bower 
```

Enter fullscreen mode Exit fullscreen mode

理论上，这应该行得通，但事实并非如此。也许作为 GCP 的一部分加载的 NuGet 包可能会导致该问题。

[![error](img/163616c53a66afa256a9d448d94c515d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gcf1L6N6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xzlpf2qxt7g9iccsk50b.PNG)

# 资源管理器工具

作为一名开发人员，我可能需要从应用程序外部检查数据。有几个查询工具可用。所有工具都可以通过谷歌云控制台访问。

## 按种类查询

我可以添加过滤器来快速查询实体列表中的数据。
[![portal](img/e06f8fa7c07fafaaf1fcae0925e46ee2.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--dA5JqQ9i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kxcjiqztx6al1gp8r0hv.PNG)

## 按 GQL 查询

我也可以编写类似查询的 SQL。

[![portal](img/aee5250f0b50885c9235f7243cea407a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y-KMEmmM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4msb64axe03uc4kb71fl.PNG)

# 总结

与存储不同，我感受到 Azure 和 GCP NoSQL 服务之间的巨大差异。从 C#的角度来看，我更喜欢 Azure Cosmos DB，因为我可以使用普通的类来定义文档和 LINQ 查询，并且我不必太在意索引。然而，当我看到数据是如何存储的，当我有大量数据时，GCP 可能会有更好的性能。

数据存储具有独特的结构，这意味着对于编码和管理来说都有学习的余地。例如，当我执行选择多个字段的足够简单的查询时，需要额外的索引。我们必须事先仔细设计数据结构。

我将在未来更深入地研究数据存储，因为我无法重复使用我的 DocumentDB 知识。

# 参考文献

[云 Datastore 101:Google 可扩展 NoSQL 文档数据库概述(Google Cloud Next ' 17)](https://www.youtube.com/watch?v=uZDk0NZGqHs&t=1467s)
[Datastore 简介](https://www.youtube.com/watch?v=fQazhzcC-rg)
[Datastore 查询、索引和事务](https://www.youtube.com/watch?v=d4CiMWy0J70&t=907s)

肯恩(男名)