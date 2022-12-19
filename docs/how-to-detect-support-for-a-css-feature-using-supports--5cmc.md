# 如何使用@supports 检测对 CSS 特性的支持？

> 原文：<https://dev.to/moreonfew/how-to-detect-support-for-a-css-feature-using-supports--5cmc>

随着许多新的 CSS 特性被定期引入，在我们使用它们之前检查对这些新的 CSS 属性的支持变得很重要。虽然有 Modernizr 等特性检测工具，但并不是每次我们都需要加载一个 JavaScript 文件来单独检测一个特性。您也可以使用 CSS `@supports`规则进行特征检测。但是，请注意，旧版本的浏览器也不支持`@supports`规则。在这种情况下，我建议您用 JavaScript 编写小代码片段来检测对您正在寻找的特性的支持。

## 如何使用`@supports` CSS 规则？

CSS 规则是其他 CSS at 规则之一，如@media 等。在定义的 CSS 属性块被应用到页面之前，`@supports`规则允许您检查对 CSS 特性的支持。换句话说，就像是说，如果“某某”特性被支持，就将这些 CSS 属性应用到页面上。让我们来看一个 CSS `@supports`规则的用法示例。

```
@supports (display: flex) {
  .main-box { display:flex; padding:5px;}
} 
```

Enter fullscreen mode Exit fullscreen mode

当浏览器解析上述代码时，它会检查浏览器是否支持`display:flex`属性，如果支持，它会考虑`@supports`规则中的 CSS 包装块。这可以使您的代码更简单、更轻便，因为这种特性检测不需要 JavaScript。有趣的是，`@supports`规则也有其他操作符，这在进行特征检测时给了你更多的权力。

## 符，表示`@supports`支持

类似于媒体查询(@media)，`@supports`也有三个操作符:

### `not`符

“not”操作符，顾名思义，就是检查指定 CSS 属性的**不支持**。这可以方便地编写一个后备版本。比如:

```
@supports not (display: flex) {
  .main-box { display:inline-block; padding:5px;}
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们可以注意到，如果浏览器不支持`display:flex`，那么保持显示为`.main-box`的内嵌块。

### `and`符

**和**操作符可用于检查某个属性“和”其他一些属性是否受支持。比如:

```
@supports (display: flex) and (align-items: center;){
  .myDiv { display:flex; padding:5px;align-items: center;}
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，只有当两个条件都为真时，CSS 块才会被渲染。您也可以将`and`运算符与`not`运算符结合使用。比如:

```
@supports (display: inline-block) and (not (display:grid)){
  .myDiv { display:inline-block; padding:5px;}
} 
```

Enter fullscreen mode Exit fullscreen mode

### `or`符

`@supports`支持的另一个操作符是 **`or`** 操作符。顾名思义,`or`操作符可以用来检查浏览器是否支持两个或多个 CSS 属性中的任何一个。例如:

```
@supports (display: flex) or (display: grid;){
  /* Block of CSS here */
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，即使浏览器支持其中一个功能，也会执行 CSS 块。您也可以使用此运算符与其他运算符组合。例如:

```
@supports not ((transform-style: preserve) or (-webkit-transform-style: preserve)) {
  /* Block of CSS here */
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你注意到了，我们需要正确地使用括号来定义它们的应用顺序。<mark>括号在同时使用`and`和`or`运算符</mark>时尤为重要。

总之，我认为`@supports`规则是 CSS 中一个非常有用的特性。然而，包括 IE11 在内的旧浏览器仍然不支持它，因此，您需要小心使用它。然而，如果你不支持老版本的浏览器，那就试试吧。你可以在 CanIUse.com 的[查看浏览器支持。请告诉我们您的使用体验。](https://caniuse.com/#feat=css-featurequeries)

如何使用@supports 检测对 CSS 特性的支持？最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。