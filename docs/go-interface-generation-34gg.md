# Go 接口生成

> 原文：<https://dev.to/dnnrly/go-interface-generation-34gg>

# 围棋界面生成

我想对我最近发现的一个工具赞不绝口。但是首先……一点历史。

我一直在和水牛一起工作，非常喜欢。一切都在你面前，在 Go(我最喜欢的语言)中，从基本的开箱即用模板到添加你自己的领域模型和业务逻辑，都很容易。

但是我也有大型企业的 Java 背景。我们谈论的是 web 容器、JCA 组件和花哨的消息传递解决方案中的完整的 [DDD](https://en.wikipedia.org/wiki/Domain-driven_design) 和[三层应用](https://www.techopedia.com/definition/24306/three-tier-application)。

我喜欢 3 层应用程序的一点是关注点的强烈分离。水牛，虽然简单，却没有同样的分离。Buffalo 通过充分利用集成测试来处理这个问题，集成测试使用完整的数据库实现和实用程序来管理数据库模式，并根据文本设备中定义的内容用数据填充数据库。

简而言之，我希望能够将业务逻辑从处理程序中取出，并将其放入一个我可以抽象出来的存储库中，这样当我进行测试时(首先)，我可以确保我一次只测试一件事情。

## 我们如何在布法罗实现这一点？

显而易见的方法是将对数据库进行查询的逻辑移到它自己的类型中，将依赖项作为这个类的成员传入。剔除依赖关系——根据我们插入的输入来验证输出的方法。

Buffalo 广泛使用了非常有用的包 [Pop](https://github.com/gobuffalo/pop) 。不幸的是，访问这个包中的数据库是通过[连接](https://godoc.org/github.com/gobuffalo/pop#Connection)类型。这很不幸，因为它是一个`struct`，这意味着我们不能将它的模拟版本插入到我们想要测试的单元中。

为了执行模拟的注入，我们需要创建一个接口，该接口公开了在`Connection`类型上导出的每个函数。

稍微搜索了一下，找到了 [`ifacemaker`](//github.com/vburenin/ifacemaker) 。该工具读取该结构并创建一个接口，该接口允许访问该类型上的每个导出函数。

为了运行这个工具，我这样做了:

```
ifacemaker --file ~/go/src/github.com/gobuffalo/pop/connection.go --struct Connection --iface DBConnection --pkg repo 
```

这呈现了以下类型:

```
package repo

// DBConnection ...
type DBConnection interface {
        String() string
        // URL returns the datasource connection string
        URL() string
        // MigrationURL returns the datasource connection string used for running the migrations
        MigrationURL() string
        // MigrationTableName returns the name of the table to track migrations
        MigrationTableName() string
        // Open creates a new datasource connection
        Open() error
        // Close destroys an active datasource connection
        Close() error
        // Transaction will start a new transaction on the connection. If the inner function
        // returns an error then the transaction will be rolled back, otherwise the transaction
        // will automatically commit at the end.
        Transaction(fn func(tx *Connection) error) error
        // NewTransaction starts a new transaction on the connection
        NewTransaction() (*Connection, error)
        // Rollback will open a new transaction and automatically rollback that transaction
        // when the inner function returns, regardless. This can be useful for tests, etc...
        Rollback(fn func(tx *Connection)) error
        // Q creates a new "empty" query for the current connection.
        Q() *Query
        // TruncateAll truncates all data from the datasource
        TruncateAll() error
} 
```

我们现在可以在我们的 Go 嘲讽工具中使用这个——例如 [`github.com/stretchr/testify/mock`](https://github.com/stretchr/testify/) 。但是如果我们要使用一个模拟，我们必须生成一个。这里我们转向[嘲讽](https://github.com/vektra/mockery/)。你第一次使用它时，它会失败。这是因为它没有填充包导入和包名称(例如，`Connection`而不是`pop.Connection`)。幸运的是这很容易修复，给出这个:

```
package repo

import (
    "github.com/gobuffalo/pop"
)

// DBConnection represents a Buffalo pop connection to a database
type DBConnection interface {
    String() string
    // URL returns the datasource connection string
    URL() string
    // MigrationURL returns the datasource connection string used for running the migrations
    MigrationURL() string
    // MigrationTableName returns the name of the table to track migrations
    MigrationTableName() string
    // Open creates a new datasource connection
    Open() error
    // Close destroys an active datasource connection
    Close() error
    // Transaction will start a new transaction on the connection. If the inner function
    // returns an error then the transaction will be rolled back, otherwise the transaction
    // will automatically commit at the end.
    Transaction(fn func(tx *pop.Connection) error) error
    // NewTransaction starts a new transaction on the connection
    NewTransaction() (*pop.Connection, error)
    // Rollback will open a new transaction and automatically rollback that transaction
    // when the inner function returns, regardless. This can be useful for tests, etc...
    Rollback(fn func(tx *pop.Connection)) error
    // Q creates a new "empty" query for the current connection.
    Q() *pop.Query
    // TruncateAll truncates all data from the datasource
    TruncateAll() error
} 
```

下一次我们运行`mockery`，它神奇地工作了。看看这个代码片段，如果它编译了，那么你就有了一个通过`struct`的接口来解锁你的单元测试。

下面的代码*应该*工作，尽管你可能需要填充空白:

```
 func AcceptConnection(tx DBConnection) {}

func TestMocks(t *testing.T) {
    tx := &pop.Connection{}
    mx := &mocks.DBConnection{}

    AcceptConnection(tx)
    AcceptConnection(mx)
} 
```

如果您做到了这一步，那么您就拥有了在 Buffalo 应用程序中添加一个可测试的存储库层所需的所有组件。