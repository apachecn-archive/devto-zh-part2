# 发布了新的 Travis CI API Go 客户端 shuheiktgw/go-travis

> 原文：<https://dev.to/shuheiktgw/released-new-travis-ci-api-go-client-shuheiktgwgo-travis-a11>

我刚刚在 Go 中发布了新的 Travis CI API 客户端，名为 [shuheiktgw/go-travis](https://github.com/shuheiktgw/go-travis) ，下面我来介绍一下我为什么要构建它以及如何使用它。

## 为什么我开始开发 shuheiktgw/go-travis

最初，我只是在 Go 中寻找一个 Travis CI API 的客户端库，以从 CI 环境中进行构建，并找到了 [Ableton/go-travis](https://github.com/Ableton/go-travis) 。不幸的是，这个库有一些问题。

*   它似乎不再被维护了。
*   它使用 API v2，最新版本是 v3。
*   它仅支持有限数量的端点。

简而言之，我在 Go 中发现了 Travis CI API 客户端领域的一个真空，我简直兴奋不已，我可能是第一个填补它的人。

此外，我经常在我的私人项目中使用 Travis CI，所以我认为为他们建立一个 Golang 客户端库可能是一个回报的好方法。

这就是我如何分叉 [Ableton/go-travis](https://github.com/Ableton/go-travis) 并开始开发 [shuheiktgw/go-travis](https://github.com/shuheiktgw/go-travis) 。

## 工作原理

shuheiktgw/go-travis 的工作方式非常简单。它的实现很大程度上受到了 [google/go-github](https://github.com/google/go-github) 的启发(就像 Ableton/go-travis 一样)，所以如果你用过 go-github，你就能立刻明白它是如何工作的。

### 安装

```
$ go get github.com/shuheiktgw/go-travis 
```

### 用法

```
client := travis.NewClient(travis.ApiComUrl, "TravisApiToken")

// List all the builds which belongs to the current user
_, err := client.Builds.List(context.Background(), nil) 
```

`travis.NewClient`有两个参数，`baseUrl`和`travisToken`。对于`baseUrl`，你需要选择`travis.ApiComUrl`或者`travis.ApiOrgUrl`，所以请阅读 [URL 部分](https://github.com/shuheiktgw/go-travis#url)。

`travisToken`可以为空，但之后可能需要通过 GitHub token 进行认证。因此，请查看[认证部分](https://github.com/shuheiktgw/go-travis#authentication)，了解更多关于认证的详细信息。

## 特性

### 支持的功能

*   支持 [Travis CI API v3](https://developer.travis-ci.com/) 的所有端点！🎉

### 不支持的功能

*   目前不支持[急切加载](https://developer.travis-ci.com/eager-loading#eager%20loading)..

就这些了，也谢谢你看帖子！如果你有机会使用 [shuheiktgw/go-travis](https://github.com/shuheiktgw/go-travis) ，我很乐意听到你的反馈。当然，也欢迎向图书馆投稿！