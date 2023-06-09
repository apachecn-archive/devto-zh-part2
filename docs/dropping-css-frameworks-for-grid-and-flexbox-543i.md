# 删除网格和 Flexbox 的 CSS 框架

> 原文：<https://dev.to/alakra/dropping-css-frameworks-for-grid-and-flexbox-543i>

[![Dropping CSS frameworks for Grid and Flexbox](img/b1fb972d55a65085142a1c8053d9aed3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GX_aqQHQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1504454449875-92cfa39e1315%3Fixlib%3Drb-0.3.5%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ%26s%3D6561d4b681332e316e7f21a6228ad9c8)

从 2000 年到 2010 年，我一直在一个充斥着 CSS 黑客的世界里进行开发。我习惯了浮动、内嵌阻塞和清除补丁来管理布局。几年前我曾试图学习使用 flexbox，但那是在它刚推出的时候，大多数属性都是浏览器前缀，并不总是一致地工作。所以我放弃了，暂时放下前端开发，等待事情成熟。

我通过订阅使用[引导](http://getbootstrap.com/)、[基础](https://foundation.zurb.com/)和[骨骼](http://getskeleton.com/) 等东西的阵营来避免痛苦。这些是安全的港湾，在这里我可以快速构建应用程序，并专注于提供可靠的后端。

当我走向我的同事，问他正在做什么时，我的兴趣又被点燃了。他向我展示了 CSS [grid](https://css-tricks.com/snippets/css/complete-guide-grid/) 和 [flex](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 的两个教程，并给了我一个快速的概念验证，告诉我它们可以一起做什么，我被震撼了。

**我决定只用 grid 和 flexbox** 构建一个没有 CSS 框架的页面。我不得不说，我很高兴不用太担心我的 HTML 的结构，就能做出一个功能性的布局。

## 固定宽度布局的 CSS 网格和 flexbox 工作流是什么样子的？

下面是`HTML` :

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">

    Test Grid Layouts
    <link rel="stylesheet" href="styles.css">
  </head>

  <body>
    <div class="container">
      <header>
        <nav>
          <div id="logo">Using Grid</div>
          <ul>
            <li><a href="#">About</a></li>
            <li><a href="#">Blog</a></li>
            <li><a href="#">Contact</a></li>
          </ul>
        </nav>
      </header>

      <main>
        <div class="content">
          <section class="hero">
            <h3>Color Me Black</h3>
          </section>

          <article>
            <h1>Title</h1>
            <p>I love content!</p>
          </article>
        </div>
      </main>

      <footer>
        <nav>
          <div class="siteinfo">Made by Angelo</div>
        </nav>
      </footer>
    </div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

和`CSS` :

```
html, body {
  margin: 0;
  padding: 0;
  height: 100%;
  font-family: 'Roboto', 'Helvetica', sans-serif;
}

.container {
  display: grid;                       
  min-height: 100%;                    
  grid-template-rows: 48px [stage] auto 48px;  
  grid-template-columns: 100%;
}

#logo {
  color: #FFFFFF;
  font-size: 1.5em;
}

header {
  display: grid;
  grid-template-columns: auto [head-center] 832px auto;
  height: 48px;
  background-color: #000000;
}

header > nav {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: flex-start;
  align-items: center;
  grid-column-start: head-center;
  grid-column-end: span 1;
}

header > nav ul {
  margin: 0 0 0 auto;
}

header > nav ul li {
  display: inline;
  padding-left: 8px;
}

header > nav ul li a {
  color: #FFFFFF;
  text-decoration: none;
  text-transform: uppercase;
}

header > nav ul li a:hover {
  color: #AAAAAA;
}

main {
  display: grid;
  grid-template-columns: auto [main] 832px auto;
  grid-template-rows: auto;
  grid-row-start: stage;
  grid-row-end: span 1;
}

main > div.content {
  grid-column-start: main;
  grid-column-end: span 1;
}

main > div.content .hero {
  height: 300px;
  width: 100%;
  background-color: #000000;
  color: #FFFFFF;
}

main > div.content .hero h3 {
  padding: 16px;
}

footer {
  display: grid;
  grid-template-columns: auto [foot-center] 832px auto;
  grid-template-rows: 48px;
  background-color: #000000;
}

footer > nav {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: flex-end;
  align-items: center;
  grid-column-start: foot-center;
  grid-column-end: span 1;
}

footer > nav > .siteinfo {
  color: #FFFFFF;
} 
```

Enter fullscreen mode Exit fullscreen mode

**为了让事情看起来更愉快，我这里还有一些其他有趣的东西，但是我们应该真正关注`display: grid`和`display: flex`在哪里被使用**。

`.container`元素是我们的顶级网格:

```
.container {
  display: grid;                       
  min-height: 100%;                    
  grid-template-rows: 48px [stage] auto 48px;  
  grid-template-columns: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用`grid-template-rows`来定义我们的顶级行(从上到下)。这些可以是固定大小、百分比或`auto`。属性下的每个规范都按照定义顺序映射到一个子项。例如，第一个`48px`条目是我的第一行，`auto`(别名为`stage`)是我的第二行，`48px`是我的最后一行。我还指定了我想让`grid-template-columns`扩展我的容器的整个宽度。

现在我已经完成了，我必须考虑我从那个网格元素创建的直接子元素，因为我应用的所有属性都是针对这个父元素的直接子元素的。

来自这个`.container`父元素的子元素(我的网格项目)是`header`、`main`和`footer`。我在这些项目上定义属性，告诉它们应该在网格上的什么位置。

我们来看看`main` :

```
main {
  display: grid;
  grid-template-columns: auto [main] 832px auto;
  grid-template-rows: auto;
  grid-row-start: stage;
  grid-row-end: span 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里做几件事。我在顶层`.container`网格下定义了另一个网格，我告诉这个网格项从`stage`行开始的地方开始，然后向下扩展(或`span`1 个单元格空间。如果我没有将起始行的别名设为`stage`，那么我将不得不向下计数我想要开始的行数，并输入那个数字(在本例中，它将是`2`)。

**基本上就是这么回事。**虽然这不像 HTML `<table>`标签，但它的布局心理模型非常相似。您可以认为`colspan`和`rowspan`的工作方式非常类似于上面的`span`属性。

**对我来说，最重要的两个概念是定义`grid`布局的父元素和定义每个“项目”的子元素**。item 是一个 DOM 元素，作为内容映射到网格中的某个位置。

**你可以嵌套`grid`来获得更复杂的布局。**我这样做是为了将页眉和页脚的背景扩展到整个页面的宽度，同时在`main`元素上维护一个“内部”网格，内容将居中，并带有自动宽度的边距。

**当我使用`grid`管理布局时，我使用** `flex` **管理子元素在布局中的定位行为。**这不仅仅是垂直和水平的定位，而是孩子之间的相对间距，甚至是排序。Flex 在这方面做得很好，并且可以(并且已经)用于布局，但是最后，随着 DOM 变得越来越复杂，它真的开始变得粗糙了。因此，如果你试图设计大画面组件，就使用`grid`。

**对于** `grid` **来说，也有更多像线条、轨迹、面积、细胞之类的东西要考虑。我也大量使用化名。**这些都是你应该去尝试和理解的事情。

**所以最好进行实验。**将 HTML 直接复制到一个文件中，保存为`index.html`，然后将 CSS 保存为`styles.css`并放入一个文件夹中。

假设您已经安装了 Python 3，并且有一个可用的 unix shell，您可以提供这些服务:

```
$ cd YOUR_FOLDER
$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ... 
```

Enter fullscreen mode Exit fullscreen mode

然后打开一个浏览器到`http://localhost:8000`，开始使用浏览器中内置的开发人员控制台调整 CSS 值，或者直接编辑磁盘上的文件，并点击浏览器上的刷新按钮来进行您想要的每个更改。

另外，在一些移动视图中尝试一下。你会很快发现宽度是固定的。你能让它以一种流畅或反应灵敏的方式工作吗？给你一个提示:看看[这个博客网站](https://angelolakra.com)的 HTML 和 CSS。

## 总结

使用网格可以做很多事情。如果你愿意，你可以加入媒体询问，你可以让你的布局完全流畅。大多数现代浏览器都支持 grid 和 flex。看看[的网格指南](https://css-tricks.com/snippets/css/complete-guide-grid/)和[的 flexbox 指南](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)或[的规范本身](https://www.w3.org/TR/css-grid-1/)以获得更多信息，最重要的是——扔掉你的黑客！

* * *

#### 参考文献

1.  CSS 网格规格:[放置项目](https://www.w3.org/TR/css-grid-1/#overview-placement)
2.  CSS 2 规格:[最小和最大高度:“最小高度”和“最大高度”](https://www.w3.org/TR/CSS2/visudet.html#min-max-heights)