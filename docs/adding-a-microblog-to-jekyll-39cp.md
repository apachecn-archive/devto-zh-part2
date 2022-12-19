# 给 Jekyll 加微博

> 原文：<https://dev.to/maxlmator/adding-a-microblog-to-jekyll-39cp>

自从我在一月份了解到[微博](https://micro.blog)T2】后，我想在我的博客上试一试。在过去的几天里，我尝试了几次。正如你在 xam.io/microblog[和](https://dev.to/microblog)[微博](https://micro.blog/xam)上看到的，我成功了。现在我想分享一下我是怎么实现的。

给[的一个小贴士:用 Jekyll](https://brightlycolored.org/2017/02/creating-a-microblog-with-jekyll/) 的帖子创建一个微博，涵盖了很多基础工作。我采纳了他的很多想法，在这篇文章中，我将更多地关注与我现有博客的整合。

## 微博集锦

因为我想将常规帖子与微博帖子分开，所以我决定使用一个[集合](https://jekyllrb.com/docs/collections/)来做到这一点。如果你不太在乎这种分离，在常规文章中使用一个专门的类别可能是更容易的选择。

这里是集合的配置:

```
collections:
  microblog:
    output: true
    permalink: microblog/:year/:month/:title/ 
```

Enter fullscreen mode Exit fullscreen mode

我决定为微博帖子保留`:year/:month/:title`路径，但是在更深的层次上保留`microblog`——当然，永久链接模式完全取决于你。

这个配置将选择`_microblog`目录中的任何文件，将它们解释为`microblog`集合的一部分，并在输出目录中创建一个呈现的 HTML 页面。

## 向索引页面添加微博帖子

集合中的项目不是通常在索引页面上循环的`site.posts`数组的一部分，它一个接一个地列出项目。相反，它们可以通过使用`site.microblog`来访问。为了将它们合并在一起(并使它们分页)，我用 [jekyll-paginate-v2 gem](https://github.com/sverrirs/jekyll-paginate-v2) 替换了(不再维护的) [jekyll-paginate](https://github.com/jekyll/jekyll-paginate) 。

在我的`index.html`的前端，我现在有了这个配置来对帖子和微博进行分页:

```
--------
layout: default
pagination:
  enabled: true
  collection: posts, microblog
  per_page: 10 # Number of entries per page
  permalink: "page/:num/" # Permalink structure
  sort_reverse: true # Newest first
--------

{% for post in paginator.posts %}
    {% if post.collection == 'microblog' %}
        {% include microblog.html %}
    {% else %}
        {% include post.html %}
    {% endif %}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

`microblog.html`是我的`post.html`模板的简化版，通常用于呈现博客文章。

## 为帖子和微博单独创建页面

如你所见，这个博客也有**帖子**页面(仅包含常规帖子)和一个**微博**页面，仅包含微博帖子。

为此，我将`index.html`复制为`microblog.html`(微博)和`posts.html`(常规帖子)。但是将`collection:`前置改为只包含`microblog`或`posts`。

分页的永久链接结构是相对于源目录的，加上帖子和微博的永久链接配置，这是该站点当前的 URL 模式:

```
/ -- Page with microblog and posts
/page/:num/ -- Pagination for both microblog and posts

/posts/ -- Page only with posts
/posts/page/:num/ -- Pagination for regular posts
/:year/:month/:title/ -- URL for single posts

/microblog/ -- Page only with microblog
/microblog/page/:num/ -- Pagination for microblog posts
/microblog/:year/:month/:title/ -- URL for a single microblog 
```

Enter fullscreen mode Exit fullscreen mode

## 为微博添加订阅源

目前，我的定期订阅源(可从 [/atom.xml](///atom.xml) 获得)仅限于定期发布的帖子。但是我为微博创建了一个单独的提要( [/microblog.xml](///microblog.xml) )。

由于常规提要也不使用任何 Jekyll 插件，所以我也选择了普通的微博提要方法——我的`microblog.xml`模板:

```
--------
layout: null
--------
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    xam.io | Microblog
    <description>Microblog of Max Kleucker</description>
    <link>{{ site.url }}</link>
    <atom:link href="{{ site.url }}/microblog.xml" rel="self" type="application/rss+xml" />
    {% for post in site.microblog reversed limit:50 %}
      <item>
        <description>{{ post.content | xml_escape }}{% if post.external-url %}{% endif %}</description>
        <pubDate>{{ post.date | date: "%a, %d %b %Y %H:%M:%S %z" }}</pubDate>
        <link>{{ site.url }}{{ post.url }}</link>
        <guid isPermaLink="true">{{ site.url }}{{ post.url }}</guid>
      </item>
    {% endfor %}
  </channel>
</rss> 
```

Enter fullscreen mode Exit fullscreen mode

这个 feed 也是我的微博账号的来源。

* * *

## 创建微博

因此，发布工作，我们可以手动创建一个新的文件在`_microblog/`目录，它将被发布在索引页，微博页和微博饲料。耶！

创建微博帖子就是在`_microblog/<YEAR>-<MONTH>-<DAY>-.md`手动创建文件。特别是``部分有点遗憾:根据@manton 的定义，微博[不使用帖子标题](http://www.manton.org/2014/09/defining-a-microblog-post.html)。但是您不能忽略它，因为否则您每天只能创建一个条目。

所以我想有一些像`jekyll post "my-title"`命令一样快速的东西。我决定添加一个`Makefile`,它可以从模板创建一个新的微博文件，并立即在我选择的编辑器中打开它——带有自动生成的标题，所以我不必关心它。

模板(在`templates/_microblog` ):

```
--------
layout: microblog
date: %CURRENT_DATE%
title: %POST_TITLE%
-------- 
```

Enter fullscreen mode Exit fullscreen mode

和`Makefile` :

```
# Makefile
MICROBLOG_TEMPLATE := _templates/microblog
POST_DATE := $(shell date +%Y-%m-%d)
POST_TIME := $(shell date +%Y-%m-%d\ %T\ %z)
POST_TITLE := $(shell openssl rand 100000 | shasum | cut -c1-8)
POST_FILE := _microblog/$(POST_DATE)-$(POST_TITLE).md
.PHONY: new-microblog
new-microblog:
    @cat $(MICROBLOG_TEMPLATE) | \
    sed "s/%CURRENT_DATE%/$(POST_TIME)/g" | \
    sed "s/%POST_TITLE%/$(POST_TITLE)/g" > ${POST_FILE} && \
    subl ${POST_FILE} 
```

Enter fullscreen mode Exit fullscreen mode

在您的 shell 上运行`make new-microblog`将会执行以下操作，并为您留下一篇准备发布的新微博:

1.  阅读`_templates/microblog`中的模板
2.  用当前日期和时间替换模板中的`%CURRENT_DATE%`
3.  用一个随机的 8 字符字符串替换`%POST_TITLE%`
4.  将此保存在`_microblog/<CURRENT_DATE>-<RANDOMIZED_TITLE>.md`
5.  在 vim 中打开这个文件。您可以将其更改为您选择的编辑器。

* * *

## 何去何从

设置好一切后，我最终了解了很多关于[微型泵标准](https://www.w3.org/TR/micropub/)和[网站提及](https://webmention.net/draft/)的信息。T4 的 IndieWeb 页面是这方面的一个很好的资源。简而言之，有一整套规范和标准定义了运行你自己的微泵后端的协议。网络提及是一种很好的方式，当你提到它们的时候，可以让别人知道。

然而，这些协议需要一个动态的 web 服务来有效地工作。运行一个静态博客看起来可能有点违反直觉，但是在 indieweb wiki 上已经有一些方法可以做到这一点。但那是另一个周末的事了。