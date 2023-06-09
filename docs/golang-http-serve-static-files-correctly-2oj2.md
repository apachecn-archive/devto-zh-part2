# Golang: HTTP 正确地服务静态文件

> 原文：<https://dev.to/hauxe/golang-http-serve-static-files-correctly-2oj2>

[![Demo](img/a8c7b2fb070142941ba0792075eec9ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--foDTYi31--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1cta07w4u7jyd1jcw2zc.png)

#### 轻松的方式

在 Golang 中处理静态文件非常简单，只需一行代码:

```
http.Handle("/", http.StripPrefix(strings.TrimRight(path, "/"), http.FileServer(http.Dir(directory)))) 
```

其中路径是 *http* 路径，而*目录*是您想要通过 http 提供服务的静态目录

但是，有一个问题，通过访问根 url，您也可以将您的目录结构公开=_=:

[![](img/79896c9ff84fe115390c4288bc06229c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BsFkv-0t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AY9U0dNsQcUroz7rp7uSVpA.png) 
*清单目录*

代码为:
[https://gist . github . com/ha uxe/09cd 680 deb 9 c 8 C4 e 36d 61568 db 57647 b](https://gist.github.com/hauxe/09cd680deb9c8c4e36d61568db57647b)

我们需要做些什么来防止这种危险

首先不要使用默认的 http 文件服务器，我们将创建我们的自定义 HTTP 文件服务器并拒绝访问目录路径的请求:
[https://gist . github . com/ha uxe/f88 a87 f 4037 BCA 23 f 04 f 6d 100 f 6 e08d 4 # File-HTTP _ static _ custom _ HTTP _ server-go](https://gist.github.com/hauxe/f88a87f4037bca23f04f6d100f6e08d4#file-http_static_custom_http_server-go)

我选择的自定义规则是:如果访问一个目录，并且该目录包含 index.html，则返回它，否则返回错误

用这个自定义结构注册第二个 HTTP 文件服务器:

```
fileServer := http.FileServer(FileSystem{http.Dir(directory)})

http.Handle("/", http.StripPrefix(strings.TrimRight(path, "/"), fileServer)) 
```

如果您再次访问它:

[![](img/2cb372e7cee1b28acadbb03f8f3ba8aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SAkbQ61Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A_C9zn45efpxTCSDbnxyBlQ.png) 
*成功阻止了*

将一个**index.html**文件放入 statics 文件夹，并使用新代码再次运行示例:

[![](img/ce596a96cb262bc2c4589671265c5202.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AtYZPA-a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2At6Lf19ENgl0YoFLBCOCEZQ.png) 
*后放 index.html*

完整工作代码:
[https://gist . github . com/ha uxe/f2ea 1901216177 CCF 9550 a1 b 8 BD 59178 # file-http _ static _ correct-go](https://gist.github.com/hauxe/f2ea1901216177ccf9550a1b8bd59178#file-http_static_correct-go)

* * *

为此我做了一个 [Repo](https://github.com/hauxe/GoM/blob/master/http/filesystem_handler.go) ,它返回 http 处理程序