# 通过在 jQuery 中添加 DOM 元素来解决问题

> 原文：<https://dev.to/nickjj/breaking-down-problems-by-prepending-a-dom-element-with-jquery-bb2>

**本文最初发布于 2018 年 2 月 27 日:[https://nickjanetakis . com/blog/breaking-down-problems-by-prepending-a-DOM-element-with-jquery](https://nickjanetakis.com/blog/breaking-down-problems-by-prepending-a-dom-element-with-jquery)T3】**

* * *

过去，我曾经写过如何将解决问题作为软件开发人员的首要技能。我在编程的早期就发现了这一点。这是我 20 年前的信念，也是我今天仍然相信的信念。

在为我的[Build a SAAS App with Flask course](https://buildasaasappwithflask.com/?utm_source=devto&utm_medium=website&utm_campaign=jquery-dom-breakdown)创建一个免费的奖励应用程序时，我必须实现一个功能，在这个功能中，我希望在侧边栏中显示社区成员发布的 5 个最新虚假事实。

## 观看演示视频中的功能

如果你好奇的话，这里有几分钟的演示，演示了这个应用程序是做什么的。我们将在课程中回顾构建这个 API 驱动的应用程序，但我在本文中谈论的功能是在下面的视频中大约 2:06:

[https://www.youtube.com/embed/qZr4xaR5v5I](https://www.youtube.com/embed/qZr4xaR5v5I)

这并不是一个很难实现的特性，尤其是对于 jQuery，但是我想回顾一下我是如何得到我最终使用的代码的，因为当我实现它的时候，我对自己傻笑，同时想到*“是的，这是一本教科书中分解问题的例子”*，我们到了。

## 识别和分解问题

为了分解问题，我们首先需要清楚地识别它。

##### 通常，问题只是伪装的特征。我的思考过程是:

> 我想列出 5 个最新的社区虚假事实。当新的虚假事实通过 websocket 通道被推送时，它们应该得到更新，所以当新的事实被广播时，客户端的列表需要得到更新。

##### 以上 3 件事可以进一步分解:

*1。我们将不得不使用 Javascript*

因为我已经在这个项目中使用了 jQuery，所以使用 jQuery 来帮助解决这个问题是有意义的，因为它有非常好的工具来处理 DOM 元素。

*2。我们将不得不向 DOM* 添加新元素

jQuery 有一个`.append()`方法，可以让您将元素添加到您设置为选择器的任何元素中。更具体地说，它有一个做同样事情的`prepend()`方法，但是它把新元素作为第一个元素，而不是最后一个元素。

太完美了。我们所要做的就是将传入的事实预先添加到某个容器选择器中。

*3。应该是 websocket 事件被触发时发生的事情*

就本文而言，这一部分并不太重要。在您的例子中，触发器可能是提交表单而不是 websocket 事件。为了完整起见，我只是想包括这一步，因为弄清楚是什么原因导致添加该项是很重要的。

##### 创建将项目添加到列表中的算法:

对于任何不简单的东西，我倾向于写出伪代码，或者用简单的英语和通用的编程术语列出步骤。它不会是“真正的”代码，但从长远来看，它极大地帮助您思考问题并节省时间。

在本例中，可能是这样的:

1.  定义最大长度参数(5)
2.  获取最新事实容器的最大长度
3.  将新事实添加到事实容器中
4.  如果#2 的长度大于#1 的长度，则删除事实容器中的最后一个元素

##### 将算法转换成实代码:

既然我们已经万事俱备，编写代码就非常简单了。我将通过不使用模板引擎或处理 websocket 事件来简化事情，但下面是代码的重要部分:

```
var $factsContainer = $('#facts-container');
var maxFactsContainerLength = 5;
var factsContainerLength = $factsContainer.children().length;

$factsContainer.prepend('<p>This would be the fake fact message.</p>');

if (factsContainerLength > maxFactsContainerLength) {
  $factsContainer.children().last().remove();
} 
```

Enter fullscreen mode Exit fullscreen mode

看看真代码和算法/伪代码有多接近。相当整洁。