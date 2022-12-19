# 日积月累:当运行配置不可用时

> 原文：<https://dev.to/fgm/goland-tip-of-the-day-when-make-run-configurations-dont-find-go-5a22>

*   [计算](https://blog.riff.org/topic/computing)
*   [Google Go = Golang](https://blog.riff.org/topic/computing/golang)
*   [戈兰德](https://blog.riff.org/taxonomy/term/242)

### 问题

在 GoLand 2018.3 EAP 运行配置中，Makefile 运行配置会在 macOS 上找到`go`二进制文件，但在 Ubuntu 上找不到，从而导致 make 目标失败:

```
# Makefile
client/test.wasm: client/main.go  # Building WASM
        GOARCH=wasm GOOS=js go build -o client/test.wasm client/main.go

$ /usr/bin/make -f (some edited path)/Makefile
client/test.wasm/bin/sh: 1: go: not found
make: *** [client/test.wasm] Error 127
$ 
```

Enter fullscreen mode Exit fullscreen mode

简单到可以修复...

### 诊断

命令行上，同样的`GOARCH=wasm GOOS=js go build -o client/test.wasm client/main.go`行正常工作。看起来像是 Goland 或者插件的路径问题。正在签入 Makefile:

```
# Makefile
client/test.wasm: client/main.go    
        # "${PATH}" 
        # Building WASM 
        GOARCH=wasm GOOS=js go build -o client/test.wasm client/main.go

$ # Building WASM
make: *** [client/test.wasm] Error 127# "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
$ 
```

Enter fullscreen mode Exit fullscreen mode

所以实际上，至少在这个版本中，没有考虑路径。让我们解决这个问题。

### 解

当然，我们可以将 Go bin 路径添加到这些目标的任务中，但是这在其他环境中是不必要的，所以我们可以提醒 GoLand 将这个路径添加到运行配置中。

1.  编辑运行配置
2.  编辑`Environment variables`字段
3.  从命令行上的`echo $PATH`中为`PATH`变量添加一个定义
4.  应用并关闭

### 怪事

像往常一样，Make 有自己独特的魅力，要求变量名使用上图所示的有括号的格式，而不是普通的`$PATH`。

试图通过添加类似于`$PATH:/usr/local/go/bin`或`${PATH}:/usr/local/go/bin`的东西来扩展路径变量是行不通的:

*   在第一种情况下，Make 将接收评估结果`$P`，然后是`ATH`字符串，因此除了`/usr/local/go/bin`中的二进制文件之外的其他二进制文件将会丢失
*   在第二种情况下，Make 检测引用自身的变量并中止任务