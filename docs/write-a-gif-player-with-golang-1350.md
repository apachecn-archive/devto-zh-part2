# 用 Golang 写一个 GIF 播放器

> 原文：<https://dev.to/ntoooop/write-a-gif-player-with-golang-1350>

我刚刚用 golang 写了一个 gif 播放器(不到 200 行代码)。我经常使用 MAC，但它不能像 windows 一样播放 gif，所以这个项目应运而生。

这是一个 Golang 项目，也是一个 korok 项目。核心代码很少:

```
func main() {
    gfile := flag.Arg(0)
    // decode !
    gp, err := Load(gfile)
    if err != nil {
        log.Fatal(err)
    }
    w, h := gp.Size()
    options := korok.Options{
        Title: "Gif Player",
        Width:w,
        Height:h,
    }
    korok.Run(&options, &MainScene{p: gp})
} 
```

Enter fullscreen mode Exit fullscreen mode

Gif 解码使用 Golong 的 gif 模块，ui 使用 korok 引擎的 GUI 系统设置。其实就用一个方法:

```
gui.Image(1, m.img, nil) 
```

Enter fullscreen mode Exit fullscreen mode

这个 API 将在屏幕的(0，0)处绘制一个纹理。

我已经把项目上传到 Github:[https://github.com/ntop001/xgif](https://github.com/ntop001/xgif)

关于 korok:它是 Golang 中基于组件的 2d 游戏引擎，包括一个简洁的 gui 系统。我用它来写一些小工具。参见:[https://korok.io/](https://korok.io/)