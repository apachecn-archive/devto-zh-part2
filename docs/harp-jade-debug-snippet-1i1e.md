# Harp/Jade 调试片段

> 原文：<https://dev.to/catchen/harp-jade-debug-snippet-1i1e>

最近在用[琴](http://harpjs.com/)配[玉](http://jade-lang.com/)。一开始，我很难搞清楚 Harp 在构建时使用的 JSON 数据结构。用 Jade 编写并在 Harp 编译时执行的 JavaScript 函数也很难调试。最后，我发现可以将 JSON 作为一个字符串转储到浏览器中的`console.log`。现在一切都简单多了。

现在我有了那个 [`debug.jade`](https://github.com/CatChen/catchen.me/blob/master/public/_partials/debug.jade) 文件在[我的项目](https://github.com/CatChen/catchen.me)里。每当我想在 Harp 中检查一些 JSON 数据时，我只需调用`!= partial('debug', { data: anything })`并传递正确的`data`。

[![](img/74b5b3a4bd3e6eeeb59220cf7608aa5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tcX_JzaN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/CatChen/English/%257E4/4ZBj4VXAuwU)