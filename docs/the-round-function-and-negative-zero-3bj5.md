# 舍入函数和“负零”

> 原文：<https://dev.to/clpsplug/the-round-function-and-negative-zero-3bj5>

我在 qiita.com 写了这篇文章。我想我也将在这里分享我的史诗般的失败。

# 我又一次失败了。

这是一个我因为没有通读文档而被浮点数嘲笑的故事。我要强调多少次我应该仔细阅读文件？！！！？？

如果你非常了解浮点数，你可以跳过这篇文章或者嘲笑我的愚蠢。如果你不知道我在说什么，这篇文章可能会对你有所帮助。

但这是*而不是*的`0.1 + 0.2 != 0.3`问题。

# 有问题的温度

我正在制作一个 web 应用程序，在那里我必须使用 [OpenWeatherMap](https://openweathermap.org/) 嵌入一些天气预报。
[OWM](https://openweathermap.org/api)的 API 有很多信息，大部分我都不需要。我必须创建一个包含我需要的东西的数组。希望有人制作了一个名为 [OpenWeatherMap-PHP-API](https://github.com/cmfcmf/OpenWeatherMap-PHP-Api) 的包装器。

API 返回的一些值也是有问题的。下面是他们返回的数据样本。

```
"temp":{  "day":15.2,  "min":10.11,  "max":15.2,  "night":10.11,  "eve":15.04,  "morn":15.2  }, 
```

Enter fullscreen mode Exit fullscreen mode

我只需要`min`和`max.`它们分别代表‘最低温度’和‘最高温度’。当 PHP 读取这个 JSON 时，它将值存储为 **`float.`**
。由于篇幅限制，我想用整数表示它们，所以我尝试将它们四舍五入。

圆形函数( [`float round ( float $val [, int $precision = 0 [, int $mode = PHP_ROUND_HALF_UP ]] )`](https://secure.php.net/manual/en/function.round.php) )正是我所需要的。这个函数将把数值四舍五入成整数*...至少我是这么认为的。*

 *# 失败

如果我输入上面的值，会发生什么？假设我们将解码后的 JSON 对象存储在`$data.`
中

```
round($data['temp']['max']) // => 15
round($data['temp']['min']) // => 10 
```

Enter fullscreen mode Exit fullscreen mode

很标准，是吧？然而，我住在一个冬天会下雪的地方。让我们在秋天和冬天之间，或者冬天和春天之间的某个时候运行这段代码。

我得到的 JSON 如下所示:

```
"temp":{  "min":-0.44,  "max":4.15,  }, 
```

Enter fullscreen mode Exit fullscreen mode

aaaaand:

```
round($data['temp']['max']) // => 4
round($data['temp']['min']) // => -0 <- WTF? 
```

Enter fullscreen mode Exit fullscreen mode

# 该死的 PHP？

你可能听说过计算机界的`0.1 + 0.2 != 0.3`。“小故障”是**浮点数的另一种规格。** <sup id="fnref1">[1](#fn1)</sup> [他们就是这么工作的](https://en.wikipedia.org/wiki/Signed_zero) [！](https://en.wikipedia.org/wiki/Signed_zero#Scientific_uses)

等等，PHP 手册对`float`函数说了什么？

```
float round ( float $val [, int $precision = 0 [, int $mode = PHP_ROUND_HALF_UP ]] ) 
```

Enter fullscreen mode Exit fullscreen mode

四处漂浮。

***浮动。*T3】**

*对付它 m8，它的* ***你的*** *史诗不倒。*

# 我是怎么解决的

由于整数最初为负数对我的用例来说无关紧要，所以我将返回转换为`int.`(我也可以忽略`round`，直接将它们转换为`int.` )

```
(int)round($data['temp']['min']) // => 0 
```

Enter fullscreen mode Exit fullscreen mode

* * *

1.  然而，PHP 输出的值*看起来*像一个整数，这可能让我感到困惑。为什么会这样呢？ [↩](#fnref1)*