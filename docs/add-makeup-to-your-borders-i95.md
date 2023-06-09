# 为您的边框增添妆容

> 原文：<https://dev.to/neshaz/add-makeup-to-your-borders-i95>

CSS border 属性非常熟悉。使用该属性，您可以定义元素边框的颜色、样式和宽度。

另一方面，border-image 属性允许您**为边框**定义渐变或图像。

border-image-property 是以下内容的简写:

*   **边框-图像-来源** -图像的来源，
*   **Border-image-slice** -定义将源图像分割成区域的尺寸，
*   **边框-图像-宽度** -定义边框图像的宽度，
*   **Border-image-outside**-定义从边框图像到元素边缘的距离，
*   **边框-图像-重复** -定义如何重复图像以填充边框区域。

当使用这种简写方式时，如果省略了任何值，**其值将被设置为 initial。**

## 语法

当使用 border-image 属性时，可以使用前面提到的一个到所有五个值。

让我们来看看语法。我将使用速记属性。

```
.box  {
  border-image:  
      url(‘images/border.png’)       /* source */
      27 /                          /* slice */
      12px 5px 15px 20px /         /* width */
      5px 12px 17px 22px          /* outset */
      round                      /* repeat */
} 
```

## 例子

让我们举例说明什么时候你想用渐变作为边框，什么时候你想用图片作为边框。

### 用重复图像制作边框

创建边框时，我将使用以下图像:

[![Border sample](img/bb5f6aca5f16525626317653a1a12956.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2b_fmJIs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/border.png)

HTML

```
<div class=”box”></div> 
```

CSS

```
.box {
  width: 200px;
  background-color: #000;
  border: 50px solid #DE31ED;
  margin: 10%;
  padding: 10px;

  border-image:
      url("img/border.png")    /* source */
      50 /                           /* slice */
      25px /                         /* width  */
      12px                           /* outset */
      round                          /* repeat */
} 
```

结果是:

[![border-image usage example](img/39c0f610181ad49951d35a3b204d53f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GbGZ46CS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/border-image.png)

### 用渐变制作边框

我将使用与上一个例子相同的 box 元素，只有 CSS 不同。

CSS

```
.box {
  width: 200px;
  height: 200px;
  background-color: #000;
  border: 20px solid transparent;
  padding: 10px;
  margin: 10%;

  border-image: repeating-linear-gradient(60deg, #DE31ED, #31E2ED 50px) 20;
} 
```

结果是:

[![border-image gradient usage example](img/67ab28e0f5dba3cc958ecc250003fa44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2muUzeJi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/border-gradient.png)

## 浏览器兼容性

了解一处房产兼容性的最佳方式是查看[can use](https://caniuse.com/)服务。

[![border-image browser compatibility](img/a8bf533c2722b67e9d37056d7c870f0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4gaO0q6C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/caniuse.png)

这里你看到兼容性普遍非常好。Chrome 中的**border-image-repeat:space**有一些问题，所以你应该留意一下。

## 结论

虽然很少使用，但边框图像属性非常有趣。它能给你的设计带来独特的维度。最好的方法来看看什么适合你，是发挥财产，释放其全部潜力！

感谢您的阅读！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/css-border-image/?utm_source=dvt)上。

用 CSS 需要帮手吗？没问题，只要给我发消息，我会确保我的团队联系到你！