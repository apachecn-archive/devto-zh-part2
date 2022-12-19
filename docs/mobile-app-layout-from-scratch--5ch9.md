# 从头开始移动 web 应用程序布局

> 原文：<https://dev.to/genejams/mobile-app-layout-from-scratch--5ch9>

 <small>虽然这篇文章中的主题可能仍然是真实的，但是这些内容引用了一个旧版本的 *zuix.js* 库。最新文档见 [zuix.js](https://zuixjs.org) 网站。</small> 

* * *

首先，我们添加标题栏，带有徽标和一个菜单按钮。

```
<div class="logo">
    <a href="/">app-logo</a>
</div>
<div data-ui-load="@lib/components/hamburger_icon"
     data-ui-options="options.menuButton"
     class="menu-button"></div> 
```

Enter fullscreen mode Exit fullscreen mode

查看下面示例的`layout`文件夹中的实际代码

[https://glitch.com/embed/#!/embed/zuix-app-1?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/zuix-app-1?previewSize=100&path=index.html)

为了在点击菜单按钮时采取一些动作，我们将在`index.js`中定义的`options.menuButton`对象“传递”给`data-ui-options`属性，就像这样

```
 menuButton: {
    on: {
      'menu:open': function() {
        // TODO: handle event
      },
      'menu:close': function() {
        // TODO: handle event
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们添加一个所谓的`DrawerLayout`，它基本上是一个包含应用程序菜单的面板，当单击菜单按钮或从左向右滑动手势时，它会显示出来。

```
<div data-ui-load="@lib/controllers/drawer_layout"
  data-ui-options="options.drawerLayout">

  <!-- Add Navigation Drawer menu and content here -->

</div> 
```

Enter fullscreen mode Exit fullscreen mode

同样，对于抽屉布局组件，我们传递了`options.drawerLayout`对象，因此当抽屉打开/关闭时，我们也可以打开/关闭菜单按钮。

```
 drawerOptions: {
    on: {
      'drawer:open': function(e) {
        if (menuButton) menuButton.open();
      },
      'drawer:close': function(e) {
        if (menuButton) menuButton.close();
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

以下是真实的例子(从左边缘滑动或单击菜单按钮)

[https://glitch.com/embed/#!/embed/zuix-app-2?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/zuix-app-2?previewSize=100&path=index.html)

现在，如果我们在页面中放置一个很长的内容，我们可能希望当用户向上/向下滚动时，标题淡出/淡入。
为此，我们在`body`元素上加载了`header_auto_hide`组件。

```
<body data-ui-load="@lib/controllers/header_auto_hide"
      data-o-target="header-bar"> 
```

Enter fullscreen mode Exit fullscreen mode

这是这个移动应用布局的最后一块

[https://glitch.com/embed/#!/embed/zuix-app-3?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/zuix-app-3?previewSize=100&path=index.html)

接下来阅读:

[![genejams](../Images/ab410dc05437d55e52306d6bb995095d.png)](/genejams) [## 独特、可爱的网页

### { Gene } Jul 26 ' 183min read

#showdev #webdev #reusability #zuixjs](/genejams/unique-lovely-web-bits--dfk)