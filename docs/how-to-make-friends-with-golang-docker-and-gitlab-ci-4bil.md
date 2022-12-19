# 如何和 Golang，Docker，GitLab CI 交朋友

> 原文：<https://dev.to/hypnoglow/how-to-make-friends-with-golang-docker-and-gitlab-ci-4bil>

让我们从演示我们将使用的简单应用程序开始:

```
package main

import (
    "log"
    "net/http"

    "github.com/gorilla/mux"
)

var port = "8080"

func main() {
    log.Fatal(http.ListenAndServe(":"+port, router()))
}

func router() http.Handler {
    r := mux.NewRouter()
    r.Path("/greeting").Methods(http.MethodGet).HandlerFunc(greet)
    return r
}

func greet(w http.ResponseWriter, req *http.Request) {
    _, _ = w.Write([]byte("Hello, world!"))
} 
```

还有，再加一个小测试:

```
package main

import (
    "net/http"
    "net/http/httptest"
    "testing"
)

func TestRouter(t *testing.T) {
    w := httptest.NewRecorder()
    req := httptest.NewRequest(http.MethodGet, "/greeting", nil)
    router().ServeHTTP(w, req)

    expected := "Hello, world!"
    actual := w.Body.String()
    if expected != actual {
        t.Fatalf("Expected %s but got %s", expected, actual)
    }
} 
```

这很简单。我想强调的一点是，我们有一个外部依赖。为了管理它(以及未来的依赖项)，我们将使用 [dep](https://golang.github.io/dep/) :

```
dep init -v -no-examples 
```

为了显示一切正常，我们在本地运行测试、构建和测试我们的应用:

```
$ go test ./...
ok      gitlab.com/hypnoglow/example-go-docker-gitlab   0.016s

$ go build -o app .
$ ./app

# and in another terminal:
$ curl http://localhost:8080/greeting
Hello, world! 
```

似乎很简单。现在，我们想对我们的应用程序进行 dockerize。

问:我们如何将依赖关系传递给 docker 构建过程？

*   选项 1:使用`COPY`命令传递`vendor`目录和源代码。

*   选项 2:使用`RUN dep ensure`在构建时安装依赖项。

第一个选项比第二个选项有一些好处:

*   我们(通常)已经在本地安装了我们的供应商，为什么还要在构建时再安装他们呢？这样我们可以加快`docker build`过程。
*   在私有依赖的情况下，比如您的包在另一个私有存储库中，您需要将 git 凭证传递给 docker 构建过程，以使 go 工具能够获取这些依赖。在第一个选项中，你没有这样的问题。

因此，我们创建了以下 Dockerfile:

```
FROM golang:1.10-alpine3.7 as build

WORKDIR /go/src/app

COPY . .

RUN go build -o app

FROM alpine:3.7

COPY --from=build /go/src/app/app /usr/local/bin/app

ENTRYPOINT ["/usr/local/bin/app"] 
```

接下来，我们需要一个`.gitlab-ci.yml`文件来运行测试并构建我们的映像。我们将如何完成这些任务？

嗯，我们需要一个作业来安装依赖项，因为上面我们决定不在 Dockerfile 中安装它们。即使我们有，我们也需要安装它们来运行我们的测试。因此，我们创建一个作业`dep`来安装依赖项，并将`vendor`目录存储为 GitLab 工件。在其他作业中，我们添加`dep`作业作为依赖项，GitLab 会将之前存储的`vendor`提取到我们的项目目录中。

```
variables:
  PACKAGE_PATH: /go/src/gitlab.com/hypnoglow/example-go-docker-gitlab

stages:
  - dep
  - test
  - build

# A hack to make Golang-in-Gitlab happy
.anchors:
  - &inject-gopath
      mkdir -p $(dirname ${PACKAGE_PATH})
      && ln -s ${CI_PROJECT_DIR} ${PACKAGE_PATH}
      && cd ${PACKAGE_PATH}

dep:
  stage: dep
  image: golang:1.10-alpine3.7
  before_script:
    - apk add --no-cache curl git
    - curl -sSL https://github.com/golang/dep/releases/download/v0.5.0/dep-linux-amd64 -o /go/bin/dep
    - chmod +x /go/bin/dep
    - *inject-gopath
  script:
    - dep ensure -v -vendor-only
  artifacts:
    name: "vendor-$CI_PIPELINE_ID"
    paths:
      - vendor/
    expire_in: 1 hour

test:
  stage: test
  dependencies:
    - dep
  image: golang:1.10-alpine3.7
  before_script:
    - *inject-gopath
  script:
    - go test ./...

build:
  stage: build
  dependencies:
    - dep
  image: docker:17
  services:
    - docker:dind
  script:
    - docker build -t app . 
```

最后，我们检查我们的管道:

[![pipeline](../Images/b8f1c77e2a911c38b7721cf427298064.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vU8kr-xC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/waox28fc4zv11x5fpazn.png)

我们完了！接下来的步骤，比如将构建的图像推送到 docker 注册表，留给读者做练习。🙂

完整的例子可以在 [GitLab 资源库](https://gitlab.com/hypnoglow/example-go-docker-gitlab)中找到。

谢谢！这是我第一篇关于**开发到**的文章，希望你喜欢。

我为任何语法和语言错误道歉，因为英语不是我的母语。如果你发现一个问题，请在评论中修正我！🤓