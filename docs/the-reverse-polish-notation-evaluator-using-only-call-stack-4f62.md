# 仅使用调用堆栈的逆向波兰符号计算器

> 原文：<https://dev.to/acomagu/the-reverse-polish-notation-evaluator-using-only-call-stack-4f62>

我使用 Go 中的调用栈为 RPN(反向波兰符号)编写了 evaluator。

```
package main

import (
    "bufio"
    "fmt"
    "os"
    "strconv"
)

func main() {
    r := bufio.NewReader(os.Stdin)

    var v []interface{}
    for {
        var s string
        if _, err := fmt.Fscan(r, &s); err != nil {
            break
        }

        var t interface{}
        n, err := strconv.Atoi(s)
        if err == nil {
            t = n
        } else {
            t = []rune(s)[0]
        }

        v = append(v, t)
    }

    calc(v)  // should be nil
}

type f func(int) f

func calc(v []interface{}) f {
    if len(v) == 0 {
        return f(func(n int) f {
            fmt.Println(n)
            return nil
        })
    }

    switch t := v[0].(type) {
    case int:
        return calc(v[1:])(t)
    case rune:
        return c(v[1:], t)
    default:
        panic(t)
    }
}

func c(v []interface{}, t rune) f {
    switch t {
    case '+':
        return op(v, func(a, b int) int {
            return a + b
        })
    case '-':
        return op(v, func(a, b int) int {
            return a - b
        })
    default:
        return op(v, func(a, b int) int {
            return a * b
        })
    }
}

func op(v []interface{}, fn func(int, int) int) f {
    return f(func(b int) f {
        return f(func(a int) f {
            return calc(v)(fn(a, b))
        })
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

它像`1 2 + 3 4 + +`一样输入空格分隔符号，像`10`一样打印。

我猜它和通常解决 RPN 的方法一样(使用真实栈，而不是调用栈)，但是不确定。有意义吗？