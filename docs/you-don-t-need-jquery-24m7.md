# 你不需要 jQuery

> 原文：<https://dev.to/navi/you-don-t-need-jquery-24m7>

我最近读了这篇文章 [***。我仍然喜欢 jQuery——你也应该喜欢。***](https://hackernoon.com/i-still-love-jquery-and-you-should-too-3114f33f249e) 老实说，在当今这个发展的时代，有人愿意为 jQuery 辩护，这让我有点惊讶。

[![](img/02f95a32291e08aca349339bfdeda8f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--loX7ccAE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AqKyXecsGDFKV5Gbti-Inzw.jpeg)

作者为 jQuery 做了一个很好的例子，但是有些陈述并不准确，并且忽略了开发的某些方面。让我们看看他支持 jQuery 的一些论点。

#### **jQuery 不会让你的站点变慢**

嗯，上面的说法是假的。作者使用了这种说法，但随后承认 jQuery 确实会影响页面的加载时间，这在大多数情况下是可以忽略的。事实上，他是对的，但是当我们需要我们的网站对每个人开放的时候，他没有把灯放在上面。在这种情况下，我们的网站可能会被网络连接不好的人使用，这时你会希望你的网站越小越好。

即使您使用 jQuery 的 min/slim 版本，大小差别也不大，并且 jQuery slim 版本中删除了一些特性，如动画和 Ajax。

作者还提到使用 jQuery 可以提高你的打字速度。大多数情况下确实如此，但是在很多情况下，jQuery 和普通 JavaScript 都有几乎相似的代码行。这时你应该真正考虑你真的想在你的站点中使用 jQuery。

下面是一些例子:

```
// Append using jQuery
$(parent).append(el);

// Append using Vanilla JS
parent.appendChild(el);

// Getting Children using jQuery
$(el).children();

// Getting Children using Vanilla JS
el.children

// Get HTML using jQuery
$(el).html();

// Get HTML using Vanilla JS
el.innerHTML 
```

使用 jQuery 是如此多余，以至于有一个名为[的网站专门展示 jQuery 和普通 JS 代码的区别，你可能不需要 jQuery](http://youmightnotneedjquery.com/) 。你可以在这个网站上找到更多类似上面提到的例子。

此外，那篇文章还忽略了 jQuery 选择器的性能。这里有一个名为 [JSPERF](https://jsperf.com/dm-jquery-vs-vanilla-selectors) 的网站，您可以在这里检查和比较 jQuery Selector 的性能。

剧透一下:Vanilla JS 选择器每次都比 jQuery 快。

<figure>[![](img/1a15f478169caf51901bbae163ce4641.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dghk5LP1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuIG2P4s1Zlp0brMy_5boZQ.png) 

<figcaption>JSPERF 的 jQuery 结果和香草 JS 选择器</figcaption>

</figure>

最后，jQuery 的主要特性之一 ajax()在 slim 版本中不可用，可以很容易地被 fetch 方法或 Axios 之类的外部库替换。

最后，我想说 jQuery 确实增加了一些作者在他的[文章](https://hackernoon.com/i-still-love-jquery-and-you-should-too-3114f33f249e)中讨论的特性。此外，使用 jQuery 也是一种选择，但是还有更好的选择。你必须做出一些权衡，选择适合你优先考虑的选项。