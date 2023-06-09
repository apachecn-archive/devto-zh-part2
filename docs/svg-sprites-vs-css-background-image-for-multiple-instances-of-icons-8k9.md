# 多个图标实例的 SVG 精灵与 CSS 背景图像

> 原文：<https://dev.to/ekafyi/svg-sprites-vs-css-background-image-for-multiple-instances-of-icons-8k9>

嗨，朋友们！

我在一个需要许多图标的网站上工作，我正在考虑使用 **SVG sprite** 作为图标。一些大型网站，如 Github、Codepen 等都采用这种方式。如果你不熟悉，这些文章是很好的介绍:

*   [https://css-tricks.com/svg-symbol-good-choice-icons/](https://css-tricks.com/svg-symbol-good-choice-icons/)

*   [https://tomhazledine.com/inline-svg-icon-sprites/](https://tomhazledine.com/inline-svg-icon-sprites/)

*   [https://medium . com/@ web productive/why-and-how-I-m-using-SVG-over-fonts-for-icons-7241 dab 890 f 0](https://medium.com/@webprolific/why-and-how-i-m-using-svg-over-fonts-for-icons-7241dab890f0)

简而言之，我们有一个包含所有图标的 SVG，分组在`<symbol>`元素中，当我们想要使用它时，我们用`id`来引用每个符号。

```
   
      <symbol id="icon-heart" viewBox="0 0 24 24">
        <!-- <path> -->  
      </symbol>
    

    
      <use xlink:href="#icon-heart" />
     
```

这个方法也适用于外部 SVG 文件，这使得缓存成为可能，引用如下:

```
 
      <use xlink:href="sprite.svg#icon-heart" />
     
```

到目前为止，一切顺利。

但是在性能方面，我很好奇对于在每个页面中多次使用的图标，使用普通的 CSS 背景图片是否比 SVG sprite 更快？

例如，在下面的[代码页](https://codepen.io/pens/)中,“查看”👁，“评论”💬和“爱”♥图标被用在每张“钢笔”卡片上。每页包含 12 张卡片。

[![Screenshot of Codepen Explore page](img/91aca0864422da33e686eae33e62a302.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vNo2tJP0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pbg43cxgisy08326rv18.png)

### 用 CSS 背景图片

假设我们想从上面的 Codepen 页面中复制“Love”按钮。如果我们使用普通的 CSS 背景图片，最少的代码应该是这样的。当用户转到下一页时，CSS 文件将被缓存。

(旁注:PNG 文件的大小会更小，但它在本质上不同于 SVG，所以我不会在这里讨论背景图片的 PNG。)

```
 .icon-heart {
        padding-left: 2rem;
        background-image: url('data:image/svg+xml, <path etc etc/>'),url('data:image/svg+xml, ');
    }

    <button class="icon icon-heart">
        <span class="count">350</span>
    </button> 
```

### 带 SVG 精灵

除了 SVG sprite 文件本身，HTML 代码会更长，因为我们需要引用 SVG。浏览器必须在按钮的每个实例中呈现这三行额外的内容(在我们的例子中是 12 次)，当用户导航到一个新的(因此是未缓存的)页面时，这些内容将被再次呈现 12 次，以此类推。再加上其他图标(比如 Codepen 页面中的“查看”和“评论”图标)，岂不是臃肿不堪？

```
 <button class="icon icon-heart">
        
          <use xlink:href="sprite.svg#icon-heart" />
        
        <span class="count">350</span>
    </button> 
```

对于 SVG sprite 的灵活性和多功能性，额外的行在页面加载速度方面是否真的微不足道，我还没有找到一个令人满意的答案。还是有什么其他我不知道的考虑？

任何反馈或解释将不胜感激。