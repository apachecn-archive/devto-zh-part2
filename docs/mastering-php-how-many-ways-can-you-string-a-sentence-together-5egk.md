# 掌握 PHP:有多少种方法可以把一个句子串在一起？

> 原文：<https://dev.to/slyfirefox/mastering-php-how-many-ways-can-you-string-a-sentence-together-5egk>

[![](../Images/d40682aebe23bbdabecb3bedf8129e71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KaZd1TBT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A90CHU_MnGSXClZKu.)

<figcaption>[伊利亚·巴甫洛夫](https://unsplash.com/@ilyapavlov?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

上的《电脑屏幕上的代码行特写》

这个标题可能看起来有点愚蠢，每个 PHP 开发人员都知道如何连接字符串吧？不过，值得更深入地思考的是，我们实际上可以用多少种不同的方法来做到这一点？有没有正确的做法？有没有一个时候，我甚至上帝都禁止我这样做，这比需要的更难？一年后再来看，哪种方式会更容易读懂？

好了，让我们来看看我能想出哪些不同的方法。

### [短跑](#sprintf)

所以让我们从一个跨几种语言通用的函数开始(它通常被称为 *printf* ，即 c .*sprintf*函数非常好，但在 PHP 中不常使用。基本上，你提供一个模板，然后变量来填充你的模板。