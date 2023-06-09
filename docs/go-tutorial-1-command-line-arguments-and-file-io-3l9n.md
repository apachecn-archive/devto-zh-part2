# Go 教程:1。命令行参数和文件 I/O

> 原文：<https://dev.to/cohhei/go-tutorial-1-command-line-arguments-and-file-io-3l9n>

*本文最初发表在 [GitHub](https://github.com/cohhei/go-to-the-handson) 上。*

## 设置

```
$ go get golang.org/x/tools/cmd/goimports
...
$ mkdir go-handson
$ cd go-handson 
```

Enter fullscreen mode Exit fullscreen mode

## 你好世界

```
$ touch hello.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// hello.go
package main

import (
    "fmt"
)

func main() {
    fmt.Println("Hello World!")
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ $GOPATH/bin/goimports -w .
# Format and add packages that should be imported

$ go run hello.go
Hello World!

$ go build -o hello .

$ ./hello
Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

## `flag`包

### `flag.StringVar`的用法

```
$ touch flag.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// flag.go
package main

import (
    "flag"
    "fmt"
)

func main() {
    var name string
    flag.StringVar(&name, "opt", "", "Usage")

    flag.Parse()

    fmt.Println(name)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run hello.go -opt option
option 
```

Enter fullscreen mode Exit fullscreen mode

如果你想了解更多关于`flag`套餐的信息，请前往[https://golang.org/pkg/flag/](https://golang.org/pkg/flag/)

### 练习 1-1

如果没有指定选项，创建一个输出`Hello World!`的 CLI 应用程序。如果一个字符串选项被指定为`-name`，它必须输出`Hello [YOUR_NAME]!`T3

```
$ go run hello.go
Hello World!

$ go run hello.go -name Gopher
Hello Gopher! 
```

Enter fullscreen mode Exit fullscreen mode

答案是 [hello.go](https://github.com/cohhei/go-to-the-handson/blob/master/01/main/hello.go)

## `os`包

### `os.Args`的用法

```
$ touch args.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// args.go
package main

import (
    "fmt"
    "os"
)

func main() {
    fmt.Println(os.Args)
    fmt.Println(os.Args[1])
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go build -o args args.go 
$ ./args Gopher
[./args Gopher]
Gopher 
```

Enter fullscreen mode Exit fullscreen mode

### 文件 I/O

#### 读取文件

```
file, err := os.Open(`/path/to/file`)
if err != nil {
    panic(err)
}
defer file.Close()

buf := make([]byte, BUFSIZE)
for {
    n, err := file.Read(buf)
    if n == 0 {
        break
    }
    if err != nil {
        panic(err)
    }

    fmt.Print(string(buf[:n]))
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 写文件

```
f, err := os.Create("/path/to/file")
if err != nil {
    panic(err)
}
defer f.Close()

b := []byte("Foo")
n, err := f.Write(b)
if err != nil {
    panic(err)
}
fmt.Println(n) 
```

Enter fullscreen mode Exit fullscreen mode

### 练习 1-2

创建一个应用程序`file.go`，它创建一个文件并向其中写入一个字符串`Hello Writing to Files!`。并且文件名必须被指定为命令行参数。

```
$ go run file.go file.txt
The number of bytes written:  23

$ cat file.txt
Hello Writing to Files! 
```

Enter fullscreen mode Exit fullscreen mode

答案是 [file.go](https://github.com/cohhei/go-to-the-handson/blob/master/01/main/file.go)

[下一个](https://dev.to/cohhei/go-tutorial-2-create-an-http-request-and-server-1h82)