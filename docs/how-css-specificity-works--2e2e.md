# CSS 特异性如何工作🤹🏼‍♀️

> 原文：<https://dev.to/sait/how-css-specificity-works--2e2e>

我们已经知道 CSS 代表级联样式表。

**级联**的真正含义是浏览器首先决定哪个样式表需要应用于 HTML。就像我们看到浏览器默认的
链接是蓝色的。

我们来谈谈**特异性**

**specification**是我们声明时应用于 CSS 的权重。

权重实际上意味着我们如何将样式应用于特定的 HTML
元素。

比如我们使用 id 属性来定位 Html 元素，或者我们使用
类属性，有时我们使用直接的元素名称，比如 h1，p 来应用
样式。

这里的特殊性在于我们使用不同的选择器来应用样式。但是浏览器根据元素的权重来应用样式。

**以重量计**

*   首先是内联 CSS
*   第二个 id
*   三级
*   第四元素/伪类/伪选择器

如果我们用选择器对元素应用样式。如果我们只是放置一个内联 CSS，它会覆盖之前使用 id、class、element 应用的所有样式。

# (重要的从不推荐)

如果加上！对我们所有的选择者都很重要。在我们添加元素之前，重要的覆盖了所有其他的样式。

我清楚地展示了这支笔，通过删除重要的内联 CSS、id 等来查看变化...

[https://codepen.io/saigowthamr/embed/KorNZa?height=600&default-tab=result&embed-version=2](https://codepen.io/saigowthamr/embed/KorNZa?height=600&default-tab=result&embed-version=2)

[在 Twitter 上找到我](https://twitter.com/saigowthamr)