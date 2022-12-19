# 概念:具有承诺的高性能 web 服务器

> 原文：<https://dev.to/theopensourceu/concept-high-performance-web-server-w-promises-kb1>

## 目的

[![Concept: High Performance web server w/ Promises](img/593ef0f969249eec6596ad3e73ccaaaf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9FwUSFjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theopensourceu.org/conteimg/2018/02/webserver-promise.png)

我的目的是提供更高水平的吞吐量——通过一个*异步 API* 每秒处理更多的请求。预计为每个请求提供服务的时间会增加，但如果我们每秒处理更多的请求，这没有问题。*或*如果没有别的，至少在问题开始出现之前处理更多的请求。

## 概念概述- *如何？*

高层次的想法是在 Express + Node 上运行 http 服务器。从那里开始，任何传入的请求都被包装在承诺中，承诺解析为对请求的响应。当然，如果在处理请求的过程中，需要其他的异步处理，可以使用其他的`then`表。

例如，这个:

```
/* GET home page. */
router.get('/', function(req, res, next) {
    debug("GET /");
    res.render('site/index', { title: 'The Open Source U' });
}); 
```

变成了:

```
/* GET home page. */
router.get('/', function(req, res, next) {
  new Promise(function() {
    debug("GET /");
    return { title: 'The Open Source U' }; //calculation or object
  })
  .then(function(payload) {
    res.render('site/index', payload)
  });
}); 
```

这是不顾需要的。所以，我故意没有异步操作，但仍然使用了一个承诺；这准确地代表了我的想法。

## 结果:无效

我很快做了一个概念验证...这表明这没有帮助。事实上，它使性能更差。在考虑*为什么*时，我怀疑服务承诺的成本(内存和处理)比简单地响应 http 请求更昂贵。

这并不是说应该在`http`请求/响应周期中避免承诺，而是应该在异步的适当时间使用。不管怎样，我们在这里学到了一些东西，我认为值得分享。

> 成功不是最终的，失败也不是致命的:重要的是继续下去的勇气。温斯顿·丘吉尔

我很失望，这甚至没有对性能产生中性影响，更不用说负面影响，但事实就是如此。

## 更新(新文章来了)

因为一个关于 [Dev.to(这里)](https://dev.to/theopensourceu/concept-high-performance-web-server-w-promises-kb1)的评论，我重新审视了这个。评论者指出了我在发布的代码中犯的一个错误。那段代码是一个例子，而不是来自原始测试代码的 1:1(我从一个 [Linode 服务器](https://www.linode.com/?r=348bfde802d0569573c9d64dea875c1c39519950)运行它)。

我在这里发布了我的测试代码:[https://github . com/TheOpenSourceU/tOSU-PromiseWebServer-Experiment](https://github.com/TheOpenSourceU/tOSU-PromiseWebServer-Experiment)

我一开始就应该这么做。