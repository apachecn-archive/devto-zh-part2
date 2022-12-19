# 使用 Go 和 TDD 构建财务跟踪 REST API 第 1 部分

> 原文：<https://dev.to/alirezabashiri/building-a-finance-tracking-rest-api-using-go-with-test-driven-development---part-1-16o8>

使用 Go 构建一个简单的 REST API 从设计架构到实现它都是一个挑战，因为没有标准的方法，开发者(软件架构)可以随心所欲，这是有代价的。

在使用 RubyOnRails 构建服务和 REST APIs 近 6 年后，我意识到开发人员对抽象充满热情，但这并不总是一个好的选择。比如说；如果你看看几乎每一个 RubyOnRails 项目，就会发现有太多的抽象和隐含的东西在进行，这使得学习曲线更像一条曲线，从非常浅的地方开始。

今天，我将向您展示用 Go 设计 REST APIs 的不同视角，它就像一个好家长，而不是让我们做任何事情，教我们练习、耐心和毅力。

让我们开始吧，首先每个 Go 项目都必须在`$GOPATH`中创建。我的`$GOPATH`是 at， ~~`~/go/src/`~~ `~/go/`。但是我们将在`~/go/src/github.com/budget-api`下创建项目，可以使用`go get`下载项目。

因此，我们将在 CLI 中运行这些命令。

```
cd ~/go/src/github.com/azbshiri
mkdir budget-api
touch main.go 
```

Enter fullscreen mode Exit fullscreen mode

(如果您没有`github.com/username`，您可以在将目录更改为`~/go/src/`后使用`mkdir -p github.com/username`创建它)

好的，我们将使用 PostgreSQL 作为我们的数据库和 Gorilla toolkit 来帮助我们构建一个 RESTful API。现在，我们只需要`github.com/go-pg/pg`能够连接到 PostgreSQL 和`github.com/gorilla/mux`进行路由。

```
go get github.com/go-pg/pg
go get github.com/gorilla/mux 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有了先决条件，我们应该考虑如何使用它们并设计架构。

我将使用一个`struct`作为名为`server`的控制器/处理器动作的容器，以便能够访问数据库、路由器等。所以我创建了一个名为`server.go`的新文件，并将声明放入其中。

```
// server.go
package main

type server struct {
    db *pg.DB
    mux *mux.Router
} 
```

Enter fullscreen mode Exit fullscreen mode

并创建一个工厂函数来初始化一个新的服务器(这将有助于测试，我将在以后展示)

```
// server.go
package main

type server struct {
    db *pg.DB
    mux *mux.Router
}

func newServer(db *pg.DB, mux *mux.Router) *server {
    s := server{db, mux}
    s.routes() // register handlers
    return &s
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个`routes.go`来声明 API
的路径

```
// routes.go
func (s *server) routes() {
    // register handlers here
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了如下的文件层次结构

```
.
├── main.go
├── rotues.go
└── server.go 
```

Enter fullscreen mode Exit fullscreen mode

为了能够运行服务器，我们应该实现`http.Handler`接口，因此我们为`server`添加了一个新的指针函数，名为`ServeHTTP`，并在路由器(`mux` )
中提供注册的处理程序

```
// server.go
package main

import (
    "net/http"

    "github.com/go-pg/pg"
    "github.com/gorilla/mux"
)

type server struct {
    db     *pg.DB
    mux    *mux.Router
}

func newServer(db *pg.DB, mux *mux.Router) *server {
    server := server{db, mux}
    server.routes() // register handlers
    return &server
}

func (s *server) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    s.mux.ServeHTTP(w, r)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以更新`main.go`文件来运行服务器

```
package main

import (
    "net/http"

    "github.com/go-pg/pg"
    "github.com/gorilla/mux"
)

func main() {
    db := pg.Connect(&pg.Options{
        User:     "alireza",
        Password: "alireza",
    })

    mux := mux.NewRouter()
    server := newServer(db, mux)
    http.ListenAndServe(":8080", server)
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们没有任何注册的处理程序，这将导致一个 API，所以我们只有一个无用的运行服务器，但我们有标准，开始这是一个伟大的事情，以加快开发过程。

由于我是一名 TDD 专家，所以让我们从编写第一个 API 的测试开始。如您所知，我们将构建一个财务跟踪 REST API，因此我们将拥有`Budget`业务模型并调用 API 端点`/budgets`。

```
// budgets_test.go
package main

import (
    "net/http"
    "os"
    "testing"

    "github.com/azbshiri/common/test"
    "github.com/go-pg/pg"
    "github.com/gorilla/mux"
    "github.com/stretchr/testify/assert"
)

var testServer *server

func TestMain(m *testing.M) {
    testServer = newServer(
        pg.Connect(&pg.Options{
            User:     "alireza",
            Password: "alireza",
            Database: "alireza",
        }),
        mux.NewRouter(),
    )
    os.Exit(m.Run())
}

func TestGetBudgets(t *testing.T) {
    res, err := test.DoRequest(testServer, "GET", "/budgets", nil)
    assert.NoError(t, err)
    assert.Equal(t, res.Code, http.StatusOK)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了编写上述测试，我们使用了两个库`github.com/stretchr/testify/assert`和`github.com/azbshiri/common/test`，第一个是为了使断言更容易，第二个有点复杂，我自己构建了这个库，以减少初始化`http.Request`和`httptest.ResponseRecorder`的重复，并将它们传递给现有的路由器，以便能够捕获响应(这是在 Go 中编写集成测试的正常方式，要了解更多信息，可以查看【https://golang.org/src/net/http/httptest/example_test.go】的[。](https://golang.org/src/net/http/httptest/example_test.go)

运行测试

```
go test -v .
=== RUN   TestGetBudgets
-------- FAIL: TestGetBudgets (0.00s)
        budgets_test.go:42:
                        Error Trace:    budgets_test.go:42
                        Error:          Not equal:
                                        expected: 404
                                        actual  : 200
                        Test:           TestGetBudgets
FAIL
exit status 1
FAIL    github.com/azbshiri/budget-api  0.018s

shell returned 1 
```

Enter fullscreen mode Exit fullscreen mode

唷！计划是在一篇文章中涵盖所有内容，但老实说，这比预期的要长，所以请允许我把它分成一系列的文章。

再次感谢您的阅读，不要忘记在下面留下您的问题和/或建议。