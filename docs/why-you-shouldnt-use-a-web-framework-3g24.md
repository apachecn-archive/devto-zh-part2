# 为什么不应该使用 Web 框架

> 原文：<https://dev.to/gypsydave5/why-you-shouldnt-use-a-web-framework-3g24>

你用的是什么框架？你用的是 Bootstrap，还是 Materialize，还是 Foundation？或者 Vue，或者 Angular 1，或者 Angular 2，或者 Ember？或 Ruby on Rails，或 Sinatra，或 Express，或 Meteor，或 Flask，或 Korbin，或哈比神，或 Spring，或...

框架都很可怕。全部都是。我是认真的——他们正在把你们都变成糟糕的开发者。现在停止使用它们。

我们来谈谈这个吧，因为我可能已经失去了你们中的大多数人。为什么像你这样的人喜欢框架？你是懦弱还是愚蠢？还是两者都有？

## 一个框架意味着我不需要多此一举

当然，我正在制造一辆自行车。我不需要重新发明轮子，我只要抓一个有轮子的框架就行了。

和方向盘

和一顶帽子。蓝色的。

和发动机。

和一个 CD 播放器

什么，你没有 CD 吗？好吧，别用它。或者只使用 CD 播放器到 MP3 播放器的适配器。

为了你的自行车。

你还在建造它。但是现在在汽车顶上。

但这没关系，因为 AutocarJS 有一个模块化的插件系统，允许你给你讨厌的汽车自行车添加自行车踏板。很简单。只需输入“汽车脚手架添加踏板”。或者编辑`autocar.js.manifest.nightmare`文件。

这就简单多了，是吧？

“你不需要重新发明轮子”在两种情况下都没问题:

*   你知道轮子是怎么工作的
*   你知道这个轮子是如何工作的
*   这个轮子没有一堆你永远不会用的垃圾
*   你只想要一个轮子

## 初学者更容易使用框架

当然，如果你是西斯。

什么？是的，一个西斯尊主在一个奇怪的黑色碉堡里。您走上了通往终极力量的捷径，但是您仍然不知道 HTTP 是如何工作的。或者发布请求是什么样子的。或者如何编写表单元素。或者如何使用一个数据库，而不会出现一些奇怪的活动记录噩梦...

它会让人上瘾，因为你不知道任何东西是如何工作的，你会花你的职业生涯去追逐新的事物，因为新的事物并没有完全满足你的需要。你会患上永恒的冒名顶替综合症，因为你不知道任何事情是如何运作的。你将完成你的工作，你将成为一名“React 开发人员”或“Rails 开发人员”,你会想知道哪里出错了。

真实的故事。当有人面试一个著名的编码训练营的职位时，他们被要求用 Ruby 做 FizzBuzz 形。他们在命令行输入的第一句话是什么？

```
gem install rails 
```

Enter fullscreen mode Exit fullscreen mode

你是阿纳金·天行者，你所能期待的只是一种生活，在这种生活中，你不知道任何事情是如何运作的，你的事业和生计都建立在你的无知之上。不，你不是达斯·维德。你没那么酷。你是阿纳金，你很差劲。

[![Angry Vader is Angry](img/9a64995bfce8dafed85c8716babe5c14.png)T2】](https://i.giphy.com/media/DVVtaUDVCGOv6/giphy.gif)

## 但是没有框架真的很难

是吗？真的吗？你*试过*不用框架写东西吗？我的意思是，没有框架，你能写出一个漂亮、复杂、面向客户的网站吗？比如说 GitHub...

[![Github has no framework nao](img/98830c71bcf23de7f0e42b3d68435a2c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GvY7sAKD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j7bk2ut9vk7nrda378e6.png)

是的，你可以！

网络比十年前——见鬼，是十个月前——要好得多。你不需要框架——你只需要花几分钟阅读 MDN 上的文档。不需要 Sass，CSS 现在有变量了。你不需要 Bootstrap，flexbox 或者 grid 都是你的朋友。

那个服务器的东西呢？除非我使用一个框架，否则这怎么可能呢？

你在开玩笑吗？你见过 HTTP 有多简单吗？

## 轻松 HTTPeasy

看，一个 HTTP 请求:

```
GET /hello-world?framework=none HTTP/1.1
Accept: text/html
Host: my-hello-world-server.com 
```

Enter fullscreen mode Exit fullscreen mode

这是不是太难解析了？真的吗？你可以像读单词一样读它。只是一根又大又旧的弦。您应该能够用您选择的任何语言提取出您想要接受的内容类型、方法和查询字符串参数。如果这么简单，为什么要依赖一个臃肿、固执己见的框架来替你做所有的工作呢？

我并不是说你应该在 HTTP 上写你自己的抽象——几乎每一种现代编程语言都有一个内置的抽象来帮你完成繁重的工作。如果它不是内置的，那么它就是一个现成的库。使用其中的一种——一种通过套接字解释请求和响应的更简单的方法。这才是你真正需要的。

“HTTP 上的抽象”是这里的关键思想。这是需要从套接字上的字节流中抽象出来的部分。框架是对...一堆乱七八糟的东西。它通常是对别人关于网站或程序应该是什么样子的想法的抽象。这不是你的想法，也不是你的产品——这是他们的，它限制了你的创造力。

## 学习基础知识，而不是某人的抽象

栈溢出充满了像“我如何在框架 Y 中做 X”这样的问题，答案从“你为什么想做 X？”，“不，你不能”，或者“你不能做 X，但为什么不做 Z？”。

这是因为可怜的 dev asking 习惯于使用一个框架，在这个框架中，数据库被附加到请求对象或其他什么上，现在他们使用一个完全不同的抽象的 NewShinyFramework。他们学错了东西。

如果你想在职业生涯中有一个坚实的开端，不要学习框架和框架的抽象；学习基础知识。

不要学快递的路由系统；了解 HTTP 是如何工作的。

不要熟悉 ActiveRecord hacks 为了让你的查询更快，要学会写 SQL。

不要学怎么用 JQuery 用 Bootstrap 用 Redux 构建 React，学 HTML，CSS，JavaScript。

对于你需要做的 99%的事情，它们已经足够了。

这种知识永远不会过时。

不像框架。

* * *

### 更新

我写了这篇文章的续篇和回应:

[![gypsydave5 image](img/ae3081c50d4c953e1125e67f5f086a97.png)](/gypsydave5) [## Todo-MVP:或者“为什么你不应该使用 Web 框架”——报复

### 大卫·威克斯 11 月 26 日 184 分钟阅读

#showdev #webdev #beginners #frameworks](/gypsydave5/todo-mvp-or-why-you-shouldnt-use-a-web-framework---the-revenge-261l)