# 这是开发者的一小步，是网络的一大步

> 原文：<https://dev.to/stereobooster/thats-one-small-step-for-a-developer-one-giant-leap-for-the-web-4b28>

> 照片由 Joey Csunyo 在 Unsplash 上拍摄

我想谈谈那些勇敢的 JS 开发者，他们永远地改变了 web 开发。

## JSON

JSON——脱胎于 web 平台的限制和一点创造力。有 XMLHttpRequest 向服务器发出请求，而不需要完全重新加载，但是 XML 在网络上“很重”,所以道格拉斯·克洛克福特想到了一个聪明的办法——我们可以使用 JavaScript 对象符号和`eval`以简单的方式将数据从服务器传递到客户端，反之亦然。但是执行任意代码(`eval`)是不安全的，尤其是当它来自第三方时。所以下一步是标准化它，并为它实现一个特定的解析器。

后来它成为所有浏览器的标准，现在我们可以把它用作`JSON.parse`。

历史注释:Crockford 说 JSON 早在 1996 年就在 Netscape 中使用了，他只是在 2001 年重新发现了它并将其标准化。

## `Document.querySelectorAll()`

有像`Document.getElementById()`和`Document.getElementsByClassName()`这样的方法，但是用起来很笨拙。John Resig 创建了方便库来处理 DOM - jQuery。最方便的部分是通用查询选择器，后来 wsa 将其标准化为`Document.querySelectorAll()`。

历史记录:jQuery 创建于 2006 年，部分灵感来自狄恩·爱德华兹的 cssQuery(出现较早)。`XPath`有类似的想法(1999 年首次出现)。

补充说明:jQuery 的广泛使用也是因为它有很多解决浏览器怪癖和不一致性的方法，我想我们可以说 jQuery 和 AJAX 一样让跨浏览器开发变得更加容易。

## CommonJS，AMD，Requirejs，模块

### 服务器端故事

Node.js 是 Ryan Dahl 在 2009 年创建的。之前也有服务器端的 JavaScript 环境，比如网景的 LiveWire Pro Web，但是没有得到那么多关注。

服务器端环境一出现，就明确需要创建一个共享模块的标准。Kevin Dangoor 在 2009 年提出了 CommonJS(最初命名为 ServerJS)作为在浏览器之外指定 JavaScript 生态系统的标准。

### 客户端故事

jQuery 有插件，这些插件基于生活模式和脚本标签。类似这样的事情

```
(function ( $ ) {
  $.fn.plugin = function() {
    //...
    return this;
  };
}( jQuery )); 
```

Enter fullscreen mode Exit fullscreen mode

插件的分发是通过复制粘贴进行的。

CommonJS 最初是为服务器提出的，但很快就被移植到了客户端(Requirejs)。

CommonJS 的主要问题是它是同步的，这对于客户端来说不是很舒服，因为客户端必须处理网络延迟。下一步是异步模块定义(AMD)，它是异步的，允许指定依赖关系。CommonJS 和 AMD 同样受欢迎，所以人们想出了兼容两者的 UMD。

异步模块的一个问题是它们会产生一连串的下载，这会非常慢。为了应对这种情况，开发人员发明了 JS bundlers，如 Browserify 和 Webpack。看起来这不是一个简单的任务，如果模块导入过程是静态的，事情会简单得多。这一想法导致了 ES6 和声模块的产生，该模块于 2014 年 7 月被 TC39 正式接受。

Webpack、Requirejs 和 Promises 导致后来的动态导入建议。

## 承诺

在 [2011](http://blog.jquery.com/2011/01/31/jquery-15-released/) 中，Promise 概念被引入为 [jQuery 延迟对象](http://api.jquery.com/category/deferred-object/)。

在 [2012 年，它被提议作为 ES2015](https://github.com/promises-aplus/promises-spec) 的规范。

## 其他

*   `jQuery.ajax()`和其他 AJAX 库的启发`fetch`
*   基于锚点(又名“hashbang”)的客户端路由灵感历史 API
*   我还忘了什么？

## 我在这里看到一个模式

我不想贬低那些支持浏览器开发和网络标准的人的贡献。他们都在努力创建可供数百万开发者使用的 API。从第一次尝试开始就很难找到正确的答案。从这个意义上说，JS 开发人员有机会首先在现场测试 API 设计，然后当它被 web 平台认可和接受时再进行测试。

在提案被接受之前，先测试驱动提案似乎是一个很好的实践，就像他们对 TC39 提案和 babel 插件所做的那样。但是可以肯定的是，不可能以这种方式测试所有的提议，有些提议需要彻底的改变，需要首先由浏览器供应商来推动。

## 接下来是什么？

不知道 JS 开创的下一个东西会被平台采用的是什么？

是 React 普及的带时间切片、延迟渲染和水合的虚拟 DOM 吗？这似乎是下一个 JSON——足够黑客(想想扔承诺)，足够有创意，诞生于网络平台的限制。

是 CSS-in-JS 吗？看起来像是下一个 jQuery——web 平台提供的东西笨拙且难以使用，所以人们围绕它开发了一些好用的工具。

我不知道，这只是一些精神食粮🤔。让我知道你认为下一件事会是什么？

* * *

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。