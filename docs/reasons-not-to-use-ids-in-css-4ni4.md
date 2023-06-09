# CSS Id vs 类在 CSS 中不使用 Id 的原因

> 原文：<https://dev.to/clairecodes/reasons-not-to-use-ids-in-css-4ni4>

有时当我审查代码时，我建议一位同事不要对 CSS 样式选择器使用 id，不可避免地会有人问为什么。就像早上 6 点在黑暗中排队、泡杯茶或化满脸的妆一样，在 CSS 中避免使用 id 是我不假思索的事情。我已经做了这么长时间而没有质疑它，以至于我花了一分钟才向其他人解释为什么。考虑到这一点，下面列出了我为什么避免在样式表中使用 id，而更喜欢使用类的原因。

## 1。类别特异性低于 ID 特异性

id 具有比类别高得多的特异性[。一个类本身不能覆盖属于一个 ID 的样式。为了“击败”ID，你需要更多的 ID 或者使用`!important`，这会在你的样式表中引发](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)[特异性战争](https://stuffandnonsense.co.uk/archives/css_specificity_wars.html)。你甚至可能需要重组你的 HTML。理想情况下，样式应该易于覆盖和扩展，以使 CSS 易于维护和使用。

## 2。类可以重用

id 在页面上应该是唯一的。这意味着，如果您将一个样式附加到一个 ID，您将无法在同一网页中重复使用它。然而，类可以出现在同一个页面上的几个 HTML 元素中。能够重用样式是 CSS 的优势之一。例如，仅仅使用 id 很难直接设计列表元素`<li>`的样式。诚然，并不是每种风格都需要重用，但是有这种选择是很好的。

## 3。一致的惯例

对于在代码库中工作的开发人员来说，知道 CSS 是附加到类上的，而 id 是用于其他目的更简单。只使用`class`属性来定义样式，比使用`class`和`id`属性的组合更容易让其他人理解。

## 4。一个元素可以有几个类，但只有一个 ID

你可以给一个 HTML 元素添加许多类，例如`<div class="text-bold banner-text js-banner-text"></div>`。这里，有用于样式的类，和一个用于 JavaScript 钩子的类，都在 class 属性中组合在一起。也可以使用这些约定来命名 id，但是请记住，每个元素拥有一个以上的[ID 是无效的。这意味着您不能像上面的类示例那样重载 ID 名称，例如`<div id="text-bold banner-text js-banner-text"></div>`。](https://www.w3.org/TR/2011/WD-html5-20110525/elements.html#the-id-attribute)

使用类的组合来设计元素是一种常见的模式。此外，对只由 JavaScript 或自动化测试使用的选择器使用命名约定是一个好主意。例如，如果我看到一个以`js-`为前缀的类，我知道它在 JavaScript 的某个地方被使用，并且不负责样式，在编辑它之前应该小心处理。(通常`qa-`前缀也被用作测试的挂钩。)我个人喜欢把我所有的钩子和样式类放在一个属性中，而不是在类和 id 之间共享。

## 免责声明

当然，这个建议附有通常的免责声明。你比任何随机的博客文章更了解你的代码和你的问题的背景，如果你认为用 ID 写 CSS 是有意义的，那么就继续使用 ID。id 在 CSS 样式表中使用是完全有效的。

### 注

这并不是说你不应该在 HTML 中使用 id。除了作为 CSS 选择器之外，id 在网页中还有很多用途。例如作为页面锚、片段标识符或将标签链接到表单字段。

### P.S

在研究这篇文章的时候，我发现了[这个 StackOverflow 的答案](https://stackoverflow.com/questions/1878810/is-there-any-pros-and-cons-if-i-use-always-css-class-instead-css-id-for-everythi)，其中*推荐*使用 id 来定义风格——这很好地提醒了前端 web 开发中的流行观点是可以改变的！