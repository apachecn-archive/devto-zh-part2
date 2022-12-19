# 使用 JavaScript 生成器在 React 中无限滚动

> 原文：<https://dev.to/dance2die/infinite-scrolling-in-react-using-javascript-generator-hk4>

*照片由[约翰娜·蒙托亚](https://unsplash.com/photos/PRumW--tkc4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com)T5 拍摄*

在阅读下面的文章[ES6 生成器的快速实用用例:构建无限重复数组](https://dev.to/sreisner/a-quick-practical-use-case-for-es6-generators-building-an-infinitely-repeating-array-1onk)时，我碰巧查看了 [Reddit API](https://www.reddit.com/dev/api/) 。

[![sreisner image](../Images/7ae9b650073b2e86ebc16a9c516c2137.png)](/sreisner) [## ES6 生成器的快速实用用例:构建无限重复的数组

### Shawn Reisner ・ Jun 6 '18 ・ 3 min read

#javascript #tutorial #generator #arrays](/sreisner/a-quick-practical-use-case-for-es6-generators-building-an-infinitely-repeating-array-1onk)

JavaScript Generator 是无限返回数据的好方法，我想我可以用它来实现🥁🥁🥁无限滚动。

让我们看看如何在 React with JavaScript Generator 中实现无限滚动。

## 🚀JavaScript 生成器？

一个[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)是实现[迭代器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterator_protocol)和[可迭代](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol)协议的简单方法。

*和往常一样，我会偷懒，把这些文章推荐给你。*

*   [我如何学会停止循环并爱上迭代器](https://dev.to/kepta/how-i-learned-to-stop-looping-and-love-the-iterator-463j)
*   [生成器–JavaScript MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)

总而言之，把它想象成一个无限返回一系列数据的对象，直到你告诉它停止。

## 🚀返回 Reddit 数据的生成器

下面是一个返回 ReactJS 子编辑数据的生成器函数。