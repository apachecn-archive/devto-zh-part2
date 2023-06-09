# Golang 和镜子-快速入门

> 原文：<https://dev.to/sohjsolwin/golang-and-the-mirror-quickstart-299b>

我一直在努力让[镜像-快速启动-运行](https://github.com/googleglass/mirror-quickstart-go)。几个月前，我能够毫无困难地在 appengine 实例上运行它。不过最近，我决定吹走 appengine 上的测试代码，重新开始。不幸的是，这次设置快速入门并不容易。在遵循设置项目、创建客户机 id 和用适当的信息配置项目的常规步骤之后，我部署了它，并陷入了授权重定向循环。我在日志查看器中收到的唯一错误是:

```
Get Token: datastore: no such entity 
```

Enter fullscreen mode Exit fullscreen mode

经过一遍又一遍的检查，我发现所有的 id 都是正确的，于是我想出了一个方法来调试这个项目，并开始追踪这个问题。快速入门只配置了少数几个 URL 来处理。否则它只会返回一个 404 错误，这是应该的。我决定利用这一点，修改 main.go 文件，将所有执行 HTTP 重定向的部分都改为重定向到诸如“/useridempty”之类的页面。

```
**if** userId **==**"" {
 //http.Redirect(w, r, "/auth", http.StatusFound)
 http.Redirect(w, r, "/useridempty", http.StatusFound)
 **return**  **nil**  
} 
```

Enter fullscreen mode Exit fullscreen mode

这使我可以追踪到第 84 行:t := authTransport(c，userId) authTransport 返回 nil。继续讨论 util.go 文件，导致问题的行是第 94 行，特别是 datastore。Get(c，key，tok)部分。事实证明，认证所需的令牌并没有存储在数据库中，由此导致的错误(以及随后的重定向)是准确的。

我又回到了 oauth2callback 方法，因为我知道这个方法至少会被调用，我直观地查看了代码，交叉引用了代码应该执行的操作。这让我想到了 storeCredential 方法。storeCredential 方法(在 auth.go 的第 78 行使用，并在 util.go 的第 83 行声明。storeCredential 方法返回一个错误，但没有检查该错误。我修改了 auth.go 文件中 storeCredential 的用法，返回如下错误:

```
if err = storeCredential(c, userId, tok); err != nil {
 return fmt.Errorf("Unable to store credential: %s", err)
} 
```

Enter fullscreen mode Exit fullscreen mode

这导致以下错误:无法存储凭据:数据存储:不支持的结构字段类型:映射[字符串]字符串

经过一番研究，我发现不再支持在数据存储中存储地图对象。我不确定它是什么时候变得不受支持的，但事实是它显然不会再工作了。oauth2 库中的令牌对象包含一个 map[string]string 类型的“Extras”字段，由于它是存储在数据存储中的令牌对象，这就是它失败的原因。令牌从未被保存，所以当它试图读取它时，它当然不在那里，但是通过演示程序调用 storeCredential 方法的方式，错误被丢弃并悄悄地过去了。

为了修复这个错误，我创建了一个简单的“SimpleToken”结构，它包含与令牌对象相同的签名，只是没有“Extras”字段。我还设置了几个简短的方法来转换 oauth。令牌转换为简单令牌，反之亦然。在 util.go 文件中，我更改了 storeCredential 方法，以接受*SimpleToken 而不是*oauth。令牌和 authTransport 方法来转换 oauth。Token 对象转换为 SimpleToken 对象，然后将其传递给数据存储。获取方法。在 auth.go 文件中，唯一需要更改的是 oauth2callbackHandler 方法。更新了方法以转换 oauth。Token 对象转换为 SimpleToken 对象。

一旦这些变更完成，项目被重新部署到 AppEngine，所有东西都被正确加载并开始工作。这是一个非常恼人的问题，追踪起来也很麻烦，但是一旦找到了根本问题，修复就出奇的简单。一旦我开始清理代码，我将分叉这个项目并发出一个拉取请求，这样任何人都不会遇到同样的问题。我希望 oauth 或 datastore 库能够得到更新，以正确解决 datastore 现在阻止存储地图类型的变化。

*原载于 2013 年 10 月 26 日*[*【blog.sohjsolwin.com】*](http://blog.sohjsolwin.com/2013/10/helpouts-meetups-and-mirror-qucikstart.html)*。*