# 使用迪纳摩本地和 Go 进行测试

> 原文：<https://dev.to/nathmclean/testing-with-dynamo-local-and-go-4d1l>

我最近用 Go 和 DynamoDB 做了一些工作，需要测试我的工作。幸运的是，亚马逊提供了 [DynamoDB local](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html) ，这意味着我不需要在 AWS 中提供任何真正的基础设施，并且可以离线运行我的测试。这篇文章将通过一个简单的例子来演示如何用 Go 与 DynamoDB 交互，以及如何用 Dynamo Local 测试这段代码。这个例子的所有代码都可以在 [GitHub](https://github.com/nathmclean/dynamodb-local-testing) 中找到。

我使用 [guregu/dynamo](http://github.com/guregu/dynamo) 库与 dynamo 交互，因为我发现它为 DynamoDB API 提供了一个很好的抽象。

首先，我创建了一个结构来表示我希望存储在 Dynamo 中的项目:

```
// item/item.go  
type Item struct {
   Id          string    `dynamo:"item_id,hash"`
   Name        string    `dynamo:"name"`
   Description string    `dynamo:"description"`
   CreatedAt   time.Time `dynamo:"created_at"`
   UpdatedAt   time.Time `dynamo:"updated_at"`
} 
```

Enter fullscreen mode Exit fullscreen mode

注意 struct 字段上的标签，这些标签为 [guregu/dynamo](http://github.com/guregu/dynamo) 库提供了一些关于如何将数据封送到 dynamo 和从 Dynamo 解封的附加信息。例如,“Id”字段将在 DynamoDb 中显示为“item_id ”,它将是一个散列(主键)。只有当我们希望使用这种类型创建 Dynamo 表时，才需要散列，稍后我们会用到它。

接下来，我将创建一个包含 DynamoDB 客户端的 ItemService，我们可以为它编写与 Dynamo 交互的方法，比如向数据库添加项目。

```
// item/item.go  
type ItemService struct {
   itemTable dynamo.Table
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，有两种方法，一种是创造新的发电机。表，它是一个用于与 Dynamo 通信的客户端，也是一个创建新项目服务的客户端

```
// item/item.go  
func newDynamoTable(tableName, endpoint string) (dynamo.Table, error) {
   if tableName == "" {
    return dynamo.Table{}, fmt.Errorf("you must supply a table name")
   }
   cfg := aws.Config{}
   cfg.Region = aws.String("eu-west-2")
   if endpoint != "" {
      cfg.Endpoint = aws.String(endpoint)
   }
   sess := session.Must(session.NewSession())
   db := dynamo.New(sess, &cfg)
   table := db.Table(tableName)
   return table, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了一些事情。首先，我们检查是否为我们提供了 tableName(我们正在连接的表的名称)。我们将该函数设为私有，以便我们可以控制何时使用它——我们只需要在设置项目服务或测试时创建一个 dynamo 客户端。

接下来，我们将设置一个带有一些配置的 AWS 会话。在现实世界的用例中，我们也将在 AWS 区域中传递，而不是硬编码它。

如果已经为我们提供了一个端点，我们将把它指向客户端，而不是允许客户端使用它的默认端点。在正常使用中，我们不会提供端点，但是为了测试，它允许我们指向 Dynamo Local。

```
// item/item.go  
func NewItemService(itemTableName string) (*ItemService, error) {
   dynamoTable, err := newDynamoTable(itemTableName, "")
   if err != nil {
       return nil, err
   }
   return &ItemService{
      itemTable: dynamoTable,
   }, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数采用我们的项目表的名称，使用我们在上面讨论过的“newDynamoTable”函数设置一个客户端，最后返回一个包含 dynamo 客户端的新项目服务。注意，我们总是发送一个空字符串作为端点，这意味着我们不能在生产中意外地发送一个无效的端点，但这确实意味着我们不能在测试中使用这个函数。

现在我们需要一些方法与 DynamoDB 交互来读写项目。这些方法将与 ItemService 相关联，这样它们就可以访问 Dynamo 客户机。

```
// item/item.go  
func (i *ItemService) CreateItem(item *Item) error {
   now := time.Now()
   item.CreatedAt = now
   item.UpdatedAt = now
   item.Id = xid.New().String()
   return i.itemTable.Put(item).Run()
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们向数据库中添加一个新项目。我们将创建和更新时间设置为当前时间，为条目生成一个 Id，并使用 Put 方法写入 Dynamo 表。

```
// item/item.go  
func (i ItemService) GetItem(item Item) error {
    return i.itemTable.Get("item_id", item.Id).One(item)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个从 DynamoDB 读取的例子。

请注意，这些都是基本示例，没有对我们正在操作的项目进行任何验证，也没有任何详细的错误处理。例如，我们可能希望在尝试实际创建项目之前，检查我们正在创建的项目是否有名称和描述。也可能有我们可以处理的错误。例如，如果我们被 Dynamo 抑制，我们可以用增量回退重试(在每次重试之间等待更长的时间)。

接下来，是时候测试代码了。在每个测试中，我都创建了一个新的、随机命名的 Dynamo 表，每个测试都可以针对它运行，这意味着如果两个测试并行运行，它们不会冲突。

我创建了一个 [test_utils 包](https://github.com/nathmclean/dynamodb-local-testing/blob/master/test_utils/dynamo-local.go)，它使用一个接口作为模式来创建一个新表。

```
// test_utils/dynamo-local.go
func CreateTable(table interface {}) (string, error) {
   cfg := aws.Config{
      Endpoint: aws.String("http://localhost:9000"),
      Region: aws.String("eu-west-2"),
   }
   sess := session.Must(session.NewSession())
   db := dynamo.New(sess, &cfg)
   tableName := xid.New().String()
   err := db.CreateTable(tableName, table).Run()
   if err != nil {
      return"", err
   }
   return tableName, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的例子中，函数接受的“表接口{}”将是我们在本文开始时创建的“项目”类型。通过一个接口，我们可以重用这个函数来创建任何表。当然，如果您传递一个不能表示 Dynamo 表的接口，这个函数将会失败。例如，一个结构没有定义散列键的标签— ( `dynamo:”hash”`)。

```
type OtherItem struct {  
   Id string `dynamo:"item_id,hash"` 
} 
```

Enter fullscreen mode Exit fullscreen mode

> 以上内容足以创建一个我们可以用来测试项目类型的表。当我们在数据库中创建项目时，其他字段将被添加到表中。只要钥匙匹配就没有问题。

现在在每次测试 ItemService 方法时，我们可以使用这个函数来为我们建立一个表。为了避免重复，我在 item_test.go 中创建了一个“newItemService”函数，该函数将调用“CreateTable”并设置一个 ItemService，该服务被配置为使用该表并使用 Dynamo 本地端点(我们稍后将设置 Dynamo Local)。

```
// item/item_test.go  
func newItemService() (*ItemService, error) {
   tableName, err := test\_utils.CreateTable(Item{})
   if err != nil {
      return nil, fmt.Errorf("failed to set up table. %s", err)
   }

   db, err := newDynamoTable(tableName, "http://localhost:9000")
   if err != nil {
      return nil, err
   }
   service := ItemService{
      itemTable: db,
   }
   return &service, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

CreateItem 和 GetItem 的测试遵循相同的模式:

1.  设置一系列测试条件(表格测试)
2.  使用“newItemService”设置 ItemService
3.  运行每个测试条件，并评估它是通过还是失败。

```
// item/item\_test.go  
func TestItemService\_CreateItem(t *testing.T) {
   cases := [] struct {
      name string
      item Item
      err bool // Whether we expect an error back or not
   }{
      {
         name: "created successfully",
         item: &Item{
            Name: "spoon",
            Description: "shiny",
         },
      },
   }

   service, err := newItemService()
   if err != nil {
      t.Fatal(err)
   }

   for _, c := range cases {
      t.Run(c.name, func (t testing.T) {
         err := service.CreateItem(c.item)
         if c.err {
            assert.Error(t, err)
         } else {
            assert.NoError(t, err)
            assert.NotEqual(t, time.Time{}, c.item.CreatedAt)
            assert.NotEqual(t, time.Time{}, c.item.UpdatedAt)
         }
      })
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个匿名结构的片段，向其中添加一个名称(或者测试的简短描述)、我们想要测试的项目以及我们是否期望它成功。如果您想测试特定的错误条件，还可以添加您预期会收到的错误。

然后，我们创建一个 ItemService，如果这个过程中有任何一个失败了，我们就测试失败(t.fatal)。

接下来，我们迭代每个测试用例，每个用例是一个子测试(t.Run)。我们使用为每个测试用例定义的名称作为 t.Run 的 name 参数，这有助于我们识别哪个测试失败了，以及我们试图用那个测试用例测试什么。

对于每种情况，我们使用[断言](https://github.com/stretchr/testify)来检查错误。如果我们期望得到一个错误，我们断言我们确实收到了一个错误，反之亦然，如果我们不期望得到一个错误。如果我们不希望收到错误，我们还会检查该项的时间设置是否正确(记住我们在 CreateItem 方法中设置了这个时间)。

现在我们有了测试，将在 Dynamo Local 中设置表，并使用这些表测试我们的代码，但我们没有运行 Dynamo Local 的实例…我们将使用 Docker 来设置它。

首先我们将建立一个‘docker file’

```
FROM openjdk:7
RUN mkdir -p opt/dynamodb
WORKDIR /opt/dynamodb
RUN wget https://s3.eu-central-1.amazonaws.com/dynamodb-local-frankfurt/dynamodb\_local\_latest.tar.gz -q -O - | tar -xz
EXPOSE 8000
ENTRYPOINT ["java", "-jar", "DynamoDBLocal.jar"] 
```

Enter fullscreen mode Exit fullscreen mode

它构建在 openjdk 容器之上，为 dynamo local 设置一个目录，下载并解压缩 jar，打开一个端口供它监听，然后设置入口点以确保 Dynamo Local 在容器启动时启动。

接下来，我们将设置一个 docker-compose.yml，它将允许我们使用 docker-compose 来构建、启动和停止我们的容器。

```
dynamo:
  build: .
  ports:
    - 9000:8000
  command:
    -sharedDb 
```

Enter fullscreen mode Exit fullscreen mode

这是从我们在同一目录中创建的 Dockerfile 构建的，将我们的本地端口 9000 映射到容器端口 8000(您可能已经注意到，我们在代码中使用 localhost:9000 作为 DynamoDB 的端点)。

最后，当我们启动容器时，它向 Dynamo Local 发送-sharedDb 标志。如果我们不使用这个标志，那么每个请求将使用一个不同的表，这意味着我们不能像读取刚刚创建的条目那样检查创建是否有效。

接下来是创建一个 Makefile，它将运行 docker-compose 来设置 dynamo local，运行测试并使用 docker-compose 来停止 dynamo local。

```
TEST?=$$(go list ./... |grep -v 'vendor')
GOFMT\_FILES?=$$(find . -name '\*.go' |grep -v vendor)

default: test

fmt:
   gofmt -w $(GOFMT\_FILES)

test: fmt
   docker-compose down
   docker-compose up -d --build --force-recreate
   go test -i $(TEST) || exit 1
   echo $(TEST) | \
      xargs -t -n4 go test -v
   docker-compose down 
```

Enter fullscreen mode Exit fullscreen mode

现在我只需运行 make，我的测试就会运行，希望能够成功。