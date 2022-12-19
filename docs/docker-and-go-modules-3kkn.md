# Docker 和 Go 模块

> 原文：<https://dev.to/plutov/docker-and-go-modules-3kkn>

这是一个文本版本的 [packagemain #14: Docker 和 Go 模块](https://youtu.be/RSSwZt1c1Yo)视频。

如你所知，Go 1.11 包含了版本化模块的选择加入功能。在 go 模块之前，Gophers 使用依赖管理器，如`dep`或`glide`，但是使用 go 模块，你不需要第三方管理器，因为它们包含在标准的`go`工具链中。

此外，模块允许废弃 GOPATH，这对于一些 Go 新手来说是一个障碍。

在这个视频中，我将演示如何为你的程序启用 go 模块，然后用 Docker 打包。你会发现这有多简单。

## 创建项目

让我们创建一个简单的 http 服务器，它将使用 logrus 包进行日志记录。

正如我之前所说，go modules 是一个选择加入的特性，可以通过设置环境变量`GO111MODULE=on`来启用。

```
export GO111MODULE=on
mkdir httpserver && cd httpserver
go mod init
go get github.com/sirupsen/logrus 
```

我们的文件夹中创建了两个新文件:go.mod 和 go.sum.

```
package main

import (
    "fmt"
    "net/http"

    log "github.com/sirupsen/logrus"
)

func main() {
    http.Handle("/", loggingMiddleware(http.HandlerFunc(handler)))
    http.ListenAndServe(":8080", nil)
}

func handler(w http.ResponseWriter, req *http.Request) {
    fmt.Fprintf(w, "package main #14")
}

func loggingMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, req *http.Request) {
        log.Infof("uri: %s", req.RequestURI)
        next.ServeHTTP(w, req)
    })
} 
```

现在，如果我们运行`go build`，它将下载去依赖并构建一个二进制:

```
go build
./httpserver 
```

## 用 Docker 打包

让我们为服务器创建一个简单的 docker 文件。

```
FROM golang

ENV GO111MODULE=on

WORKDIR /app

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build

EXPOSE 8080
ENTRYPOINT ["/app/httpserver"] 
```

```
docker build -t httpserver .
docker run -p 8080:8080 httpserver 
```

## 缓存 go 模块

如您所见,`go build`下载了我们的依赖项。但这里不好的是，每次我们建立一个图像，它都会这样做。想象一下，如果你的项目有很多依赖项，它会减慢你的构建过程。让我们在 main.go 文件中更改一些内容，然后再次运行 build。

要解决这个问题，我们可以使用`go mod download`，它将首先下载依赖项。但是如果我们的 go.mod / go.sum 文件被更改，我们应该重新运行它。

我们可以先将 go.mod / go.sum 文件复制到 docker 中，然后运行`go mod download`，再复制其他所有文件，运行`go build`。

```
FROM golang

ENV GO111MODULE=on

WORKDIR /app

COPY go.mod .
COPY go.sum .

RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build

EXPOSE 8080
ENTRYPOINT ["/app/httpserver"] 
```

## 多阶段构建

我喜欢用 Dockerfiles 做的另一件事是使用多阶段构建来减小最终图像的大小。要运行我们的服务器，我们只需要一个二进制文件，我们不需要安装 go，所以在一个 docker 文件中，我们可以首先使用`golang`映像构建程序，然后只从其中复制一个二进制文件。

```
# build stage
FROM golang as builder

ENV GO111MODULE=on

WORKDIR /app

COPY go.mod .
COPY go.sum .

RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build

# final stage
FROM scratch
COPY --from=builder /app/httpserver /app/
EXPOSE 8080
ENTRYPOINT ["/app/httpserver"] 
```

## 结论

所以我认为 go modules 是一个很好的特性，你肯定应该尝试一下，我在我写的所有服务中都使用它。当然，它需要一些改进，但它在实践中运行良好。

[GitHub 上的代码](https://github.com/plutov/packagemain/tree/master/14-go-modules-docker)