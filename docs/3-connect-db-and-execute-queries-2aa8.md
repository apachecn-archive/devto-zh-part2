# Go 教程:3。连接数据库并执行查询

> 原文：<https://dev.to/cohhei/3-connect-db-and-execute-queries-2aa8>

*本文最初发表在 [GitHub](https://github.com/cohhei/go-to-the-handson) 上。*

## 设置

```
$ docker run -d --name docker-postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 postgres:alpine
...

$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
d9065d8c1f30        postgres:alpine     "docker-entrypoint.s…"   3 seconds ago       Up 4 seconds        0.0.0.0:5432->5432/tcp   docker-postgres 
```

Enter fullscreen mode Exit fullscreen mode

## 连接 PostgreSQL

```
$ touch connect.go
$ go get -u github.com/lib/pq 
```

Enter fullscreen mode Exit fullscreen mode

```
// connect.go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/lib/pq"
)

func main() {
    // connStr := "user=postgres dbname=postgres sslmode=disable"
    // db, err := sql.Open("postgres", connStr)

    // [user]:[password]@[address]/[DB name]
    connStr := "postgres://postgres:postgres@localhost:5432/postgres?sslmode=disable"
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        fmt.Println(err)
        return
    }

    defer db.Close()

    err = db.Ping()
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println("Ping OK")
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run connectdb.go
Ping OK 
```

Enter fullscreen mode Exit fullscreen mode

## 创建表格

```
$ touch queries.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// queries.go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/lib/pq"
)

const createTable = `
DROP TABLE IF EXISTS ACCOUNT;
CREATE TABLE ACCOUNT
(
    ID serial,
    NAME varchar(50),
    MAIL_ADDRESS varchar(50),
    LANG varchar(5)
);
`

func main() {
    db, err := connectPostgres()
    if err != nil {
        return
    }

    defer db.Close()

    if _, err = db.Exec(createTable); err != nil {
        fmt.Println(err)
        return
    }

    if _, err = db.Exec("DROP TABLE ACCOUNT"); err != nil {
        fmt.Println(err)
        return
    }
}

func connectPostgres() (*sql.DB, error) {
    connStr := "postgres://postgres:postgres@localhost:5432/postgres?sslmode=disable"
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        fmt.Println(err)
        return nil, err
    }

    err = db.Ping()
    if err != nil {
        fmt.Println(err)
        return nil, err
    }
    fmt.Println("Ping OK")

    return db, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run queries.go
Ping OK 
```

Enter fullscreen mode Exit fullscreen mode

## 练习 3-1

在`queries.go`中增加一个功能`insertAccount()`。它通过在事务内部将包含任何静态账户数据的记录插入到`account`表中。

```
// queries.go
func main() {
    db, err := connectPostgres()
    if err != nil {
        return
    }

    defer db.Close()

    if _, err = db.Exec(createTable); err != nil {
        fmt.Println(err)
        return
    }

    if err := insertAccounts(db); err != nil {
        fmt.Println(err)
        return
    }

    if _, err = db.Exec("DROP TABLE ACCOUNT"); err != nil {
        fmt.Println(err)
        return
    }
}

// You should implement the function
func insertAccounts(db *sql.DB) error {} 
```

Enter fullscreen mode Exit fullscreen mode

提示:可以用 [`DB.begin`](https://golang.org/pkg/database/sql/#DB.Begin) 创建一个事务，用 [`Tx.Commit`](https://golang.org/pkg/database/sql/#Tx.Commit) 提交。

## 练习 3-2

在`queries.go`中增加一个功能`getAccounts()`。它返回`account`表中的所有记录。

```
// queries.go
    if err := insertAccounts(db); err != nil {
        fmt.Println(err)
        return
    }

    accounts, err := getAccounts(db)
    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Printf("%+v\n", accounts)
...

// You should implement the function and define the Account struct
func getAccounts(db *sql.DB) ([]Account, error) {} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run queries.go
Ping OK
[{ID:1 Name:My Name MailAddress:my_name@example.com Lang:ja} {ID:2 Name:Your Name MailAddress:your_name@example.com Lang:en}] 
```

Enter fullscreen mode Exit fullscreen mode

提示:可以用`DB.Query`执行`SELECT`查询，用 [`Rows.Scan`](https://golang.org/pkg/database/sql/#Row.Scan) 得到结果。

答案是[query . go](https://github.com/cohhei/go-to-the-handson/blob/master/03/main/queries.go)。

[上一张](https://dev.to/cohhei/go-tutorial-2-create-an-http-request-and-server-1h82) | [下一张](https://dev.to/cohhei/sample-application--todo-list-api--2lnk)