# 用 Tailwind CSS 构建响应菜单

> 原文：<https://dev.to/ttntm/building-a-responsive-menu-with-tailwind-css-2l87>

## 顺风 CSS

> 最后更新日期:2020 年 3 月

根据他们的文档，“ **Tailwind 是一个实用的 CSS 框架，用于快速构建定制用户界面。**

用于引导和/或其他基于组件的框架，这是一种完全不同的方法。

使用 Tailwind，基本上你从这些框架中知道的一切都是可能的，但是到达那里的方式有一点不同。没有`navbar`、`card`或`modal`——相反，Tailwind 的所有类都可以用来实际构建那些组件。基本上，这是很多的自由(*这是伟大的！*)，但这也需要更多的 HTML 知识，尤其是 CSS 知识，才能达到使用成品组件的效果。

哦，还有一件事:Tailwind 是一个纯 CSS 框架，没有任何前端 JavaScript——这意味着如果你想在页面滚动时有一个粘性的标题，或者在点击按钮时有一个折叠的导航条，你需要构建它。

### 让我们建立一个菜单

导航当然是建立网站的基本要素之一，所以让我们直接进入主题。

1.  我们将建立一个经典的老式页眉，顶部有一个更大的徽标，下方有一个水平菜单。

2.  当页面向下滚动时，菜单应该变得有粘性，这样导航选项就可以在长页面(如博客文章)上保持可访问性。

3.  当在移动设备上浏览我们的示例页面时，整个页面标题应该折叠，变成页面顶部的一个固定导航条，当单击一个按钮时，导航条向上展开。

所有这些都应该使用相同的标题/菜单和一些 JavaScript 来检查滚动位置并提供展开/折叠功能。

### 表头和菜单

我们将尽可能使用 Tailwind 的类，将自定义 CSS 保持在最低限度。

这就是我们的标题的样子:

```
<header id="top" class="w-full flex flex-col fixed sm:relative bg-white pin-t pin-r pin-l">
  <nav id="site-menu" class="flex flex-col sm:flex-row w-full justify-between items-center px-4 sm:px-6 py-1 bg-white shadow sm:shadow-none border-t-4 border-red-900">
    <div class="w-full sm:w-auto self-start sm:self-center flex flex-row sm:flex-none flex-no-wrap justify-between items-center">
      <a href="#" class="no-underline py-1">
        <h1 class="font-bold text-lg tracking-widest">LOGO</h1>
      </a>
      <button id="menuBtn" class="hamburger block sm:hidden focus:outline-none" type="button" onclick="navToggle();">
        <span class="hamburger__top-bun"></span>
        <span class="hamburger__bottom-bun"></span>
      </button>
    </div>
    <div id="menu" class="w-full sm:w-auto self-end sm:self-center sm:flex flex-col sm:flex-row items-center h-full py-1 pb-4 sm:py-0 sm:pb-0 hidden">
      <a class="text-dark font-bold hover:text-red text-lg w-full no-underline sm:w-auto sm:pr-4 py-2 sm:py-1 sm:pt-2" href="https://ttntm.me/blog/tailwind-responsive-menu/" target="_blank">About</a>
      <a class="text-dark font-bold hover:text-red text-lg w-full no-underline sm:w-auto sm:px-4 py-2 sm:py-1 sm:pt-2" href="#bottom">Features</a>
    </div>
  </nav>
</header> 
```

Enter fullscreen mode Exit fullscreen mode

这里有 2 个`div`元素，首先是导航栏，然后是带`id="menu"`的菜单。菜单按钮`menuBtn`有一个`onclick`属性，运行一个叫做`navToggle()`的功能。该功能将确保按下按钮时菜单可以展开/折叠。

Tailwind 是“移动优先”的，所以它的响应工具如`sm:hidden`的工作方式可以被描述为“从到指定断点的有效*”。因此，`sm:hidden`翻译成“在比定义的`sm`-断点更大的屏幕上隐藏”(默认:576px)。*

### JavaScript

我们需要 JavaScript 来启用菜单的两个功能，第一个是移动设备的展开/折叠功能，第二个是向下滚动时的粘性导航。

#### 展开和折叠菜单

为了实现手机菜单的正确功能，我们已经在菜单中调用了`navToggle()`函数。

这个函数本身相当简单，看起来像这样:

```
function navToggle() {
  var btn = document.getElementById('menuBtn');
  var nav = document.getElementById('menu');

  btn.classList.toggle('open');
  nav.classList.toggle('flex');
  nav.classList.toggle('hidden');
} 
```

Enter fullscreen mode Exit fullscreen mode

如上面的代码片段所示，默认情况下，`nav` HTML 元素被设置为`hidden`。`navToggle()`添加/删除`flex`类，从而展开/折叠菜单。

#### 粘性菜单

向下滚动足够远应该会使我们的`nav`在任何大于 576 像素的屏幕上停留在屏幕的顶部，为网站的用户提供持久的导航。

为了实现这一功能，我们需要获取`scroll`事件，并使用它向`nav` HTML 元素添加一个类，使其保持不变。这可以这样做:

```
var nav = document.getElementById('site-menu');
var header = document.getElementById('top');

window.addEventListener('scroll', function() {
  if (window.scrollY >=400) { // adjust this value based on site structure and header image height
    nav.classList.add('nav-sticky');
    header.classList.add('pt-scroll');
  } else {
    nav.classList.remove('nav-sticky');
    header.classList.remove('pt-scroll');
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们不仅添加了`nav-sticky`，还有第二个名为`bod-pt-scroll`的类，它将`padding-top`添加到`<body>`中，从而确保当`nav`停留在页面顶部并从 DOM 元素的常规流中取出时，页面内容不会回流。

### 自定义 CSS

为了提供必要的填充和`nav`-元素的粘性，我们使用了一些定制的非顺风 CSS:

```
@media (max-width: 576px) {
  .content {
    padding-top: 51px;
  }
}

@media (min-width: 577px) {
  .pt-scroll {
    padding-top: 51px;
  }

  .nav-sticky {
    position: fixed!important;
    min-width: 100%;
    top: 0;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, .1);
    transition: all .25s ease-in;
    z-index: 1;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`<body>`的填充当然可以用 Tailwind 类来完成，但是这个例子来自一个项目，在这个项目中，在它们的 responsive variation 中不需要 Tailwind 的填充和边距实用程序，因此只使用这里显示的 5 行自定义 CSS 就可以使整个 CSS 包变得更小。

### 结论

Tailwind CSS 是一个非常通用、强大和可定制的框架。一旦你进入其中，它也会变得很快，让你工作起来充满乐趣。

我希望这篇文章对外面的人有所帮助。为了方便起见，这里有一支笔和一个优秀顺风备忘单的链接:

*   [密码笔](https://codepen.io/ttntm/full/dqaNPp)
*   [Cheatsheet](https://nerdcave.com/tailwind-cheat-sheet)