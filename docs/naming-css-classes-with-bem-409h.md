# 用边界元法命名 CSS 类

> 原文：<https://dev.to/frugencefidel/naming-css-classes-with-bem-409h>

这篇文章最初发布在[我的博客](http://frugencefidel.com/blog/naming-css-classes-with-bem)。

BEM-**B**lock**E**lemet**M**odifier 是命名 CSS 类的方式，这样它们可以更有意义和可重用。

BEM 有助于避免名字冲突，减少需要修改 CSS 类时的痛苦。众所周知，在处理大型项目时，修改一个 CSS 类可能会导致几十个受影响的页面。BEM 有助于避免所有这些问题。

让我解释一下我对 BEM 的了解:

**B** -闭塞

块是设计的一个独立的、可重用的和独立的部分。块可以相互嵌套。

```
 <!-- hero block -->
  <section class="hero">
    <!-- btn block -->
    <a href="#" class="btn">Click Me!!</a>
  </section> 
```

Enter fullscreen mode Exit fullscreen mode

**E** -元素

元素属于块，不能独立。简单地说，它不能在它所属的块之外使用。元素可以相互嵌套。CSS 类由块名加两个下划线加元素名组成。

```
 <!-- hero block -->
  <div class="hero">
    <!-- content element belongs to hero block -->
    <div class="hero__content">

      <!-- title element belongs to hero block -->
      <h1 class="hero__title">I'm main title</h1>

      <!-- text element belongs to hero block -->
      <p class="hero__text">Cupiditate dolores iste reiciendis ea non iure est.</p>
    </div>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

**M** 修改

修饰符用于更改元素或块的默认行为或状态。此外，修饰符不是独立，这意味着它们不能在它们所属的块之外使用。它们显示与默认的大小、差异或行为。CSS 类由块名加上两个减号和元素名组成。

```
 <div>
    <!-- default btn block -->
    <a href="#" class="btn">I'm OG</a>

    <!-- btn block modified by red modifer -->
    <a href="#" class="btn btn--red">I'm red</a>

    <!-- btn block modified by red and large modifer -->
    <a href="#" class="btn btn--red btn--large">I'm red and large</a>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在这里看关于 BEM 的视频

[https://www.youtube.com/embed/ifaof7_h7oA](https://www.youtube.com/embed/ifaof7_h7oA)