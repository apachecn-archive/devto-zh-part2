# 让我们构建:‘加载以获得更快的早期 web 开发

> 原文：<https://dev.to/cess11/lets-build-load-to-get-faster-early-web-development-2pmn>

当在一个网站或服务上尝试新东西或做早期工作时，一遍又一遍地重启应用程序服务器会显得缓慢和零碎。

你可以使用一个简单的小技巧，适用于测试应用程序模块或刚刚开始一个项目时，而不是在每次调整时都使用 Ctrl+C 或 D，也可能重新启动 httpGate 或 nginx。

开发 l

```
# libraries for the app server
(load "@lib/http.l" "@lib/xhtml.l" "@lib/form.l") 

(de work () 
    (load "app.l"))

(de go ()
    (server 8080 "!work")) 
```

一旦你有了这个，你就跑，然后忘掉它:

```
$ pil dev.l -go -wait 
```

现在，如果你访问 localhost:8080，它不会引人注目。

在“app.l”中输入一些 GUI 样板文件。

app.l

```
(app) # assuming you want session handling
(action 
    (html 0 "Test bed for savvy gravy" "@lib.css" NIL
        (<h1> NIL "It's alive!"))) 
```

下次访问看起来会更好，当您处理并保存“app.l”并重新加载 localhost:8080 时，您将立即看到所做的更改，而无需重新启动“main.l”。

app.l

```
(app)
(setq *Awesome T)
(action 
    (html 0 "Test bed for savvy gravy" "@lib.css" NIL
        (if *Awesome
            (<h1> 'awesome "Apparently you're awesome!")
            (<h1> 'notsoawesome "Pretty sure you won't see this!") ))) 
```

在“生产”中使用这种技术并不是一个好主意，因为文件 I/O 性能已经很糟糕了，但是与手动一遍又一遍地重新启动相同的脚本，同时允许将“dev.l”中的开发助手函数和全局变量与“app.l”中的应用程序代码分开相比，这将会感觉非常强大。

当然，还有其他方法可以实现这一点或类似的结果，例如，您可以使用协程或后台任务，为更复杂的集成开发和生产管理环境奠定基础。或者不管它叫什么，当你通过 pew-pew 对你的服务器进行编程时:在对象上发送消息，并观察 web 服务从中成长。

下次我们将创建一个小的留言簿应用程序。

Stagling@Patreon 为咖啡捐款，除非你也许，你知道，想雇用我或什么的，那么我相信我们可以解决一些事情。