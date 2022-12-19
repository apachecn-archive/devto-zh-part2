# 在 CSS 中选择

> 原文：<https://dev.to/nickymeuleman/selecting-in-css-2bk4>

瞄准一个特定的 HTML 元素并不一定会引起麻烦。
许多人习惯于通过类(带点`.`)或 id(带十字`#`)进行选择。这些远不是 CSS 工具中的唯一工具。
我希望看完这篇博文后，你的工具箱会变大一点！

> 我意识到这篇文章中的大多数选择可以写得更简单。目的是展示不同种类的 CSS 选择器以及它们是如何工作的！

## CSS 家谱

当一个 HTML 元素在另一个元素的中被发现**时，它就是另一个元素的**后代**。

一个 HTML-element 是另一个元素的**子元素**，当它在另一个元素**中找到一个**单级时。**

好吧，那个解释听起来甚至让我感到困惑，我写了它！让我们看一个例子。

```
 <div class="quarter-finals">
        <div class="semi-finals">
            <div class="final">
                <div class="champion"></div>
            </div>
        </div>
    </div> 
```

在这个例子中，`quarter-finals`是所有其他元素的祖先。
`final`是孩子(所以也是后代！)的`semi-finals`。

```
 <div class="quarter-finals">
        <div class="semi-finals">
            <div class="final">
                <div class="champion"></div>
                <div class="runner-up"></div>
            </div>
        </div>
    </div> 
```

除了是祖先/父元素或后代/子元素，一个元素也可以是另一个元素的**兄弟**。
这是在另一个元素的同一级别找到**元素的情况。
`runner-up`是`champion`的同级。**

## 组合子选择器

可以利用这些特征来选择 CSS 中的某个元素。
说你要给四分之一决赛的所有后代一个背景色。你可以做类似于
的事情

```
.quarter-finals div {
  background-color: black;
} 
```

在`.quarter-finals`和`div`之间的那个**空间**就是**的后代组合子**。(花括号前的其他空间只是用来格式化的)

您可以像这样阅读选择器:“选择任何 div-element，它是具有类 quarter-finals 的任何元素的后代”。

让我们用红色覆盖`semi-finals`上的黑色背景。
为此，我们将使用**子组合子**。

```
.quarter-finals > div {
  background-color: red;
} 
```

在`.quarter-finals`和`div`之间的**插入符号**是**子组合子**。您可以这样阅读选择器:“选择任何 div 元素，该元素是任何具有 quarter-finals 类的元素的子元素”。

看起来有点悲伤，它离冠军头衔如此之近。

我们可以用相邻的**兄弟组合子**来选择它。

```
.champion + div {
  background-color: yellow;
} 
```

在`.champion`和`div`之间的**加上**就是**相邻兄弟组合子**。您可以像这样阅读选择器:“选择紧跟在具有类 champion 且具有相同父元素的元素之后的任何 div 元素。”

[https://codepen.io/MrNime/embed/QxaLMP/?height=600&default-tab=result&embed-version=2](https://codepen.io/MrNime/embed/QxaLMP/?height=600&default-tab=result&embed-version=2)

如果任何元素出现在`.champion`和`.runner-up`之间，因此它们不再直接跟随彼此，我们编写的相邻兄弟组合子将不再工作。这是使用**通用兄弟组合器**的好时机。

```
 <div class="quarter-finals">
        <div class="semi-finals">
            <div class="final">
                <div class="champion"></div>
                <span>won against</span>
                <div class="runner-up"></div>
            </div>
        </div>
    </div> 
```

这意味着我们的`.runner-up`将不再有黄色背景。它不再紧跟在`.champion`之后，所以我们上面写的 CSS 不会对它进行样式化。让我们修复它

```
.champion ~ div {
  background-color: yellow;
} 
```

`.champion`和`div`之间的**波浪号**是**通用兄弟组合器**。你可以这样理解这个选择器:“选择任何一个 div 元素，它跟在一个类 champion 的元素后面，并且有相同的父元素。”

## 伪类选择器

如果您想基于元素的**状态**或该元素内的**结构**(例如:只针对每个奇数元素)应用 CSS 规则，那么伪类就是为您准备的！你可以通过他们的冒号认出他们。

```
a:hover {
  color: white;
} 
```

上面的规则只针对被悬停的 a 元素。它属于**状态**部分。

如果我们想选择列表中的第一个元素，我们依赖于一个**结构**伪选择符，比如`:first-child`

我们忘了给上面的`.champion`一个特殊的颜色。

```
.final :first-child {
  background-color: gold;
} 
```

注意到`.final`和`:first-child`之间的间隔了吗？就是我们之前说的后代选择器。它应该在那里，因为**伪类只针对它附加到**的元素。

这个选择器很具体，如果一个不同的元素出现在`.champion`上面怎么办？

```
 <div class="quarter-finals">
        <div class="semi-finals">
            <div class="final">
                <span>in this match</span>
                <div class="champion"></div>
                <span>won against</span>
                <div class="runner-up"></div>
            </div>
        </div>
    </div> 
```

冠军辉煌背景色已经一去不复返了。如果元素是第一个子元素，它会选择它所附加的元素。你说，它不依附于任何东西(除了空白)？想一想是这样写的:`.final *:first-child`

不要为冠军而恐惧，我们可以选择某型的第一胎*搭配`:first-of-type`* 

```
.final div:first-of-type {
  background-color: gold;
} 
```

它不会在某件事开始时停止。你还得对面:`:last-child`和`:last-of-type`。
也可以用`:nth-child()`和`:nth-of-type()`选择特定位置的元素。

应该在这些双亲之间的是一个数字。或者计算数字的简单数学。

`li:nth-of-type(3)`将选择第三个列表项。不是第四个像很多习惯从 0 开始计数的程序员会想的那样！

[![arrays start at 0](../Images/b96cd031f77d0465c105798d5a785a31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vVXwhkIP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/VRSkSGd.jpg)T3】

```
li:nth-child(2n + 1) {
  font-weight: bold;
} 
```

将列表中的每个奇数元素加粗。浏览器运行这个简单的等式，输入 0，1，2，...代替`n`并使用结果数。

这些只是几个例子，我鼓励你在 MDN 上探索更多关于[伪类的知识。](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)

## 伪元素

与伪类密切相关的是伪元素。

其中**伪类**选择了一个**元素**。

**伪元素**将选择元素的**部分。**

语法也很相似。

对于伪类，你使用一个冒号`:`。

对于伪元素，你使用两个冒号`::`

许多浏览器允许你只使用一个冒号。仅仅因为你*能*并不意味着你*应该*。

```
span::first-letter {
  color: green;
} 
```

这段 CSS 将使我们示例中的 2 `spans`的首字母变成绿色。

还有更多选项，你可以在 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/pseudo-elements) 上阅读/了解它们。

## 属性选择器

您还可以根据元素的属性及其属性值来选择元素。如果你仔细想想，这确实是你在使用 ID( `#`)或 class( `.`)选择器时已经在做的事情。

```
 <div class="really-fast-drive lemans-winner tiny">
        Fernando Alonso
    </div>
    <div class="really-slow-driver non-lemans-winner tall">
        Nicky Meuleman
    </div> 
```

最简单的形式就是检查一个属性是否存在。

```
div[class] {
  /* Fernando Alonso is so tiny in real life */
} 
```

这将只针对具有 class 属性的 div。

匹配某个属性有精确值的元素

```
span[class="really-fast-driver"] {
  font-weight: bold;
} 
```

这将使阿隆索大胆的权利？不对！该值必须准确，我们只指定了一个类，而不是所有的类(它们也需要以正确的顺序工作。)

```
div[class~="really-fast-driver"] {
  font-weight: bold;
} 
```

这正合我意。

**波浪号**选择空格分隔列表中属性值为**的任意元素**

让我们选择 class 属性以`really`
开头的所有元素

```
div[class^="really"] {
  color: red;
} 
```

**向上插入符号**选择任何属性**以**开始的给定值的元素。
记住:如果`really-fast-driver`和`really-slow-driver`不是类列表中的第一个，这将不起作用！

```
div[class|="really"] {
  color: red;
} 
```

**管道**选择属性为**以**给定值开始的任何元素，可选的是**后跟破折号**。

```
div[class$="driver"] {
  background-color: black;
} 
```

**美元符号**选择任何属性**以**结束的给定值的元素。

```
div[class*="lemans"] {
  border: 1px solid blue;
} 
```

**星号**选择属性**包含给定值的**子串**的任意元素。**

#### 区分大小写

在右括号前包含一个`i`将使选择器不区分大小写。

```
div[class*="LeMans" i] {
  border: 1px solid blue;
} 
```

上面的选择器将匹配与示例中相同的元素，但没有大写字母和 I。

## 结合

当我们在关于伪类的部分中使用后代选择器时，我们已经尝到了组合的滋味。

你将如何为一个被选中的复选框选择标签？你有这个 HTML 要处理，作者没有遵循适当的可访问性指导方针，忽略了 [`for`属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)。

```
<input type="checkbox">
<label>Belgium wins the world cup</label> 
```

一种可能的方法是:

```
input[type="checkbox"]:checked + label {
  color: red;
} 
```

您可以这样阅读选择器:“选择任何前面有一个`input`的`label`元素。该输入应该将`checkbox`作为`type`属性的值，并且应该对其进行检查。”

CSS 规则只有在所有这些条件都满足时才会被应用！

## 甚至更多的工具

CSS 有许多更有用的特性，你可以碰到一些你在 CSS 中不熟悉的符号。
你甚至可能会考虑 CSS(预)处理器，比如[萨斯/SCSS](https://sass-lang.com/) 。你经常看到的一个普通 CSS 中没有的符号是&符号`&`。它用于引用父选择器(是的，在 CSS 中嵌套🤯).
CSS-tricks 有一篇很棒的[文章](https://css-tricks.com/the-sass-ampersand/)会帮助你理解它。

使用的来源:

*   [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors)
*   [CSS-招数](https://css-tricks.com/)
*   [CSS:权威指南](http://shop.oreilly.com/product/0636920012726.do)*