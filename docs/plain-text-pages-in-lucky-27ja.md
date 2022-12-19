# Lucky 中的纯文本页面

> 原文：<https://dev.to/jwoertink/plain-text-pages-in-lucky-27ja>

有时候你可能需要用 HTML 来呈现一个不是网页的页面，而更多的是纯文本。一个例子就是一个[机器人。现在，你可能会想“为什么不创建一个静态的文本文件，并把它放到你的公共目录中呢？”。嗯，你是对的，但是那样我就没有理由写这篇文章了😛。另一个原因(我将向您展示)是为了一些动态内容。](http://www.robotstxt.org/robotstxt.html)

首先我们将在`src/actions/static_pages/robots.cr`
中做一个新动作

```
class StaticPages::Robots < BrowserAction
  get "/robots.txt" do
    text <<-TEXT User-Agent: * TEXT
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！启动您的服务器，检查您的 localhost:3000/robots.txt，检查内容类型是否设置为应该设置的`text/plain`。

对于[幸运](https://luckyframework.org/)，有一个辅助方法叫做`text`。这是对你的动作的一个`redirect`或`render`的替换，这意味着你只调用它一次。它是不可链接的，所以如果你有很多文本要呈现，你可以把它分解成方法，然后做类似于`text my_formatted_output`的事情。

现在，我确实提到过，如果你想做一些有活力的事情，你可能想走这条路。假设您有一个[多租户应用](https://en.wikipedia.org/wiki/Multitenancy)，其中多个域指向您的单个应用。在公共目录中放一个 robots.txt 文件可能行不通。每个域可能有不同的需求。

```
class StaticPages::Robots < BrowserAction
  get "/robots.txt" do
    text <<-TEXT Sitemap: https://#{current_site.host}/sitemap.xml
    User-Agent: *
 TEXT
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？很有用，现在就去用吧！