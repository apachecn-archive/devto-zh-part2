# :焦点在内

> 原文：<https://dev.to/prestonjlamb/focus-within-54p>

我已经有一段时间没有真正花时间使用 CSS 了，但是最近我不得不在工作中构建一个模板。这需要一点时间来恢复，但我已经学到了很多很酷的新技巧。好吧，至少从我最后一次做这些以来是新的。然而，我想在这篇博文中强调的是 CSS 伪类`:focus-within`。

`:focus-within`类似于伪类`:focus`,当一个元素有焦点时，它将某些样式应用到你的站点。不同的是`:focus`只适用于单个元素。当单个元素被聚焦时，样式被应用。然而，使用`:focus-within`，可以放置在父元素上，当任何子元素或父元素本身被聚焦时，样式将被应用。

当一个孩子`input`被聚焦的时候，当你想要设计一个`form`的样式时，这真的很方便。或者，在我的例子中，当用户输入一个`input`字段时，显示一个下拉菜单。在我正在处理的案例中，我想要一个下拉菜单，当一个人输入输入内容时，它会显示一些建议。`:focus-within`让这变得非常简单。以下是我的搜索栏的相关 HTML 部分:

[![HTML demo code for the :focus-within dropdown](../Images/a8ab7d0f5de2bfc43897bf6bc05f306e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GJRXFy2K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestonlamb.com/static/images/blogPosts/focus-within/focus-within-html.png)

这是让一切运转的 SCSS:

[![SCSS demo code for the :focus-within dropdown](../Images/9b8bb341b8e03170d9752da25bf3845e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0ojC1kPT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestonlamb.com/static/images/blogPosts/focus-within/focus-within-scss.png)

如您所见，完成这项工作真的不需要太多 HTML 或 CSS。成品如下所示:

[![A GIF showing the dropdown show and hide when focus is placed in the search box.](../Images/7a6afdf88d703c4835ec93e94b419b07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yJDA9FlK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.prestonlamb.com/static/images/blogPosts/focus-within/focus-within.gif)

有很多方法可以达到这种预期的输出，也有很多应用。但是我惊讶于这是多么的简单，我能如此迅速地实现它。我肯定会推荐给任何正在寻找类似功能的人。目前它只在 Chrome、Firefox 和 Safari 上可用，但如果你能做到这一点，那就完美了。