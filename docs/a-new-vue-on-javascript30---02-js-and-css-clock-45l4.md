# JavaScript30 - 02 JS 和 CSS 时钟上的新 Vue

> 原文：<https://dev.to/davefollett/a-new-vue-on-javascript30---02-js-and-css-clock-45l4>

这篇文章是 JavaScript30 系列的 [A New Vue 的一部分，该系列探索了使用](https://dev.to/t/anewvueonjs30) [Vue](https://vuejs.org) 重新实现 [#JavaScript30](https://JavaScript30.com) 项目。今天我们将与韦斯博斯的第二个([@韦斯博斯](https://twitter.com/wesbos) ) [#JavaScript30](https://JavaScript30.com) 项目合作，项目名称为: **02 - JS 和 CSS 时钟**。这是一个看起来很酷的模拟时钟，它使用一个`setInterval()`回调来控制时钟指针，根据当前时间旋转它们。

## 关键 Vue 概念

本文讨论了以下 Vue 概念:

*   用`:style`(对`v-bind:style`的简写)进行样式绑定
*   `data`反应性
*   `mounted`生命周期挂钩

## Vue 实现

与 JavaScript 30-01 JavaScript Drum Kit 上的新 Vue 不同，这次我只创建了一个 Vue 实现，但我遵循了类似的模式。首先，我将原始#JavaScript30 项目中的代码插入到我的[入门](https://dev.to/davefollett/a-new-vue-on-javascript30---getting-started-4o61)文章中的[基础启动文件](https://github.com/davefollett/JavaScript30/blob/master/00%20-%20Getting%20Started/index-VUE.html)中相应的 Vue 位置:

*   HTML 部分放在根目录`<div id="app">`中
*   这些功能进入了`methods`部分
*   页面加载时执行的 JavaScript 被放在了`mounted`函数中
*   `<style>`部分被原封不动地移到我的 Vue 版本中
*   不需要`computed`和`watch`部分，因此将其移除

有了这些改变，我们可以非常接近一个正常工作的时钟，但还不够。`querySelector()`通话没地方去；没有这些，旋转变换将不起作用。为了解决这个问题，我做了一些改动。首先，我向 Vue 实例数据对象添加了三个字段:`secondsDegrees`、`minsDegrees`和`hourDegrees`。这些将保存每个时钟指针的度数。