# 让我们做一个框架

> 原文：<https://dev.to/meudeusdoceunaoencontronome/lets-make-a-framework-4dhc>

大家好，这是我的第一个帖子，所以我有点紧张。

我想谈一谈做一个框架。

重要的事情先来。在这篇文章中，我想用 MongoDB 做一个代码到数据库的交互框架。

用 C#使用 MongoDB，我们已经有了一个很好的框架:mongodriver。
我们对 mongodriver 的一些操作做一个抽象，这样就可以在其他层的代码中更快的编码 de business。

先说配置。

使用 mongodb，我们曾经将数据库与一个“MongoUrl”类链接起来，我们可以使用一个配置类来动态构建这个类。

让我们(使用 C#)为 MongoDB 框架编写一个简单的配置类:

```
public class Configuration
{
    public MongoUrl mongoUrl { get; set; }
    public string dbName { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们记住到本地主机的简单 mongodb Url 的格式:

```
MongoUrl mongoUrl = new MongoUrl("mongodb://localhost:27017/CONTROLE_OD_DB") 
```

Enter fullscreen mode Exit fullscreen mode

在这个简单的配置示例中，我们不会关心动态构建或抽象 MongoUrl，这不是本文的重点。现在我们将进入这篇文章的真正要点，写一个通用框架的艺术，即使是一个小框架。

让我们用通用操作来创建一个类。

再说一遍，重要的事情先来。我们来做配置吧。我将把它命名为类上下文。

所以:

```
public class Context
{
    #region Configuration
    public static Configuration _configuration { get; set; }
    static MongoClient client;
    static IMongoDatabase db;

    public static void Initializer(Configuration configuration)
    {
        _configuration = new Configuration();
        _configuration = configuration;
        client = new MongoClient(_configuration.mongoUrl);
        db = client.GetDatabase(_configuration.dbName);
    }
    public static void Dispose()
    {
        client = null;
    }
    #endregion
} 
```

Enter fullscreen mode Exit fullscreen mode

先来了解一下帖子。我们希望制作一个框架，让我们的生活变得更简单。第一件事是找出仅使用旧框架 mongodriver 编写代码的枯燥部分。第一个无聊的部分是处理配置。我们必须获取数据库，每次都创建 mongoUrl，所以我们在这里创建了一个“Context”类来将配置保存在一个静态变量中，这样就不需要一直有新的实例了。
我们只会得到数据库一次，并得到一次 IMongoDatabase。更好的是，我们再也不用担心他们了。

因此，让我们开始构建一个基本的“集合”操作区域。

```
 #region Collections

    public static void CreateCollection(string collectionName)
    {
        db.CreateCollection(collectionName);
    }
    public static IMongoCollection<T> GetCollection<T>(string collectionName)
    {

        return db.GetCollection<T>(collectionName);
    }
    public static IMongoCollection<T> GetCollection<T>()
    {
        return GetCollection<T>(typeof(T).Name + "_COLLECTION");
    }
    #endregion 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用泛型类型来获得我们想要的任何集合，并使用我们方法的“T”类的类型来设置集合。所以，让我们去掉设置集合的字符串名称的问题，让框架使用类名设置集合的名称。只是为了组织我们可以在最后写一个“_ 集合”，这样读起来会更好。

现在我们有了这门课。

```
public class Context
{
    #region Configuration
    public static Configuration _configuration { get; set; }
    static MongoClient client;
    static IMongoDatabase db;

    public static void Initializer(Configuration configuration)
    {
        _configuration = new Configuration();
        _configuration = configuration;
        client = new MongoClient(_configuration.mongoUrl);
        db = client.GetDatabase(_configuration.dbName);
    }
    public static void Dispose()
    {
        client = null;
    }
    #endregion
    #region Collections

    public static void CreateCollection(string collectionName)
    {
        db.CreateCollection(collectionName);
    }
    public static IMongoCollection<T> GetCollection<T>(string collectionName)
    {
        return db.GetCollection<T>(collectionName);
    }
    public static IMongoCollection<T> GetCollection<T>()
    {
        return GetCollection<T>(typeof(T).Name + "_COLLECTION");
    }
    #endregion
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们创建了数据库和集合，这正是我们开始编写操作区域 CRUD(创建、读取、更新、删除)区域所需要的。

让我们制作插页:

```
 #region CRUD
    public static T Insert<T>(T document)
    {
        var collection = GetCollection<T>(typeof(T).Name + "_COLLECTION");
        collection.InsertOne(document);
        return document;
    }
    public static List<T> Insert<T>(List<T> documents)
    {
        GetCollection<T>(typeof(T).Name + "_COLLECTION").InsertManyAsync(documents);
        return documents;
    }
    public static List<T> Insert<T>(string collectioName, List<T> documents)
    {
        GetCollection<T>(collectioName).InsertManyAsync(documents);
        return documents;
    }
    public static T Insert<T>(string collectioName, T document)
    {
        GetCollection<T>(collectioName).InsertOne(document);
        return document;
    }
    public static List<BsonDocument> Insert(string collectioName, List<BsonDocument> documents)
    {
        GetCollection<BsonDocument>(collectioName).InsertManyAsync(documents);
        return documents;
    }
    public static BsonDocument Insert(string collectioName, BsonDocument document)
    {
        GetCollection<BsonDocument>(collectioName).InsertOneAsync(document);
        return document;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 mongodriver 中，我们总是要在插入多个，或者插入一个，插入一个异步，插入 bson，插入对象之间进行选择，我不想再担心这些了，让框架选择每一个来使用。对于所有插入方法(以及我们将对 CRUD 序列进行的所有操作),我们将使用已经声明的数据库和 getcollection 方法。

我们选择每个不用担心的东西，并且用一般的方法来抽象它们。

发生...

让我们把“CRUD”变成 R (Read):

```
 public static List<T> GetDocuments<T>(FilterDefinition<T> filter)
    {
        return GetCollection<T>().FindAsync(filter).Result.ToList<T>();
    }

    public static List<T> GetDocuments<T>(string collectionName, FilterDefinition<T> filter)
    {
        return GetCollection<T>(collectionName).FindAsync(filter).Result.ToList<T>();
    }

    public static T GetDocument<T>(FilterDefinition<T> filter)
    {
        return GetCollection<T>().FindAsync(filter).Result.SingleOrDefault<T>();
    }
    public static T GetDocument<T>(string collectionName, FilterDefinition<T> filter)
    {
        return GetCollection<T>(collectionName).FindAsync(filter).Result.SingleOrDefault<T>();
    } 
```

Enter fullscreen mode Exit fullscreen mode

一件很好的事情是让所有返回的东西都是我想要使用的对象类型，我不想把 c#集合转换成列表，例如，我希望每个方法的每个返回都是我想要使用它们的方式，在这个例子中，它将是一个列表或一个简单的 t。

让我们做 D(删除)和 U(更新) :

```
 public static void Update<T>(MongoDB.Driver.FilterDefinition<T> filter, KeyValuePair<string, object> property, bool updateOne, bool IsUpsert)
    {
        var update = Builders<T>.Update.Set(property.Key, property.Value);            
        if (updateOne)
            GetCollection<T>()
                .UpdateOneAsync(filter, update, new UpdateOptions { IsUpsert = IsUpsert });
        else
            GetCollection<T>()
                .UpdateMany(filter, update, new UpdateOptions { IsUpsert = IsUpsert });
    }
    public static void Update<T>(MongoDB.Driver.FilterDefinition<T> filter, Dictionary<string, object> properties, bool updateOne, bool IsUpsert)
    {
        foreach(var property in properties)
            Update<T>(filter, property, false, true);
    }
    public static void Update<T>(MongoDB.Driver.FilterDefinition<T> filter, Dictionary<string, object> properties)
    {
        foreach(var property in properties)
            Update<T>(filter, property, false, true);
    }
    public static void Update<T>(MongoDB.Driver.FilterDefinition<T> filter, Dictionary<string, object> properties, bool updateOne)
    {
        foreach(var property in properties)
            Update<T>(filter, property, false, true);
    }
    public static void Update<T>(MongoDB.Driver.FilterDefinition<T> filter, KeyValuePair<string, object> property)
    {
        Update<T>(filter, property, false, true);
    }

    public static void Update<T>(MongoDB.Driver.FilterDefinition<T> filter, KeyValuePair<string, object> property, bool updateOne)
    {
        Update<T>(filter, property, false, true);
    }

    public static void Delete<T>(FilterDefinition<T> filter, bool DeleteOne)
    {
        if (DeleteOne)
            GetCollection<T>().DeleteOne(filter);
        else
            GetCollection<T>().DeleteMany(filter);

    }
    public static void Delete<T>(FilterDefinition<T> filter)
    {
        Delete<T>(filter, false);
    } 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以继续到美丽的部分后。让我们创造更多新方法，让一切变得更容易。

现在一切都在改变上下文类，或者创建一个基本的配置类。现在让我们创建一个基类。

我将创建一个名为“Base”的新类。

```
public class Base<T>
{
    public T Insert(T T)=>Context.Insert<T>(T);
    public void Upsert(FilterDefinition<T> filter, T entity)
    {
        if(List(filter).Count > 0)
        {
            Dictionary<string, object> props = new Dictionary<string, object>();
            foreach (var responsibleProp in entity.GetType().GetProperties()) props.Add(responsibleProp.Name, responsibleProp.GetValue(entity));
            Context.Update<T>(filter, props);        
        }
        else Context.Insert<T>(entity);    
    }
    public T Find(FilterDefinition<T> filter)=>Context.GetDocument<T>(filter);
    public List<T> List(FilterDefinition<T> filter)=>Context.GetDocuments<T>(filter);
    public void Delete(FilterDefinition<T> filter)=>Context.Delete<T>(filter);
} 
```

Enter fullscreen mode Exit fullscreen mode

我不想担心的一件事是我们是否正在进行插入或更新，因此我们可以创建一个 Upsert 方法。如果过滤器在查询中得到任何结果，它将用实体更新这些结果，否则它将在数据库中插入一个新的实体。

用一个简单的过滤器“查找”，用“列表”返回一个“列表”(通用对象的列表)，然后更新。

现在我们可以将框架配置到一个新软件中:让我们创建一个 ConfigDB 类:

```
public class ConfigDB<T> : Base<T>
{
    public ConfigDB()
    {
        Configuration configuration = new Configuration();
        Connection conn = new Connection();
        configuration.dbName = conn.GetDatabaseName();
        configuration.mongoUrl = new MongoUrl(conn.GetUrl());
        Context.Initializer(configuration);

    }
    ~ConfigDB()
    {
        Context.Dispose();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们制作一个简单的代理类，只是为了使用基本操作:

```
public class Agent<T> : ConfigDB<T>
{

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以创建我们的业务类，我将把它命名为 UserBusiness:

```
public class User
{
    public string login {get;set;}
    public string password {get;set;}
    public string curriculum {get;set;}
    public string aboutMe {get;set;}

    [BsonId(IdGenerator = typeof(StringObjectIdGenerator))]
    public string ID { get; set; }
    public bool deleted { get; set; } = false;
    public bool active { get; set; } = true;
    [BsonDateTimeOptions(Kind = DateTimeKind.Local)]
    public DateTime creationDate { get; set; } = DateTime.Now;
}

public class UserBusiness
{
    public User InsertUser(User user) => new Agent<User>().Insert(user);
    public User findUserByID(string ID)=> new Agent<User>().Find(Builders<User>.Filter.Where(u => u.ID == ID));
    public User findUserByLogin(string login)=> new Agent<User>().Find(Builders<User>.Filter.Where(u => u.active == true && u.login == login));
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是构建框架时如何思考的一个小例子。

我们现在需要担心的是我们不想在未来担心的事情。

这是我的信息，希望你喜欢。