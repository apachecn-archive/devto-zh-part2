# Go Mod 简介

> 原文：<https://dev.to/defman/introducing-go-mod-1cdo>

# 简介

在 Go 1.11 中，一个新的工具已经到来。叫`go mod`。对于那些不知道那是什么的人，我来告诉你。

这个工具的目的之一是去掉`$GOPATH`，这样你就可以在任何地方`git clone`一个 Golang 库并在上面工作，而完全不用担心`$GOPATH`。当然还有其他目的，比如更简单的版本和使用，但是我认为 RIP `$GOPATH`是最值得注意的。

如果存储库是一个`mod`，所有你经常使用的工具(`go get`、`go build`等)。)会很好的处理，不改变任何东西。

# 用法

`go mod init`。是的，这就是你要做的。如果你试图`go mod` -ify 一个使用`$GOPATH`的现有项目，你应该导出`GO111MODULE=on`变量(`go mod`也会告诉你)。

在创建生产标签之前有一个建议:运行`go mod tidy`。这将更新您的`go.mod`,因此每个依赖项(内部的和被依赖项使用的)都将被锁定。

如果你感兴趣，你可以阅读官方维基。它提供了更多关于`go mod`的信息，我只是给了你应该设置好的基础知识。

# IDE 支持

即使不出售 VS 代码中的依赖项，Goto definition 和 auto-completion 也能很好地工作。我认为其他像微软一样支持 Golang 的编辑会很好地处理`go mod`。

# 社区

[@韵脚](https://dev.to/rhymes)也写了一篇关于`go mod`的文章，并报道了他喜欢它的地方。我推荐也读一读！

[![rhymes](../Images/91ed8188209dceb0374b6a8c88308333.png)](/rhymes) [## 去拿模块

### 押韵 Aug 25 ' 182min read

#go](/rhymes/go-gets-modules-12ei)