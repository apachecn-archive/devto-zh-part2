# CSS 选择器回顾与例子

> 原文：<https://dev.to/willamesoares/css-selectors-reviewed-with-examples-3f29>

[![header-image](img/0b811665de5cdfe2d102b258a5ad5afb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vjjwCNCB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ei40ngfyeglatxwyr1mv.png)

您是否曾经发现自己很难使用 CSS 选择器来样式化一个元素，该元素是包含具有特定值的属性的元素的`nth`子元素的第一个兄弟元素，或者类似的东西？如果你使用过动态布局，你可能已经经历过了。

这是 CSS 选择器、属性和伪类的快速参考，并附有示例。我想在一个私人文档中这样做，供个人使用，因为那里有大量的 CSS 备忘单。但是，也许我会用一种不同的方式来表达，这样会更容易被其他人理解。那么，为什么不分享给大家呢？

对于这篇文章，在继续之前，有几个基本概念你必须知道，它们是选择器(类型、属性、id、类)、伪类、伪元素和组合子。

让我们开始吧。

#### 选择器

尽管 CSS 教程经常把我们在这里将要讨论的所有内容都看作 CSS 选择器，但是指出 CSS 样式表中每种类型的选择器都有一个特定的术语还是有好处的。

##### 类型选择器

这些选择器用于匹配 DOM 中的元素，比如`p`、`div`、`span`，甚至是定制的标签，如下例所示。

[![type-selector](img/6d37b9714ca678b188c0ead577182a2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BQsW6LjS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e1rqyczv2c9yajzmmgsm.png)T3】

```
custom {
  border: 1px solid black;
}

p {
  color: green;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<custom>Custom element</custom>

<p>Paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

##### 班级选择器

这些用于选择 DOM 中具有特定类作为属性的元素。为此，您必须在想要匹配的名称的开头添加一个`.`。

[![class-selector](img/4fb8d5ca41dd67873c756b8244a16758.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0tuPF1d3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/30w37ky2z3yzj0odb3lk.png)T3】

```
.bordered {
  border: 1px solid red;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<div class="bordered">
    Bordered element.
</div> 
```

Enter fullscreen mode Exit fullscreen mode

##### 属性选择器

这些将匹配具有相关联的特定属性的元素。这可用于匹配单个属性和与其值相关联的属性。对于这种类型的选择器，必须用方括号将属性名括起来。检查下面的例子。

[![attribute-selector](img/e52582f7b075efa020971b77255e6a41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PmRrEx61--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cl1778lj76pmyhgjhsbh.png)T3】

```
[name] {
  background-color: green;
}

[name="age"] {
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<input type="text" name="name" placeholder="Your name" />
<input type="number" name="age" placeholder="Your age" /> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，您可以注意到，最初，我们为所有具有属性`name`的元素创建了一个 CSS 规则。这将为您提供 HTML 代码中显示的两个输入，因为它们都具有属性。然而，在此之后，我们将创建一个更具体的规则，告诉浏览器对属性为`name`且值为`age`的元素使用红色背景，这将为您提供最后一次输入。

除了使用`=`之外，您还可以使用不同的运算符来匹配值。下面来看看吧。

##### `~=`

该操作符匹配由空格分隔的单词列表，其中一个单词就是所传递的值。

[![attr-selector-1](img/0abbe5bc7724549223d041023169437d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H67hfIjF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2awyk42hf3dab4z70zx4.png)T3】

```
[class~="bordered"] {
  border: 3px solid green;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<button class="btn bordered">Click Here</button> 
```

Enter fullscreen mode Exit fullscreen mode

##### `|=`

该操作符匹配属性值为传递的精确字符串或以后跟`-`的字符串开始的元素。

[![attr-selector-2](img/9e7d45dee18c8cf22ee594f4cb0758d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VL6TXx2n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ersfpgcr3l0xiz57ba8z.png)T3】

```
[class|="primary"] {
  color: green;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<button class="primary-btn">Click Here</button>
<p class="primary">Paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

##### `^=`

该操作符将元素与以传递的字符串开始的属性值进行匹配。

[![attr-selector-3](img/eeb7a7defcb85428206021c0acbaed04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0PoGjIcR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wfz4hd01mvzuky9d3o42.png)T3】

```
[class^="call"] {
  color: green;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<button class="caller">Caller</button>
<button class="called">Called</button>
<button class="calling">Calling</button> 
```

Enter fullscreen mode Exit fullscreen mode

##### `$=`

该运算符匹配属性值以传递的字符串结尾的元素。

[![attr-selector-4](img/5cd453f4b966c49ce9937b40108a17e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7RcchT6r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l6eaxa0l7m2vctmeju92.png)T3】

```
[class$="ed"] {
  background-color: pink;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<div class="required">
    <p>Required</p>
</div>
<div class="validated">
    <p>Validated</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

##### `*=`

该运算符至少匹配一次属性值包含所传递字符串的元素。

[![attr-selector-5](img/636bcb741577dca3808b34e97d35745d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--37gIGgQg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6zevpjaeqwaa8jha34ew.png)T3】

```
[class*="prim"] {
  background-color: pink;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<button class="btn-primary">Click here</button> 
```

Enter fullscreen mode Exit fullscreen mode

PS。:为了以不区分大小写的方式执行这些匹配，您可以向规则附加一个`i`，例如:`[attr="value" i]`。

##### ID 选择器

这些将匹配具有与之相关联的特定标识符的元素。对于这种类型的选择器，您必须在想要匹配的 ID 之前使用一个`#`。检查下面的例子。

[![id-selector](img/e4b0f9dc77b2e72f3a12b3257809aa71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Zpw9wiA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kmc7qb3l06fp9zrjhuc4.png)T3】

```
#d123 {
  font-weight: bold;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<div>block without ID</div>
<div id="d123">block with ID of d123</div> 
```

Enter fullscreen mode Exit fullscreen mode

##### 万能选择器

这个用来匹配页面中的所有元素。例如，它可以用来重置所有元素的边距和填充。但是，没有指出这一点，因为它会使页面的呈现过程过载。

[![universal-selector](img/d291fe72f985fd9a58d39d7e15c1367e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4XJ6HZuF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6m3ilmvypghgz9v4h1t5.png)T3】

```
* {
  border: 1px solid yellow;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<ul>
  <li>
    <a href="#">Item 1</a>
  </li>
  <li>Item 2</li>
  <li>Item 3</li>
  <li>Item 4</li>
  <li>
    <span>Item 5</span>
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

#### 伪类

伪类用于以更广泛的方式匹配内容，因为在几乎所有情况下，它们可以引用 DOM 中没有直接表示的元素。这些类型的选择器通常指的是元素的状态。因此，可以基于用户交互动态访问它们。典型的例子是`:link`和`:visited`伪类，它们用于根据链接是否被访问来设计链接的样式。为了使用伪类，您必须在类名的开头添加一个`:`。由于在本文中很难静态地描述这些选择器，所以我认为最好将它们放在 JSFiddle 中，这样如果你愿意的话就可以使用它们。在那里，您可以简单地取消您想要的 CSS 和 HTML 部分的注释，并在右侧选项卡中看到结果。点击[此处](https://jsfiddle.net/soawillb/tc5cgo50/85/)查看下面列出的选择器示例(有些在表格下面有解释，有些过于直观，不需要澄清)。

*   `:focus`匹配当前聚焦的元素，例如，当用户点击编辑输入或`textarea`元素时。

*   `:active`匹配用户点击一个元素的瞬间。只要用户在元素上按住鼠标，就会应用该样式。

*   `:hover`匹配鼠标当前所在的元素。

*   `:empty`匹配没有任何子元素的元素。

*   `:valid`根据每个输入类型匹配包含合法值的输入。一个例子是，当您有一个电子邮件值的输入时，您可以在用户输入有效的电子邮件时应用一个样式。请注意，没有设置值的输入被视为处于有效状态。

*   `:checked`匹配当前选中的复选框输入。

*   匹配未访问的链接。

*   `:in-range`匹配值在特定范围内的输入元素。

*   匹配不是作为参数传递的每个元素。

*   `:first-child`匹配特定父代中的第一个子代。例如，如果你做了`p:first-child`,它将匹配其父段落中的第一段。

*   匹配给定类型的第一个元素。例如，如果你简单地使用`:first-of-type`，它将匹配特定父元素中第一个元素。

*   `:lang(language)`将属性为`lang`的元素与特定值进行匹配。

*   `:nth-child(n)`匹配特定父元素中的第 n 个元素。对于这些规则，您可以为索引指定一个整数值，例如，您可以传递类似于`2n`的东西来获取每两个索引位置中的元素。

*   `:nth-last-child(n)`与`nth-child`相同，但从末尾开始计数。

*   `:nth-of-type(n)`匹配特定父元素中给定类型的第 n 个元素。

*   `:nth-last-of-type(n)`与`nth-of-type`相同，但从末尾开始计数。

*   `:only-of-type`匹配特定父元素中该类型的唯一元素。例如，如果你做`p:only-of-type`，它将匹配唯一的现有段落，如果有多个段落，它将不匹配任何内容。

*   `:only-child`匹配特定父元素的唯一子元素。如果该父级中有多个子级，则没有匹配项。

*   `:optional`匹配没有`required`属性的输入元素。

*   `:read-only`匹配具有`read-only`属性的元素。

*   `:read-write`匹配没有`read-only`属性的元素。

*   `:required`用一个`required attribute`匹配元素。

*   `:root`匹配文档的根元素。这个选择器通常用于设置全局 CSS 变量。

#### 伪元素

伪元素可用于引用源代码中不存在的内容，即后来生成的内容。下面我们就来看看他们。如果您想查看本节的示例，请查看[这个小提琴](https://jsfiddle.net/soawillb/tc5cgo50/82/)。

*   `::after`和`::before`分别用于添加和样式化所选元素的最后一个和第一个子元素。这通常用于给标签、输入等添加工具提示或图标。

*   `::first-letter`选择元素内文本的第一个字母。

*   `::first-line`选择元素内文本的第一行。

*   `::selection`选择用户选择的当前文本。

#### 组合子

在我看来，组合子是 CSS 选择器中最有趣的部分。此时，您将能够收集到目前为止所学的所有内容，并将其与组合子相关联，以获得更复杂的 CSS 规则。让我们检查一下可用的 CSS 组合子。查看此处的示例[。](https://jsfiddle.net/soawillb/tc5cgo50/98/)

*   选择器组`,`当你想对几个元素应用相同的样式时，可以使用这个，所以不用为每个元素创建不同的块，你可以简单地用`,`列出它们，然后编写一次规则。

```
h1, h2, h3, h4, h5, h6 {
  margin: 1rem;
  text-decoration: underline;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的规则一次将`margin`和`text-decoration`应用于所有的标题标签。

*   后代选择器`一个空格可以用来选择一个特定块内(后代)的元素。`

```
.container .col {
  padding: .5rem 1rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面，我们给类为`container`的元素内的类为`col`的所有元素添加填充

*   子选择器`>`这个选择特定元素的直接子元素。它不同于空格组合符，因为使用它不能选择给定父代的孙代。

```
.container > ul {
  border: 1px solid black;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面我们选择了所有的`ul`元素，它们是`.container`块的直接子元素。

*   相邻兄弟选择器`+`这个组合器帮助选择给定元素的相邻兄弟。检查下面的例子。

```
.item2 + li {
  font-size: 2rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将字体大小`2rem`应用于`li`，它是`.item2`元素的直接兄弟。注意，这个和下一个组合子只向下看树。

*   通用兄弟选择器`~`这个组合器给你一个给定元素的所有兄弟。

```
.item2 ~ li {
  color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们选择所有的`li`元素，它们是`.item2`元素的同胞，再一次，向下看树。

暂时就这样吧！我希望这篇快速回顾对你有所帮助:)

PS。:这是一个动态文档，如果您有任何评论、提示或了解缺失的选择器，请在评论中告诉我，我会更新它。`