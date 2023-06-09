# 同步。泳池

> 原文：<https://dev.to/hsatac/syncpool-34pd>

官方文件:[[https://godoc.org/sync#Pool](https://godoc.org/sync#Pool)

`sync.Pool`的用法在高并发场景中非常常见。您可能会创建大量的 goroutines，并且每个 goroutines 都会分配短期对象，从而导致缓慢的 GC。

我们可以避免这种情况。在没有任何通知的情况下，在 GC 之后池内的对象将被清理，所以`sync.Pool`不适合连接池。

被`Put()`放回池中的对象可能会被`Get()`并在 GC 发生之前被重用。这意味着你有机会`Get()`一个具有旧价值观的对象。

这里的例子:

```
package main

import (
    "fmt"
    "runtime"
    "runtime/debug"
    "sync"
)

func main() {
    newFunc := func() interface{} {
        return make([]byte, 32)
    }
    pool := sync.Pool{New: newFunc}

    v1 := pool.Get().([]byte)
    fmt.Printf("v1: %v\n", v1)
    v1[0] = 1
    fmt.Printf("modified v1: %v\n", v1)

    // modified v1, put back to pool, before GC
    pool.Put(v1)
    v2 := pool.Get().([]byte)
    fmt.Printf("v2: %v\n", v2)
    pool.Put(v2)

    // After GC
    debug.SetGCPercent(100)
    runtime.GC()
    v3 := pool.Get().([]byte)
    fmt.Printf("v3: %v\n", v3)
} 
```

Enter fullscreen mode Exit fullscreen mode

所以一定要记得在你回来之前清理对象，或者在你回来之后初始化对象，以防止任何意外。