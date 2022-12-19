# UI 布局:自然大小与依赖大小

> 原文：<https://dev.to/mortoray/ui-layout-natural-versus-dependent-size--3lai>

元素有自然大小、从属大小或两者的组合。元素的大小会影响它的位置。UI 布局系统的用户和创建者必须对大小有共同的期望。

> 这篇文章是关于编写 UI 引擎的系列文章的一部分。

## 一个长方形

让我们先用一个基本的`Rectangle`来研究一下这些概念。没有任何其他规格，一个`Rectangle`没有任何尺寸。如果有人问你，它的自然大小是多少，你不会有答案，或者你可能会回答零。

现在对比一下`Rectangle{Width=100 Height=50}`。这个矩形有固定的大小`100,50`。布局引擎可以要求它的大小，它会得到这个值；它知道这个矩形应该有多大。

要求所有东西都有固定大小的布局引擎是没有用的。我们需要一种方法来给第一个平原`Rectangle`一个尺寸。我们不是说它没有大小，而是定义它相对于其父对象的大小。考虑这个基本的 UI 树:

```
Panel{Width=100 Height=100}
    Rectangle 
```

Enter fullscreen mode Exit fullscreen mode

我们允许矩形扩展到可用空间:父元素的大小。我们的矩形现在的大小是`100,100`。扩展的子节点是依赖大小调整的一个例子。

拥有一个部分依赖的大小也很方便，比如这个例子:

```
Panel{Width=100 Height=100}
    Rectangle{Width=50} 
```

Enter fullscreen mode Exit fullscreen mode

我们从矩形属性中获取宽度，从父元素中获取高度，得到大小为`50,100`。这种类型的依赖大小在布局中很常见。当然，现在还有一个问题，那就是在面板中放置矩形的位置，但是我们稍后会谈到这个问题。

## 一幅图像

图像很有趣，因为它既可以有自然大小，也可以有从属大小。考虑一个尺寸为 200x100 的图像`fun.png`。图像的尺寸看起来是一个合理的自然尺寸——UI 引擎可以询问图像的尺寸并得到`200,100`作为响应。

> 我们必须小心解释 200，150 在这里意味着什么。这是源分辨率，但它与设备点、像素、毫米或其他值一致吗？您可能也有适合不同密度显示器的图像。200x100 的图像适用于 1:1 的点对点密度，而 400x200 的图像适用于 2:1 的点对点密度。

图像也可以完全拉伸以填充它们的父图像。

```
Panel{Width=300 Height=200}
    Image{File=fun.png} 
```

Enter fullscreen mode Exit fullscreen mode

`Image` *元素*的大小为`300,200`。现在，由图像类型本身决定如何拉伸实际图像以填充该区域。元素大小与其内容的实际大小之间的差异是一个反复出现的概念。*(我们将在以后的文章中回到图像尺寸)*

一个图像定义一个比率；我们的 200x100 图像的大小比例为 2:1。这对于定义部分相关的尺寸也很有用。考虑在`StackPanel` :
中堆叠图像

```
StackPanel{Width=300}
    Image{File=fun.png} 
```

Enter fullscreen mode Exit fullscreen mode

父对象的宽度为 300，图像应该能够放入其中。在这种情况下，我们使用 2:1 的大小比例得出 150 的高度。这给了图像一个基于父对象的合理的大小，并保持了纵横比——以免扭曲图像。

## 文本

文本也有固定和依赖的大小，但这种关系不同于图像。让我们首先考虑一个不涉及软换行的情况:长行不换行。缺少换行的文本具有固定的自然大小。当然，这取决于字体，但那是文本属性的一部分，而不是布局。

```
Text{Font=Arial FontSize=32 Value="Hello"} 
```

Enter fullscreen mode Exit fullscreen mode

字符串`Hello`具有固定大小，使用 Arial 字体，磅值为 32。

换行文本具有相关的大小。考虑这个例子:

```
Panel{Width=200}
    Text{Font=Arial FontSize=32 Wrap Value="Welcome a bit longer text that might span multiple lines"} 
```

Enter fullscreen mode Exit fullscreen mode

假设父面板的宽度为 200，我们希望文本宽度不要超过 200。当查询文本元素的大小时，它将使用这个宽度的知识，计算换行，并得出一个高度来覆盖所有的行。

## 继续前进

理解自然大小和从属大小对于编写 UI 引擎至关重要。这里我们仅仅触及了基本的可能性，定义了这意味着什么，并给出了关于大小的推理基础。

在后面的文章中，我们将讨论元素如何获取依赖信息来计算它的大小。

> 阅读更多关于[写 UI 引擎](https://mortoray.com/topics/writing-a-ui-engine/)的文章。[在推特上关注我](https://twitter.com/edaqa)或者[脸书](https://www.facebook.com/mortoray/)，为了让我继续写作，考虑[成为赞助人](https://www.patreon.com/mortoray)。