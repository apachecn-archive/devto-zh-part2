# 在 Go 中检查不推荐使用的内容

> 原文：<https://dev.to/yyagi/check-deprecated-things-in-go-5eh8>

## 如何弃用函数？

Go 官方建议在想要弃用函数、结构字段的文档中使用`Deprecated:`注释。
[Godoc:记录 Go 代码](https://blog.golang.org/godoc-documenting-go-code)

它在标准图书馆中使用。Ref: [标准库](https://golang.org/search?q=Deprecated:)。

例如`os/file.go`的 [`SEEK_SET`常量](https://golang.org/src/os/file.go?h=Deprecated%3A#L80)，使用注释如下。

```
// Deprecated: Use io.SeekStart, io.SeekCurrent, and io.SeekEnd.
const (
    SEEK_SET int = 0 // seek relative to the origin of the file
    SEEK_CUR int = 1 // seek relative to the current offset
    SEEK_END int = 2 // seek relative to the end
) 
```

所以我们应该在图书馆遵守这条规则。

但如果遵循这个规则，弃用的函数现在显示在 Go doc 中([相关讨论](https://github.com/golang/go/issues/17056))。
此外，golint 不检查关于被否决的评论([相关讨论](https://github.com/golang/lint/issues/238))。
因此，go 官方工具对不赞成的评论没有特定的行为。

## 使用`staticcheck`进行检查

[static check](https://github.com/dominikh/go-tools/tree/master/cmd/staticcheck)[中的](https://github.com/dominikh/go-tools)go-tools可以检查弃用的东西。

`staticcheck`如果使用不推荐使用的东西，报告如下。

```
package main

import (
    "net/http/httputil"
)

func main() {
    httputil.NewClientConn(nil, nil)
} 
```

```
$ staticcheck main.go
deprecated.go:8:2: httputil.NewClientConn is deprecated: Use the Client or Transport in package net/http instead.  (SA1019) 
```

`staticcheck`显示落后于`Deprecated:`的评论。在 CI 中用这个好。

## 结论

*   对不赞成使用的东西使用`Deprecated:`注释。
*   用`staticcheck`勾选弃用的东西。