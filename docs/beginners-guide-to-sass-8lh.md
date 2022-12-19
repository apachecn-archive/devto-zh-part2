# SASS 初学者指南

> 原文：<https://dev.to/akanksha_9560/beginners-guide-to-sass-8lh>

# 萨斯入门指南

面对现实吧，写 css 很乱。当审查别人写的代码时更是如此。我发现 SASS 是这个问题的答案。它帮助你写出清晰易读的代码。

让我们开始吧。让我们创建一个简单的 HTML，它有嵌套的 div，悬停，焦点，重复的 CSS 属性，你知道我们在日常生活中面对的标准 HTML。

> 理解我们为什么使用 classes**parent 1 _ _ link 1**是很重要的。它是 BEM(块元素修改器)方法的一部分，其中* * parent1 * *是块，而* * link1 * *是元素。BEM 遵循* * simple * *规则来命名和组织你的 CSS，并为项目中的每个人提供一个声明性语法，他们可以使用这个语法，这样他们就在同一个页面上了。
> 你可以在这里读到它

[![Example HTML Markup](../Images/e1f4422b1f3ed48acc507b0e98a38730.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FWIIHO0B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AzanUCVgJsz7WDMu1wq00YA.jpeg)*HTML 标记示例*

## 变量

让我们从为 header 写 css 开始。非常简单的东西，背景颜色和颜色属性。

[![CSS for header class](../Images/671530023cc16d3a137f784a971facb5.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--rQ6Pfznm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2702/1%2AVU4eN80-fTIESW6IDJsNCw.jpeg) * CSS 为表头类*

在 SASS 的帮助下，我们可以将** *颜色代码更改为变量** *，以便重复使用。因此，让我们使用** *$(这对于在 SASS 中创建变量很重要)为我们的颜色代码创建变量(您可以使用[这个](http://www.color-blindness.com/color-name-hue/)来为颜色变量命名)。***我们新的头部 css 将如下所示。我们也可以对很多东西使用变量填充，空白，颜色基本上任何你想重复使用的东西。

[![Use variable for reusable properties](../Images/f9963f8cd88a0f1236d5ee6d804a5433.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--avZB_iHJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AZOfjSD9byucKBwzAK6ZTAw.jpeg) *为可重用属性使用变量*

## 进口

为了使 css 更加模块化，SASS 提供了导入功能。我们可以把所有的颜色名称，混合，常见的样式放在不同的文件中，然后导入。所以，让我们创建一个 *_color.scss* 文件。

> 记下** _color.scss**文件的名称。这被称为**部分文件**,意思是这个文件包含 CSS 的小片段，你可以将它们包含在其他 Sass 文件中。* *下划线* *让 SASS 知道该文件只是一个部分文件，不应该生成 CSS 文件

[![_color.scss file](../Images/defececf94dd0f3528ef263a9baa3cac.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--AjfGSCgI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2672/1%2AFdoOFhPo6WFLgJKVrrQE6Q.jpeg) * _color.scss 文件*

我们可以将这个文件导入到我们的主 scss 文件中。使用命令

> *@ import’。/_color.scss'*

并继续使用如上所述的颜色变量。

## 嵌套

接下来我们跳到** *parent1 ** (请参考上面的 HTML 标记)*和它的孩子。

### **问题陈述 1** :为 ***parent1 ** *和** *parent1__link1* **写 CSS

对于这个问题，我们将使用 *** & ** *来串联类** *parent1* **和 ***parent1__link1*** 。请参考下图(注意这里变量的使用)

[![](../Images/2bd158157d11f0639172bc14ff681f34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2gmMekRF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2198/1%2ANm2tjjwx9BfSQGRp7lwagw.jpeg)

### **问题陈述 2** :在**元素

 *对于这个问题，我们将使用 & **将伪类和元素连接到**parent 1 _ _ link 1 * *。**

> 注意:伪类用 ***& :*** 连接，伪元素用 ***& ::*** 连接

伪类和伪链接的综合列表可在[这里](http://www.growingwiththeweb.com/2012/08/pseudo-classes-vs-pseudo-elements.html)获得

[![Use of Pseudo classes in SASS](../Images/f7b622c439385e871d2455dc406b883a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--MjDJEypg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2014/1%2AH7oc3zJlyvc39-oUAHp2HA.jpeg) *利用伪类在萨斯*

## Mixins

如上所述，我们已经为 parent1 编写了 CSS。我们也将为 parent 编写相同的 CSS。

[![complete css file with styles for parent1 and parent2](../Images/bcc2c4ce63506b42c045d50ed076c41e.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--cR0D1vQ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2320/1%2AoU7OU3td-wXpAebfSxQc5Q.jpeg) *完整的 css 文件，样式为父 1 和父 2*

正如我们在上面的文件中看到的，parent1 和 parent2 共享许多 css 属性。我们可以使用*MIXINS 让这个 css 更干净，更简单，更易读。mixin 的工作原理如下:

我们使用 ***@mixin*** 关键字创建一个自定义的通用样式，如下图所示。它包含 parent1 和 parent2 共有的所有属性。

[![Mixin for common style of parent](../Images/c8e917a402aa9dd2cdbaedf18d202285.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--v_kB8WMn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2088/1%2AfcGfQnkhC5CFpFsd9AS9UQ.jpeg) *米欣为父*的共同风格

接下来，我们将 mixin 包含在父样式中。

[![Usage of Mixins](../Images/d667730ddbfe0ef28337f0c7f2d0aeb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KheHLKHr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2706/1%2AjGmb-5lw_Nfc4XQBRJ3Djw.jpeg)*Mixins 用法*

在这里，我为*链接样式*和*父样式*创建了 mixin。它使代码更加清晰易读。

这是开始使用 SASS 的基本指南。当然，SASS 提供了各种各样的功能，在编写 CSS 时，这些功能可以让您的生活更加轻松。你可以在这里查阅 SASS [的完整指南。](https://sass-lang.com/guide)*