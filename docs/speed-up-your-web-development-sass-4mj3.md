# 加速你的网络开发:Sass

> 原文：<https://dev.to/stiv_ml/speed-up-your-web-development-sass-4mj3>

这是一系列文章中的第一篇，旨在通过给你提供有用的、业内公认的工具和技巧，让你在开发网站的同时，生活更轻松，提高你的工作效率。

* * *

场景很简单，你正在开发一个网站或 web 应用程序，当然你希望它看起来尽可能好，这显然是通过使用 CSS 来实现的。然后，您发现自己在重复标记名称、属性、颜色，并且有一个巨大的 main.css 文件(或者在 HTML 头中有许多`<src rel="stylesheet">`标记？).如果你想改变网站的主色调呢？仅仅因为`#7a19a8`看起来比`#7211a0`稍微好一点，几十行就变了。

[![Imgur](img/a2e1d319457a647f9561ddfb264c53c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uaPxwHf5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6o46QvA.png) 
*甚至有区别吗？*

为了解决所有这些以及更多的问题，2006 年创建了语法上令人敬畏的样式表，Sass for the friends。Sass 是一种预处理器脚本语言，它被解释或编译成简单的 CSS。所有这些意味着 Sass 扩展了 CSS 语法，使用类似的结构，具有更多的功能。

### 用法

所有安装步骤都包含在 [Sass 的官方安装指南](https://sass-lang.com/install)中。在这篇文章中，我将向你展示一些基本的语法，这样你就可以从第一天开始就把你的 CSS 做得更好。

#### *变量*

变量是 Sass 的核心部分，前提是您在 Sass 中以有效数据类型的形式声明任何数据，并且您可以在整个样式表中使用它。下面是语法:

[![Imgur](img/7f8d33997e0a837b96ba953408a107a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--avMBr9wy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Ul2b42X.png)

#### *嵌套选择器*

使用 Sass 选择器嵌套，我们可以转换这个:

```
div {
  // Some properties for a div
} 
div span {
  // Some properties for a span tag inside a div
}
div p {
  // Some properties for a p tag inside a div
} 
```

Enter fullscreen mode Exit fullscreen mode

成:

```
div {
  // Some div properties

    span {
      // Some properties of a span tag inside a div 
    }

    p {
      // Some properties of a p tag inside a div
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，您可以将选择器嵌套在选择器中，以避免每次为了赢得特异性而需要更深入时重复标记。

你也可以添加伪元素，并用 **&** 符号修改它们内部的父选择器，这种方式:

```
div {
  // div properties
  &.steve {
    // properties of a div with steve class
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### *Mixins*

mixin 是一个 Sass 特性，它允许您定义可重用的代码片段，甚至允许参数，就像函数一样，以便修改其行为，而不必像`.margin-box-size-1`那样利用语义类。来看看他们:

```
@mixin box-size($size: 1em) {
    margin: $size;
    padding: $size;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`@mixin`定义一个 mixin，然后命名它，在本例中是`box-size`，并写下它将接收的参数，如果有的话。在这种情况下，我们还有一个默认参数`$size: 1em`。变量将在构建时被替换为它们的值，mixin 中的所有内容将被放置在样式表中使用的任何位置。通过使用 **[@include](https://dev.to/include)** 后跟 mixin 名称来调用 mixin 函数。

```
 div {
    @include box-size(3em);
}

// Will be replaced by

div {
    margin: 3em;
    padding: 3em;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### *控制指令*

借助 Sass，我们可以将常见的编程语句放入样式表中，使用控制指令`@if`、`@for`、`@each`和`@while`，我们可以整理、重复和提取一张地图或列表中的任何一段代码。让我们简单看一下每种语法。

#### `@if`

`@if`指令接受任何[sass script](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#sassscript)true 或 false 表达式，并执行任何适用的代码段:

```
@if (true) {
    // This code will always run
} @else {
    // this code will never run
} 
```

Enter fullscreen mode Exit fullscreen mode

#### `@for`

`@for`指令非常简单，它运行括号内的代码一定的次数。语法如下:

```
// We declare a variable $i for the iterator, which starts at [from] and ends in [trough] values
@for $i from 1 through 6 {
    margin: $i+px;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### `@while`

`@while`指令的工作方式与`@for`指令非常相似，不同之处在于执行循环的条件完全取决于我们，而不仅仅是一个增量操作符。

```
$execute: true;

@while ($execute) {
    @debug($execute);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 提示

会在构建时将任何值打印到控制台，顾名思义，它非常适合调试！

#### `@each`

最后但并非最不重要的一点是，`@each`的工作方式与其他指令略有不同，因为它遍历一个集合，比如一个映射或一个列表，而不仅仅是一个条件，并公开当前值供您使用。

```
$colors: red green blue;

@each $color in $colors {
    background: $color
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

就是这样！有了这个简单的工具，你就可以从现在开始编写更干净、更易维护的 CSS 了。

如果你有任何疑问，你可以在 twitter 上找到我，我的名字是 [@stiv_ml](https://twitter.com/stiv_ml) 。我将非常乐意回答任何问题！

这篇文章最初发表在 [Kaizen Softworks 博客](https://blog.kzsoftworks.com/)上，那也是我工作的地方！如果你想查看我的更多内容，你可以点击[这里](https://blog.kzsoftworks.com/author/pablo/)。