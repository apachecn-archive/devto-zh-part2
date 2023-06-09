# 在 SCSS 写 CSS 变量更快

> 原文：<https://dev.to/meduzen/write-css-variables-faster-in-scss-1mon>

**更新**(2022 年 10 月 20 日):虽然这个助手仍然很酷，但我已经决定不在个人项目之外使用它。与 IDE 自动完成相比，它的好处可能不值得与任何额外的抽象相关的认知负荷。

* * *

我喜欢 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/var) ( [这是为什么](https://dev.to/meduzen/comment/6k2d))但是语法对我来说不是很可读:

*   名字里有这个双连字符的东西:`--varName`；
*   访问它需要`var`函数:`color: var(--myColor);`。

疯狂。让我们简化一下…

## `v()`功能！

```
@function v($var) {
  @return var(--#{$var});
} 
```

Enter fullscreen mode Exit fullscreen mode

*   [Codepen 上的演示](https://codepen.io/meduzen/pen/YRyEPe?editors=1100)
*   [NPM 套餐](https://github.com/meduzen/v-helper)！

## 用法

首先，正常定义你的 CSS 变量:

```
:root {
  --primary: #000;
  --bg: #fff;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，配合`v(varName)`使用。

```
html {
  background: v(bg);
  color: v(primary);
} 
```

Enter fullscreen mode Exit fullscreen mode

它生成这个:

```
html {
  background: var(--bg);
  color: var(--primary);
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然只有 4 个字符，但对于可读性来说这是一个很酷的方法，你会经常使用它。

就是这样！

## 可选第二个参数作为回退

正如[在评论中愉快地提到的](https://dev.to/equinusocio/comment/6kda)，CSS `var()`函数可以将一个回退作为第二个参数:如果想要的 CSS 变量没有定义或者没有浏览器可以理解的值，将使用这个默认值。

所以，让我们相应地更新`v()`:

```
@function v($var, $fallback: null) {
  @if($fallback) {
    @return var(--#{$var}, #{$fallback});
  } @else {
    @return var(--#{$var});
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们编写一些受益于可选回退的内容，比如“将视频钉在角落”功能:

[https://codepen.io/meduzen/embed/EOPoao?height=600&default-tab=result&embed-version=2](https://codepen.io/meduzen/embed/EOPoao?height=600&default-tab=result&embed-version=2)

## 一点点乐趣

您知道`--`是 CSS 自定义属性的有效名称吗？不幸的是，在 SCSS 很难申报和使用它。但是`v()`助手[让你的生活稍微轻松一点](https://github.com/meduzen/v-helper#---is-a-valid-custom-property-name)。✌️

## 其他本来很酷的尝试，但是…

如果你想知道如何改进我的方案，我把三次失败的尝试整理成一个概要。随时提出 CSS 变量提示！