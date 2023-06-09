# 100%纯 HTML/CSS 页面导航-不需要 JavaScript

> 原文：<https://dev.to/hakash/100-pure-htmlcss-page-navigation---no-javascript-required-2em5>

# 仅使用 HTML 和 CSS 构建页面导航系统

这是我多年来获得的一个小技巧，对于大多数用例来说，它在保持我的代码干净、简洁和没有不必要的 JavaScript 方面创造了奇迹。

想象一个单页应用程序或者一个显示和隐藏面板、模式等的页面。无需重新加载页面。点击这些触发器之一将改变页面的内容。

传统上，这是使用 jQuery 或一些自制的 JavaScript 函数来完成的，这些函数改变了相关元素的样式或类。设置一个用`display: none;`隐藏或者用`display: block;`显示的类是经典的，同时直接在元素上设置样式。

嗯，完全没有必要做得这么复杂。

## 例子

看看这个 HTML:

```
...
<!-- Just a typical nav-element with a list of links -->
<nav>
    <a href="#page1">Page 1</a>
    <a href="#page2">Page 2</a>
    ...
    <a href="#page5">Page 5</a>
</nav>
...
<!-- 
    All the divs with the content you want to serve. Notice page1 sits last. 
    This is important for the CSS to work.
-->
<div id="page2" class="page">[some content]</div>
<div id="page3" class="page">[some content]</div>
...
<div id="page5" class="page">[some content]</div>
<div id="page1" class="page default">[some content]</div>
... 
```

这里我们有包含链接的`nav`元素和包含内容的`div`元素。请注意，DIV 的 ID 与锚链接的 ID 相同。一件重要的事情是将第一个页面，或者更确切地说是包含您希望在没有链接被点击时显示的内容的 div 放在最后。

下面是 CSS 的重要部分:

```
/* This hides all pages */
.page {
    display: none;
}

/* This displays the first page */
.default {
    display: block;
}

/* This displays the page corresponding to the one you clicked on */
:target {
    display: block;
}

/* This hides the default page when another page is clicked */
:target ~ .default {
    display: none;
} 
```

还是浓缩:

```
.page, :target ~ .default {
    display: none;
}

.default, :target {
    display: block;
} 
```

## 演示

现在，我在下面嵌入的 CodePen 包含了本例中没有显示的 CSS 和 HTML，但这只是为了使它“漂亮”,与导航的实际功能没有任何关系。只是布局。在我们揭开秘密酱之前，先试试吧，还有一个警告。

[https://codepen.io/canis1980/embed/WaEXZz/?height=600&default-tab=result&embed-version=2](https://codepen.io/canis1980/embed/WaEXZz/?height=600&default-tab=result&embed-version=2)

## 揭开秘密

这个秘密的第一部分是链接。`href`只包含散列标识符，就像你用来上下跳转页面的那些。CSS 使用`:target`伪选择器获取 URL 中的散列，允许我们使用它来改变 ID 等于 URL 中散列的元素的样式。

第二部分是我们如何使用选择器来隐藏默认页面。因为我们把它放在最后，所以我们可以使用兄弟选择器来定位所有带有类`.default`的元素，该类在被定位的元素之后。

现在需要注意的是，除非页面的结构或布局使得“页面”的顶部也在浏览器的顶部，否则页面可能会跳转。一些只支持 CSS/HTML 的攻击可以通过使用容器 DIV 来完成，容器 DIV 保存 ID 并用`top: 0px;`定位，并包含用边距下推的实际内容 DIV，或者一些其他类似的方法。

希望你喜欢！

哦，如果你有其他方法不用 JavaScript 就能做 SPA 和类似的类型导航，请在评论中分享。