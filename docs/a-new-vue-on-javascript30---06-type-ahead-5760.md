# JavaScript 上的新 Vue 30-06 提前输入

> 原文：<https://dev.to/davefollett/a-new-vue-on-javascript30---06-type-ahead-5760>

本文是 JavaScript30 系列的 [A New Vue 的一部分，该系列探索了使用](https://dev.to/t/anewvueonjs30) [Vue](https://vuejs.org) 重新实现韦斯博斯([@韦斯博斯](https://twitter.com/wesbos) ) [#JavaScript30](https://JavaScript30.com) 项目。今天我将使用#JavaScript30 的 **06 - Type Ahead** 项目。该项目使用输入来过滤用户输入的城市列表。除了过滤列表，它还在结果中突出显示输入值。这是一个动态的 gif 图片。

[![#JavaScript30 Finished Type Ahead Project](img/c97581d29e209379159b83124b1475e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---U163QXF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://davefollett.io/2018/11/02/a-new-vue-on-javascript30-06-type-ahead/js30-type-ahead.gif)

## 🔑“概念”视图

*   `v-for`指令
*   `v-model`在表单输入上创建双向数据绑定的指令
*   `v-html`指令
*   `mounted`生命周期挂钩
*   计算属性

## 🏗️ Vue 实施

第一步和我的其他文章一样，从我的[入门](https://dev.to/davefollett/a-new-vue-on-javascript30---getting-started-4o61)文章中抓取[基础启动文件](https://github.com/davefollett/JavaScript30/blob/master/00%20-%20Getting%20Started/index-VUE.html)，并将原始#JavaScript30 项目中的代码插入到它们对应的 Vue 位置。

[![Animation of inserting #JavaScript30 code into their corresponding Vue locations](img/f960d44f6270e781c5ad9fc3c873c3b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sUdOj7Oe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://davefollett.io/2018/11/02/a-new-vue-on-javascript30-06-type-ahead/js30-to-vue-locations.gif)

*   HTML 部分被放在根目录`<div id="app">`中
*   `endpoint`和`cities`变量被放入`data`部分
*   `numberWithCommas()`功能被放入`methods`部分
*   `findMatches()`和`displayMatches()`功能被放入`computed`部分
*   在页面加载时执行的`fetch()`调用被放到了`mounted`函数中
*   不需要`watch`部分，因此将其删除
*   因为样式表是用一个`<link>`标签拉进来的，所以删除了`<style>`部分

咻，那里发生了很多事情，但是如果你已经阅读了以前的文章，希望它是熟悉的😀。不幸的是，我们还有相当多的工作要做。下一步是调整`displayMatches()` computed 属性，为每个城市返回一个对象，而不是 HTML 列表项。