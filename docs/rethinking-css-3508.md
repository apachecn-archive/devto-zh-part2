# 重新思考 CSS

> 原文：<https://dev.to/leonorader/rethinking-css-3508>

我爱前端和后端。缓慢变化的健壮后端技术和不断变化的前端框架。

但是当我说总是变化的时候，我犯了一个错误。就像现在除了初学者没有人会使用 VanillaJS 一样，数百万人使用纯 CSS。我在想...为什么会这样？CSS 就像一种稳定的技术，它在不断发展，但不是以 JavaScript 世界的方式发展。

因此...我有一个问题要问你:你会把 CSS 改成别的吗？如果是，如何实现？如果没有，为什么？在继续之前，请思考几秒钟...

我在谷歌上搜索“反思 CSS”寻找其他开发者的想法，我发现的第一件事就是约翰·波拉切克的想法。我猜你们很多人以前都读过。它是关于原子或函数 CSS 的，所以这将是我想评论的第一件事。

## 0。原子 CSS

这个想法很简单。为常用的行创建 CSS 类，你将拥有数百万个类，你的 HTML 标记将会更长。给你看一个例子:

```
<p class="padding-small margin-big color-red">I am a p tag! How cool is that? :)</p> 
```

Enter fullscreen mode Exit fullscreen mode

```
.padding-small { padding: 10px; }
.margin-big { margin: 20px; }
.color-red { color: red } 
```

Enter fullscreen mode Exit fullscreen mode

它看起来与人们通常习惯的不同，但我认为这不是真正的“重新思考”,这只是使用语言元素的另一种方式。所以我在想其他的方法...

## 1。面向对象的 CSS

作为一名 Java 开发人员，这是我想到的第一件事，所以我再次谷歌了一下，找到了关于 [Envato](https://code.tutsplus.com/tutorials/object-oriented-css-what-how-and-why--net-6986) 的教程。如果你检查这篇文章，它又是另一种结构，试图看起来像面向对象。

我想我会说它看起来更像 OOCSS。我知道像 SASS 和 LESS 这样的现代工具给了我们一个简单的方法来做同样的事情。
(抱歉，我无法使用高亮显示，因为它明显显示错误)

```
.card {

  {
    color: #333;
    padding: 20px;
    background-color: white;
  }

  .title {
    font-size: 24px;
    margin: 10px;
  }

  .body {
    ...
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 2。OOCSS +原子

如果我们想让它更面向对象，我们想获得原子思维的优势，那么考虑这个:

```
.padding-big { padding: 20px; }
.margin-small { margin: 10px; }

.card {

  {
    padding-big;
    color: #333;
    background-color: white;
  }

  .title {
    margin-small;
    font-size: 24px;
  }

  .body {
    ...
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 3。Web 组件+ CSS

如果我们能做这样的事情呢？(我真的不喜欢 CSS 类)

```
<card>
  I am an awesome CARD! :)
  <body>...</body>
</card> 
```

Enter fullscreen mode Exit fullscreen mode

```
card {

  {
    color: #333;
    padding: 20px;
    background-color: white;
  }

  title {
    font-size: 24px;
    margin: 10px;
  }

  body {
    ...
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

在这些例子中，我试图使它们与原始语法相似，但是如果我也能改变它，会怎么样呢？

## 4。其他结构

我再次谷歌了一下，发现 [YAML CSS](http://www.yaml.de/) 是另一个模块化框架，和我想要的完全不一样。

我还有些别的想法:CSS 有它简单的语法，但是如果我也可以使用一些其他的结构呢？比如 YAML，在我看来，它更具可读性:

```
 card:
  color: #333
  background:
    color: white
  title: 
    margin: 
      bottom: 10px
  body: 
    font:
      weight: normal
    padding: 20px 
```

Enter fullscreen mode Exit fullscreen mode

## +1。CSS 的小片段

在最新的 JS 框架中，我们已经限定了 CSS 的范围，所以这意味着我们可以在每个文件中使用 CSS。但是有些人不喜欢，包括我，因为我们习惯了有 1 个大的静态 CSS 文件。不幸的是，随着时间的推移，它会变得很长。

如果我们把它们放在更小的 CSS 文件中，但是仍然在同一个地方，会怎么样？我可以想象有一个包含实用程序类的全局类，其他的可以是组件。

```
css
  -- global.css
  -- card.css
  -- slider.css
  -- navigation-bar.css
  -- ... 
```

Enter fullscreen mode Exit fullscreen mode

我认为这样 CSS 也更容易重用。当然，即使是现在，你也可以用这种方式组织你的规则，这是对我们通常在项目中所拥有的东西的一种改进，但是这又是一件可以改变游戏规则的事情。

## 总结

我知道很多人都在研究如何更容易、更聪明地使用 CSS，但是也许作为一个社区，我们可以分享知识来创造一个“新的 CSS”。我有一些想法，我只是对你的意见感兴趣，所以请分享一下吧！:)