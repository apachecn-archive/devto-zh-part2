# 快速教程:写 Go，跑 WASM！

> 原文：<https://dev.to/cia_rana/quick-tutorial-write-go-run-wasm-2ilf>

在 Go 1.11 发布之前，它为 WebAssembly(简称 WASM)引入了一个实验端口，我将演示如何将 Go 源代码编译成 WASM 并在浏览器中执行！

以下是步骤:

1.  设置`go1.11beta3`
2.  编译去 WASM
3.  使用 HTML 和 JavaScript 加载 WASM
4.  启动 web 服务器
5.  打开网站！

### 第一步:设置`go1.11beta3`

尽管我们需要仍处于测试阶段的 Go 版本 1.11(预计在 8 月推出！)，因为 Go 是开源的，所以我们可以下载最新的测试版。

[https://github.com/golang/go/releases/tag/go1.11beta3](https://github.com/golang/go/releases/tag/go1.11beta3)T2】

```
$ go get golang.org/dl/go1.11beta3
$ go1.11beta3 download
# => Success. You may now run 'go1.11beta3' 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:编译去 WASM

我们现在可以写 Go 了！计划是打印经典的`hello, Go/WASM!`,但是是在浏览器中——在开发者控制台中。

```
// main.go
package main

import "fmt"

func main() {
    fmt.Println("hello, Go/WASM!")
} 
```

Enter fullscreen mode Exit fullscreen mode

为了从 Go to WASM 编译我们的程序，我们使用命令`go build`。

**注意:**我们需要告诉 Go 为 web 而不是默认环境编译；我们的电脑。所以我们需要设置环境变量`GOOS=js`和`GOARCH=wasm`来定位网络。

```
$ GOOS=js GOARCH=wasm go1.11beta3 build -o test.wasm main.go 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:使用 HTML 和 JavaScript 加载 WASM

这一步并不难；Go 团队准备了一些文件供我们试验。

```
# -s == silent mode
# -O == output mode
$ curl -sO https://raw.githubusercontent.com/golang/go/master/misc/wasm/wasm_exec.html
$ curl -sO https://raw.githubusercontent.com/golang/go/master/misc/wasm/wasm_exec.js 
```

Enter fullscreen mode Exit fullscreen mode

### 第四步:启动网络服务器

现在我们需要启动一个简单的 web 服务器来服务一些静态资产。这只是几行代码:

```
// server.go
package main

import (
    "flag"
    "log"
    "net/http"
)

func main() {
    port := flag.String("port", "8000", "port")
    flag.Parse()
    log.Fatal(http.ListenAndServe(":"+*port, http.FileServer(http.Dir("."))))
} 
```

Enter fullscreen mode Exit fullscreen mode

开始吧！干杯！

```
$ go1.11beta3 run server.go

# or to run on a different port:
# $ go1.11beta3 run -port=8080 server.go 
```

Enter fullscreen mode Exit fullscreen mode

### 第五步:打开网站！

打开`localhost:8000/wasm_exec.html`！

**注意:**在编译后的 WASM 加载之前,“运行”按钮(在左上角)将被禁用。

启用“运行”按钮后，打开控制台并按下按钮！

参见`hello, Go/WASM!`？？

恭喜你！🎉

您刚刚通过 WASM 编译、加载并运行了一个 Go 程序！

这是真的吗？

为什么不在`test.wasm`的响应头中确认`Content-Type`是`application/wasm`？；)

### 鸣谢

谢谢你 [ZAYDEK](https://twitter.com/username_ZAYDEK) 推荐我把[我的文章](https://qiita.com/cia_rana/items/bbb4112b480636ab9d87)翻译成英文并评论这篇文章。

### 参考文献

*   [サクッと围棋→网页汇编を試す(Original 日文版)](https://qiita.com/cia_rana/items/bbb4112b480636ab9d87)
*   [Go 和 wasm:使用 Go - Gopher Academy 博客社区提供的 Go 文章和教程生成和执行 wasm](https://blog.gopheracademy.com/advent-2017/go-wasm/)