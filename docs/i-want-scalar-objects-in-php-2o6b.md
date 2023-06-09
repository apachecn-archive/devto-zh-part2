# 我想要 PHP 中的标量对象

> 原文：<https://dev.to/mattsparks/i-want-scalar-objects-in-php-2o6b>

*[最初贴在 DevelopmentMatt.com 的](http://developmentmatt.com/i-want-scalar-objects-in-php/)*

最近，我读了 Andrew Carter 的一篇有趣的文章，题为[让 PHP 再次变得伟大](http://andrew.carterlunn.co.uk/programming/2018/06/25/make-php-great-again.html) *【廉价插件:这个链接包含在我最近的[简讯](http://developmentmatt.com/newsletter/)*。在这篇文章中，Andrew 提出了标量对象的话题。如果你不熟悉标量对象，它们代表单个值(整数、布尔、字符串等)。)可以在*上执行操作。在 PHP 中，这可能类似于:

```
$name = 'Matt';

var_dump($name->length()); // int(4) 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，据我所知，你使用术语“标量”的上下文是很重要的。不同的语言对标量有不同的实现和定义。

在标量物体进入我的脑海后不久，我看到了下面的推文:

液体错误:内部

对此我回答道:

> ![Matt Sparks profile image](img/70df7b83166ac7ab2a60fb77b6fa1963.png)马特火花@ development Matt![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Nikola posa](https://twitter.com/nikolaposa)我喜欢标量对象。2018 年 7 月 16 日 21 点 32 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1018971564287713281)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1018971564287713281)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1018971564287713281)

## PHP 中标量对象的情况

如果你读了 Andrew 的文章，你会发现他讨论了 PHP 的核心函数是如何不一致的。这个话题并不新鲜:

*   [PHP:糟糕设计的分形](https://eev.ee/blog/2012/04/09/php-a-fractal-of-bad-design/#standard-library)
*   [PHP RFC:一致的函数名](https://wiki.php.net/rfc/consistent_function_names)

PHP 8 是一个清除这些不一致的好机会。引入标量对象会提供一个一致的 API，使用起来会更加流畅和愉快。以下哪个对你来说更直观？

```
$hello = str_replace('Matt', 'Bob', 'Hello Matt!');

var_dump($hello); // Hello Bob! 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$hello = 'Hello Matt!';

var_dump($hello->replace('Matt', 'Bob')); // Hello Bob! 
```

Enter fullscreen mode Exit fullscreen mode

我知道这是一个愚蠢的例子，但是第二段代码不是感觉更好吗？

让我们在 PHP 8 中获取标量对象！谁和我在一起？！？

[T2`#ScalarIn8`](https://twitter.com/hashtag/ScalarIn8?src=hash)