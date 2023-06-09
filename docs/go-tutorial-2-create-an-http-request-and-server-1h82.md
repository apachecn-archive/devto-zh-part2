# Go 教程:2。创建 HTTP 请求和服务器

> 原文：<https://dev.to/cohhei/go-tutorial-2-create-an-http-request-and-server-1h82>

*本文最初发表在 [GitHub](https://github.com/cohhei/go-to-the-handson) 上。*

## `nat/http`包

### 创建请求

```
$ touch request.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// request.go
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    res, err := http.Get("https://api.github.com/users/defunkt")
    if err != nil {
        panic(err)
    }
    defer res.Body.Close()

    body, err := ioutil.ReadAll(res.Body)

    fmt.Println(string(body))
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$  go  run  request.go  |  jq  {  "login":  "defunkt",  "id":  2,  "node_id":  "MDQ6VXNlcjI=",  "avatar_url":  "https://avatars0.githubusercontent.com/u/2?v=4",  "gravatar_id":  "",  "url":  "https://api.github.com/users/defunkt",  "html_url":  "https://github.com/defunkt",  "followers_url":  "https://api.github.com/users/defunkt/followers",  "following_url":  "https://api.github.com/users/defunkt/following{/other_user}",  "gists_url":  "https://api.github.com/users/defunkt/gists{/gist_id}",  "starred_url":  "https://api.github.com/users/defunkt/starred{/owner}{/repo}",  "subscriptions_url":  "https://api.github.com/users/defunkt/subscriptions",  "organizations_url":  "https://api.github.com/users/defunkt/orgs",  "repos_url":  "https://api.github.com/users/defunkt/repos",  "events_url":  "https://api.github.com/users/defunkt/events{/privacy}",  "received_events_url":  "https://api.github.com/users/defunkt/received_events",  "type":  "User",  "site_admin":  true,  "name":  "Chris Wanstrath",  "company":  "@github ",  "blog":  "http://chriswanstrath.com/",  "location":  "San Francisco",  "email":  null,  "hireable":  true,  "bio":  "🍔 ",  "public_repos":  107,  "public_gists":  273,  "followers":  20213,  "following":  210,  "created_at":  "2007-10-20T05:24:19Z",  "updated_at":  "2018-06-05T19:29:51Z"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 设置服务器

```
$ touch server.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// server.go
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, %q", r.URL.Path)
    })

    fmt.Println("http://localhost:8080")
    log.Fatal(http.ListenAndServe(":8080", nil))
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run server.go
http://localhost:8080
...

$ curl http://localhost:8080
Hello, "/" 
```

Enter fullscreen mode Exit fullscreen mode

## `json`包

```
$ touch json.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// json.go
package main

import (
    "encoding/json"
    "fmt"
)

func main() {
    str := []byte(`{"id":1,"name":"Gopher"}`)
    data := struct {
        ID   int    `json:"id"`
        Name string `json:"name"`
    }{}

    if err := json.Unmarshal(str, &data); err != nil {
        panic(data)
    }
    fmt.Println("ID: ", data.ID, "Name: ", data.Name)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run json.go
ID:  1 Name:  Gopher 
```

Enter fullscreen mode Exit fullscreen mode

## 练习 2-1

创建一个应用程序`request.go`，它创建一个对[https://api.github.com/users/defunkt](https://api.github.com/users/defunkt)的`GET`请求，并解析只有`json.Unmarshal``login`、`id`、`site_admin`和`bio`的响应体。

```
$ go run request.go
Login:  defunkt
ID:  2
SiteAdmin:  true Bio:  🍔 
```

Enter fullscreen mode Exit fullscreen mode

答案是 [request.go](https://github.com/cohhei/go-to-the-handson/blob/master/02/main/request.go)

## 练习 2-2

创建一个构建服务器的应用程序`server.go`。它有三个端点，`/`、`/json`和`/404`。`/`归来`Hello, "/"`。`/json`返回`{"id":1,"name":"my_name","mail_address":""}`。`/404`返回带有 404 状态代码的`Not Found, /404`。

```
$ go run server.go
http://localhost:8080

$ curl http://localhost:8080/
Hello, "/"

$ curl http://localhost:8080/json
{"id":1,"name":"my_name","mail_address":""}

$ curl http://localhost:8080/404
Not Found, /404 
```

Enter fullscreen mode Exit fullscreen mode

答案是 [server.go](https://github.com/cohhei/go-to-the-handson/blob/master/02/main/server.go) 。

[上一张](https://dev.to/cohhei/go-tutorial-1-command-line-arguments-and-file-io-3l9n) | [下一张](https://dev.to/cohhei/3-connect-db-and-execute-queries-2aa8)