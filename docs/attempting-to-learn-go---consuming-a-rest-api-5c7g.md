# 尝试学习围棋——使用 REST API

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g>

最近想了很多关于 REST APIs 的事情。在 SaaS 公司工作似乎几乎可以保证你会接触到一个(或几十个)，所以我认为这是有道理的。由于我一直在编写一些基本代码和练习围棋，我想记下一些笔记和一些代码来继续我的学习围棋“系列”可能会很好。

由于 Go 内置的`net/http`包，开始使用基本的 API 非常容易。实际上，查询远程 API 所需的一切都可以在 Go 标准库中找到。

我们将从标准的围棋开局开始...坚持用`main`作为我们的包名，因为这只是一个例子。我们将导入`fmt`、`io/ioutil`、`net/http`——严格来说，我们不需要`fmt`，但是由于我们并没有真正对 API 响应做任何事情，除了打印它之外，它被包含在内。

```
package  main  import  (  "fmt"  "io/ioutil"  "net/http"  ) 
```

接下来，让我们打开我们的`main`函数并声明我们的 API URL，在这个练习中，我们只需查询`httpbin.org`。将我们的 URL 和“GET”方法传递给`http.NewRequest`会给我们一个合适的`http.Request`对象(如果没有错误发生)，我们可以在下一步中使用它。

```
func  main()  {  APIURL  :=  "https://httpbin.org/get"  req,  err  :=  http.NewRequest(http.MethodGet,  APIURL,  nil)  if  err  !=  nil  {  panic(err)  } 
```

使用`http`我们创建一个默认客户端，这将处理对远程服务器的实际请求。最后，我们将调用`Do()`并实际发出请求，接收回响应或错误。这是 Go，所以我们将检查错误，如果没有错误就继续。

```
 client  :=  http.DefaultClient  resp,  err  :=  client.Do(req)  if  err  !=  nil  {  panic(err)  } 
```

假设我们收到了有效的响应，我们将开始处理它。首先，我们将`defer`关闭响应体，这样我们可以读出数据，一旦周围的函数返回，Go 将简单地关闭它。但是首先，我们将使用`ioutil.ReadAll`读取主体并返回一个字节数组——只要没有错误。

```
 defer  resp.Body.Close()  body,  err  :=  ioutil.ReadAll(resp.Body)  if  err  !=  nil  {  panic(err)  } 
```

最后，让我们将字节数组转换成一个字符串，并将其打印到标准输出。

```
 fmt.Printf("%v",  string(body)) 
```

转眼间。这就是在 Go 中从远程 REST 端点获取数据的全部内容。

```
{  "args":  {},  "headers":  {  "Accept-Encoding":  "gzip",  "Connection":  "close",  "Host":  "httpbin.org",  "User-Agent":  "Go-http-client/1.1"  },  "origin":  "68.211.xx.xx",  "url":  "https://httpbin.org/get"  } 
```

下次我想我会把它扩展成一个模块，我们可以用它来做未来的 REST 工作。从那以后，我们可能会写一些实际使用被消费的 API 的东西。

* * *

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content p-5" itemprop="text">

# 尝试学习围棋

在这里你可以找到我为尝试学习围棋而写的代码，这些代码是我在 [Dev.to](https://dev.to/shindakun) 上写的。

## 帖子索引

| 邮政 | 密码 |
| --- | --- |
| [制作下载器第 01 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl) | - |
| [制作下载器第 02 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i) | - |
| [制作下载器第 03 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214) | - |
| [制作下载器第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9) | - |
| [建造下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
| [使用 REST API](https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-01/main.go) |
| [继续休息冒险](https://dev.to/shindakun/attempting-to-learn-go---continuing-rest-adventures-2l4l) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-02/main.go) |
| [现在发送休息请求](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-03/main.go) |
| [REST API 和模板上的位](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-04/main.go) |
| [再次通过 API 发送电子邮件](https://dev.to/shindakun/attempting-to-learn-go---sending-email-via-api-again-2e4e) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-05/main.go) |
| [让我们模块化吧！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i) | [src](https://github.com/shindakun/mailgunner) |
| [让我们再一次模块化！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular---again-10cd) | [src](https://github.com/shindakun/mailgunner) |
| [构建开发日志第 1 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-01-1c3m) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-01/main.go) |
| [构建开发日志第 2 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-02-179c) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-02/main.go) |
| [构建开发日志第 3 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-03-7lk) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-03/main.go) |
| [构建开发日志第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-04-2bok) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-04/main.go) |
| [构建开发日志第 5 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-05/main.go) |
| [按扩展名 01 列出文件](https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [按扩展名 02 列出文件](https://dev.to/shindakun/attempting-to-learn-go---sorting-and-moving-files-by-extension-227j) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [开发至 API 01](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devtoapi-01/main.go) |
| [开发至 API 02](https://dev.to/shindakun/interacting-with-the-devto-article-api---again-sort-of-2o8g) | 参见上面的代码 |

</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *