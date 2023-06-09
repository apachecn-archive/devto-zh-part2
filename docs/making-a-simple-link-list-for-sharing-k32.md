# 制作一个简单的分享链接列表

> 原文：<https://dev.to/jessachandler/making-a-simple-link-list-for-sharing-k32>

*我最初在[我自己的博客](http://jessachandler.com/2018/05/simple-link-list/)* 上发表了这篇略有不同的文章

## 你的网站需要一个链接页面！

我看到很多开发人员的 Instagram 简介，他们会进入主页，或者不包括一个快速的方法来根据他们的帖子找到我最感兴趣的地方。早在五月，我为两个网站编写了一些链接列表——一个在杰基尔，另一个在 EJS。我在我的博客上分享了基本代码和外观，现在我想在这里分享，以帮助其他开发人员。

### 为什么是另一页？

我知道，对吗？另一页。我们不是已经有了一个导航栏和一个页脚，天知道还有多少其他页面。然而，对于在日益移动的网络上分享来说，制作一个简单的导航是很好的——对于 Instagram 来说几乎是必不可少的。好吧，我完全是因为 Instagram 而开始从事这项工作的，但后来我看到了其他潜在的用途。

我们在这里创建的只是一个页面，上面有一些链接指向你网站的关键区域，人们在看到关于你的其他信息后可能会想要自我指导。我就当是你的 Instagram feed 吧。只要在你的 Instagram 个人资料中放上你最终编码的链接，作为你的网站。这里使用相同的想法来添加折扣或当前博客文章的临时链接，或者人们可能正在寻找的任何内容。😃

## 如何编码起来

我为两个网站做了这个，一个是我在 Jekyll 的博客，另一个是我使用 EJS 和 Express 的网站。因为两者的设置都很简单，但又非常不同，所以我将在这里分享两者。

最终结果在手机上看起来像这样。

[![Preview of Jekyll and EJS links from this tut](img/09b72e78cbe679d9651448ddea4bc324.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pvPC9qyf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ouxzucz9zmv2ox9vjayf.png)

### 杰基尔降价促销

我的博客是用 jekyll 和 markdown 写的。为了建立一个干净的链接页面，我必须创建一个新的布局和一个新的页面。

我需要一个新的布局，因为我没有任何现有的布局，可以将所有内容集中在页面上，而不是打印我的典型页面的页眉和页脚。我创建了一个名为 plain 的新布局，没有页眉和页脚。

*/layouts/plain . html*T2】

```
<!DOCTYPE html>
<html>
  {% include head.html %}
  <body>
    <div class="page-content">
      <div class="wrapper">
          <header class="post-header center">
            <h1 class="post-title">{{ page.title }}</h1>
            <h5 class="post-description">{{ page.description }}</h5>
          </header>
          <div class="center">
            {{ content }}
          </div>
      </div>
    </div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

降价需要包括 YAML，以表明我们正在使用我们的新的平面布局，标题，链接和描述。我还有一个`nav`标签，如果页面现在应该出现在主导航中，这个标签就是假的。你是否需要它取决于你的导航条和标题是如何编码的。

*/pages/links . MD*T2】

```
--------
layout: plain
title: Welcome
permalink: /links/
description: I can see you - just kidding
nav: false
-------- 
[About]({{site.url}}/){: .button .mylink}

[Code]({{site.url}}/code){: .button .mylink}

[Travel]({{site.url}}/travel){: .button .mylink}

[Projects]({{site.url}}/projects){: .button .mylink}

[Blog]({{site.url}}/blog){: .button .mylink} 
```

Enter fullscreen mode Exit fullscreen mode

结尾的`{:}`代码块是一种放置。css 进入 markdown。我的 markdown 页面中通常没有 css，但是如果不添加 css，我就无法获得我想要的按钮外观。如果您在 jekyll 站点中更频繁地使用按钮，或者打算这样做，那么这个链接页面可能会转到一个布局，在每个链接上循环并创建一个按钮。我这里的 css 设置是:

```
/* in /_sass/basscss/_base-buttons.scss */

button,
.button {
  font-family: $button-font-family;
  font-size: $button-font-size;
  font-weight: $button-font-weight;
  text-decoration: none;
  cursor: pointer;
  display: inline-block;
  box-sizing: border-box;
  line-height: $button-line-height;
  padding: $button-padding-y $button-padding-x;
  margin: 0;
  height: auto;
  border: 1px solid transparent;
  vertical-align: middle;
  -webkit-appearance: none;
}

::-moz-focus-inner {
  border: 0;
  padding: 0;
}

.button:hover {
  text-decoration: none;
}

/* in /_sass/_me.scss */

.mylink {
    background: $theme-color;
    color: #fff;
    font-size: 1.5em;
    width: 75%;
    align-self: center;
    text-align: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

### EJS/HTML/Express

我用 EJS 维护 [Gifts Done](https://www.gifts-done.com) 网站，用 Express 维护 HTML。代码是另一种直截了当的。首先，创建一个链接页面。我用 EJS 视图和页面。我不会张贴长的`headernonav`文件，但它只是头元和主体标签的开始。对于这个网站，我使用 Bootstrap 4，所以我没有设置任何特殊的样式，链接只是列表组的一部分。

*/视图/页面/链接. ejs*

```
<% include ../partials/headernonav %>

<div class="tall">
    <div class="px-3 py-3 pt-md-5 pb-md-4 mx-auto text-center">
        <a class="navbar-brand brand-logo" href="/">
        <img srcimg/gifts-done-name.png" alt="gifts-done logo"/></a>
        <p class="lead">Automate your Gifting!</p>
        <div class="container list-group py-3">
            <a href="/" class="list-group-item list-group-item-action">Learn More</a>
            <a href="/about" class="list-group-item list-group-item-action">About Us</a>
            <a href="/users/login" class="list-group-item list-group-item-action">Log In</a>
            <a href="/suppliers" class="list-group-item list-group-item-action">Become a Supplier</a>
            <a href="http://blog.gifts-done.com" class="list-group-item list-group-item-action">Our Blog, The Wrap </a>
            <a href="/faq" class="list-group-item list-group-item-action">FAQs</a>
            <a href="/contact" class="list-group-item list-group-item-action">Contact Us</a>
        </div>
    </div>
</div>

<!-- footer -->
<% include ../partials/footer %>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" ></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下要记住的是，你不能只是创建页面。你还需要添加一条路线，这样当有人去`http://yoursite/links/`的时候，服务器就会知道你想提供你制作的**链接**页面。

```
// routes/index.js
const express = require('express');
const router = express.Router();
const controller = require('../controllers/index');
// IG LINKS
router.get('/links', controller.show_links)
// .. a bunch of other router stuff

// controllers/index.js
// .. other controller stuff
exports.show_links = (req, res, next) => {
    res.render('pages/linkpage',{ page: 'landing'} );
}; 
```

Enter fullscreen mode Exit fullscreen mode

快乐上市——你可以从我的 [Instagram Feed](https://www.instagram.com/jess__chandler/) 中看到这段代码

**你不仅限于你网站上的链接**你的自定义链接列表页面可能包括你在 github 上的作品集、codepen 上的作品集、你在这里的最新文章，以及你能想到的任何东西。

请让我知道你如何扩展这个代码。我很想看看你的作品！