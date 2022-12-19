# 一种面向 Go 的错误包装策略

> 原文：<https://dev.to/chuck_ha/an-error-wrapping-strategy-for-go-14i1>

这个输出看起来熟悉吗？

```
error running program: error parsing yaml: error opening file: error no such file "somefile.yaml" 
```

Enter fullscreen mode Exit fullscreen mode

以上是您可能遇到的伪堆栈跟踪。这比仅仅将`error no such file "somefile.yaml"`作为您的错误要好，因为这至少给出了一点关于错误来源的上下文。但并不伟大。很难追踪代码，读起来也不太好看。产生这个错误的代码可能是这样的:

```
func main() {
    if err := run(); err != nil {
        fmt.Printf("error running program: %v\n", err)
    }
}

func run() error {
    if err := parseYAML("somefile.yaml"); err != nil {
        return fmt.Errorf("error parsing yaml: %v\n", err)
    }
    return nil
}

func parseYAML(file string) error {
    _, err := os.Open(file)
    if err != nil {
        return fmt.Errorf("error opening file: %v", err)
    }
    return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

[用上面的代码](https://play.golang.org/p/YAclBp1ebMQ)链接到 go 游乐场。

这种方法不仅容易出现程序员错误，而且在没有行号和文件信息的情况下跟踪代码仍然很痛苦。对这段代码可以做的改进是记录堆栈跟踪！

经过这次改进后，上面的代码看起来应该更像这样:

```
func main() {
    if err := run(); err != nil {
        fmt.Printf("encountered an error: %v\n", err)
        if stackerr, ok := err.(StackTrace); ok {
            fmt.Println(string(stackerr.StackTrace()))
        }
    }
}

func run() error {
    return parseYAML("somefile.yaml")
}

// A local error type that holds a stack trace
type Error struct {
    Err   error
    Stack []byte
}
func (e *Error) Error() string {
    return e.Err.Error()
}
func (e *Error) StackTrace() []byte {
    return e.Stack
}

type StackTrace interface {
    StackTrace() []byte
}

func parseYAML(file string) error {
    _, err := os.Open(file)
    if err != nil {
        return &Error{
            // keep the original error that was found
            Err: err,
            // Modify it with the stack trace
            Stack: debug.Stack(),
        }
    }
    return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

[用上面的代码链接到 go 游乐场](https://play.golang.org/p/Rg66y0QBCc8)

现在，输出准确地指定了发生了什么错误以及错误发生在哪里。

这种重构的规则非常简单:

1.  在程序的边缘包装错误。
2.  在顶层打印堆栈。

### 规则 1

从技术上讲，这条规则应该是“包装任何还没有堆栈跟踪的错误”，但是因为不能保证库会将堆栈跟踪添加到它们返回的错误中，所以更容易假设没有库会添加堆栈跟踪，并且总是添加堆栈跟踪。标准库也不例外。

### 规则二

打印或记录堆栈非常重要，尤其是当生成堆栈的系统/环境不可访问时。在开源世界中，提交者复制并粘贴他们运行的命令的输出和他们遇到的错误是很常见的问题。如果您的错误没有堆栈跟踪，那么在相同的情况下，希望有人能够异步重新运行您的程序或自定义二进制文件，或者使用特殊的标志。有些人会这样做，这很好，但是如果每一期都有这样的信息就更好了。

如果你正在寻找一个为你做这件事的图书馆，看看 https://github.com/pkg/errors 图书馆就知道了。