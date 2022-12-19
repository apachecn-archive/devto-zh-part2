# 网站路由变得简单易行

> 原文：<https://dev.to/swlkr/website-routing-made-simple-and-easy-3mn6>

我自欺欺人了很久。我认为自己是一名工程师，我正在开发复杂的十亿用户网络应用程序，我正在设计前端和后端，当然我是。但我没有。

我做网站。

当我终于明白了别人是如何看待我的时候，我开始了网站制作启蒙运动。我停止使用复杂的 js 框架，复杂的后端 api 技术，我退出 graphql，我退出 REST APIs，我退出做愚蠢的事情，我开始完成项目。

我希望你也完成你的项目，第一步是抛弃那些复杂的前端框架，也许拥抱 clojure 的简单。

### 路由究竟是如何变得简单或容易的。这到底是什么意思？

我带你去看。这里有一条单独的路线

```
[:get  "/"  `home] 
```

Enter fullscreen mode Exit fullscreen mode

这里有两条路线

```
[[:get  "/"  `home]  [:get  "/@:name"  `profile]] 
```

Enter fullscreen mode Exit fullscreen mode

这是 clojure
上的一条海岸路线

```
(ns  your-project  (:require  [coast.delta  :as  coast]))  (defn  home  [request]  {:status  200  :headers  {"Content-Type"  "text/html"}  :body  "Hello world!"})  (def  routes  [[:get  "/"  `home]])  (def  app  (coast/app  routes))  (app  {:request-method  :get  :uri  "/"})  ; => Hello world! 
```

Enter fullscreen mode Exit fullscreen mode

coast 中的路线是向量的向量，最上面的路线首先匹配。

```
(defn  hello  [request]  {:status  200  :headers  {"Content-Type"  "text/html"}  :body  "hello world!"})  (defn  goodbye  [request]  {:status  200  :headers  {"Content-Type"  "text/html"}  :body  "goodbye, cruel world!"})  (def  routes  [[:get  "/"  `hello]  [:get  "/"  `goodbye]])  (def  app  (coast/app  routes))  (app  {:request-method  :get  :uri  "/"})  ; => hello world! 
```

Enter fullscreen mode Exit fullscreen mode

如果不想整天写矢量，可以用 coast 的路线函数

```
(ns  routes  (:require  [coast.router  :refer  [get  post  put  delete  wrap-routes]])  (:refer-clojure  :exclude  [get]))  (defn  home  [request]  "welcome!")  (defn  profile  [request]  (str  "hello, "  (get-in  request  [:params  :name])))  (def  routes  (->  (get  "/"  `home)  (get  "/@:name"  `profile)))  (def  app  (coast/app  routes)) 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个更完整的例子，比如和[好友](https://github.com/funcool/buddy)
一起认证

```
(ns  routes  (:require  [coast.router  :refer  [get  post  put  delete  wrap-routes]]  [coast.responses  :as  res]  [controllers.home  :as  c.home]  [controllers.users  :as  c.users]  [buddy.auth])  (:refer-clojure  :exclude  [get]))  (defn  wrap-auth  [handler]  (fn  [request]  (if  (buddy.auth/authenticated?  request)  (handler  request)  (res/forbidden  "I'm sorry dave, I can't let you do that."))))  (def  auth  (->  (get  "/users/:id"  `c.users/show)  (wrap-routes  middleware/wrap-auth)))  (def  public  (get  "/"  `c.home/index))  (def  routes  (concat  public  auth)) 
```

Enter fullscreen mode Exit fullscreen mode

clojure 上的 [coast 中的路由意味着简单和易于理解。希望我给你的只是网站制作的一个方面，而不是复杂的过度设计的前端沉重的网络应用程序可以变得简单和容易。](https://github.com/swlkr/coast)

—

如果你正在捡起我放下的东西，试试 clojure 上的 [coast 吧！](https://github.com/swlkr/coast)