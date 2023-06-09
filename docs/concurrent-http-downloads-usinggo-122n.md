# 使用 Go 进行并发 HTTP 下载

> 原文：<https://dev.to/dhanushgopinath/concurrent-http-downloads-usinggo-122n>

最近，为了在 Geektrust 中实现一个功能，我们不得不从互联网上下载多个图像并使用它。这必须是一个高效的实现。这篇文章简要解释了使用 goroutines 和 channels 完成的简单实现。

[![Image courtesy — https://hackernoon.com/why-we-love-concurrency-and-you-should-too-c64c2d08a059](img/e96c45bab5a360dc3cdfba20002f4ec4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5kAiqh3q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AqBiZD7mvwkNcIGDrkyZeCQ.jpeg)

Go 有一个 http 包，里面有简单的 API 来完成 HTTP 操作。例如，如果你想下载一个页面，你需要做的就是这个。

```
resp, err := http.Get("http://example.com/")
if err != nil {
    // handle error
}
defer resp.Body.Close()
body, err := ioutil.ReadAll(resp.Body) 
```

Enter fullscreen mode Exit fullscreen mode

下面给出的是从一个公共 URL 下载一个文件并把它作为字节使用的实现。