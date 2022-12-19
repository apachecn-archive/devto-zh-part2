# 萨斯混合食品

> 原文：<https://dev.to/neshaz/sass-mixins-5917>

这是第三篇关于特定 Sass 特性的文章，这一篇是关于 mixins 的。之前我写过[导入特性](https://kolosek.com/sass-import)和[扩展特性](https://kolosek.com/css-extend/)，还有一篇是关于[嵌套](https://kolosek.com/nesting-in-less-and-sass/)的。

这个特性用在特定的场合，不要太频繁但是掌握了就太有必要了。

## [T1】@ mixin](#mixin)

定义该特征类型`@mixin`和你的创建名称。之后，在 CSS style - within `{}`中，键入所有你想用它定义的内容。

```
 @mixin creation-name{
    property1:value;
    property2:value;
    property3:value;
 } 
```

当它被定义时，你应该在某个地方使用它...要调用 mixin 特性，请在选择器中键入`@include`，后跟 mixin 名称。

```
 selector{
    @include creation-name;
 } 
```

将这段代码编译成 CSS 后，看起来会像这样:

```
 selector{
    property1:value;
    property2:value;
    property3:value;    
 } 
```

## 灵活 Mixin

mixin 允许你传入一个参数，使其更加灵活。这是通过在 mixin 名称后添加值来定义的。该值可以在 mixin 内部使用(只要您需要)。

```
 @mixin creation-name($value){
    property1:$value;
    property2:value;
    property3:value;
 } 
```

After mixin 是这样定义的，当它在`@include`和创建名称之后被调用时，您必须将该值转发给它。

```
 selector{
    @include creation-name(10px);
 } 
```

编译后...

```
 selector{
    property1:10px;
    property2:value;
    property3:value;    
 } 
```

**您可以在用`,`分隔的`()`中添加无限个值。**

## 用在什么地方

现在当你明白它是关于什么的时候，你一定会觉得它有特定的案例。每个使用 Sass(并正确使用它)的人都有自己的收藏，这是我的收藏:

### 伪

当使用`::before`和`::after`时，你总是需要这三个，所以我们在你每次使用它时都节省了两行代码。

```
@mixin pseudo{
    content: '';
    display: block;
    position: absolute;
} 
```

### 位置居中

块[水平和垂直](https://kolosek.com/css-position-relative-vs-position-absolute/)居中辅助(重要:你必须有一个带`position: relative`的父元素)。

```
 @mixin center {
    position:absolute;
    top:50%;
    left:50%;
    -webkit-transform: translate(-50%, -50%);
    -ms-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
 } 
```

### 占位符

我仍然相信 mixin 是为我们发明的，用于浏览器前缀。这只是其中的一个例子。

```
 @mixin input-placeholder {
    &.placeholder { @content; }
    &:-moz-placeholder { @content; }
    &::-moz-placeholder { @content; }
    &:-ms-input-placeholder { @content; }
    &::-webkit-input-placeholder { @content; }
 } 
```

### 断点

你可以使用断点或媒体查询，但无论哪种方式，你会喜欢这个。在这个例子中，使用了`@content` mixin 特征。你可以从例子中了解到*这个特性允许在`@include`* 里面添加 CSS。

```
@mixin bp-large {
    @media only screen and (max-width: 60em) {
        @content;
    }
}
@mixin bp-medium {
    @media only screen and (max-width: 40em) {
        @content;
    }
}
@mixin bp-small {
    @media only screen and (max-width: 30em) {
        @content;
    }
} 
```

如何使用它:

```
.sidebar {
    width: 60%;
    float: left;
    @include bp-small {
        width: 100%;
        float: none;
    }
} 
```

下面是一些其他的合集，你可以在:
[Andy.scss](https://github.com/gillesbertaux/andy) ， [bourbon](https://www.bourbon.io/) ， [npm](https://www.npmjs.com/package/scss-mixins-collection) 中找到很多有趣的东西，如果你想要 mixin 特性的高级选项可以查看[这篇文章](https://scotch.io/tutorials/how-to-use-sass-mixins)。

## 总结

制作你的收藏并混合起来！(其实就是把它复制/粘贴到每一个新的 Sass 项目中即可)。

最初发布于 [Kolosek 博客](https://kolosek.com/sass-mixins/)。