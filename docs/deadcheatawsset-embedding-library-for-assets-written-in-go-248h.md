# 用 Go 编写的资产的 dead cheat/高脚杯嵌入库

> 原文：<https://dev.to/deadcheat/deadcheatawsset-embedding-library-for-assets-written-in-go-248h>

## 情况

在 Go 中创建单个二进制应用程序需要嵌入资产。

我曾经使用 github.com/jteeuwen/go-bindata,，但 go-bindata 不知何故被删除(网址现在还活着，但原作者不在那里)。

出于这个原因，我考虑将 go-bindata 迁移到 github.com/jessevdk/go-assets。go-assets 还不错，但对我来说有一些问题，如下所示-

*   幂等性，对于 go-assets，我必须创建。使用 go-assets-builder 从资产转到文件。而是生成的。go 文件不是常量，每次执行都有变化。
*   似乎没有得到维护，有一些 PRs 没有得到处理。

所以我创建了我的图书馆。

[github.com/deadcheat/goblet](https://github.com/deadcheat/goblet)

## 如何使用

来产生。从资产转到文件，

*   安装

```
go get -u github.com/deadcheat/goblet/... 
```

Enter fullscreen mode Exit fullscreen mode

*   执行命令(请在此之前将＄GOPATH/bin 设置为 PATH env)

```
goblet %MYASSETDIR%... 
```

Enter fullscreen mode Exit fullscreen mode

*   写一个代码来使用它们，例如，它可以用于 http。文件服务器

```
http.Handle("/static/", http.FileServer(assetsbin.Assets.WithPrefix("/static/")))
log.Println("start server localhost:3000")
if err := http.ListenAndServe(":3000", nil); err != nil {
    log.Fatal(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想改变生成文件中的包名，使用`-p`标志。

它对我的用例来说还不错，但我希望它更好，请告诉我你的想法。

感谢您的阅读。

再见。