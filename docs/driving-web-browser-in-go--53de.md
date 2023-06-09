# 在 Go 中驱动网络浏览器

> 原文：<https://dev.to/ykyuen/driving-web-browser-in-go--53de>

*最初发布在[水手长博客](https://blog.boatswain.io/post/driving-web-browser-in-go/)上。*

* * *

这篇文章是关于在 Golang 中驱动 web 浏览器的。

## 刺鼠

Agouti 是一个验收和测试框架。如果你正在为你的 [Go](https://golang.org/) 项目寻找一个完整的测试解决方案，它可以和[银杏](http://onsi.github.io/ginkgo/)一起使用，后者是一个 BDD 测试框架和 [Gomega](http://onsi.github.io/gomega/) 匹配器/断言库。

下面的例子只利用 [Agouti](https://agouti.org/) 来驱动一个浏览器。

## 在 GOPATH 中创建项目

在*$ GOPATH/src/git lab . com/yk yuen/driving-web-browser-in-go-example*下创建 *main.go* 。

*main . go*T2】

```
package main

import (
  "log"

  "github.com/sclevine/agouti"
)

func main() {
  // driver := agouti.PhantomJS()
  driver := agouti.ChromeDriver()
  // driver := agouti.ChromeDriver(
  //   agouti.ChromeOptions("args", []string{"--headless", "--disable-gpu", "--no-sandbox"}),
  // )

  if err := driver.Start(); err != nil {
    log.Fatal("Failed to start driver:", err)
  }

  page, err := driver.NewPage()
  if err != nil {
    log.Fatal("Failed to open page:", err)
  }

  if err := page.Navigate("https://agouti.org/"); err != nil {
    log.Fatal("Failed to navigate:", err)
  }

  sectionTitle, err := page.FindByID(`getting-agouti`).Text()
  log.Println(sectionTitle)

  if err := driver.Stop(); err != nil {
    log.Fatal("Failed to close pages and stop WebDriver:", err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[Agouti](https://agouti.org/) 支持 [PhantomJS](http://phantomjs.org/) 、 [Selenium](http://www.seleniumhq.org/) 和 [Chrome](https://www.google.com/chrome/) 等 web 驱动。

### 下载 Go 依赖项

让我们使用 [dep](https://github.com/golang/dep) 来管理 [Go](https://golang.org/) 依赖项。只需运行 *dep init* 命令。

```
[ykyuen@camus driving-web-browser-in-go-example]$ dep init
  Using ^2.0.0 as constraint for direct dep github.com/sclevine/agouti
  Locking in v2.0 (b920a9c) for direct dep github.com/sclevine/agouti 
```

Enter fullscreen mode Exit fullscreen mode

在我写这篇文章的时候。 [dep](https://github.com/golang/dep) 默认下载的 [Agouti](https://agouti.org/) 版本太旧。让我们手动更新到最新的主版本。

*戈 pkg.toml*

```
[[constraint]]
  name = "github.com/sclevine/agouti"
  branch = "master" 
```

Enter fullscreen mode Exit fullscreen mode

然后运行 *dep 确保*更新 [Agouti](https://agouti.org/) 版本。

### 运行代码

```
[ykyuen@camus driving-web-browser-in-go-example]$ go run main.go
2018/01/21 19:02:42 Getting Agouti 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的是 ChromeDriver，你应该能看到 [Chrome](https://www.google.com/chrome/) 浏览器会自动启动并执行 *main.go* 中所述的任务。

## 运行在无头浏览器中

我们也可以在无头浏览器中运行代码，比如 [PhantomJS](http://phantomjs.org/) 。 [Chrome](https://www.google.com/chrome/) 也支持无头模式。让我们将代码更新如下。

*main . go*T2】

```
package main

import (
  "log"

  "github.com/sclevine/agouti"
)

func main() {
  // driver := agouti.PhantomJS()
  // driver := agouti.ChromeDriver()
  driver := agouti.ChromeDriver(
    agouti.ChromeOptions("args", []string{"--headless", "--disable-gpu", "--no-sandbox"}),
  )
  ... 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然可以从 Agouti 网站获得标题，而不需要打开 T2 Chrome 窗口。

## 总结

*   这个例子不是关于编写测试用例，而是展示如何驱动 web 浏览器。
*   为了测试，我们可能需要[银杏](http://onsi.github.io/ginkgo/)和[戈美加](http://onsi.github.io/gomega/)。
*   我还尝试了另一个名为 [chromedp](https://github.com/chromedp/chromedp) 的 [Go](https://golang.org/) 软件包，但效果不佳。
    *   [chromedp 用作功能测试工具？](https://github.com/chromedp/chromedp/issues/115)
    *   [用无头 chrome 保存截图会引发“无效图像大小”错误](https://github.com/chromedp/chromedp/issues/167)
    *   [点击在特定环境下不起作用](https://github.com/chromedp/chromedp/issues/70)
*   完整示例可在[gitlab.com](https://gitlab.com/ykyuen/driving-web-browser-in-go-example)获得