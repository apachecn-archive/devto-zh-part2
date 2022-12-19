# 使用 Async，Await，加上一点延迟？

> 原文：<https://dev.to/catriname/using-async-await-plus-a-slight-delay-2h7d>

今天的问题是关于我们如何在线查看发票。我们使用 iSeries 上的一个应用程序创建一个 PDF，并将其发送到一个设定的目的地。在我们的例子中，这个目的地是一个普通的 windows 服务器，文件存放在一个小站点:pdf.mycompany.com。

我最初的方法很简单，使用我在 iSeries 上的 PHP API 调用程序——向它传递特定发票的参数，等待响应(它给了我新创建的文件名),然后重定向到那个 URL。该方法看起来像这样:

```
[HttpGet]
public async Task<ActionResult> GetInvoiceAsync(int invoice)
{
    GetInvoice getInvoice = new GetInvoice();
    var client = new HttpClient();

    string fileName = await getInvoice.LoadPDF(invoice);

    string url = "http://pdf.mycompany.com/";
    url += fileName + ".pdf";

    return Redirect(url);
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法非常有效……90%的时候，但是另外 10%的时候，我太快地点击了一张发票，然后被转到了 404。

## 迷茫

LoadPDF 是一个异步方法，位于我的服务文件夹中，上面的方法位于我的控制器中。我假设，因为这是一个 await，那么，GetInvoiceAsync 应该等到服务器完成。那么，为什么我过早地被转发了？它为什么不等呢？

## 真题

GetInvoiceAsync 正在等待，它正在等待响应。

到底发生了什么:

*   LoadPDF 获取数据，并将其序列化。
*   LoadPDF 创建一个 HttpWebRequest 并将数据发送到 iSeries
*   iSeries 用一个文件名来响应
*   做出回应。等待结束
*   用户被转发到新文件
*   *iSeries 仍在通过*复制文件
*   用户获得 404

## 易解

我通过简单地在 LoadPDF 中添加一个小延迟来解决这个问题。因此，它会得到响应，然后再等待 1.5 秒，然后向控制器确认它已完成。下面是解决它的代码:

```
//declared at top of class
CancellationTokenSource source = new CancellationTokenSource();

//iSeries needs time to copy file over to pdf server
//added in LoadPDF just before return(fileName);
await Task.Delay(TimeSpan.FromSeconds(1.5), source.Token); 
```

Enter fullscreen mode Exit fullscreen mode

#### 你知道我处理这件事的更好方法吗？