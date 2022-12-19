# Clojure web 开发:什么是环？

> 原文：<https://dev.to/swlkr/clojure-web-development-whats-a-ring-3dme>

Clojure web 开发可能是一个相当疯狂的地方，有相当多的方法来处理 http 请求并将数据放入数据库，但为了保持简单，并无耻地自我推销我的全栈框架 [coast](http://coastonclojure.com) ，我将使用 coast 向您展示这些疯狂的 Clojure 人到底发生了什么。

首先，让我们从表示 http 请求的标准可互换库开始: [ring](https://github.com/ring-clojure/ring) 。

下面是 clojure 上安装了 coast 的准系统 http 服务器的样子:

```
(ns  your-project.core  (:require  [coast.core  :as  coast]))  (defn  home  [request]  (coast/ok  "You're coasting on clojure!"))  (def  app  (->  (coast/get  "/"  home)  (coast/wrap-coast-defaults)))  (coast/start-server  app  {:port  1337})) 
```

clojure 中基于环的 http 服务器与其他语言的区别在于，本例中的 web 应用程序`app`可以独立于服务器运行和测试，就像这个

```
(ns  your-project.core  (:require  [coast.core  :as  coast]))  (defn  home  [request]  (coast/ok  "You're coasting on clojure!"))  (def  app  (->  (coast/get  "/"  home)  (coast/wrap-coast-defaults)))  (app  {:request-method  :get  :uri  "/"})  ; => "You're coasting on clojure!" 
```

只是一个功能，你可以在不运行服务器的情况下测试它！对我来说，这是令人兴奋的，测试变得非常容易，如果你快速看一下戒指请求图，事情会变得更加容易:

```
{  :uri  "/"  ; the url that someone requested  :request-method  :get  ; the requested http method, could be :get, :head, :options,  :put, :post, or :delete  :query-string  {}  ; parsed query string  :headers  {}  ; any headers  :body  {}  ; raw body that can be parsed into whatever you want!  :server-port  1337  ; the port of the request  :scheme  :http  ; http or https  } 
```

那一行是一堆包装好的环中间件，这样你就不必担心每个环中间件以及如何将它们组合在一起以获得一个工作的 web 应用，它基本上给你会话(默认存储在一个 cookie 中...仍在工作)，它解析 url 和主体参数，并向请求映射添加一个`:params`键，就像这个

```
(defn  show-item  [request]  (let  [id  (get-in  request  [:params  :id])]  (coast/ok  (str  "id: "  id))))  (def  app  (->  (coast/get  "/items/:id"  show-item)  (coast/wrap-coast-defaults)))  (app  {:request-method  :get  :uri  "/items/123"})  ; => id: 123 
```

它不仅会匹配参数，还会强制数字、uuids、布尔和向量之类的东西。

* * *

这只是 clojure web 开发的冰山一角，如果你想知道 clojure 是否适合你的下一个 web 应用程序，请查看位于[clojure.org](http://clojure.org)的所有 clojure 资源，位于[clojuredocs.org](http://clojuredocs.org)的文档，关于 coast 的具体内容，你可以随时联系我，并访问 github 上的[coast](https://github.com/swlkr/coast)或 [coast 的官方网站](http://coastonclojure.com)。