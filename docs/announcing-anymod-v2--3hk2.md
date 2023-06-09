# 宣布 Anymod v2🎉🎉🎉

> 原文：<https://dev.to/tyrw/announcing-anymod-v2--3hk2>

我们已经发布了 **[Anymod v2](https://anymod.com)** ！

成千上万的开发者现在使用 Anymod 的在线编辑器来构建模块化功能，并将其添加到任何网站上。

[![](img/c9d2f5fbc7d8b6dc06e22c8c0cea4d25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DBhk2HZK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/component/image/upload/b_rgb:1f2223%2Cc_pad%2Ch_250%2Cw_600/v1540832245/landing_uq0qgl.gif)

那么 **v2** 有什么新内容？

## Mods 加载速度比⚡快 5-10 倍

在 **v2** 中，我们已经完全重新设计了 mod 被发送到网页的方式，最终结果是加载时间显著加快。

对于典型的页面:

*   v1:100-200 毫秒内的 mod 数据加载
*   **v2:10-20 毫秒内的 mod 数据加载**

**v2** 脚本大量使用缓存，在 DOM 完全加载之前，脚本立即发送缓存数据的请求。一旦接收到数据并且 DOM 准备好了，mod 就会被渲染。

mod 也以 JS 文件的形式交付，而不是通过 AJAX 请求，这消除了多次往返的 DNS 和 OPTIONS 请求，节省了更多的时间。

## 使用任何 JS 框架(或者不使用)💻

### 删除了对 Vue 的依赖

对于 **v2** ，我们已经去除了对 Vue.js 的依赖，现在你可以在构建 mod 时使用普通的 JavaScript 或者任何你想要的框架(包括 [Vue](https://guide.anymod.com/examples/vue.html) 、 [React](https://guide.anymod.com/examples/react.html) 、 [Angular](https://guide.anymod.com/guide/angular.html) 和 jQuery)。

这也意味着底层的 Anymod 脚本更小，加载更快(以前是 41kB，现在是 11kB)。

### HTML 预编译带手柄

现在，您可以使用 Handlebars 语法将内容直接添加到您的 mod HTML 中，所以您不需要依赖框架来获取 HTML 中的可编辑内容。

```
<!-- Old syntax (based on Vue.js) -->
<span v-text="message"></span>

<!-- New syntax (handlebars) -->
{{ message }} 
```

Enter fullscreen mode Exit fullscreen mode

## 超过 100 种新模式

最后，我们在库中添加并分类了 100 多个新的、随时可用的 mod。快速补充:

*   [图片库](https://anymod.com/library?tag=gallery)
*   [内容管理(CMS)](https://anymod.com/library?tag=content)
*   [表格](https://anymod.com/library?tag=form)
*   [导航](https://anymod.com/library?tag=navigation)

看图书馆里最受欢迎的 mod。

-

Anymod 的目标是帮助开发者和团队构建 web。我们认为 v2 将使您的工作流程更快更简单，希望您喜欢！