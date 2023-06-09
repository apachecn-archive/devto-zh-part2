# 阴影:阴影中有东西

> 原文：<https://dev.to/neshaz/css-box-shadow-there-is-something-in-the-shadow-2n1k>

box-shadow 属性用于给元素添加阴影效果。

这个属性经常被用来给网站上的元素赋予深度，所以完全理解它是很有用的。

## 语法

box-shadow 属性的语法如下:

```
.box {
  box-shadow: 5px 10px 5px 4px #666;
} 
```

让我们翻译一下，这样你就能理解每个值的意思:

```
box-shadow: horizontal offset | vertical offset | blur radius | spread radius (optional) | color 
```

## 值

*   **水平偏移** -阴影向左或向右扩展多远(如果值为正，它将向右扩展，如果值为负，它将向左扩展)。
*   **垂直偏移** -阴影将在元素上方或下方延伸多远(如果值为正，它将延伸到底部，如果值为负，它将延伸到顶部)。
*   **模糊半径** -数值越大，阴影越模糊，如果数值为 0，则阴影越清晰。
*   **扩散半径** -默认值为 0，数值越大阴影越大，
*   **颜色** -定义阴影的颜色。

## 例子

CSS

```
.box {
  background-color: hotpink;
  height: 200px;
  width: 200px;

  box-shadow: 5px 5px 10px 4px rgba(0,0,0,0.5);
} 
```

结果是:

[![box_shadow_1](img/a018fd3f732b6b1bd38d2235a2ae1553.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V045x5KI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/box_shadow_1.png)

通常，没有必要使用纯色的阴影，增加一些不透明度会让它看起来更自然。

### 嵌入阴影

创建插入阴影时，产生的效果是阴影进入元素内部，使元素内部的内容看起来很压抑。

CSS

```
.box {
  background-color: yellow;
  height: 200px;
  width: 200px;

  box-shadow: inset 5px 5px 10px 4px rgba(0,0,0,0.5);
} 
```

[![box_shadow_2](img/f706014f46ee4477f6abaee4cc253333.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KJRREmZ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/box_shadow_2.png)

### 两个不同的影子

你也可以创建一个有两种不同阴影，宽度不同颜色的元素。

CSS

```
.box {
  background-color: orange;
  height: 200px;
  width: 200px;

  box-shadow: 5px 5px 10px purple, -7px -4px 4px green;
} 
```

[![box_shadow_3](img/b2c217c8488a94d878eac6a055fe0ffa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i1cqk4ly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/box_shadow_3.png)

### 实心阴影

CSS

```
.box {
  background-color: #999;
  border: solid 3px #000;
  height: 200px;
  width: 200px;

  box-shadow: 5rem -20px red;
} 
```

[![box_shadow_4](img/59f6f93099af1249cd2e51d3880f1d39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZiF5KhPU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/box_shadow_4.png)

从这个例子中，你可以看到使用一个固体阴影是好的，但通常给它一些传播和不透明度会产生更好的效果。

## 方框阴影发生器

既然你已经理解了这个属性，并且已经看到了它的作用，那么提一下你也可以使用很多**生成器**是个好主意。通常很难将设想的设计转化为框阴影属性值，因此有一些工具可以帮助您。

可以使用 [CSS matic](https://www.cssmatic.com/box-shadow) 、 [CSS3 Gen](https://css3gen.com/box-shadow/) 、[Mozilla](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Background_and_Borders/Box-shadow_generator)box-shadow generator。这些都有助于你在视觉上达到你想要的方框阴影效果。

## 浏览器兼容性

正如我在以前的一篇文章中提到的，检查您正在学习的新属性的浏览器兼容性是一个很好的做法。目前，最好使用的服务是[can use](https://caniuse.com/)。

[![box_shadow_use-1](img/8eb37d6fc9cb0b950633371e4de54ab4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AZ1j4wAT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/box_shadow_use-1.png)

可以看到，浏览器支持非常好，除了使用 **Opera Mini** 的时候。你应该知道这个特性在那里是行不通的。

## 结论

我解释了**盒子阴影属性**以及它的所有值代表什么。现在，就看你如何进一步探索和释放它的全部潜力了。

感谢您的阅读！

你需要 CSS 和网页设计方面的帮助吗？[给我们发消息](https://kolosek.com/contact/)科洛塞克团队会去查的！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/unlocking-css-box-shadow/?utm_source=dvt)上。