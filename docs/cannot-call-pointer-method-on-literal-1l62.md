# 无法对文本调用指针方法

> 原文：<https://dev.to/hitochan777/cannot-call-pointer-method-on-literal-1l62>

我试图在结构文本上调用指针方法。

```
package main

import "fmt"

type Greeter struct {
        name string
}

func (h *Greeter) hello() {
        fmt.Printf("hello %s!\n", h.name)
}

func main() {
        (Greeter{"hitoshi"}).hello()
} 
```

Enter fullscreen mode Exit fullscreen mode

但是它失败了，并显示以下消息。

```
./hoge.go:14:22: cannot call pointer method on Greeter literal
./hoge.go:14:22: cannot take the address of Greeter literal 
```

Enter fullscreen mode Exit fullscreen mode

应用& operator 使它工作。根据 stackoverflow post 的说法，这是因为文字没有地址。

我的问题是，当我们对一个字面量应用& operator 时，是否在内存中创建了该字面量的一个实例，并因此分配了一个地址？