# Pwned 在一起:黑客开发到

> 原文：<https://dev.to/antogarand/pwned-together-hacking-devto-hkd>

# [T1】简介](#intro)

在阅读了这篇关于 Dev.to 的源代码的文章后，我受到了启发。为了庆祝我在这里的第 500 个追随者，我给自己的挑战是黑掉 dev.to！

[![joshcheek](../Images/79a668ebe5d467cb7670a9261a6760c4.png)](/joshcheek) [## 这个网站是开源的

### 乔希·齐克 8 月 21 日 183 分钟阅读

#opensource](/joshcheek/this-website-is-open-source-3db4)

最后，我在一个定制的液体标签里发现了一个储存的 XSS。这意味着如果你浏览了一篇被感染的博客文章，我可以控制你的浏览器，并代表你执行操作！

# 上下文

XSS 是一种缺陷，恶意用户(在本例中为 *me* )可以在页面中注入 javascript 代码。
有了 JavaScript，特别是在一个单页应用程序中，我可以在这个网站上用你的账户做几乎任何事情！

我可以更新你的个人资料，发布新帖子，喜欢和评论出版物，等等！拥有一个 XSS 意味着我从浏览器的角度完全控制了网站，因此这是一个相当严重的问题。

XSS 有不同类型:

*   反射 XSS:需要用户访问恶意链接才会被触发。这意味着您需要被重定向或单击链接才能触发该漏洞。
*   存储的 XSS:这是一个保存在服务器上的 XSS，因此由网站本身呈现。这是一篇精心制作的博文。这要严重得多，因为它可以由正常的用户行为触发，并为每个人复制。

举个例子，几年前在 [TweetDeck](https://tweetdeck.twitter.com/) 上发现了一个存储的 XSS，恶意代码在那里自动转发，最终得到了数量惊人的转发:

> ![*andy profile image](../Images/6fa46112e4b3b9ea3f8b1dbcc9834053.png)*安迪@ dergeruhn![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)<script class = " XSS ">$('。XSS’)。父母()。等式(1)。查找(' a ')。等式(1)。单击()；$('[data-action=retweet]')。单击()；alert(' XSS in tweetdeck ')</script>♥2014 年 6 月 11 日下午 16:36[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=476764918763749376)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=476764918763749376)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=476764918763749376)

# 漏洞

## 原始代码

正如在[编辑器指南](https://dev.to/p/editor_guide)中提到的，这里实现了许多自定义液体标签。其中一个是在提到的博客文章中由 Josh 新创建的，所以我决定从那里开始检查安全问题！

这些液体标签的源代码位于 [/app/liquid_tags](https://github.com/thepracticaldev/dev.to/tree/master/app/liquid_tags/) 文件夹下。
很快，[要点](https://github.com/thepracticaldev/dev.to/blob/e588fa7ece36d2c5aa398ba5eedee6b9d60b0818/app/liquid_tags/gist_tag.rb)引起了我的兴趣:给定的标签被直接渲染成脚本标签！

```
html = <<~HTML <div class="ltag_gist-liquid-tag">
      <script id="gist-ltag" src="#{@link}.js"></script>
  </div> HTML 
```

Enter fullscreen mode Exit fullscreen mode

对于它的验证有很多变通方法:

```
def valid_link?(link)
    link.include?("gist.github.com")
end 
```

Enter fullscreen mode Exit fullscreen mode

在[文档](https://dev.to/p/editor_guide)中，`gist`链接的用法如下:

```
{% gist https://gist.github.com/QuincyLarson/4bb1682ce590dc42402b2edddbca7aaa %} 
```

Enter fullscreen mode Exit fullscreen mode

添加`.js`为我们提供了一个脚本，围绕要点内容创建了一个漂亮的嵌入: