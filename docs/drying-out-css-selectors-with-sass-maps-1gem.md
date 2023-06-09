# 用 Sass 映射干燥 CSS 选择器

> 原文：<https://dev.to/clairecodes/drying-out-css-selectors-with-sass-maps-1gem>

## 第一次尝试:重复选择器

我最近写了一些类似这样的 Sass 代码:

```
$color-apple: green;
$color-lemon: yellow;
$color-strawberry: red;

.apple-button {
  background-color: $color-apple;
}

.strawberry-button {
  background-color: $color-strawberry;
}

.lemon-button {
  background-color: $color-lemon;
}

// This compiles to:

// .apple-button {
//   background-color: green;
// }
//
// .strawberry-button {
//   background-color: red;
// }
//
// .lemon-button {
//   background-color: yellow;
// } 
```

Enter fullscreen mode Exit fullscreen mode

(不，我不是在一个蔬菜水果商的网站上工作，水果主题只是为了举例！🍏🍓🍋)

乍一看，还行。

但是这段代码非常重复。除了名称和颜色值中的一个单词之外，每个选择器的结构是相同的。

此外，这种模式不能有效地扩展。如果我想添加一个`blueberry-button`和一个`grape-button`怎么办？我需要编写更多几乎相同的选择器。如果我想将颜色应用于字体而不是背景，该怎么办？我必须在多行中更改 CSS 属性。这是低效的！

我们怎样才能把这段代码写得更短更简洁呢？也就是遵循*的[干原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)不重复自己*？像这样:

## 重构:干选择器

```
$fruit-map: (
  apple: green,
  lemon: yellow,
  strawberry: red
);

@each $fruit, $fruit-colour in $fruit-map {
  .#{$fruit}-button--dry {
    background-color: #{$fruit-colour};
  }
}

// This compiles to:

// .apple-button--dry {
//   background-color: green;
// }
//
// .lemon-button--dry {
//   background-color: yellow;
// }
//
// .strawberry-button--dry {
//   background-color: red;
// } 
```

Enter fullscreen mode Exit fullscreen mode

一个 [Sass map](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps) 是一个键值对的集合，很像 JavaScript 中的一个对象。在这个例子中，`$fruit-map`是一张地图。果名是关键，颜色是价值。

我们可以使用 [Sass each 指令](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#each-directive)来迭代每一对。我们将键和值赋给变量，我们可以使用[变量插值](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#interpolation_)(也就是这个语法`#{$foobar}`)来构造我们的选择器。

这段代码伸缩性很好。如果我们想要生成一个新的选择器，我们只需要向映射添加一个新的键值对，例如`blueberry: blue`。如果我们想把颜色分配给字体而不是背景，我们只需要在一行中改变 CSS 属性，而不是很多行。太好了！

## 增强:多值

但是等等，我们可以更进一步！现在每个水果主题的按钮也有了不同的边框样式。我们如何调整我们的映射来包含每个键的多个值？试试这个:

```
$fruitier-map: (
  apple: (green, 1px solid black),
  lemon: (yellow, 2px dashed grey),
  strawberry: (red, 3px dotted orange)
);

@each $fruit, $fruit-colours in $fruitier-map {
  $bg-colour: nth($fruit-colours, 1);
  $border-style: nth($fruit-colours, 2);

  .#{$fruit}-button--fruitier {
    background-color: #{$bg-colour};
    border: #{$border-style};
  }
}

// This compiles to:

// .apple-button--fruitier {
//   background-color: green;
//   border: 1px solid black;
// }
//
// .lemon-button--fruitier {
//   background-color: yellow;
//   border: 2px dashed grey;
// }
//
// .strawberry-button--fruitier {
//   background-color: red;
//   border: 3px dotted orange;
// } 
```

Enter fullscreen mode Exit fullscreen mode

*   将每个键的值转换为映射。给地图添加颜色和新的边界值。
*   使用第[个函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#nth-instance_method)访问每个地图元素，并将它们分配给变量。
*   像以前一样，使用这些变量创建带有插值的动态 CSS 选择器。

最终的 Sass 代码易于扩展和维护，也是 dry。全面的 [Sass 文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)包含了关于这些特性和许多其他特性的更多细节。