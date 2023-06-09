# 围棋教程:4。示例应用程序-待办事项列表 API-

> 原文：<https://dev.to/cohhei/sample-application--todo-list-api--2lnk>

*本文最初发表在 [GitHub](https://github.com/cohhei/go-to-the-handson) 上。*

我们将为 TODO 应用程序开发一个包含以下功能的小型 RESTful API:

*   返回所有待办事项
*   保存新待办事项
*   删除待办事项

在构建 API 之前，请创建一个新目录。

```
$ mkdir todo
$ cd todo 
```

Enter fullscreen mode Exit fullscreen mode

## 数据模式

API 与客户的交易待办事项以 JSON 文件的形式出现，如:

```
[  {  "id":  1,  "title":  "Do dishes",  "note":  "That will be done by Gopher.",  "due_date":  "2000-01-01T00:00:00Z"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们用 Go 来定义 schema。

```
$ mkdir schema
$ touch schema/model.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// schema/model.go
package schema

import "time"

type Todo struct {
    ID      int       `json:"id"`
    Title   string    `json:"title"`
    Note    string    `json:"note"`
    DueDate time.Time `json:"due_date"`
} 
```

Enter fullscreen mode Exit fullscreen mode

将模式定义为一种结构是非常常见的模式。`Todo`结构有四个字段，`ID`、`Title`、`Note`和`DueDate`。它们分别有各自的类型和 [json 标签](https://golang.org/pkg/encoding/json/)。当对`Todo`结构进行编码时，标签将成为字段名。如果不写标签，json 字段名将与结构名相同。

## 储存库

创建`db`目录和`db/repository.go`。

```
$ mkdir db
$ touch db/repository.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// db/repository.go
package db

import (
    "context"

    "../schema"
)

const keyRepository = "Repository"

type Repository interface {
    Close()
    Insert(todo *schema.Todo) (int, error)
    Delete(id int) error
    GetAll() ([]schema.Todo, error)
}

func SetRepository(ctx context.Context, repository Repository) context.Context {
    return context.WithValue(ctx, keyRepository, repository)
}

func Close(ctx context.Context) {
    getRepository(ctx).Close()
}

func Insert(ctx context.Context, todo *schema.Todo) (int, error) {
    return getRepository(ctx).Insert(todo)
}

func Delete(ctx context.Context, id int) error {
    return getRepository(ctx).Delete(id)
}

func GetAll(ctx context.Context) ([]schema.Todo, error) {
    return getRepository(ctx).GetAll()
}

func getRepository(ctx context.Context) Repository {
    return ctx.Value(keyRepository).(Repository)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个`Repository`接口有四个方法。我们可以通过[上下文](https://golang.org/pkg/context/)中的接口访问我们的数据库。该接口可以为每个中间件(如 PostgreSQL 或 MySQL)划分应用程序逻辑和实现。`SetRepository`函数为上下文设置实现`Repository`接口的结构。

## 样本待办事项

首先，我们将创建一个结构，它返回静态 TODO 列表作为样本，而不是 DB 中的动态值。

```
$ touch db/samples.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// db/samples.go
package db

import "../schema"

type Sample struct{}

func (s *Sample) Close() {}

func (s *Sample) Insert(todo *schema.Todo) (int, error) {
    return 0, nil
}

func (s *Sample) Delete(id int) error {
    return nil
}

func (s *Sample) GetAll() ([]schema.Todo, error) {
    return nil, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

`Sample`结构有`Close`、`Insert`、`Delete`和`GetAll`方法，所以我们可以把它作为`Repository`接口类型。但是这些方法还没有任何作用。首先为它们创建测试，然后实现它们。

### 对样品进行测试

创建`db/sample_test.go`并编写测试。

```
$ touch db/sample_test.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// db/sample_test.go
package db

import (
    "reflect"
    "testing"
    "time"

    "../schema"
)

func TestClose(t *testing.T) {
    sample := Sample{}

    sample.Close()
}

func TestInsert(t *testing.T) {
    sample := Sample{}

    todo := &schema.Todo{}

    got, err := sample.Insert(todo)
    if err != nil {
        t.Error(err)
    }

    if got != 0 {
        t.Fatal("Want: 0, Got: ", got)
    }
}

func TestDelete(t *testing.T) {
    sample := Sample{}

    err := sample.Delete(1)
    if err != nil {
        t.Error(err)
    }
}

func TestGetAll(t *testing.T) {
    sample := Sample{}

    got, err := sample.GetAll()
    if err != nil {
        t.Error(err)
    }

    want := []schema.Todo{
        {
            ID:      1,
            Title:   "Do dishes",
            Note:    "",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.UTC),
        },
        {
            ID:      2,
            Title:   "Do homework",
            Note:    "",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.UTC),
        },
        {
            ID:      2,
            Title:   "Twitter",
            Note:    "",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.UTC),
        },
    }

    if !reflect.DeepEqual(got, want) {
        t.Fatalf("Want: %v, Got: %v\n", want, got)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于`Close`、`Insert`和`Delete`，我们的样本库没有任何完整的特性。所以对他们来说测试毫无意义，已经通过了。当然，`TestGetAll`还会失败。

```
$ go test ./db/*
-------- FAIL: TestGetAll (0.00s)
        sample_test.go:71: Want: [{1 Do dishes  2000-01-01 00:00:00 +0000 UTC} {2 Do homework  2000-01-01 00:00:00 +0000 UTC} {2 Twitter  2000-01-01 00:00:00 +0000 UTC}], Got: []
FAIL
FAIL    command-line-arguments  0.041s 
```

Enter fullscreen mode Exit fullscreen mode

### 实现为样本

```
// db/samples.go
package db

import (
    "time"

    "../schema"
)

// ...

func (s *Sample) GetAll() ([]schema.Todo, error) {
    todoList := []schema.Todo{
        {
            ID:      1,
            Title:   "Do dishes",
            Note:    "",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.UTC),
        },
        {
            ID:      2,
            Title:   "Do homework",
            Note:    "",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.UTC),
        },
        {
            ID:      2,
            Title:   "Twitter",
            Note:    "",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.UTC),
        },
    }

    return todoList, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

执行测试。他们会过去的。

```
$ go test ./db/*
ok      command-line-arguments  0.182s 
```

Enter fullscreen mode Exit fullscreen mode

## 服务

创建`service`目录和`service/todo.go`。这个服务只有简单的函数，这些函数调用`db`包中同名的函数。如果你想升级这个 TODO API，这个服务会有很多复杂的功能。

```
$ mkdir service
$ touch service/todo.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// service/todo.go
package service

import (
    "context"

    "../db"
    "../schema"
)

func Close(ctx context.Context) {
    db.Close(ctx)
}

func Insert(ctx context.Context, todo *schema.Todo) (int, error) {
    return db.Insert(ctx, todo)
}

func Delete(ctx context.Context, id int) error {
    return db.Delete(ctx, id)
}

func GetAll(ctx context.Context) ([]schema.Todo, error) {
    return db.GetAll(ctx)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 路由和处理程序

### 样本处理器

```
$ mkdir handler
$ touch handler/todo.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// handler/todo.go
package handler

import (
    "encoding/json"
    "net/http"

    "../db"
    "../service"
)

type todoHandler struct {
    samples  *db.Sample
}

func (handler *todoHandler) GetSamples(w http.ResponseWriter, r *http.Request) {
    ctx := db.SetRepository(r.Context(), handler.samples)

    todoList, err := service.GetAll(ctx)
    if err != nil {
        responseError(w, http.StatusInternalServerError, err.Error())
        return
    }

    responseOk(w, todoList)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个包有一个`GetSamples`方法，它为上下文设置了一个`db.Sample`结构。因此 TODO 服务将返回样本 TODO 列表。

创建效用函数`responseOk`和`responseError`。

```
// handler/todo.go
func responseOk(w http.ResponseWriter, body interface{}) {
    w.WriteHeader(http.StatusOK)
    w.Header().Set("Content-Type", "application/json")

    json.NewEncoder(w).Encode(body)
}

func responseError(w http.ResponseWriter, code int, message string) {
    w.WriteHeader(code)
    w.Header().Set("Content-Type", "application/json")

    body := map[string]string{
        "error": message,
    }
    json.NewEncoder(w).Encode(body)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 路由

创建`handler/routes.go`。工艺路线将被写入文件中。

```
$ touch handler/routes.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// handler/routes.go
package handler

import (
    "net/http"

    "../db"
)

func SetUpRouting() *http.ServeMux {
    todoHandler := &todoHandler{
        samples:  &db.Sample{},
    }

    mux := http.NewServeMux()
    mux.HandleFunc("/samples", todoHandler.GetSamples)

    return mux
} 
```

Enter fullscreen mode Exit fullscreen mode

## 功能测试

我们已经完成了端点`/samples`的构建，它应该以 Json 文件的形式返回样本 TODO 列表。让我们编写功能测试来确认它。

```
$ mkdir functional
$ touch functional/todo_test.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// functional/todo_test.go
package functional

import (
    "net/http"
    "strings"
    "testing"

    "../handler"
)

func TestGetSamples(t *testing.T) {
    testServer := setupServer()

    req, err := http.NewRequest(http.MethodGet, "http://localhost:8080/samples", nil)
    if err != nil {
        t.Fatal(err)
    }

    rec := httptest.NewRecorder()
    testServer.ServeHTTP(rec, req)

    got := strings.TrimSpace(rec.Body.String())

    want := `[{"id":1,"title":"Do dishes","note":"","due_date":"2000-01-01T00:00:00Z"},{"id":2,"title":"Do homework","note":"","due_date":"2000-01-01T00:00:00Z"},{"id":2,"title":"Twitter","note":"","due_date":"2000-01-01T00:00:00Z"}]`

    if got != want {
        t.Fatalf("Want: %v, Got: %v", want, got)
    }
}

func setupServer() *http.ServeMux {
    return handler.SetUpRouting()
} 
```

Enter fullscreen mode Exit fullscreen mode

这个测试很简单。它只是调用端点并将响应体与预期值进行比较。应该会过去的。

```
$ go test ./functional/todo_test.go
ok      command-line-arguments  0.099s 
```

Enter fullscreen mode Exit fullscreen mode

## main.go

终于到了让`main.go`调用`handler.SetUpRouting()`并设置 HTTP 服务器的时候了。

```
$ touch main.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// main.go
package main

import (
    "fmt"
    "log"
    "net/http"

    "./handler"
)

func main() {
    mux := handler.SetUpRouting()

    fmt.Println("http://localhost:8080")
    log.Fatal(http.ListenAndServe(":8080", mux))
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`main.go`并访问`http://localhost:8080/samples`。将返回示例 Json 文件。

```
$  go  run  main.go  $  curl  "http://localhost:8080/samples"  |  jq  %  Total  %  Received  %  Xferd  Average  Speed  Time  Time  Time  Current  Dload  Upload  Total  Spent  Left  Speed  100  221  100  221  0  0  27949  0  --:--:--  --:--:--  --:--:--  31571  [  {  "id":  1,  "title":  "Do dishes",  "note":  "",  "due_date":  "2000-01-01T00:00:00Z"  },  {  "id":  2,  "title":  "Do homework",  "note":  "",  "due_date":  "2000-01-01T00:00:00Z"  },  {  "id":  2,  "title":  "Twitter",  "note":  "",  "due_date":  "2000-01-01T00:00:00Z"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

## PostgreSQL

我们已经创建了返回待办事项列表的 API。但是它只能返回一个静态文件，所以我们不能添加一个新任务，更新一个已存在的任务，并删除它们。我们必须实现`Postgres`存储库和新的端点来调用它的方法。

### DB 为测试

```
$ mkdir testdb
$ touch testdb/testdb.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// testdb/testdb.go
package testdb

import (
    "database/sql"
)

const createTable = `
DROP TABLE IF EXISTS todo;
Alter SEQUENCE todo_id RESTART WITH 1;
CREATE TABLE todo (
  ID serial PRIMARY KEY,
  TITLE TEXT NOT NULL,
  NOTE TEXT,
  DUE_DATE TIMESTAMP WITH TIME ZONE
);
`

type TestDB struct {
    db *sql.DB
}

func Setup() *sql.DB {
    db, err := connectPostgresForTests()
    if err != nil {
        panic(err)
    }

    if _, err = db.Exec(createTable); err != nil {
        panic(err)
    }

    return db
}

func connectPostgresForTests() (*sql.DB, error) {
    connStr := "postgres://postgres:postgres@localhost:5432/postgres?sslmode=disable"
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        return nil, err
    }

    err = db.Ping()
    if err != nil {
        return nil, err
    }

    return db, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为 Postgres 考试

创建`db/postgres.go`并添加方法来实现`Repository`接口。

```
$ touch db/postgres.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// db/postgres.go
package db

import (
    "database/sql"

    "github.com/cohhei/go-to-the-handson/04/schema"
)

type Postgres struct {
    DB *sql.DB
}

func (p *Postgres) Close() {}

func (p *Postgres) Insert(todo *schema.Todo) (int, error) {
    return 0, nil
}

func (p *Postgres) Delete(id int) error {
    return nil
}

func (p *Postgres) GetAll() ([]schema.Todo, error) {
    return nil, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

然后创建`db/postgres_test.go`。

```
$ touch db/postgres_test.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// db/postgres_test.go
package db

import (
    "reflect"
    "testing"
    "time"

    "../schema"
    "../testdb"
    _ "github.com/lib/pq"
)

func TestPostgres_Insert(t *testing.T) {
    postgres := &Postgres{testdb.Setup()}
    defer postgres.Close()

    todo := &schema.Todo{
        Title:   "title1",
        Note:    "note1",
        DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.Local),
    }

    got, err := postgres.Insert(todo)
    if err != nil {
        t.Fatal(err)
    }

    want := 1

    if got != want {
        t.Fatal(err)
    }
}

func TestPostgres_GetAll(t *testing.T) {
    postgres := &Postgres{testdb.Setup()}
    defer postgres.Close()

    todo := &schema.Todo{
        Title:   "title1",
        Note:    "note1",
        DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.Local),
    }

    _, err := postgres.Insert(todo)
    if err != nil {
        t.Fatal(err)
    }

    got, err := postgres.GetAll()
    if err != nil {
        t.Fatal(err)
    }

    want := []schema.Todo{
        {
            ID:      1,
            Title:   "title1",
            Note:    "note1",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.Local),
        },
    }

    if equal(got, want) {
        t.Fatalf("Want: %v, Got: %v", want, got)
    }
}

func TestPostgres_Delete(t *testing.T) {
    postgres := &Postgres{testdb.Setup()}
    defer postgres.Close()

    todo := &schema.Todo{
        Title:   "title1",
        Note:    "note1",
        DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.Local),
    }

    id, err := postgres.Insert(todo)
    if err != nil {
        t.Fatal(err)
    }

    err = postgres.Delete(id)
    if err != nil {
        t.Fatal(err)
    }

    got, err := postgres.GetAll()
    if err != nil {
        t.Fatal(err)
    }

    if len(got) > 0 {
        t.Fatal("The record is not deleted.")
    }
}

func equal(got interface{}, want interface{}) bool {
    return reflect.DeepEqual(got, want)
} 
```

Enter fullscreen mode Exit fullscreen mode

测试函数在`Postgres`结构中包含其他方法，例如`TestPostgres_Delete`不仅有`Postgres.Delete`方法，还有`Insert`和`GetAll`。这种模式太糟糕了。当`Insert`有任何 bug 时，即使`Delete`已经正确执行了，`TestPostgres_Delete`也会失败。如果可能的话，您应该构建向 DB 输入和输出的函数。

当然，测试还是会失败。

```
$ go test ./db/postgres*
-------- FAIL: TestPostgres_Insert (0.03s)
        postgres_test.go:31: <nil>
FAIL
FAIL    command-line-arguments  0.077s 
```

Enter fullscreen mode Exit fullscreen mode

### 实现为 Postgres

```
// db/postgres.go
package db

import (
    "database/sql"

    "../schema"
    _ "github.com/lib/pq"
)

type Postgres struct {
    DB *sql.DB
}

func (p *Postgres) Close() {
    p.DB.Close()
}

func (p *Postgres) Insert(todo *schema.Todo) (int, error) {
    query := `
        INSERT INTO todo (id, title, note, due_date)
        VALUES (nextval('todo_id'), $1, $2, $3)
        RETURNING id;
    `

    rows, err := p.DB.Query(query, todo.Title, todo.Note, todo.DueDate)
    if err != nil {
        return -1, err
    }

    var id int
    for rows.Next() {
        if err := rows.Scan(&id); err != nil {
            return -1, err
        }
    }

    return id, nil
}

func (p *Postgres) Delete(id int) error {
    query := `
        DELETE FROM todo
        WHERE id = $1;
    `

    if _, err := p.DB.Exec(query, id); err != nil {
        return err
    }

    return nil
}

func (p *Postgres) GetAll() ([]schema.Todo, error) {
    query := `
        SELECT *
        FROM todo
        ORDER BY id;
    `

    rows, err := p.DB.Query(query)
    if err != nil {
        return nil, err
    }

    var todoList []schema.Todo
    for rows.Next() {
        var t schema.Todo
        if err := rows.Scan(&t.ID, &t.Title, &t.Note, &t.DueDate); err != nil {
            return nil, err
        }
        todoList = append(todoList, t)
    }

    return todoList, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

要通过测试，您应该设置 PostgreSQL。如果您已经安装了 Docker，您可以使用一行命令轻松地构建它。

```
$ docker run -d --name docker-postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 postgres:alpine
$ go test ./db/postgres*
ok      command-line-arguments  0.250s 
```

Enter fullscreen mode Exit fullscreen mode

### Postgres 功能测试

```
// functional/todo_test.go

// ...
import (
    // ...
    "bytes"
    "fmt"
    "net/http/httptest"
    "reflect"
    "time"
    // ...
    "../db"
    "../schema"
    "../testdb"
    // ...
)
// ...
func TestGetSamples(t *testing.T) {
    testServer := setupServer(nil)
//...
func setupServer(postgres *db.Postgres) *http.ServeMux {
    return handler.SetUpRouting(postgres)
}
// ...
func TestGetAllTodo(t *testing.T) {
    postgres := &db.Postgres{testdb.Setup()}
    testServer := setupServer(postgres)

    todo := &schema.Todo{
        Title:   "My Task1",
        DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.Local),
    }

    _, err := postgres.Insert(todo)
    if err != nil {
        t.Fatal(err)
    }

    req, err := http.NewRequest(http.MethodGet, "http://localhost:8080/todo", nil)
    if err != nil {
        t.Fatal(err)
    }

    rec := httptest.NewRecorder()
    testServer.ServeHTTP(rec, req)

    got := strings.TrimSpace(rec.Body.String())

    want := `[{"id":1,"title":"My Task1","note":"","due_date":"2000-01-01T00:00:00+09:00"}]`

    if got != want {
        t.Fatalf("Want: %v, Got: %v", want, got)
    }
}

func TestSaveTodo(t *testing.T) {
    postgres := &db.Postgres{testdb.Setup()}
    testServer := setupServer(postgres)

    body := []byte(`{"id":1,"title":"My Task1","note":"","due_date":"2000-01-01T00:00:00+09:00"}`)

    req, err := http.NewRequest(http.MethodPost, "http://localhost:8080/todo", bytes.NewReader(body))
    if err != nil {
        t.Fatal(err)
    }

    rec := httptest.NewRecorder()
    testServer.ServeHTTP(rec, req)

    got := strings.TrimSpace(rec.Body.String())
    want := "1"

    if got != want {
        t.Fatalf("Want: %v, Got: %v", want, got)
    }

    gotTodo, err := postgres.GetAll()
    if err != nil {
        t.Fatal(err)
    }

    wantTodo := []schema.Todo{
        {
            Title:   "My Task1",
            DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.Local),
        },
    }

    if !reflect.DeepEqual(got, want) {
        t.Fatalf("Want: %v, Got: %v\n", wantTodo, gotTodo)
    }
}

func TestDeleteTodo(t *testing.T) {
    postgres := &db.Postgres{testdb.Setup()}
    testServer := setupServer(postgres)

    todo := &schema.Todo{
        Title:   "My Task1",
        DueDate: time.Date(2000, 1, 1, 0, 0, 0, 0, time.Local),
    }

    id, err := postgres.Insert(todo)
    if err != nil {
        t.Fatal(err)
    }

    body := []byte(fmt.Sprintf(`{"id":%d}`, id))

    req, err := http.NewRequest(http.MethodDelete, "http://localhost:9999/todo", bytes.NewReader(body))
    if err != nil {
        t.Fatal(err)
    }

    rec := httptest.NewRecorder()
    testServer.ServeHTTP(rec, req)

    got := rec.Body.String()

    want := ""

    if got != want {
        t.Fatalf("Want: %v, Got: %v", want, got)
    }

    gotTodo, err := postgres.GetAll()
    if err != nil {
        t.Fatal(err)
    }

    if len(gotTodo) > 0 {
        t.Fatalf("Should return the empty slice, Got: %v\n", gotTodo)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go test ./functional/todo_test.go
# command-line-arguments
functional/todo_test.go:164:22: too many arguments in call to handler.SetUpRouting
        have (*db.Postgres)
        want ()
FAIL    command-line-arguments [build failed] 
```

Enter fullscreen mode Exit fullscreen mode

### 新增路由和处理程序

```
// handler/todo.go
import (
    // ...
    "io/ioutil"
    // ...
    "../schema"
)

type todoHandler struct {
    postgres *db.Postgres
    samples  *db.Sample
}
//...
func (handler *todoHandler) saveTodo(w http.ResponseWriter, r *http.Request) {
    ctx := db.SetRepository(r.Context(), handler.postgres)

    b, err := ioutil.ReadAll(r.Body)
    if err != nil {
        responseError(w, http.StatusInternalServerError, err.Error())
        return
    }

    var todo schema.Todo
    if err := json.Unmarshal(b, &todo); err != nil {
        responseError(w, http.StatusBadRequest, err.Error())
        return
    }

    id, err := service.Insert(ctx, &todo)
    if err != nil {
        responseError(w, http.StatusInternalServerError, err.Error())
        return
    }

    responseOk(w, id)
}

func (handler *todoHandler) deleteTodo(w http.ResponseWriter, r *http.Request) {
    ctx := db.SetRepository(r.Context(), handler.postgres)

    b, err := ioutil.ReadAll(r.Body)
    if err != nil {
        responseError(w, http.StatusInternalServerError, err.Error())
        return
    }

    var req struct {
        ID int `json:"id"`
    }
    if err := json.Unmarshal(b, &req); err != nil {
        responseError(w, http.StatusBadRequest, err.Error())
        return
    }

    if err := service.Delete(ctx, req.ID); err != nil {
        responseError(w, http.StatusInternalServerError, err.Error())
        return
    }

    w.WriteHeader(http.StatusOK)
}

func (handler *todoHandler) getAllTodo(w http.ResponseWriter, r *http.Request) {
    ctx := db.SetRepository(r.Context(), handler.postgres)

    todoList, err := service.GetAll(ctx)
    if err != nil {
        responseError(w, http.StatusInternalServerError, err.Error())
        return
    }

    responseOk(w, todoList)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在每个处理函数中设置了一个存储库，但这只是为了解释 Go 中的接口。所以你不应该在你的产品代码中使用这种模式。

```
// handler/routes.go
package handler

import (
    "net/http"

    "../db"
)

// - func SetUpRouting() {
func SetUpRouting(postgres *db.Postgres) *http.ServeMux {
    todoHandler := &todoHandler{
        postgres: postgres,
        samples:  &db.Sample{},
    }

    mux := http.NewServeMux()
    mux.HandleFunc("/samples", todoHandler.GetSamples)
    mux.HandleFunc("/todo", func(w http.ResponseWriter, r *http.Request) {
        switch r.Method {
        case http.MethodGet:
            todoHandler.getAllTodo(w, r)
        case http.MethodPost:
            todoHandler.saveTodo(w, r)
        case http.MethodDelete:
            todoHandler.deleteTodo(w, r)
        default:
            responseError(w, http.StatusNotFound, "")
        }
    })

    return mux
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go test ./functional/todo_test.go
ok      command-line-arguments  0.179s 
```

Enter fullscreen mode Exit fullscreen mode

通过功能测试后，就可以停止 docker 容器了。

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
530507a60b58        postgres:alpine     "docker-entrypoint.s…"   19 hours ago        Up 19 hours         0.0.0.0:5432->5432/tcp   docker-postgres

$ docker stop 530507a60b58 
```

Enter fullscreen mode Exit fullscreen mode

### 更新 main.go

```
// main.go

import (
    //...
    "time"
    "./db"
    //...
)

func main() {
    var postgres *db.Postgres
    var err error
    for i := 0; i < 10; i++ {
        time.Sleep(3 * time.Second)
        postgres, err = db.ConnectPostgres()
    }
    if err != nil {
        panic(err)
    } else if postgres == nil {
        panic("postgres is nil")
    }

    mux := handler.SetUpRouting(postgres)

    fmt.Println("http://localhost:8080")
    log.Fatal(http.ListenAndServe(":8080", mux))
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不创建`db/ConnectPostgres`函数。添加到`db/postgres.go`。

```
// db/postgres.go
func ConnectPostgres() (*Postgres, error) {
    connStr := "postgres://postgres:postgres@postgres/postgres?sslmode=disable"
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        return nil, err
    }

    err = db.Ping()
    if err != nil {
        return nil, err
    }

    return &Postgres{db}, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

功能和`testdb.connectPostgresForTests`有什么区别？是`connStr`字符串。`localhost:5432`在`testdb.connectPostgresForTests`中被配置为主机名。但是`postgres`是在这里配置的。Docker 将解析名称。

## 码头工人

### Postgres

```
$ mkdir postgres
$ touch postgres/up.sql 
```

Enter fullscreen mode Exit fullscreen mode

```
-- postgres/up.sql
DROP TABLE IF EXISTS todo;
CREATE SEQUENCE todo_id START 1;
CREATE TABLE todo (
  ID serial PRIMARY KEY,
  TITLE TEXT NOT NULL,
  NOTE TEXT,
  DUE_DATE TIMESTAMP WITH TIME ZONE
); 
```

Enter fullscreen mode Exit fullscreen mode

```
$ touch postgres/Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

```
# postgres/Dockerfile
FROM postgres:10.3
COPY up.sql /docker-entrypoint-initdb.d/1.sql
CMD ["postgres"] 
```

Enter fullscreen mode Exit fullscreen mode

### 待办事宜 API

```
$ touch Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

```
# Dockerfile
# build stage
FROM golang:1.10.2-alpine AS build
ARG dir=/todo
ADD . ${dir}
RUN apk update && \
    apk add --virtual build-dependencies build-base git && \
    cd ${dir} && \
    go get -u github.com/lib/pq && \
    go build -o todo-api

# final stage
FROM alpine:3.7
ARG dir=/todo
WORKDIR /app
COPY --from=build ${dir}/todo-api /app/
EXPOSE 8080
CMD ./todo-api 
```

Enter fullscreen mode Exit fullscreen mode

### 码头工-化合物. yml

```
$ touch docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

```
# docker-compose.yml
version: "3.6"

services:
  postgres:
    build: "./postgres"
    restart: "always"
    environment:
      POSTGRES_DB: "postgres"
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
  todo-api:
    build: "."
    depends_on:
      - postgres
    ports:
      - 8080:8080 
```

Enter fullscreen mode Exit fullscreen mode

```
$ docker-compose up -d
Creating todo_postgres_1 ... done Creating todo_todo-api_1 ... done

 $ docker-compose ps
     Name                    Command              State           Ports
-------------------------------------------------------------------------------------
todo_postgres_1   docker-entrypoint.sh postgres   Up      5432/tcp
todo_todo-api_1   /bin/sh -c ./todo-api           Up      0.0.0.0:8080->8080/tcp 
```

Enter fullscreen mode Exit fullscreen mode

## 请求

我们已经完成了 TODO-API，但是不能在浏览器中使用。创建`requests/request.go`，一个 cli 工具来使用我们的 API。

```
$ mkdir requests
$ touch requests/requests.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// requests/requests.go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "os"
    "strconv"
)

const usage = `
usage: todo COMMAND

Commands:
  samples     Get sample todo tasks
  all         Get all todo tasks
  add         Add new todo task
  delete      Remove a todo task
`

func main() {
    if len(os.Args) < 2 {
        fmt.Print(usage)
        return
    }

    switch command := os.Args[1]; command {
    case "samples":
        get("samples")
    case "all":
        get("todo")
    case "add":
        add()
    case "delete":
        del()
    default:
        fmt.Printf("'%s' is not a todo command.", command)
    }
}

func get(path string) {
    res, err := http.Get(fmt.Sprintf("http://localhost:8080/%s", path))
    if err != nil {
        panic(err)
    }
    defer res.Body.Close()

    b, err := ioutil.ReadAll(res.Body)
    if err != nil {
        panic(err)
    }

    if len(b) == 0 {
        fmt.Println("The response is empty.")
        return
    }
    fmt.Println(string(b))
}

const usage_add = `
usage: todo add TODO_NAME TODO_NOTE DUE_DATE
`

func add() {
    if len(os.Args) < 3 {
        fmt.Print(usage_add)
        return
    }

    name := os.Args[2]
    var note string
    var date string

    if len(os.Args) > 3 {
        note = os.Args[3]
        if len(os.Args) > 4 {
            date = os.Args[4]
        }
    }

    todo := struct {
        Title   string `json:"title"`
        Note    string `json:"note,omitempty"`
        DueDate string `json:"due_date,omitempty"`
    }{
        name, note, date,
    }

    b, err := json.Marshal(todo)
    if err != nil {
        panic(err)
    }
    fmt.Println(string(b))

    res, err := http.Post("http://localhost:8080/todo", "application/json", bytes.NewReader(b))
    if err != nil {
        panic(err)
    }
    defer res.Body.Close()

    b, err = ioutil.ReadAll(res.Body)
    if err != nil {
        panic(err)
    }

    fmt.Println(string(b))
}

func del() {
    if len(os.Args) < 3 {
        fmt.Print("usage: todo delete TASK_ID")
        return
    }

    id, err := strconv.Atoi(os.Args[2])
    if err != nil {
        fmt.Println("TASK_ID should be number")
        return
    }

    b, err := json.Marshal(map[string]int{"id": id})
    if err != nil {
        panic(err)
    }

    req, err := http.NewRequest(http.MethodDelete, "http://localhost:8080/todo", bytes.NewReader(b))
    if err != nil {
        panic(err)
    }

    client := &http.Client{}
    res, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    res.Body.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过子命令、`samples`、`all`、`add`和`delete`调用所有 API 的端点。

```
$ go run requests/requests.go

usage: todo COMMAND

Commands:
  samples     Get sample todo tasks
  all         Get all todo tasks
  add         Add new todo task
  delete      Remove a todo task 
```

Enter fullscreen mode Exit fullscreen mode

例如，在`add $TASK_NAME $NOTE`之前添加一个新任务，在`all`之前获得所有任务。

```
$  go  run  requests/requests.go  add  Task1  'this  is  the  first  task.'  $  go  run  requests/requests.go  all  |  jq  [  {  "id":  1,  "title":  "Task1",  "note":  "this is the first task.",  "due_date":  "2000-01-01T00:00:00Z"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

[上一个](https://dev.to/cohhei/3-connect-db-and-execute-queries-2aa8)