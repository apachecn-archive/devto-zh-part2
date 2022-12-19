# 用 CSS 创建图形

> 原文：<https://dev.to/nikolisan/creating-graphics-with-css-48f>

不久前，我开始学习 web 开发。作为初学者，我从 HTML 和 CSS 开始。我在网上看了几个视频，看了几篇文章。我总是对网站上展示的动画和智能图形感到兴奋，我很好奇我如何才能创造它们。

然后我看到了阿里制作的一个很棒的视频，我渴望用动画制作一个简单的 CSS 图形。我首先想到的是一张礼品卡，就像我们在互联网还没有出现的时候分享的那种。

### 所以，我们来做一张简单的礼品卡，与你的情人节分享吧。

主要想法是创造一个心和下面提出一个爱的信息。为此，我将使用一个`div`表示心脏，一个`div`表示消息。

#### 心形造型

[![heart-mock](../Images/5e21f9d8ba73219704336cb420585195.png "Heart mockup")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nuyKruuN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bit.ly/2yH2VF3)

心脏由三种形状组成。一个正方形和两个圆形。可以只使用一个`div`模块和两个伪元素`::before`和`::after`来创建心形。

```
.heart {
  width: 100px;
  height: 100px;
  background: #EF9A9A;
  transform: rotate(45deg);
}
.heart::after,
.heart::before {
  position: absolute;
  content: '';
  width: 100px;
  height: 100px;
  background: #EF9A9A;
  border-radius: 50%;
  border-top: 5px #E57373 solid;
}
.heart::after {
  top: -55px;
}
.heart::before {
  top: -5px;
  left: -50px;
} 
```

使用 **CSS** 我创建了三个正方形，并使用`border-radius: 50%`创建了其中的两个圆形(T0 和 T1)。然后我小心翼翼地把它们摆成我想要的形状。

##### 接下来就是打造类似心跳的效果。

在`.heart`类中，我创建了一个无限动画，动画元素的比例。

```
.heart {
  animation: heart-beat 2s ease-in-out infinite;
}
@keyframes heart-beat {
  50% {
    transform: rotate(45deg) scale(0.8);
  }
} 
```

心脏就完成了。我做了一些进一步的设计，结果你可以在下面看到。
[https://codepen.io/nikolisan/embed/qJpjdK?height=600&default-tab=result&embed-version=2](https://codepen.io/nikolisan/embed/qJpjdK?height=600&default-tab=result&embed-version=2)T2】

*PS:* 我可以用三个`div`来创建心脏的形状，并以同样的方式放置它们。我想如果我能练习使用伪元素会很棒。

### 生日蛋糕动画

我对心脏动画的效果非常满意，我认为这将是尝试在 github 中重新创建一个我以前见过的形状的最佳时刻。

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png)[Elena torro](https://github.com/elenatorro)/[CSS cake](https://github.com/elenatorro/CSSCake)

### 用 CSS 制作的生日蛋糕

<article class="markdown-body entry-content container-lg" itemprop="text">

# CSS 生日蛋糕

用 CSS 做的生日蛋糕。

</article>

[View on GitHub](https://github.com/elenatorro/CSSCake)

我按照上面相同的概念试了一下，最终结果如下。
[https://codepen.io/nikolisan/embed/BqJdGr?height=600&default-tab=result&embed-version=2](https://codepen.io/nikolisan/embed/BqJdGr?height=600&default-tab=result&embed-version=2)T2】

我希望你喜欢我的文章，因为这是我第一篇完整的文章。任何提示，建议或任何真正的感谢。