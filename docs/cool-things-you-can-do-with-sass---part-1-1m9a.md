# 用 Sass 可以做的很酷的事情-第 1 部分

> 原文：<https://dev.to/lhuria94/cool-things-you-can-do-with-sass---part-1-1m9a>

过去两年我一直在使用 Sass，现在我是它的超级粉丝。尽管我们在编写 CSS 方面做得很好，但它从来没有给我们 Sass 提供的那种灵活性，就像随着我们的应用程序变得越来越丰富，管理样式表的复杂性一样。不管怎样，关于我的经历已经说得够多了，因为今天我们已经讨论了很多很酷的事情！

## 什么是 Sass？

这是一个 CSS 预处理器，如果你开始谷歌搜索，你会得到它，这是真的，但要保持这种想法，因为还有更多。

Sass 允许您使用变量、嵌套、继承等功能，并且所有功能都与 CSS 兼容。当你想减少重复，让你庞大的样式表更有条理的时候，这真的很有帮助。

重要提示:它不是 CSS 的替代品，它是一种解释器/传输器，实际上它最终会把 CSS 吐出来。

你一定在想，那 Sass 和 Scss 有什么区别？

Sass 本身被刻成两种类型的语法/文件类型:`.sass` & `.scss`。

Sass (较老的语法)不太被大多数人接受。原因主要是因为它是基于缩进而不是大括号的，并且它不需要分号，这增加了书写时出现人为错误的可能性。

Where **Scss** (新语法)被广泛接受，因为它与我们编写 css 的方式非常相似。使用 Scss 是一种双赢，因为我们可以编写 css 这样的样式表，但我们仍然可以获得 Sass 的好处。厉害！

> 从现在开始，本文将假设您理解 Sass 并精通其基础知识和实际用法，如果没有，请参考[本](http://sass-lang.com/guide)了解更多信息。

事不宜迟，让我们来看看在编写 SCS 时可以使用的很酷的东西:

## 使用变量作为选择器

我们可以定义我们的选择器并在样式表中使用它。
例如:

```
// Define your base selector.
$header-base-class: '.header';

#{$header-base-class} {
  background: white;
}

// Compiles to css
.header {
  background: white;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，这可能有很多非常有用的用例！比如嗯..在媒体询问中？我们来过一遍:

```
$small-only: 640px (Define somewhere else like maybe in _settings.scss)

@media (max-width: #{$small-only}) {
  // Here do your thing, the styles will only be applied to viewport < $small-only breakpoint.
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吗？😃

现在，您不必在每个媒体查询中定义硬编码的值。就像在这种情况下，只需在一个地方改变，就可以在任何地方得到结果。

## 父引用使用前缀

你一定很熟悉我们常用的`&`与号前缀。这种语法通常允许您参照父代嵌套嵌套选择器修饰符。

例如，你想在鼠标悬停时改变一个 div 的背景颜色。

```
.highlight {
  &:hover {
    color: red;
  }
}

// Compiles to css
.highlight:hover {
  color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是`&`也可以仅仅作为前缀来引用它的父节点。

假设你有一个基本的选择器，并且想只在有特定的类选择器存在时应用特定的样式，通常，我们倾向于这样做:

```
$base-item-selector: '.base-selector';

#{$base-item-selector} {
  &--conditional-selector {
    #{$base-item-selector} {
      &__details {
        display: none;
      }
    }
  }
}

// Compiles to css
.base-selector--conditional-selector .base-selector__details {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

有了父引用前缀，我们可以修改为:

```
#{$base-item-selector} {
  &--conditional-selector & { // See the usage of ampersand here
    &__details {
      display: none;
    }
  }
}

// This will also compiles to similar css.
.base-selector--conditional-selector .base-selector__details {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

很酷，对吧？节省了一个深度，看起来更有条理。

## 定义变量为默认值

使用 Sass，我们可以在`!default`标志的帮助下设置变量优先级和范围。
将`!default`放在声明的末尾实际上做了以下事情:

*   如果你定义了一个变量为`null`，它将被认为是未赋值的，将被赋值为`!default`。
*   如果变量已经被赋值，那么它将不会被重新赋值。

迷茫？好吧，让我们把它说出来。

例如，我们将文本颜色定义为红色，正常过程如下:

```
// _my-component.scss
$text-color: red !default;

a {
  color: $text-color;
}

// Compiles to css
a {
  color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止还说得通，对吧？好，我们继续。因此，现在您可以在导入组件样式时覆盖颜色。

```
$text-color: blue;
@import ‘my-component’;

// Compiles to css
a {
  color: blue;
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`!default`标志只有在值没有被预先赋值/实例化的情况下才起作用，否则它将被覆盖。

这是编写模块化 CSS 的一种重要方式。这就是为什么我们倾向于在一个文件中定义我们的大多数变量，以获得更好的可访问性。当使用 mixins 和插件类型代码时,`!default`标志非常有用。

你可以在这里找到一个更有趣的例子

## Mixins 和自定义函数

混合和函数都存在于同一个家族中，因为它们都接受变量作为参数，但是它们的职责有所不同。大家一个一个讨论吧。

Mixins: Mixins 可以接受参数并进行所需的计算，但是 mixin 的输出将是一个 CSS 规则。

用`@include`调用时会自行展开。

例如，假设我们需要在应用程序中定义不同的字体。那么创建 mixins 会更有意义:

```
// _my-typography-rules.scss
@mixin my-typography-small {
  font-size: .75rem;
  font-weight: 300;
  letter-spacing: .01em;
  line-height: 1;
}

// _my-component.scss
#{$base-selector} {
  @include my-typography-small;
}

// Compiles to
.base-selector {
  font-size: .75rem;
  font-weight: 300;
  letter-spacing: .01em;
  line-height: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

**函数:**它非常类似于 mixins，但是在输出的情况下，它返回一个值。就像我们在其他语言中都有自定义函数一样，在 sass 中使用自定义函数对您的应用程序来说是非常健康的。

自定义函数的返回值可以是任何数据类型。例如数字、字符串、布尔值等。

我们这里有一个最好的例子，就是根据基本单元大小计算 rem。我们在应用程序中广泛使用了这一点，它可以在任何地方使用。非常通用！

比方说，我们使用字体大小的 rem 单位，每次使用 rem 单位时，我们都必须进行手工计算。糟透了。！不是吗？

我们如何真正计算出`rem`是通过除以目标`size/base size` = `your result`。所以下面的函数会给我们想要的结果，我们可以在整个应用程序中使用它。

```
//_utility.scss
/**
 * Calculate rems based on a base unit.
 */
@function remCalc($size, $base) {
  $remSize: $size / $font-base-size;
  @return #{$remSize}rem;
}

// _my-component.scss
a {
  font-size: remCalc(20, $font-base-size);
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望现在你明白了为什么自定义函数非常有用，因为它们帮助我们避免了重复计算。SCSS 给了我们大量的内置函数，你可以在这里看一看

最好是先检查内置函数，而不是每隔一段时间就创建自己的函数。

## 默认参数

现在是我们讨论默认参数的时候了，因为我们刚刚包装了 mixin 和自定义函数。他们有点关系。

Mixins 和自定义函数都支持参数，锦上添花的是我们还可以定义默认参数。

例如:

```
@mixin abc-with-margin($x, $y, $z, $margin: 20px) {
  // Do your stuff with x, y, z.
  margin: $margin;
}

// _my-component.scss
#{$selector} {
  @include abc-with-margin(12, 23, 34);
}

// Compiles to css
.selector {
  // Stuff we did with x,y,z.
  margin: 20px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以覆盖默认值。例如

```
// _my-component.scss
#{$selector} {
  @include abc-with-margin(12, 23, 34, 10px);
}

// Compiles to css
.selector {
  // Stuff we did with x,y,z.
  margin: 10px; // Overridden.
} 
```

Enter fullscreen mode Exit fullscreen mode

## 扩展你的选择器

使用 Scss，您可以在 css 输出中组合选择器的样式。由于我们从父类或其他类继承属性，这与其他编程语言非常相似，但过程非常不同。同样，目的是重用不同的选择器样式，这样我们就不必重写已经在使用的代码。

```
.menu {
  background-color: white;
}

.header-menu {
  @extend .menu;
  color: black;
}

// Compiles to css
.menu, .header-menu {
  background-color: white;
}

.header-menu {
  color: black;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们现在看到的,`.header-menu`拥有`.menu`的所有属性以及它已经拥有的属性。传统继承权？不管怎样。菜单将定义它将被`.header-menu`继承，也可以很容易被覆盖。

你一定在想，为什么我们不能在这里使用 mixins，然后就完事了，为什么是另一种逻辑。我会告诉你为什么不。

**如果我们跟着 mixins 走:**

```
@mixin menu {
  background-color: white;
  font-size: 1rem;
  font-weight: bold;
  color: grey;
  padding: 20px;
}

.header-menu {
  @include menu;
  color: black;
}

.footer-menu {
  @include menu;
  color: black;
}

// Compiles to css
.header-menu {
  background-color: white;
  font-size: 1rem;
  font-weight: bold;
  color: grey;
  padding: 20px;
  color: black;
}

.footer-menu {
  background-color: white;
  font-size: 1rem;
  font-weight: bold;
  color: grey;
  padding: 20px;
  color: black;
} 
```

Enter fullscreen mode Exit fullscreen mode

看看最后一个属性有什么不同，只要菜单类型的数量增加，就会导致越来越多的重复样式，每种类型的菜单都会不断增加。这个问题可以通过扩展你的选择器来解决。

另一个优点是它可以和 UI 库一起使用，比如 foundation，bootstrap 等等。一个有趣的阅读是我们如何在这里扩展复杂的选择器。

## 占位符

我们将借助前面的例子，我们刚刚创建了一个`.menu`选择器，以便子选择器可以继承样式。但是，如果这个选择器甚至不存在，或者没有在任何地方被使用，而只是通过它的子类使用，那该怎么办呢？我们不妨摆脱它。

在我们继续之前，先澄清几件事:

*   `.menu`被认为是一个阶级。
*   `#menu`被认为是 id。
*   `%menu`被视为占位符。

这样我们就不会混淆了。

这将导致与前一个相同的输出。
例如:

```
%menu {
  background-color: white;
}

.header-menu {
  @extend %menu;
  color: black;
}

.footer-menu {
  @extend %menu;
  color: gray;
}

// Compiles to css
.header-menu, .footer-menu {
  background-color: white;
}

.header-menu {
  color: black;
}

.header-menu {
  color: gray;
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一的区别是菜单从输出中消失了，因为它只是一个占位符选择器。这真的很有帮助，因为您使用的是应该只输出到结果 CSS 中的东西。欲了解更多信息，请阅读此处的占位符。

好了，我们结束吧。我想在这里也讨论一下控制指令，但是这本身就是一个很大的话题。我们将在我的下一篇博客中解决这个问题。敬请期待！

感谢阅读。😃

访问我的博客，查看最初的[帖子](https://lhuria94.github.io/cool-things-you-can-do-with-sass-part-1/)。