# CSS clear float 属性的解释[示例]

> 原文：<https://dev.to/neshaz/explanation-of-the-css-clear-float-property-examples-5hd2>

不久前，我写了关于 [float](http://kolosek.com/css-float/) 属性的文章。所以，现在是解释清楚**的**属性的好时机。

clear 属性与 float 属性直接相关。它指定一个元素是应该在浮动元素的旁边，还是应该在它们下面移动。该属性适用于浮动和非浮动的元素。

如果一个元素可以放入浮动元素旁边的水平空间，**它就会**。除非您将 clear 属性应用于与浮动方向相同的**中的元素。然后，该元素将**移动到浮动元素的下方**。**

CSS 中创建的每个元素都需要添加[质量设计](https://kolosek.com/10-ways-to-improve-your-website-design/)。

clear 属性可以有以下值:

*   **None** -元素是**不向下移动**清除过去的浮动。
*   **左** -向下移动该元素以清除过去的**左**浮动。
*   **右** -该元素被向下移动以越过**右**浮动。
*   **两个** -元件向下移动，越过左右浮动的**。**

## 支持

咨询了[能不能用](https://caniuse.com/#search=clear)服务，看到支持度是**超过 87%** 。

[![clear_usage](img/6e79448623adf9c509e33a65495199ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Il84klNm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/02/clear_usage.png)

## 例子

HTML

```
<div class=”container”>
  <div class=”left-segment”></div>
  <div class=”left-segment”></div>
  <p class=”text-clear”>Cardigan aesthetic direct trade, migas locavore shoreditch DIY bicycle rights lyft street art bitters.</p>
</div> 
```

CSS

```
.container {
  border: solid thin #ccc;
}

.left-segment {
  float: left;
  background-color: #C98EED;
  height: 200px;
  width: 200px;
  margin-right: 10px;
}

.text-clear {
  clear: left;
} 
```

[![clear_example_1](img/9f93c73b1e499849cffaf6ccc7c5c254.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--30-8HV_T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/02/clear_example_1.png)

这里你可以看到 div 应用了 **float: left** 。当我将**设置为 clear: left** 到文本段落后，它**移到了浮动元素**的下面。

HTML

```
<div class=”container”>
  <div class=”right-segment”></div>
  <div class=”right-segment”></div>
  <p class=”text-clear”>Cardigan aesthetic direct trade, migas locavore shoreditch DIY bicycle rights lyft street art bitters.</p>
</div> 
```

CSS

```
.container {
  border: solid thin #ccc;
}

.right-segment {
  float: right;
  background-color: #8FC9FF;
  height: 200px;
  width: 200px;
  margin-left: 10px;
}

.text-clear {
  clear: right;
} 
```

[![clear_example_2](img/fecc072cb1991537945b7cef5bc3f022.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xF0yzDwC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/02/clear_example_2.png)

接下来，您可以看到两个带有 **float: right** 属性的 div 和一个带有 **clear: right** 属性的段落。通过设置这个，段落被移动到浮动元素下面**。**

现在是给文本添加一些格式的好时机。

HTML

```
<div class=”container”>
  <div class=”left-segment”></div>
  <div class=”right-segment”></div>
  <p class=”text-clear”>Cardigan aesthetic direct trade, migas locavore shoreditch DIY bicycle rights lyft street art bitters.</p>
</div> 
```

CSS

```
.container {
  border: solid thin #ccc;
}

.left-segment {
  float: left;
  background-color: #8FC9FF;
  height: 200px;
  width: 200px;
}

.right-segment {
  float: right;
  background-color: #8FC9FF;
  height: 200px;
  width: 200px;
}

.text-clear {
  clear: both;
} 
```

[![clear_example_3](img/1afb392a0ce267f9f77fc5973ae5eeef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7MmyPDUo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/02/clear_example_3.png)

最后，最后一个例子展示了 **clear: both** 属性的用法。两个 div 被浮动**左右**，而段落被添加了 clear 属性。这样，**被移动到两个[浮动元素](http://kolosek.com/css-float/)的**下方。值得一提的是，这是最常用的 clear 属性。

## 总结

希望这篇文章对你的项目有所帮助。使用该属性时要小心，众所周知，它在过去已经引起了很多混乱。但是，我确信读完这篇文章后，你就可以走了！

编码快乐！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/css-clear-float/)上。