# 向您的 web 应用程序添加 ViewPager

> 原文：<https://dev.to/genejams/adding-a-viewpager-to-the-mobile-web-app-n67>

 <small>虽然这篇文章中的主题可能仍然是真实的，但是这些内容引用了一个旧版本的 *zuix.js* 库。最新文档见 [zuix.js](https://zuixjs.org) 网站。</small> 

* * *

## 什么是 ViewPager？

正如在 Android 开发者文档中所描述的，一个 T2 浏览器是一个

> 允许用户左右翻动数据页面的布局管理器。

## 用法

在页面的`head`部分包含`zuix.js`:

```
<script src="https://cdn.jsdelivr.net/npm/zuix-dist@1.0.0/js/zuix.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

添加 ViewPager，如下所述:

```
<div data-ui-load="@lib/controllers/view_pager"
     data-o-paging="true">
  <div><!-- Add page 1 content here --></div>
  <div><!-- Add page 2 content here --></div>
  <div><!-- Add page 3 content here --></div>
  <!-- etc.. -->
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 示例应用

示例应用程序设计最初的灵感来自于 *Twitter* mobile，但它可以用作不同类型项目的基础。

[![Web App 4-1](../Images/83ee9d4a785d454e1eea01f45aab3d27.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ijJ-kqsY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3mib65guw55oj39n83k.gif)

设计总结:

*   应答的
*   主`view_pager`在内容部分之间切换
*   侧面抽屉带有选项菜单，可通过点击用户头像打开
*   向上滚动时折叠页眉/页脚，向下滚动时展开
*   带有章节名称的页面指示器(只是另一个`view_pager`)

*故障*上完全可播放的信号源:

[https://glitch.com/embed/#!/embed/zuix-app-4?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/zuix-app-4?previewSize=100&path=index.html)

我推荐用 <small>**直接链接**</small>[zuix-app-4 . glitch . me](https://zuix-app-4.glitch.me)全屏尝试这个例子。

## 结构

该应用程序由一个主`index`和以下元素组成:

```
// a small component implementing a 'card'
// with a picture and random data

components/random_item.css
components/random_item.html
components/random_item.js

// a static list used in each ViewPager page and
// that holds a bunch of lazy-loaded 'random_item'

content/fake_list.css
content/fake_list.html

// the menu used in the side `drawer_layout'

content/user_menu.css
content/user_menu.html

// the header bar with a title, clickable
// user profile avatar and sections names

layout/header.css
layout/header.html

// a footer bar with links to main app screens

layout/footer.css
layout/footer.html

// the main app files

index.css
index.html
index.js 
```

Enter fullscreen mode Exit fullscreen mode

因此，`index.html`页面包括:

*   `layout/header`
*   `layout/footer`
*   `drawer_layout`从`content/user_menu`载入哪些内容
*   一个`view_pager`来实现页面指示器(tab layout)，带有节名( *Feed* ， *Week* ， *Month* 等..)
*   main `view_pager`,它保存包含填充有`random_item`的`fake_list`的每个部分的页面

我希望这是有意义的=)并且即使对于初学者来说代码也是容易理解的。如果没有，请留下您的评论，我会尽力改进这一点。

延伸阅读:

*   [查看器](https://zuixjs.github.io/zkit/pages/controllers/view-pager/)
*   [zuix.js](https://zuixjs.org)

阅读下一篇:

[![genejams](../Images/ab410dc05437d55e52306d6bb995095d.png)](/genejams) [## 使用 zuix.js 在浏览器中捆绑 Web 应用程序

### { Gene } Aug 1 ' 184 分钟读取

#showdev #webdev #javascript #zuixjs](/genejams/web-app-bundling-in-the-browser-with-zuixjs-4efm)