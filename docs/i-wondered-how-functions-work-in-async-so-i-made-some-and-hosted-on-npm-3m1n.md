# 我想知道异步模块是如何工作的，所以我做了一些函数并托管在 npm 上

> 原文：<https://dev.to/meet_zaveri/i-wondered-how-functions-work-in-async-so-i-made-some-and-hosted-on-npm-3m1n>

自从我从事 node.js 开发以来，我经常使用曹兰的异步模块。它们提供了最佳的集合来提高 API 开发的效率。

有一天，我在桌子上想知道这个`async.auto()`或`async.parallel()`是如何如此完美地工作的。我也很好奇，想知道这背后会是什么样子，比如函数组合，回调实现和处理一系列控制流迭代。

我开始尝试实现其中的一些，尽管没有像曹兰那样完成更复杂的方法。我做了重构，只维护了其中的一些，并且局限于主流/通用的控制流方法。

测试了所有的，虽然需要更多的用例来发现问题，但当我把它从一个小沙箱中拿出来时，它仍然是工作的。

动机——对 async 很好奇，想从漂亮的回调中获得更多。
Github-[https://github.com/meetzaveri/elite-set-of-async-utilities](https://github.com/meetzaveri/elite-set-of-async-utilities)T3】https://www.npmjs.com/package/vanilla-async-[NPM](https://www.npmjs.com/package/vanilla-async)

非常感谢您的反馈！