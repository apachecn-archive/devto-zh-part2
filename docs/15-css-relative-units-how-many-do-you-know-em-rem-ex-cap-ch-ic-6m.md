# 15 CSS 相对单位，你知道几个？em、rem、ex、cap、ch、ic...

> 原文：<https://dev.to/bytegasm/15-css-relative-units-how-many-do-you-know-em-rem-ex-cap-ch-ic-6m>

* * *

如果你正在读这篇文章，我敢肯定你知道至少 5 种使用 CSS 来居中 HTML 元素的方法。因此，15 个相对大小的选项不会让您感到惊讶。让我告诉你，有些很奇怪。

## **故事**

我看到了 JavaScript 老师发的这条推文，我只知道其中的 5-7 个属性，它给了我一次大规模的 [FOMO](https://www.urbandictionary.com/define.php?term=fomo) 攻击。所以，我决定写这篇文章，把你从 FOMO 中拯救出来。

液体错误:内部

### **什么是相对单位&我们为什么需要它们？**

你有没有想过你拥有的屏幕没有一个是相同的大小和尺寸？但是它们都显示网页。因此，网页需要以一种适应显示(或任何其他 HTML 元素)的相对比例的方式来设计。

简而言之，使这种响应迅速、适应性强的布局成为可能。

[![new app downloads](img/a98eb6dad1bb646f92014f82586f5ae2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sed0tQ_i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AOkcOPoWR3bxKY2axlDX9UA.gif)

### **1。不可预测的 em**

相对于当前元素的**字号**(`2em`表示当前字号的两倍)。依赖于字体大小的长度属性，是啊，多么神奇。

```
.post {   
   font-size: 24px;
   border-left: 0.25em solid #4a90e2;
}

/* The border-left-width will compute to 6px. */ 
```

Enter fullscreen mode Exit fullscreen mode

使用`em`可能会变得棘手，因为它从其父元素获得相对值。在嵌套元素的情况下，当父元素也有`em`值时会变得混乱。

### **2。救世主雷姆(root em)**

就像`em`一样，但是相对于文档的根元素的字体大小。

```
p {
    margin-left: 1rem;
}

h4 {
    font-size: 2rem;
}

/* Unlike the em, which may be different for each element, the rem is constant throughout the document */ 
```

Enter fullscreen mode Exit fullscreen mode

对于这项技术，最基本的是设置你的基本字体大小，默认情况下通常是 16px。

### **3、4。vh 和 vw**

不像`em`和`rem`依赖于字体大小`vh` & `vw`依赖于视口的大小。

`1vh` = **视口高度的 1%** 或**1/100**视口宽度的
`1vw` = **1%** 或**1/100**

你可能见过一些网站，当你调整浏览器窗口大小时，它们的排版会很漂亮，`vw` & `vh`让这一切发生。

### **5、6。vmax & vmin**

视口最大值和视口最小值:1vw 或 1vh，分别取较大值或较小值。

`1vmax` = **1%** 或**1vw 或 1vh 之间较大值的 1/100**
T1】=**1%**或**1vw 或 1vh 之间较小值的 1/100**

### **7。Good'ol %**

相当流行，每个人的朋友&相当明显`%`是相对于它的父元素。

```
.post {
  font-size: 24px;
}
.post-category {   
  font-size: 85%; 
} 
.post-title {   
  font-size: 135%; 
}
/*      
Child elements with % font sizes...      

  85%     
  0.85 * 24 = 20.4 px        
  135%     
  1.35 * 24 = 32.4 px
*/ 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在我们来深挖一些比较隐晦和相对不为人知的。

### **8、9。实验六& vb**

这些是实验性的 API，并不意味着在生产中使用。不支持任何浏览器。

`1vb` =包含块的初始[尺寸的 1%，在根元素的](https://medium.com/r/?url=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FCSS%2FAll_About_The_Containing_Block)[块轴](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Logical_Properties#block-dimension)方向。
`1vh` =包含块的初始[大小的 1%，在根元素的](https://medium.com/r/?url=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FCSS%2FAll_About_The_Containing_Block)[内联轴](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Logical_Properties#inline-dimension)方向。

它们不是很受欢迎或支持，因为它们很新，你可以在 CSS 值和单位模块级别 4 中了解更多。

### **10，11。lh & rlh**

它们就像`em` & `rem`一样，但它们依赖的不是字体大小，而是行高。

`lh` =等于使用它的元素的[行高](https://developer.mozilla.org/en-US/docs/Web/CSS/line-height)属性的计算值。
`rlh` =等于根元素上[行高](https://developer.mozilla.org/en-US/docs/Web/CSS/line-height)属性的计算值。

### **12。ex**

很少使用`**ex**`单元。它的目的是表示必须与字体的 x 高度相关的大小。 **x 高度**大致是小写字母的高度，如 a、c、m 或 o

### **13。cap**

这是对所谓的帽高的测量。[规范](https://drafts.csswg.org/css-values-4/#cap)将帽高定义为*大约等于大写拉丁字母* *的* *高度。*

### **14。ch**

*表示字符 0* 、的* *宽度，或者更准确地说，表示元素字体中字形“0”(零，Unicode 字符 U+0030)的前进度量。

### **15。ic '象形文字计数**

`ic`是上面提到的`ch`的东方等价物。是 CJK(中文/日文/韩文)表意文字`*水*`(“水”，U+6C34)的大小，所以大致可以解释为“表意文字数”。

其中一些非常奇怪和不常见，但我非常确定它们中的每一个都有自己的使用案例和存在的理由。你最喜欢的是什么？