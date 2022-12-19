# 使用 syg 正常关闭服务器

> 原文：<https://dev.to/nasa9084/graceful-shutdown-server-with-syg--334h>

*最初发布于[blob . web-apps . tech](https://blog.web-apps.tech/graceful-shutdown-with-syg)T3】*

使用 [`github.com/nasa9084/syg`](https://github.com/nasa9084/syg) ，可以很容易地将信号映射到回调函数。(c.f. [syg:简单地向 Go](https://dev.to/nasa9084/syg-simply-signal-to-callback-mapping-in-go-24kf) 中的回调映射发送信号)

现在，让我们实现一个 HTTP 服务器，它在捕获 SIGINT 时优雅地关闭。

```
package app

import (
    "context"
    "net/http"
    "os"
    "time"

    "github.com/nasa9084/syg"
)

type Server struct {
    server *http.Server
    closed chan struct{}
}

func NewServer() *Server {
    http.HandleFunc("/", longlongHandler)
    return &Server{
        server: &http.Server{
            Addr: ":8080",
        },
        closed: make(chan struct{}),
    }
}

func (s *Server) Run() error {
    // os.Interrupt　= syscall.SIGINT
    cancel := syg.Listen(s.shutdown, os.Interrupt)
    defer cancel()

    err := s.server.ListenAndServe()
    <-s.closed
    return err
}

func (s *Server) shutdown(os.Signal) {
    s.Shutdown(context.Background())
    close(s.closed)
}

func longlongHandler(w http.ResponseWriter, r *http.Request) {
    // a very long process!
    time.Sleep(10 * time.Second)
    w.Write([]byte("hello"))
} 
```

Enter fullscreen mode Exit fullscreen mode

来自 main 的调用:

```
package main

import (
    "log"

    "foo/bar/app" // assume the app package above is in $GOPATH/foo/bar/app
)

func main() {
    s := app.NewServer()
    if err := s.Run(); err != http.ErrServerClosed {
        log.Print(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，你不需要考虑 goroutine、正常关机或信号监听。

检查服务器是否运行良好。

首先，构建并运行服务器:

```
$ go build main.go
$ ./main 
```

Enter fullscreen mode Exit fullscreen mode

然后再启动一个终端，发送一个请求:

```
$ curl localhost:8080
hello 
```

Enter fullscreen mode Exit fullscreen mode

10 秒钟后，返回消息“hello”。

现在，再次发送请求，在返回响应之前，在第一个终端中键入`Ctrl-C`(这意味着 SIGINT)。
服务器会在返回响应后关闭，不会突然关闭。

我们做到了！