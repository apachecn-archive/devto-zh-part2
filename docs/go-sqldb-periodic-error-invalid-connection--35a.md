# 转到 sql。数据库周期性错误:无效连接

> 原文：<https://dev.to/aaronellington/go-sqldb-periodic-error-invalid-connection--35a>

## 背景

到目前为止，我的团队主要是 PHP 开发人员。我们最近将我们的第一个 Go 应用程序(REST API)部署到我们的生产服务器上，以处理我们的主 [Symfony](https://symfony.com/) 应用程序的一些后端功能。

## 一期

在 15-20 分钟的不活动之后，对我们的 API 的下一个请求将会失败，因为在尝试进行任何数据库查询时会出现一个`invalid connection`错误，但是下一个查询将会成功。

这通常会在夜间发生，此时我们的 API 流量会低得多。

我怀疑空闲连接被 MySQL 服务器杀死了。PHP 从来没有这个问题，因为每个请求都会建立一个新的 MySQL 连接，或者如果一个 PHP 脚本运行超过 15 分钟，它就会不断地发出请求以保持连接。

## 解

我想调整 MySQL 服务器上的超时，但它是由另一个团队为我们管理的，所以我希望找到一个我的团队可以实现的解决方案。

通过查看`go doc sql.DB`，我发现它有一个`Ping()`方法。描述说:`Ping verifies a connection to the database is still alive, establishing a connection if necessary.`

这听起来就像是能解决我的问题的方法，而且确实如此！我只是设置了一个简单的 goroutine 来每 5 分钟运行一次`Ping()`方法。

```
package main

import (
    "database/sql"
    "log"
    "time"

    _ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

func main() {
    db, _ = sql.Open("mysql", "user:password@/dbname")
    // Error handling omitted for this example

    go periodicPing()

    // Start the web server
}

func periodicPing() {
    for {
        time.Sleep(time.Minute * 5)

        err := db.Ping()
        if err != nil {
            log.Println(err)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更新(2019 年 1 月 3 日)

我承认这不是一个好的解决方案，我想回来参考我的问题的更新:

[![aaronellington](img/fd4f5b13b16f16714c41b783579a9529.png)](/aaronellington) [## 配置 Go sql 的重要性。数据库连接

### 亚伦艾灵顿 1 月 4 日 192 分钟阅读

#go #database #beginners](/aaronellington/importance-of-configuring-go-sqldb-connections-15bc)