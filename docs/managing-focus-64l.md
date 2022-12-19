# 为可访问性管理焦点

> 原文：<https://dev.to/robdodson/managing-focus-64l>

> 如果我正在构建一个单页面 web 应用程序，当用户点击一些导航时，`focus`会发生什么？

[https://www.youtube.com/embed/srLRSQg6Jgg](https://www.youtube.com/embed/srLRSQg6Jgg)

这是我不久前拍摄的一个视频，但这个问题出现得太频繁了，我想在 dev.to 上再次分享——另外，这也是我写第一篇帖子的一个好借口😁

通常在一个单页应用程序中，你会有一个看起来*大致*像这样的结构:

```
<nav>
  <a href="/">Home</a>
  <a href="/cart">Shopping Cart</a>
  <a href="/settings">Settings</a>
</nav>
<main>
  <!-- some content -->
</main> 
```

Enter fullscreen mode Exit fullscreen mode

因为是单页 app，点击链接不会做硬刷新。相反，它将触发一个路由更改，通常使用一点 AJAX 来获取更多数据并填充`<main>`内容区域。

对于一个有视力的用户来说，这很好——但是如果我是一个用屏幕阅读器导航的没有视力的用户呢？我可能不知道新内容已经被添加到页面中(也许我期待一个硬刷新)，也没有迹象表明我应该导航回`<main>`区域。

改善这种体验的一个简单方法是在新加载的内容中找到一个好的标题，并把`focus`指向它。实现这一点最简单的方法是给标题一个-1 的`tabindex`,并调用它的`focus()`方法。

```
<main>
  <h2 tabindex="-1">Welcome to your shopping cart</h2>
</main>
<script>
  // Assuming this gets called every time new content loads...
  function onNewPage() {
    var heading = document.querySelector('h2');
    heading.focus();
    // You can also update the page title :)
    document.title = heading.textContent;
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

然后屏幕阅读器会宣布新的标题，以及它所包含的`main`地标区域。请注意，您可能不希望在用户第一次到达您的站点时进行这种焦点管理——您可能只希望在后续导航时进行这种管理，比如当用户点击链接时。

希望你喜欢这篇文章！这是一个简单的技巧，可以为你的一些用户带来很大的不同。如果你对更多的 a11y 截屏感兴趣，请在 YouTubes 上查看我的播放列表📺

✌️