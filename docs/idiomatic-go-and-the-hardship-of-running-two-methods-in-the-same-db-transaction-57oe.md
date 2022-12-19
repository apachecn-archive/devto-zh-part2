# 惯用的 Go 和在同一个数据库事务中运行两种方法的困难

> 原文：<https://dev.to/rhymes/idiomatic-go-and-the-hardship-of-running-two-methods-in-the-same-db-transaction-57oe>

为了更好地学习 Go，我正在尝试重构一系列函数，这些函数接受 DB 连接作为 struct 方法的第一个参数，以及一些更“惯用”的 Go。

现在我的“数据存储”方法是这样的:

```
func CreateA(db orm.DB, a *A) error {
    db.Exec("INSERT...")
}

func CreateB(db orm.DB, b *B) error {
    db.Exec("INSERT...")
} 
```

Enter fullscreen mode Exit fullscreen mode

这些功能工作得非常好。`orm.DB`是 go-pg 的 [DB 接口。](https://godoc.org/github.com/go-pg/pg/orm#DB)

因为这两个函数接受 db 连接，所以我可以传递一个实际的连接或者一个[事务](https://godoc.org/github.com/go-pg/pg#Tx)(实现相同的接口)。我可以肯定，发出 SQL INSERTs 的两个函数在同一个事务中运行，避免了在其中一个失败的情况下数据库中出现不一致的状态。

当我决定阅读更多关于如何更好地构建代码，并在需要时使其“可模仿”时，麻烦就开始了。

所以我搜索了一下，阅读了文章[Go 中的实际持久性:组织数据库访问](http://www.alexedwards.net/blog/organising-database-access)，并试图重构代码以使用适当的接口。

结果是这样的:

```
type Store {
    CreateA(a *A) error
    CreateB(a *A) error
}

type DB struct {
    orm.DB
}

func NewDBConnection(p *ConnParams) (*DB, error) {
    .... create db connection ...
    return &DB{db}, nil
}

func (db *DB) CreateA(a *A) error {
...
}

func (db *DB) CreateB(b *B) error {
...
} 
```

Enter fullscreen mode Exit fullscreen mode

这让我可以编写如下代码:

```
db := NewDBConnection()
DB.CreateA(a)
DB.CreateB(b) 
```

Enter fullscreen mode Exit fullscreen mode

而不是:

```
db := NewDBConnection()
CreateA(db, a)
CreateB(db, b) 
```

Enter fullscreen mode Exit fullscreen mode

实际问题是，我失去了在同一个事务中运行这两个功能的能力。我还没来得及做:

```
pgDB := DB.DB.(*pg.DB) // convert the interface to an actual connection
pgDB.RunInTransaction(func(tx *pg.Tx) error {
    CreateA(tx, a)
    CreateB(tx, b)
}) 
```

Enter fullscreen mode Exit fullscreen mode

或者类似于:

```
tx := db.DB.Begin()

err = CreateA(tx, a)
err = CreateB(tx, b)

if err != nil {
  tx.Rollback()
} else {
  tx.Commit()
} 
```

Enter fullscreen mode Exit fullscreen mode

这或多或少是一回事。

因为这些函数接受连接和事务之间的公共接口，所以我可以从我的模型层中抽象出事务逻辑，发送完整的连接或事务。这允许我在“HTTP 处理程序”中决定什么时候创建事务，什么时候不需要。

请记住，连接是一个全局对象，表示由 go 自动处理的连接池，所以我尝试的方法是:

```
pgDB := DB.DB.(*pg.DB) // convert the interface to an actual connection
err = pgDB.RunInTransaction(func(tx *pg.Tx) error {
    DB.DB = tx // replace the connection with a transaction
    DB.CreateA(a)
    DB.CreateB(a)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这显然是一个坏主意，因为虽然它有效，但它只有效一次，因为我们用一个事务替换了全局连接。以下请求会中断服务器。

我破坏了我的服务器，试图从根本上提高代码质量

有什么想法吗？周围找不到这方面的资料，可能是因为不知道合适的关键词:-D