# 为标签云效果动态调整文本大小

> 原文：<https://dev.to/carlymho/dynamically-sizing-text-for-a-tag-cloud-effect-15g0>

常见的是以标签云格式显示数据，也就是说，一大块关键词或术语内嵌显示，每个术语的大小显示其在所有现有术语中的相对重要性或频率。

[![tag cloud of New York Stock Exchange symbols](../Images/fbd43fba8c991c3165fe22b9614242d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OdCyLYsG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/7/79/Top_500_by_volume_on_the_NYSE.png)

很多时候，我们看到这些标签云内置于内容管理系统中或通过插件生成，但如果我们想从头开始，我们该如何制作这样的东西呢？在本教程中，我将介绍我是如何为自己的网站构建这个功能的，以显示我使用不同技术的相对时间。

## 设置

我试图让标记非常简单:

```
<ul>
  <li data-year="2001">HTML</li>
  <li data-year="2002">CSS</li>
  <li data-year="2003">PHP</li>
  <li data-year="2010">Javascript</li>
  <li data-year="2012">Ruby</li>
  <li data-year="2010">Python</li>
  <li data-year="2017">Node.js</li>
  <li data-year="2010">MySQL</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的无序列表。这里唯一特别的是每个`<li>`都有一个数据属性`data-year`，它表示我开始使用该特定技术的年份。如果您正在为博客上带有给定标签的文章数量创建标签云，您可能会调用数据属性`data-count`，并将其设置为带有该标签的文章数量。

继续风格，再次保持简单。大多数情况下，我去掉了默认格式，将内容居中对齐，并将列表项设置为`display: inline-block`，这样它们就合并成了一个文本块。

```
ul {
  list-style: none;
  margin: 0 auto;
  padding: 0;
  text-align: center;
}

ul li {
  margin: 0 5px;
  display: inline-block;
  vertical-align: middle;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 脚本编写

首先，让我们把所有东西都包装在窗口的`load`事件的事件处理程序中，只是为了确保在我们开始应用字体大小之前，所有东西都准备好了。我在这里使用了一点 jQuery 来加速这个过程，但是没有理由不能用普通的 Javascript 来完成。

```
$(window).on('load', function() {
  //...
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您的字体大小在某些屏幕大小断点处发生变化，您可能还想在`resize`和`load`上重新触发。

现在我们已经完成了，我们需要得到在我们的标签云中表示的数字范围。

```
 var nums = $('ul li').map(function() {
    return parseInt($(this).data('year'));
  }).get();

  var maxVal = Math.max(...nums);
  var minVal = Math.min(...nums); 
```

Enter fullscreen mode Exit fullscreen mode

`map`函数遍历每个列表项，并返回被解析为整数的`data-year`属性的值；`.get()`方法将输出格式化成一个数组。

然后，我们将这些数字传递给 Javascript 的`Math.max`和`Math.min`函数，分别获得最大值和最小值。(`nums`以`...`开头，表示应该作为数组读取。)

现在，我们将计算字体的大小。

```
 var currentDate = new Date();
  var currentYear = currentDate.getFullYear();
  var baseFont = $('ul li').css("font-size");
  var fontsplit = baseFont.match(/([0-9\.]+)\s?([a-z\%]+)/);
  var minFont = parseInt(fontsplit[1]);
  var maxFont = parseInt(fontsplit[1])*3;
  var fontUnits = fontsplit[2]; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们得到的是当年的值，因为我们想让程序计算出我用一项技术工作了多长时间。如果你得到的是一些博客文章或库存数量，你可以跳过这一步！

然后我们从 CSS 中获取基本字体大小。它带有单位，所以我插入了一个正则表达式来匹配一个数字和一个单位缩写。当前字体大小成为标签云的最小字体大小，分配给`minFont`；最大字体大小是最小字体大小的三倍。您可以根据个人喜好调整这些值——例如，如果您希望最小字体大小为基本字体大小的 80%,您可以设置`minFont = parseInt(fontsplit[1])*.8`。

现在是时候实际应用字体大小了！

```
 $('ul li').each(function() {
    var itemYear = parseInt($(this).data('year'));
    if (itemYear) {
      var num = currentYear - itemYear;
      var fontSize = (((maxVal - itemYear )/(maxVal - minVal)) * (maxFont - minFont)) + minFont;
      $(this).css("font-size", fontSize+fontUnits);
      $(this).attr("title", (currentYear - itemYear)+" years");
    } else {
      // if we can't determine the year, just set it to 90% size
      $(this).css("font-size", (minFont*.9)+fontUnits);
    }
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

首先，它执行快速检查，以确保在试图访问之前`data-year`确实被设置。然后我们做一点数学计算，从当前年份中减去`data-year`的值来确定年经验(同样，如果您不想确定年经验，可以跳过这一步)。

我们通过计算当前数字在最小值和最大值之间的百分比，乘以`maxFont`和`minFont`之间的差值，然后将其加到`minFont`来确定字体大小。然后，我们将 css `font-size`的值设置为这个数字，并在最后将这些单元加回去。

此外，为了确保信息以非视觉媒体可访问的方式呈现，我们将列表项的 title 属性设置为“[N] years”

## 最终产品

所有这些加起来的结果看起来有点像这样！

[https://codepen.io/cmho/embed/RMMOvo?height=600&default-tab=result&embed-version=2](https://codepen.io/cmho/embed/RMMOvo?height=600&default-tab=result&embed-version=2)

如果你向下滚动到底部，你还可以在我的个人网站上看到一个实时数据版本。