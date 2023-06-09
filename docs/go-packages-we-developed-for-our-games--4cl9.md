# 我们为游戏开发的 Go 套装

> 原文：<https://dev.to/hajimehoshi/go-packages-we-developed-for-our-games--4cl9>

我们已经发布了一些围棋游戏，比如[赎罪之钟](http://blockbros.net/tsugunai/)和[快乐蓝鸟](http://blockbros.net/bluebird/)。不幸的是，由于一些商业原因，我们还没有将它们开源。然而，我们现在正在逐步将代码拆分成一些游戏的公共包，以便围棋游戏开发者可以利用它们，围棋游戏开发将会变得更加流行。在这篇文章中，我想列出我们为游戏开发和使用的公共围棋包！

[![](img/e20ce2d7a39dd26eca08ac8d8709d4a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--prx9EpMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ux6sxr77fg5vzv1mjzbg.png)

# [`github.com/hajimehoshi/ebiten`T4】](https://github.com/hajimehoshi/ebiten)

一个非常简单的 2D 游戏库。基本上这是一个 OpenGL 包装。这可以在多种平台上运行，不仅是桌面，还有手机，甚至是 web 浏览器。事实上，我们能够在桌面和浏览器上开发游戏，并在手机上发布。

Ebiten(海老天)的意思是虾的天妇罗，这是我最喜欢的日本食物之一:-)

# [`github.com/hajimehoshi/go-mp3`T4】](https://github.com/hajimehoshi/go-mp3)

一个 MP3 解码器。这是 [PDMP3](https://github.com/technosaurus/PDMP3) 的一个端口，一个公共领域许可的 MP3 解码器。因为我只是在不理解规范的情况下自动移植了它，所以我仍然很难理解我的库中发生了什么:-)

我开发 MP3 解码器而不是 Ogg 等其他解码器的动机是，我们想让游戏在浏览器上工作。现在 [MP3 被大多数现代浏览器](https://caniuse.com/#feat=mp3)支持，但是其他的就不太支持了(比如 [Ogg](https://caniuse.com/#search=ogg) )。我们可以在浏览器上使用浏览器原生解码器，而不是在 Go 中使用 MP3 解码器来提高性能。MP3 曾有专利问题，但专利已于 2017 年到期。

# [`github.com/hajimehoshi/oto`T4】](https://github.com/hajimehoshi/oto)

较低级别的音频库。它的伟大之处在于可移植性:它可以在 Windows、macOS、Linux、Android、iOS 和网络浏览器上运行。这只是提供了一个`io.Writer`，你可以通过向 writer 写入字节来播放任何声音。用`go-mp3`可以用 [`io.Copy`](https://golang.org/pkg/io/#Copy) ( [举例](https://github.com/hajimehoshi/go-mp3/blob/master/example/main.go))播放 MP3 文件！

```
func  run()  error  {  f,  err  :=  os.Open("classic.mp3")  if  err  !=  nil  {  return  err  }  defer  f.Close()  // Decode MP3 stream and the result is an `io.Reader`  d,  err  :=  mp3.NewDecoder(f)  if  err  !=  nil  {  return  err  }  defer  d.Close()  // Create an oto's player, which is an `io.Writer`  p,  err  :=  oto.NewPlayer(d.SampleRate(),  2,  2,  8192)  if  err  !=  nil  {  return  err  }  defer  p.Close()  // With `io.Copy`, you can play the decoded MP3 on your machine!  if  _,  err  :=  io.Copy(p,  d);  err  !=  nil  {  return  err  }  return  nil  } 
```

Oto(音)在日语中是‘声音’的意思。

# [`github.com/hajimehoshi/file2byteslice`T4】](https://github.com/hajimehoshi/file2byteslice)

又一个嵌入二进制到 Go 文件的工具，就像 [go-bindata](https://github.com/jteeuwen/go-bindata) 。与其他类似的软件包相比，非常简单。这只是将一个二进制转换为一个字节的切片文字。就是这样！我开发这个的动机是发生了一个[事件](https://www.reddit.com/r/golang/comments/7vv9zz/popular_lib_gobindata_removed_from_github_or_why/) : go-bindata 的原作者删除了他们的 GitHub 账户，另一个人又使用了同一个 GitHub 账户，我认为这个库不再可信了。因此，我试图自己开发它。

```
Usage of file2byteslice:
  -compress
        use gzip compression
  -input string
        input filename
  -output string
        output filename
  -package string
        package name (default "main")
  -var string
        variable name (default "_") 
```

# [`github.com/hajimehoshi/go-mplusbitmap`T4】](https://github.com/hajimehoshi/go-mplusbitmap)

提供 [`font.Face`](https://godoc.org/golang.org/x/image/font#Face) 对象的包 [M+位图字体](http://mplus-fonts.osdn.jp/mplus-bitmap-fonts/)用于 8/16 bitty 风格的日文文本。`font.Face`是表示字体的半标准接口，Go 中有[TrueType 解析器](https://github.com/golang/freetype)来制作`font.Face`实例。用 [Ebiten 的文字包](https://godoc.org/github.com/hajimehoshi/ebiten/text)可以很轻松的画出文字！当然，这可以用于(半)标准渲染包([示例](https://github.com/hajimehoshi/go-mplusbitmap/blob/master/example/main.go))。

```
const  text  =  `Hello, World!

こんにちは世界!`  func  run()  error  {  const  (  ox  =  16  oy  =  16  )  dst  :=  image.NewRGBA(image.Rect(0,  0,  320,  240))  // Initialize the destination image dst by filling with white.  draw.Draw(dst,  dst.Bounds(),  image.NewUniform(color.White),  image.ZP,  draw.Src)  f  :=  mplusbitmap.Gothic12r  // golang.org/x/image/font's Drawer draws a text on a specified image.  d  :=  font.Drawer{  Dst:  dst,  Src:  image.NewUniform(color.Black),  Face:  f,  Dot:  fixed.P(ox,  oy),  }  for  _,  l  :=  range  strings.Split(text,  "\n")  {  d.DrawString(l)  d.Dot.X  =  fixed.I(ox)  d.Dot.Y  +=  f.Metrics().Height  }  // Now the text is drawn to the image dst.  return  nil  } 
```

# [`github.com/hajimehoshi/chinesegamefonts`T4】](https://github.com/hajimehoshi/chinesegamefonts)

这提供了(压缩的)CJK 字体的 TTF 二进制文件。这个软件包的字体在某些方面不同于原来的诺托 CJK 字体。

*   字体是 TrueType，不是 OpenType，因为到目前为止还没有实际可用的 Go 包来解析 OpenType 字体( [`golang.org/x/image/font/sfnt`正在开发中](https://github.com/golang/go/issues/22451))。
*   许多字形被从原始字体中删除，并且`chinesegamefonts`字体仅包括常见的 CJK 字形以减小文件大小。原来的文件大小是 46.1 MB(简体和繁体都有)，缩小后是 4.4 MB(简体)和 7.6 MB(繁体)！

我们的游戏到目前为止还没有中文支持，但是我们现在用这个字体包实现了。

* * *

除了上述软件包，我们现在正在开发软件包，例如用户界面。敬请期待！