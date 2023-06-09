# 如何用 CSS 垂直对齐任何东西

> 原文：<https://dev.to/flores8/how-to-vertically-align-anything-with-css-i78>

无论你是一个成熟的网站开发者还是网站创建新手，你可能会认为你知道如何垂直排列… [一分钟](http://www.quickmeme.com/meme/3rdv7x/)。但是别担心，我们都会遇到这种情况。

以下是我最喜欢的三种在 css 中垂直对齐的方式。

### 变换

如果你从事 web 开发已经有一段时间了，你肯定对这种方法很熟悉。在 flexbox 或 css grid 出现之前，这是在 css 中垂直对齐的唯一可靠方式。不要让那愚弄你，我今天仍然使用它。

```
.parent {
    position: relative;
}
.child {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你发现使用这种方法时，元素变得模糊，因为它们被放置在半个像素，尝试这个:

```
 .parent {
    position: relative;
 }
 .child {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: perspective(1px) translate(-50%, -50%);
 } 
```

Enter fullscreen mode Exit fullscreen mode

### Flexbox

要使用 flexbox 居中一个对象，你只需要两个居中属性:

```
.element {
     display: flex;
     justify-content: center;
     align-items: center;
 } 
```

Enter fullscreen mode Exit fullscreen mode

### CSS 网格

这是一种简单的居中方式。这将使该元素的直接子元素居中。如果，这个元素中有五个子元素，每个子元素有两个子元素。它将只使五个子节点居中(而不是嵌套在内部的两个子节点)。

```
.element {
   display: grid;
   place-items: center;
   height: 100vh; // or however tall you want your box to be
 } 
```

Enter fullscreen mode Exit fullscreen mode

### 仅对齐一行文本？

如果你只是想在某个元素中垂直对齐文本，那就简单多了。你所需要做的就是给文本和它所在的容器一样的行高。

例如，如果文本在一个按钮内，而该按钮的高度为 32px。只需将文本的行高设为 32px，它就会垂直居中。

```
.button {
    height: 32px;
 } 
 .text {
   line-height: 32px;
 } 
```

Enter fullscreen mode Exit fullscreen mode

我希望这有助于你在 CSS 中居中处理事情。你最终选择了哪种方法？让我知道。