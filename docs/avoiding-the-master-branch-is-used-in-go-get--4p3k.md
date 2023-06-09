# 避开“主”分支用于“go get”

> 原文：<https://dev.to/yyagi/avoiding-the-master-branch-is-used-in-go-get--4p3k>

`go get`默认检索包的默认分支。这通常使用`master`分支。但是`master`分支有时包含工作进展中的代码，所以要避免使用它。
为了解决这个问题，有一种划分默认分支和开发分支的方法。但是这有点复杂。

解决这个问题的其他方法是使用名为`go1`的分支或标签。
`go get`有一个规则，用于查找与本地安装的 Go 版本相匹配的标记分支。它在文档中描述了。

```
When checking out or updating a package, get looks for a branch or tag that matches the locally installed version of Go. The most important rule is that if the local installation is running version "go1", get searches for a branch or tag named "go1". If no such version exists it retrieves the default branch of the package. 
```

[https://golang . org/cmd/go/# HDR-Download _ and _ install _ packages _ and _ dependencies](https://golang.org/cmd/go/#hdr-Download_and_install_packages_and_dependencies)

这也可以在`get.go`中得到证实。下面是对应部分的代码。

```
// selectTag returns the closest matching tag for a given version.
// Closest means the latest one that is not after the current release.
// Version "goX" (or "goX.Y" or "goX.Y.Z") matches tags of the same form.
// Version "release.rN" matches tags of the form "go.rN" (N being a floating-point number).
// Version "weekly.YYYY-MM-DD" matches tags like "go.weekly.YYYY-MM-DD".
//
// NOTE(rsc): Eventually we will need to decide on some logic here.
// For now, there is only "go1". This matches the docs in go help get.
func selectTag(goVersion string, tags []string) (match string) {
    for _, t := range tags {
        if t == "go1" {
            return "go1"
        }
    }
    return ""
} 
```

[https://github . com/golang/go/blob/fde4b 9 ed 14 e 339 b 5064373 C1 D4 a 73 e 211 EC 32 AC 4/src/cmd/go/internal/get/get . go # L541-L556](https://github.com/golang/go/blob/fde4b9ed14e339b5064373c1d4a73e211ec32ac4/src/cmd/go/internal/get/get.go#L541-L556)

不幸的是，我们现在只能使用焦油或树枝。

但是可以使用`go1`来控制使用的版本。让我们试着使用它。