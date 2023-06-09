# 错误就是价值

> 原文：<https://dev.to/dannypsnl/error-is-value-23f1>

我想大多数 Gopher 都读过[错误处理和运行](https://blog.golang.org/error-handling-and-go)

有人看过[去抬](https://www.youtube.com/watch?v=1B71SL6Y0kA)吗？

让我们从**开始，去抬**！

**Go Lift** 的要点是:误差就是值。

当然，我们知道这个事实。但你真的明白这意味着什么吗？

在 **Go Lift** 中，**约翰·肉桂和**提到了一个关于通过命令执行程序包装错误的技巧。

例如，我们通过 TCP 创建一个到`server:6666`的连接。

```
conn := net.Dial("tcp", "server:6666") 
```

Enter fullscreen mode Exit fullscreen mode

可以吗？啊…，不！

正确的代码是

```
conn, err := net.Dial("tcp", "server:6666")
if err != nil {
    panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们写一些东西到连接上。

```
nBtye := conn.Write([]byte{`command`}) 
```

Enter fullscreen mode Exit fullscreen mode

我们希望如此，但真正的代码是

```
nBtye, err := conn.Write([]byte{`command`})
if err != nil {
    panic(err)
}
// using nByte 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们从`server:6666`中读取一些内容，因此我们创建了一个阅读器。

```
reader := bufio.NewReader(conn)
response := reader.ReadString('\n') 
```

Enter fullscreen mode Exit fullscreen mode

不要！我们必须处理这个错误。

```
response, err := reader.ReadString('\n')
if err != nil {
    panic(err)
}
// using response 
```

Enter fullscreen mode Exit fullscreen mode

但是事情还没有结束，如果我们必须重写命令，如果响应告诉我们命令失败？

如果我们在为一个服务器工作，我们不能惊慌失措吗？

所以**去抬**有一个解决办法:

```
func newSafeConn(network, host string) *safeConn {
    conn, err := net.Dail(network, host)
    return &safeConn{
        err: err,
        conn: conn, // It's fine even conn is nil
    }
}

type safeConn struct {
    err error

    conn net.Conn
}

func (conn *safeConn) Write(bs []byte) {
    if conn.err != nil {
    // if contains error, do nothing
        return
    }
    _, err := conn.Write(bs)
    conn.err = err // update error
}

func (conn *safeConn) ReadString(delim byte) string {
    if conn.err != nil {
        return ""
    }
    reader := bufio.NewReader(conn.conn)
    response, err := reader.ReadString("\n")
    conn.err = err
    return response
} 
```

Enter fullscreen mode Exit fullscreen mode

那么用法将变成

```
conn := newSafeConn("tcp", "server:6666")
conn.Write([]byte{`command`})
response := conn.ReadString('\n')

if conn.err != nil {
    panic(conn.err)
}
// else, do following logic 
```

Enter fullscreen mode Exit fullscreen mode

但是我们能做得更多吗？

是啊！我们可以有一个错误包装器来执行任务。

```
type ErrorWrapper struct {
    err error
}

func (wrapper *ErrorWrapper) Then(task func() error) *ErrorWrapper {
    if wrapper.err == nil {
        wrapper.err = task()
    }
    return wrapper
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以把你想要的任何东西放进去。

```
w := &ErrorWrapper{err: nil}
var conn net.Conn
w.Then(func() error {
    conn, err := net.Dial("tcp", "server:6666")
    return err
}).Then(func() error {
    _, err := conn.Write([]byte{`command`})
}) 
```

Enter fullscreen mode Exit fullscreen mode

等等！但是我们需要在没有外部作用域变量的情况下将连接发送到下一个任务。但是怎么做呢？

现在让我们进入`reflect`魔法。

```
type ErrorWrapper struct {
    err error
    prevReturns []reflect.Value
}

func NewErrorWrapper(vs ...interface{}) *ErrorWrapper {
    args := make([]reflect.Value, 0)
    for _, v := range vs {
        args = append(args, reflect.ValueOf(v))
    }
    return &ErrorWrapper{
        err: nil,
        prevReturns: args,
    }
}

func (w *ErrorWrapper) Then(task interface{}) *ErrorWrapper {
    rTask := reflect.TypeOf(task)
    if rTask.NumOut() < 1 {
        panic("at least return error at the end")
    }
    if w.err == nil {
        lenOfReturn := rTask.NumOut()
        vTask := reflect.ValueOf(task)
        res := vTask.Call(w.prevReturns)
        if res[lenOfReturn-1].Interface() != nil {
            w.err = res[lenOfReturn-1].Interface().(error)
        }
        w.prevReturns = res[:lenOfReturn-1]
    }
    return w
}

func (w *ErrorWrapper) Final(catch func(error)) {
    if w.err != nil {
        catch(w.err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们像
一样编码

```
w := NewErrorWrapper("tcp", "server:6666")

w.Then(func(network, host string) (net.Conn, error) {
    conn, err := net.Dail(network, host)
    return conn, err
}).Then(func(conn net.Conn) error {
    _, err := conn.Write([]byte{`command`})
    return err
}).Final(func(e error) {
    panic(e)
}) 
```

Enter fullscreen mode Exit fullscreen mode