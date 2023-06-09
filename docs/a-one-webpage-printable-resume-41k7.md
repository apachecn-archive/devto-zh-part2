# 一个网页，可打印的简历

> 原文：<https://dev.to/gonsie/a-one-webpage-printable-resume-41k7>

5 年多来，我一直把自己的简历放在一个可打印的网页上(2012 年[首次 git 提交](https://github.com/gonsie/gonsie.github.io/commit/c3b13b29035b6692d324b36d9e42ebf0db780361))。因为所有的样式和内容都是独立的，所以很容易使用。

我在 GitHub 上制作了一份简历模板[。它是基于](https://github.com/gonsie/Cthulu-Resume)[这本由](https://css-tricks.com/one-page-resume-site/)[克里斯·科伊尔](http://github.com/chriscoyier)原创的一页 CSS 技巧简历。

## 为什么要 HTML 和 CSS

我坚信风格和内容应该分开。在这里，简历内容与应用的样式是分开的(与所有结构良好的 HTML 和 CSS 页面一样)。虽然我确实认为 GUI 工具对视觉设计更有用，但 CSS 确实允许通过编程方式表达视觉设计。与 LaTeX 不同，LaTeX 隐藏了许多细粒度的布局控件，CSS 是一个功能齐全的布局控制器。更好的是，它了解内容呈现媒体的差异，允许此简历模板利用非打印项目。

这个项目的一个基本改进是重构 CSS 并抽象一些概念。这将更容易改变字体和整体比例(而不是弄乱严格的像素值)。

## 在教学中使用 Git

我使用模板作为向学生教授 git 的简单方法。在对 git 概念和命令进行了基本介绍之后，我让学生们使用模板 repo 并创建他们自己的简历。每个学生都可以添加或多或少的信息，他们还可以尝试 HTML 格式。事实上，这有一个额外的好处，那就是让他们上 GitHub 并使用 GitHub 页面。