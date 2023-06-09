# Clojure/ClojureScript Web Dev 生态系统 1 的初学者之旅

> 原文：<https://dev.to/elarouss/a-beginners-journey-inside-the-clojureclojurescript-web-dev-ecosystem-1-2nm0>

我想与你分享我在做一个网络应用程序时的进展，这个项目主要是为了(我的)学习目的，通过与你分享这个，我旨在获得你的帮助、建议或批评，或者也许你正在试图建立类似的东西，另一方面，你可能只是好奇用提到的技术创造一些东西，并愿意看到一个(不知何故)初学者尽最大努力找到出路。

我仍在学习和尝试，并试图适应 Clojure 生态系统，这真的很棒，但严重缺乏文档，你可以告诉初学者这意味着什么。因此，这将是一系列的文章，在这些文章中，我将谈论我面临的问题，以及我发现或创造的好东西。

该项目是关于一个新闻社交网络“O2SN”，这将有以下特点:

*   通过位置获取新闻。
*   新闻将使用各种标准排名:作者的诚实，客观性…所有这些都将自动计算。
*   看到(几乎)实时的新闻排名变化(排名高的新闻变得更高)
*   每个人都可以发一个故事(要真实客观，否则会影响作者的声誉，以后的新闻排名也不好)
*   如果你想发布一个故事，但一个相似的故事已经存在，你可以声明它，两个(或更多)故事将被合并(他们的排名…)，人们可以看到(新的)一个故事的所有版本
*   人们可以将一个故事标记为真实或谎言(这将影响它的排名)
*   当一个人将一个故事标记为真实或谎言时，它的属性会根据这个人的声誉以及他与故事或事件发生地点之间的地理距离而变化。
*   更多...

我使用 luminus 模板创建了这个项目，但是我也添加了一些其他的库，所以这是我当前的工具箱:

```
 :dependencies  [[org.clojure/clojure  "1.9.0"]  [org.clojure/clojurescript  "1.10.238"  :scope  "provided"]  [org.clojure/tools.cli  "0.3.6"]  [org.clojure/tools.logging  "0.4.0"]  [org.clojure/data.json  "0.2.6"]  [org.clojure/tools.trace  "0.7.9"]  [org.clojure/tools.namespace  "0.2.11"]  [org.clojure/test.check  "0.10.0-alpha2"]  [buddy  "2.0.0"]  [ch.qos.logback/logback-classic  "1.2.3"]  [cider/cider-nrepl  "0.15.1"]  [clj-oauth  "1.5.5"]  [clj-time  "0.14.3"]  [cljs-ajax  "0.7.3"]  [compojure  "1.6.0"]  [cprop  "0.1.11"]  [funcool/struct  "1.2.0"]  [luminus-aleph  "0.1.5"]  [luminus-nrepl  "0.1.4"]  [luminus/ring-ttl-session  "0.3.2"]  [markdown-clj  "1.0.2"]  [metosin/compojure-api  "1.1.12"]  [metosin/muuntaja  "0.5.0"]  [metosin/ring-http-response  "0.9.0"]  [mount  "0.1.12"]  [org.webjars.bower/tether  "1.4.3"]  [cljsjs/semantic-ui-react  "0.79.1-0"]  [cljsjs/react-transition-group  "2.3.0-0"]  [cljsjs/react-motion  "0.5.0-0"]  [re-frame  "0.10.5"]  [reagent  "0.7.0"]  [ring-webjars  "0.2.0"]  [ring/ring-core  "1.6.3"]  [ring/ring-defaults  "0.3.1"]  [secretary  "1.2.3"]  [selmer  "1.11.7"]  [com.arangodb/arangodb-java-driver  "4.3.4"]  [day8.re-frame/http-fx  "0.1.6"]  [com.draines/postal  "2.0.2"]] 
```

最后，这是目前唯一可以工作的部分，^ ^

[![signup form without errors](img/7a8667811de00e26380a55a487ababdc.png "signup form without errors")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i-h4IvFy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wsg2wcs75u98udaz62ss.png)

[![signup form with errors](img/9e2d37737daae397aa2be7528a71f795.png "signup form with errors")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wzur6ZxS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5n81bkhdxhbp0e91ggig.jpg)

这只是一个开始，我希望我能最终得到一些有用的东西(可能是几个月后)，两个月左右，直到我完成项目的骨干，清理它，你的帮助会得到高度赞赏。

最后，这是 Github 上的项目资源库: [O2SN](https://github.com/elarouss/O2SN)