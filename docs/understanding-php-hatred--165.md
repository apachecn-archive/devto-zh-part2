# 理解 PHP 的仇恨

> 原文：<https://dev.to/joshghent/understanding-php-hatred--165>

### 了解 PHP 仇恨

<figure>[![](img/9d95e36d34c9a002196022287383bfb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2u-XC2My--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbrjYnVlXwM3j_SWXImT0Rg.png) 

<figcaption>图为:PHP 开发者在他们自然状态下的沉默蔑视</figcaption>

</figure>

这是一个古老的令人讨厌的笑话。但是为什么大家这么不喜欢呢？毕竟， [PHP 驱动了 80%的网络](https://w3techs.com/technologies/details/pl-php/all/all)(其中大部分归功于 [Wordpress](https://wordpress.com/) ，但仍然如此)。在这篇文章中，我分解了 PHP 开发的主要问题，并分享了关于语言和系统设计的建议。

#### 方法命名不一致

人们第一次看 PHP 时看到的最大问题是标准语言方法的不一致性。当 [PHP 在 1994 年](https://secure.php.net/manual/en/history.php.php)第一次发布时，它没有[命名空间](https://en.wikipedia.org/wiki/Namespace)，这意味着所有方法必须在**根**级别全局存在。当[名称空间最终被引入 PHP 5](https://secure.php.net/manual/en/language.namespaces.basics.php) 时，损害已经造成了。通常在特定类别下以[命名空间](https://en.wikipedia.org/wiki/Namespace)命名的方法(例如* *String** 或* *Array** )，只是用类别链接和前缀。

这就产生了诸如 [array_map](https://secure.php.net/manual/en/function.array-map.php) 和 [str_repeat](https://secure.php.net/manual/en/function.str-repeat.php) 这样的名字。这很好，但问题是前缀+下划线的方法并不总是被使用。很快，有一大堆名为[strtolow](https://secure.php.net/manual/en/function.strtolower.php)和 [ucfirst](https://secure.php.net/manual/en/function.ucfirst.php) 的方法打破了这些规则。

此外，这些方法名对 [snake_case](https://en.wikipedia.org/wiki/Snake_case) 的用法不一致——大多数 string 方法都是如此。你有 [strtotime](https://secure.php.net/manual/en/function.strtotime.php) 和 [str_split](https://secure.php.net/manual/en/function.str-split.php) 这样的函数——为什么不是 str_to_time？谁知道呢。

此外，在研究 PHP 方法的[列表之前，我没有注意到的另一个小矛盾是**‘to’**和**‘2’**的用法。在某些情况下,**‘2’**被替换到方法名中，大概是为了看起来像 21 世纪初一个十几岁的少年在用](https://secure.php.net/manual/en/indexes.functions.php)[诺基亚 3310](https://en.wikipedia.org/wiki/Nokia_3310) 发短信。

因此，我们现在有了诸如' [bin **2** hex](https://secure.php.net/manual/en/function.bin2hex.php) 和' [deg **2** rad](https://secure.php.net/manual/en/function.deg2rad.php) 以及 [str **到** time](https://secure.php.net/manual/en/function.strtotime.php) 和 [str **到** lower](https://secure.php.net/manual/en/function.strtolower.php) 等方法。

说了这么多，你和我有什么收获？在软件设计和大多数事情中，一致性是关键。通过为程序提供一致的界面，使其既能以编程方式使用(具有一致且符合逻辑的 API 端点和参数)，又能以可视化方式使用(具有易于使用且功能强大的 UI ),我们为使用我们的 UI 的人员和集成了我们的 API 的开发人员提供了更加符合逻辑和清晰的用法。

不要对 PHP 开发人员太苛刻，但是很明显，很少有人考虑规划这种语言或考虑它的未来范围。不要犯同样的错误。深入了解你的系统的所有小特性和怪癖，以及你将来可能添加的特性和怪癖。不可能为每一个最终结果做好准备，但拥有前瞻性的观点是值得的，所以你不太可能对“这个”产品有狭隘的看法。软件总是会变的。

#### 论证顺序不一致

另一个不一致之处是参数排序。数组、字典、散列，无论你怎么称呼它们，它们都是使用该语言的开发人员日常使用的任何语言不可或缺的一部分，并且构成了在任何系统上存储和操作数据的核心部分。

你可能会认为，作为语言中如此重要的一部分，它们至少应该是一致的。不幸的是，你错了。

如果你曾经做过 PHP 开发，你可能会遇到这个问题。你得到了一个数组，你想把它加倍，然后返回到一个新的数组中。没问题！你说。

> “我就用[数组 _ 映射](https://secure.php.net/manual/en/function.array-map.php)！”。

所以你写代码，然后运行它，然后…

[![](img/684b2787ed83f94d5521f8d0ead1f6c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EsX2kb0o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/858/0%2AiKeBG9ial1LFcExP.)

什么？你说。经过大量的调试，考虑到您的方法可能有问题，您最终求助于 PHP 文档。

在那里你会发现…

<figure>[![](img/0e953aa8a7c13f2cb9c6283e0d373fc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UGBI4K5u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWdQmqjGHTWJD8Avo29rMjw.png) 

<figcaption>文档为数组 _ 地图([【https://secure.php.net/manual/en/function.array-map.php】](https://secure.php.net/manual/en/function.array-map.php))</figcaption>

</figure>

**先回调。不是最后回调，就像你刚才用 array_filter 做的那样。**

我不知道这样做了多少次，但每次你都会忍不住轻轻地诅咒拉斯马斯·勒德尔夫这个名字。

除了我们已经谈到的一致性的重要性，我们还能学到什么。在我看来，这是有用的错误信息。不要像在这种情况下吐出一些模糊和有意义的东西，而是写一些有帮助和可操作的东西。我读过一篇关于这个话题的很棒的文章——你可以在这里找到它。我强烈推荐阅读它。理想情况下，你希望你的 UI(包括可视化的和编程式的 UI)尽可能的直观，但是要考虑到有人犯错的情况(我们都会犯错)，并且通过引导用户到正确的路线来优雅地处理它。

#### 令人沮丧的用法

除了命名不当，这些方法的使用也令人沮丧。

[Explode](https://secure.php.net/manual/en/function.explode.php) 是一个方法，它接受一个字符串和一个分隔符，并将分隔符上的字符串分解成一个数组。简单吧？你可能在使用 [String.split()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) 和其他语言的 Javascript 中见过这种情况。奇怪的是，传递空字符串("")或 null 作为分隔符会导致该方法返回 false。PHP 没有像对待其他语言一样简单地对待它(空字符串将每个字符作为数组的一个元素返回，或者 null 将整个字符串返回)，而是决定将其视为一个错误条件。但是因为它不会抛出错误，所以您必须手动检查它。

另一个恶化的方法使用情况是在操作数组时。PHP 中的 Sort() 和所有其他的数组排序方法(有很多，名字都比上一个更容易混淆)就地操作数组，并且不返回新的被操作的数组。它们只是返回真或假。这阻止了方法链接，并使您使用数组操作编写的代码比其他方式编写的代码更加冗长。此外， [array_reverse](https://secure.php.net/manual/en/function.array-reverse.php) (在相同的数组操作类别中)确实返回了一个新数组，但是这又意味着更多的不一致性(尽管在这种情况下，不一致性是好的)。

然而，毫无疑问，烦恼的三重来源于在字符串中查找字符串。这再简单不过了。每种语言都有这样的方法，它们都是一样的。接受一个针(您要查找的字符串)和一个干草堆(您要在其中查找它的字符串)，然后该方法返回找到该针的干草堆索引，如果没有找到，则返回-1。Javascript、C 和大多数其他语言都是如此。然而，作为语言潮人的 PHP 认为这还不够好，并决定打破现状，如果针没有找到，就返回 false。这听起来并不坏(虽然与现有的其他语言不一致)，但如果你在 PHP 中粗略地比较 false，它就变成了 0。这是以下代码的问题

[![](img/1b0e9f5ac34f05f446d01e93e1e2964b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--roHhIh3t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAtvD1m_29mSkdMJw_2IQhw.png)

不幸的是，因为开发人员期望从 [strpos](https://secure.php.net/manual/en/function.strpos.php) 方法得到除-1 之外的任何响应，所以即使针显然不在干草堆中，该代码也将返回 true。我发现这是 PHP 中最明显的疏忽之一，因为在编写依赖于此的程序时很容易出错，而且与其他语言不一致。

#### 错误信息不良

错误消息是 PHP 的一个主要问题。我清楚地记得我对 PHP 的第一次抱怨——调试。由于当时不熟悉 PHP，我没有想到使用第三方工具来调试我的代码。这应该是内置的，对吗？令人惊讶的是(不幸的是)没有。我花了一段时间在谷歌上搜索，发现我[必须用一些特定的变量和调试级别打开错误](https://stackoverflow.com/questions/1053424/how-do-i-get-php-errors-to-display)。如果你看看“[如何打开 php 错误](https://www.google.co.uk/search?q=How+to+turn+on+php+errors&oq=How+to+turn+on+php+errors&aqs=chrome..69i57&sourceid=chrome&ie=UTF-8)”或“ [PHP 黑屏，无错误](https://www.google.co.uk/search?q=PHP+blank+screen%2C+no+error&oq=PHP+blank+screen%2C+no+error&aqs=chrome..69i57j69i64&sourceid=chrome&ie=UTF-8)”的搜索结果，这个问题很快就变得明显了。

现在，你可能已经得到了错误信息 ***实际上*** 正在工作，但是迟早你会碰到这个宝石。

> **PHP:** [解析错误:语法错误，意外的 T _ pamayim _ nekudotaim](http://phpsadness.com/sad/1)
> 
> **你**:一个什么？！
> 
> PHP :当然是 Paamayim Nekudatayim

对于外行人来说，[pamayim nekudatayim](https://en.wiktionary.org/wiki/%D7%A4%D7%A2%D7%9E%D7%99%D7%99%D7%9D_%D7%A0%D7%A7%D7%95%D7%93%D7%AA%D7%99%D7%99%D7%9D#Hebrew)是希伯来语“两次冒号”的罗马化版本，指的是[范围解析操作符](https://en.wikipedia.org/wiki/Scope_resolution_operator#PHP)(::)。你可以用它来调用这样的静态方法

[![](img/6be34f11d1e9868c77629f225f206b31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SQf9Qkvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap1H01HBwqUr8OhBQf-556w.png)

这最初是由 PHP 3 中以色列建造的 Zend Engine 引入的。这对于说希伯来语的人来说没问题，但是英语被广泛认为是编程和互联网的通用语言。同样，这一切都与易用性有关。在发现其含义后，在某种程度上，我有点喜欢它作为 PHP 的一个有趣的怪癖，有一个有趣的背景故事，但它对新的 PHP 开发人员(或开发人员句号)来说非常困惑。

这个错误消息在 PHP 7 中一直存在。

PHP 错误消息的主要问题是细节和特殊性。在某些时候，因为你不是一个机器人(或者也许你是，如果是这样，请在继续之前填写这个验证码)，你会错过输入一些东西，可能会缺少一个括号或引号。也许你的代码看起来像这样。

[![](img/02e5343c9ff6c62fb489c378f69d8d0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s9c02o9y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAODwsrc4MDA1ZfPh4les3w.png)

<figure>[![](img/bfc97d500e581b32477ccce215d84eab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wj0rbIxg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/750/1%2AzQTkfZ1biXDZ8r_rflvaaw.png) 

<figcaption>以上代码的结果</figcaption>

</figure>

如果您是一名久经沙场的 PHP 开发人员，那么您可能已经发现第 5 行的右引号不见了。然而 PHP 认为从第 8 行返回消息是有帮助的。

快速调试对于编程语言来说是一个非常重要的问题。这不仅让开发人员感到沮丧，而且模糊的错误消息意味着开发人员要花费更多的时间进行调试，这会给公司和/或客户带来成本。在上面的例子中，看起来这是一座由鼹鼠丘组成的山，但是想象一下在一个大的应用程序中，这个问题会变得越来越糟糕。

#### 方法重复

最后但同样重要的是方法重复的问题——就像[死亡](https://secure.php.net/manual/en/function.die.php)和[退出](https://secure.php.net/manual/en/function.exit.php)以及[内爆](https://secure.php.net/manual/en/function.implode.php)和[加入](https://secure.php.net/manual/en/function.join.php)的情况一样。现在，这似乎不是最大的罪过。毕竟，die 来自 Perl，因此对于具有这种背景的程序员来说更容易使用，而 exit 来自 C，这又允许他们有一个更容易的过渡。

对于新程序员或没有 C/Perl 背景的程序员来说，这个问题并没有变得更容易，只是更令人困惑。你最后会问用哪个？一个比另一个好吗？一个对另一个的强制应该在样式指南中吗？所有有效的问题都让开发人员陷入了语法怪癖的兔子洞，而不是实际着手手头的任务。

有益的是，PHP 手册澄清了这些差异

[![](img/4ad3b0c1e49dc8c58c70bb768a33d9ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wp9ARf7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/740/1%2AEBTgMqTKHbUjL5mIEQQyDg.png)

是啊…也许不是。从这一点来看，重要的是不要用一个或另一个决策背后的遗留推理故事来污染您的代码库文档(正如这里所做的)。另一方面，质疑一开始是否应该有那些故事。

尽管还有很多从有趣到彻底疯狂的怪癖，我发现 PHP 非常有用，全世界成千上万的开发人员也是如此。如果你是一个有创业想法的 PHP 开发人员，不要等着学习一门新的语言或框架来构建它。用 PHP 做就行了。软件总是可以迭代的，而担心“我应该用什么编程语言来做”比 PHP 的一些怪癖更加疯狂和多余。所有的语言都可以做任何事情(当然除了系统特定的本地应用程序，但是你要明白这一点)——有些只是和其他的不同，这就是为什么我们有这么多的语言。但是，你用不同的语言编写应用程序而不是用你知道的语言编写应用程序所获得的速度提升将会被抵消 10 倍，因为你需要花时间来学习新的语言。

古老的谚语是“只有拙劣的工匠才会责怪他的工具”。现在用 PHP 写这该死的东西。

* * *