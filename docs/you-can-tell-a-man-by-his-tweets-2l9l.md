# 你可以通过一个人的微博来判断他

> 原文：<https://dev.to/ericbonfadini/you-can-tell-a-man-by-his-tweets-2l9l>

前一段时间，我读了 Kenneth Reitz 的一篇推文，他是我在 Twitter 上关注的一位非常著名的 Python 开发人员，他问道:

> ![Kenneth Reitz 🐍 profile image](img/a0f59b812abbebdaa722db773f611639.png)肯尼斯·雷兹🐍[@ kennethreitz](https://dev.to/kennethreitz)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)制作一份 Python 开发者 twitter 账号列表，以供效仿。你推荐谁？2018 年 1 月 14 日下午 14:49[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=952553176925958145)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=952553176925958145)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=952553176925958145)

Starting from this, I decided to analyze some tweets from pretty popular Python devs in order to understand a priori how they use Twitter, what they tweet about and what I can gather using data from Twitter APIs only.
Obviously you can apply the same analysis on a different list of Twitter accounts.

# 设置环境

对于我的分析，我用以下主要库建立了一个 Python 3.6 虚拟环境:

*   用于与 Twitter APIs 交互的 Tweepy
*   [熊猫](https://pandas.pydata.org/)用于数据分析和可视化
*   [美汤 4](https://pypi.org/project/beautifulsoup4/) 、 [NLTK](https://www.nltk.org/) 和 [Gensim](https://radimrehurek.com/gensim/) 用于处理文本数据

稍后将介绍一些额外的库，以及我所做的步骤的解释。

为了访问 Twitter APIs，我[注册了我的应用程序](https://apps.twitter.com/)，然后我向 tweepy 库提供了 consumer_key、access_token、access_token_secret 和 consumer_secret。

我们现在准备从 Twitter 获取一些真实的数据！

# 选择 Twitter 账户列表

首先，我选择了 8 个流行的 Python devs 的列表，从[PyPI](https://pythonwheels.com/)上下载量最高的 360 个包开始，并选择一些我知道或日常使用的库。

这是我最后的清单，包括 Twitter 账户和图书馆的链接(从上面提到的清单中),这些人因为这些而出名:

*   [@kennethreitz](https://twitter.com/kennethreitz) :请求
*   [@三光彦](https://twitter.com/mitsuhiko) : jinja2/flask
*   [@ zzzeek](https://twitter.com/zzzeek):SQL anywhere
*   [@ teoliphant](https://twitter.com/teoliphant):numpy/scipy/numba
*   [@benoitc](https://twitter.com/benoitc) : gunicorn
*   [@asksol](https://twitter.com/asksol) :芹菜
*   [@wesmckinn](https://twitter.com/wesmckinn) :熊猫
*   [@cournape](https://twitter.com/cournape) : scikit-learn

# 从 Twitter 获取数据

我只获得了两个端点的所有数据:

*   通过调用 [lookup users](https://developer.twitter.com/en/docs/accounts-and-users/follow-search-get-users/api-reference/get-users-lookup) ,我可以获得关于帐户的所有信息(创建日期、描述、计数、位置等。)
*   通过调用[用户时间线](https://developer.twitter.com/en/docs/tweets/timelines/api-reference/get-statuses-user_timeline.html)，我可以获得单个用户的推文以及与每条推文相关的所有信息。我将该调用配置为获取转发和回复。

我将两次调用的结果保存在两个 Pandas 数据帧中，以便简化数据处理，然后保存到 CSV 文件中，作为下一步的起点，而不用每次都重新调用 Twitter API。

# 预处理推文

用户数据框包含了我需要的所有信息；我刚刚又创建了三列:

*   追随者/追随者比率，一种“受欢迎程度”指标
*   每天的推文比率，将推文总数除以自帐户创建以来的天数
*   从位置开始的坐标，如果可用，使用 [Geopy](https://pypi.org/project/geopy/) 。 [@benoitc](https://dev.to/benoitc) 没有位置，而@zzzeek 有一个通用的“东北”，地理位置在内布拉斯加州:-)

这是最终用户数据框架:

| 屏幕名称 | 名字 | 已证实的 | 描述 | 创建日期 | 位置 | 位置 _ 首席运营官 | 时区 | tweets 总数 | 收藏夹 _ 计数 | 关注者 _ 计数 | 跟随计数 | 列出 _ 计数 | 关注者 | 每日推文数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 肯纳茨雷茨 | 肯尼斯·雷茨 | 错误的 | @DigitalOcean & @ThePSF。请求的创造者:人类的 HTTP。我设计艺术——用代码、照相机、乐器和英语。 | 2009-06-24 23:28:06 | 弗吉尼亚州温彻斯特 | [39.1852184, -78.1652404] | 东部时间(美国和加拿大) | Fifty-eight thousand one hundred and ninety-five | Twenty-six thousand five hundred and seventy-five | Eighteen thousand two hundred and thirteen | Four hundred and eighty | Eight hundred and twenty-two | 37.94375 | 17.950339296730412 |
| 光彦 | 阿明·罗纳彻 | 真实的 | 烧瓶的创建者；在@ getsentryprev @fireteamltd，@ splash damage——撰写和谈论系统架构、API 设计和大量 Python | 2008-02-01 23:12:59 | 奥地利 | [47.2000338, 13.199959] | 维也纳 | Thirty-one thousand seven hundred and seventy-four | Two thousand and fifty-nine | Twenty-one thousand eight hundred and one | Five hundred and ninety-three | Nine hundred and forty-one | 36.76391231028668 | 8.470807784590775 |
| zzzeek | 迈克·拜尔 | 错误的 |  | 2008-06-11 19:22:19 | 东北 | [41.7370229, -99.5873816] | 基多 | Fourteen thousand seven hundred and seventy-one | One thousand one hundred and six | Three thousand and thirteen | Two hundred and nine | One hundred and ninety-four | 14.416267942583731 | 4.080386740331492 |
| 大象 | 特拉维斯·奥列芬特 | 错误的 | SciPy、NumPy 和 Numba 的创造者；Anaconda 公司创始人兼董事 NumFOCUS 公司创始人。全信首席执行官 | 2009-04-17 20:04:57 | 德克萨斯州奥斯汀 | [30.2711286, -97.7436995] | 中部时间(美国和加拿大) | Three thousand eight hundred and seventy-five | One thousand five hundred and six | Eighteen thousand and fifty-two | Four hundred and eighty-three | Seven hundred and forty-six | 37.374741200828154 | 1.1706948640483383 |
| 伯努瓦 c | 贝努瓦·切桑尼 | 错误的 | 网络工匠 | 2007-02-13 16:53:37 |  |  | 巴黎 | Twenty-seven thousand one hundred and seventy-two | Five hundred and forty-eight | One thousand nine hundred and seventy-one | Seven hundred and four | Two hundred and forty-seven | 2.799715909090909 | 6.620857699805068 |
| 阿斯克苏 | 问 Solem | 错误的 | 声音、噪声、流处理、分布式系统、数据、开源 python 等。在@robinhoodapp 工作🌳🌳🌴🌵 | 2007-11-11 18:56:33 | 加利福尼亚州旧金山 | [45.4423543, -73.4373087] | 太平洋时间(美国和加拿大) | Three thousand two hundred and forty-nine | One hundred and ninety-one | Two thousand five hundred and nine | Five hundred and thirteen | One hundred and twenty-six | 4.89083820662768 | 0.8476389251239238 |
| 韦斯麦金 | 韦斯·麦金尼 | 真实的 | https://t.co/YVn0VFqgj0 的数据科学工具制造商。熊猫的创造者@IbisData。@ApacheArrow @ApacheParquet PMC。写 Python 做数据分析。我自己的观点 | 2010-02-18 21:01:15 | New York, NY | [40.7306458, -73.9866136] | 东部时间(美国和加拿大) | Seven thousand seven hundred and forty-nine | Three thousand and twenty-one | Thirty-three thousand one hundred and thirty | Seven hundred and eighty-four | One thousand two hundred and seventy-seven | 42.25765306122449 | 2.5804195804195804 |
| 古典组曲的第二乐章 | 大卫库尔纳波 | 错误的 | Python 和统计极客。前 NumPy/Scipy 核心撰稿人。领导 ML 工程团队@cogentlabs 偶尔思考经济/音乐/日本文化 | 2010-06-14 03:17:21 | 日本东京 | [34.6968642, 139.4049033] | 阿姆斯特丹 | Fifteen thousand five hundred and seventy-seven | Five hundred and five | eight hundred | Four hundred and twenty-seven | One hundred and twelve | 1.873536299765808 | 5.395566331832352 |

相反，tweets 数据帧需要一些额外的预处理。

首先，我发现了关于 Twitter 用户时间线 API 的一个恼人的限制:可以返回的 tweets 有一个最大数量(大约 3200，包括转发和回复)。因此，我决定按用户名对推文进行分组，并获取每个用户最早的推文日期:

| 用户名 | 数数 | 部 | 最大 |
| --- | --- | --- | --- |
| 阿斯克苏 | Two thousand nine hundred and ninety-one | 2009-07-19 19:24:33 | 2018-05-10 14:58:17 |
| 伯努瓦 c | Three thousand one hundred and ninety-nine | 2017-02-21 14:55:37 | 2018-05-11 19:36:21 |
| 古典组曲的第二乐章 | Three thousand one hundred and seventy-nine | 2017-06-12 19:13:20 | 2018-05-11 16:55:39 |
| 肯纳茨雷茨 | Three thousand two hundred | 2017-08-26 20:48:35 | 2018-05-11 21:07:46 |
| 光彦 | Three thousand two hundred and twenty-six | 2017-06-14 13:23:57 | 2018-05-11 19:26:07 |
| 大象 | Three thousand two hundred and one | 2013-02-19 03:54:16 | 2018-05-11 16:48:39 |
| 韦斯麦金 | Three thousand two hundred and five | 2014-01-26 17:45:07 | 2018-05-03 14:51:29 |
| zzzeek | Three thousand two hundred and fourteen | 2015-05-05 13:35:38 | 2018-05-11 14:17:02 |

然后我过滤掉了第一次约会(2017-08-26 20:48:35)之间最大值之前的所有推文。
从这些数据开始， [@kennethreitz](https://dev.to/kennethreitz) 正在影响截稿日期，因为他发推特的次数比其他用户多得多；但是通过这种方式，我们至少可以获得所有用户相同的时间段，并比较同一时期的推文。

经过过滤后，我得到了 25418-14470=10948 条推文，按以下方式划分:

| 用户名 | 数数 | 部 | 最大 |
| --- | --- | --- | --- |
| 阿斯克苏 | fifty-two | 2017-09-11 18:36:19 | 2018-05-10 14:58:17 |
| 伯努瓦 c | One thousand eight hundred and forty-nine | 2017-08-26 20:49:13 | 2018-05-11 19:36:21 |
| 古典组曲的第二乐章 | One thousand eight hundred and eighty-eight | 2017-08-27 01:02:11 | 2018-05-11 16:55:39 |
| 肯纳茨雷茨 | Three thousand two hundred | 2017-08-26 20:48:35 | 2018-05-11 21:07:46 |
| 光彦 | Two thousand three hundred and twenty-eight | 2017-08-27 08:22:19 | 2018-05-11 19:26:07 |
| 大象 | Four hundred and forty-three | 2017-08-26 22:57:23 | 2018-05-11 16:48:39 |
| 韦斯麦金 | Five hundred and ninety-one | 2017-08-28 18:07:08 | 2018-05-03 14:51:29 |
| zzzeek | Five hundred and ninety-six | 2017-08-26 22:14:11 | 2018-05-11 14:17:02 |

其他预处理步骤:

*   我使用 Beautiful Soup 解析源信息，因为它包含 HTML 实体
*   我删除了文本中的智能引号
*   我将文本转换成小写
*   我删除了网址和号码

经过这些步骤后，我过滤掉了所有带有空文本的推文(即只包含 URL 等)，我得到了 10948-125=10823 条推文。

最后，我创建了一个包含“tweet 类型”(标准、回复或转发)的新列和另一个包含 tweet 长度的列。

以下是最终推文数据框架中的一些栏目(前 5 行):

| 用户名 | 创建日期 | 全面测试 | 文本 _ 清理 | 语言 | 收藏 _ 计数 | 转发次数 | 来源 | tweet_type | 推特 _len |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 肯纳茨雷茨 | 2018-05-11 21:07:46 | RT @IAmAru: Trio 是@kennethreitz 认可的。#PyCon2018 | rt trio 获得批准 | 在中 | Zero | one | IOs 的 Tweetbot | 转发 | Fifty-four |
| 肯纳茨雷茨 | 2018-05-10 21:55:35 | 如果你想打个招呼，请在开幕酒会期间到 DigitalOcean 展台来一趟！#PyCon2018 | 如果你想打个招呼，可以在开幕酒会期间去数字海洋展台 | 在中 | Twenty | Two | IOs 的 Tweetbot | 标准 | Ninety-five |
| 肯纳茨雷茨 | 2018-05-10 20:34:20 | @dbinoj 24x 1x dynos 现在 | x x dynos 现在 | 在中 | Zero | Zero | IOs 的 Tweetbot | 回答 | Thirty |
| 肯纳茨雷茨 | 2018-05-10 20:11:37 | 请访问@IndyPy 展台，您将有机会赢得 Python 搭便车指南的签名版！✨🍰https://t.co/CZhd2If5s0✨[https://t.co/3kUaqu5TMX](https://t.co/CZhd2If5s0)[T3】](https://t.co/3kUaqu5TMX) | 请到展台来，您将有机会赢得一本签名版的《python 搭便车指南》 | 在中 | Twenty-five | three | IFTTT | 标准 | One hundred and fifty-two |
| 肯纳茨雷茨 | 2018-05-10 13:53:31 | 就这么办[https://t.co/6xLCE4WCqA](https://t.co/6xLCE4WCqA)https://t.co/ERiMmffe8LT2 | 让我们开始吧 | 在中 | Twenty-two | one | IFTTT | 标准 | Sixty-one |

# 探索性数据分析

用户数据框架本身已经显示了一些洞察力:

*   只有两个账号有验证标志:@mitsuhiko 和@wesmckinn
*   @wesmckinn、 [@kennethreitz](https://dev.to/kennethreitz) 、@teoliphant 和@mitsuhiko 是榜单中最受欢迎的账号(根据我的“人气”指标):![popindicator](img/f69c5c1f8359a72e59a81e702e7b99c9.png)
*   [@kennethreitz](https://dev.to/kennethreitz) 自从他的账户创建以来，每天发的微博数量至少是名单中其他开发者的两倍:![tweetsperday](img/5a94b60549de8470226c2c027f271594.png)
*   名单中的大多数账户都居住在美国；我用[叶子](https://github.com/python-visualization/folium)创建了一个显示地点的地图:![map](img/d0115ecb8ead749cb8a6f505e2bbc125.png)

相反，在我们收集到一些好的见解之前，tweets 数据框架需要一些操作。

首先，让我们检查每个账户的推文“风格”。例如，从下面的图表中我们可以看到@cournape 转发了很多，而@mitsuhiko 回复了很多: [![tweettype](img/ddb85a9ba4d5a561b2420460152c6c39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Ms20e6z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nv1czyjrk6q9ipoxdxfj.png)

我们也可以按用户名和推文类型分组，并显示平均推文长度的图表。例如，@kennethreitz 写的回复比标准推文短，而@teoliphant 写的推文比其他人长(超过 140 个字符的限制): [![tweetlen](img/cc23ae8c3c2f0edc9fb3ca64b033b42c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NrwJ_JuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jm1m8gt31f8eg1o93mn.png)

好了，现在让我们过滤掉转发，让我们专注于标准推文和回复中使用的机器检测语言。五种最常见的语言是:英语、德语、法语、未定义语言和一种相当奇怪的“他加禄语”(ISO 639-1 代码“tl”)，也许是自动检测中的错误？).大多数推文是英文的；@mitsuhiko 用德语发了很多推文，而@benoitrc 用法语: [![lang](img/87f46b11984eb4cb212db72afb619e56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MlkwLa-V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x15a61kev9e9r9p1zn2q.png)

所以，让我们只选择英语或未定义的推文:所有接下来的图表只是考虑英语推文和回复(但显然你可以调整不同的分析)。
让我们按用户名分组，统计每个用户收藏/转发的次数:

| 用户名 | 收藏夹计数计数 | 最大收藏计数 | favorite_count 均值 | 收藏夹 _ 计数标准 | 最大转发计数 | 转发计数均值 | 转发计数标准 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 阿斯克苏 | Forty-six | Forty-one | 1.608695652173913 | 6.111840097055933 | Three | 0.10869565217391304 | 0.48204475908203187 |
| 伯努瓦 c | One thousand and nine | Thirty | 0.6531219028741329 | 1.8313280878865186 | Seventeen | 0.13676907829534193 | 0.7644934696088941 |
| 古典组曲的第二乐章 | Two hundred and fourteen | Sixty | 1.2757009345794392 | 4.449367547428712 | Twenty-five | 0.205607476635514 | 1.7481758044670788 |
| 肯纳茨雷茨 | Two thousand six hundred and thirty-seven | Three thousand nine hundred and thirty-two | 10.062571103526736 | 82.09998594317476 | Two thousand five hundred and seventy-three | 2.620781190747061 | 50.79602602503255 |
| 光彦 | One thousand five hundred and forty-seven | Seven hundred and fifty-two | 9.657401422107304 | 41.06463543974671 | Two hundred and twenty | 1.8526179702650292 | 9.932970595417615 |
| 大象 | One hundred and eighty-six | Eight hundred and eight | 26.080645161290324 | 69.54002504187612 | One hundred and thirty-four | 7.806451612903226 | 17.085639972995896 |
| 韦斯麦金 | Four hundred and thirty-three | Two thousand and eighty-one | 45.750577367205544 | 142.2699008271913 | Six hundred and ninety-five | 12.270207852193995 | 48.083342617014644 |
| zzzeek | Four hundred and thirty-nine | Eighty-five | 2.173120728929385 | 6.417876507767421 | Twenty-eight | 0.44874715261959 | 1.896040581838119 |

从这个表中我们可以看出:

*   [@kennethreitz](https://dev.to/kennethreitz) 在数据框中拥有最多转发和最受欢迎的推文。下面是推文:

    > ![unknown tweet media content](img/012272ceadc61b5b3f60a36dd886b492.png)![Kenneth Reitz 🐍 profile image](img/a0f59b812abbebdaa722db773f611639.png)肯尼斯 雷兹🐍[@ kennethreitz](https://dev.to/kennethreitz)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)选择一个 OS下午 2018 年 04-04 15:[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=981547972239417345)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=981547972239417345)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=981547972239417345)

*   @ wesMcKinnhas the second most retweeted and favorite tweet in the data frame. Here's the tweet:

    > ![Wes McKinney profile image](img/8318060c95786c97904c5dd16da8c5c1.png)Wes McKinney@ Wes McKinney![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)Big news today! I set up Ursa Lab, which is an open source data science development lab supported by [@ apacherrow](https://twitter.com/ApacheArrow) . I work with [@ Hadley Wickham](https://twitter.com/hadleywickham) and [@ rstudio](https://twitter.com/rstudio) , Make all this possible [wesmckinney.com/blog/announcin …](https://t.co/X93v53lEbG)April 19, 2018 at 16: 00 pm[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png) ](https://twitter.com/intent/tweet?in_reply_to=986998077767716865) [ ![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png) ](https://twitter.com/intent/retweet?tweet_id=986998077767716865) [ ![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=986998077767716865)

*   @wesmckinn 的转发次数和收藏次数的平均值最高

因为@wesmckinn 也有最高的关注者数，如果我们使用关注者数标准化数据帧，这些统计数据会有什么变化？显然，一条推文甚至可以获得非关注者的支持/转发，但这种标准化可能会产生更公平的结果，因为关注者数量越多，该推文可能会被浏览得越多。

| 用户名 | 收藏夹计数百分比计数 | favorite_count perc max | 收藏夹计数百分比平均值 | 收藏夹计数百分比标准 | retweet_count perc max | retweet_count 百分比平均值 | retweet _ 计数百分比标准 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 阿斯克苏 | Forty-six | 1.634117178158629 | 0.06411700486942658 | 0.243596655920922 | 0.11956954962136308 | 0.0043322300587450395 | 0.019212624913592345 |
| 伯努瓦 c | One thousand and nine | 1.5220700152207 | 0.0331365754882869 | 0.09291365235345102 | 0.8625063419583967 | 0.0069390704360904 | 0.038787086230791176 |
| 古典组曲的第二乐章 | Two hundred and fourteen | Seven point five | 0.1594626168224299 | 0.556170943428589 | Three point one two five | 0.02570093457943925 | 0.21852197555838485 |
| 肯纳茨雷茨 | Two thousand six hundred and thirty-seven | 21.588974908032725 | 0.055249388368344046 | 0.4507768404061633 | 14.127271728984791 | 0.014389618353632417 | 0.27889982992935036 |
| 光彦 | One thousand five hundred and forty-seven | 3.4493830558231275 | 0.04429797450624903 | 0.18836124691411713 | 1.009128021650383 | 0.008497857760034094 | 0.04556199530029643 |
| 大象 | One hundred and eighty-six | 4.475958342565921 | 0.14447510060541938 | 0.38522061290647097 | 0.7423000221582097 | 0.043244247800261586 | 0.09464679798911974 |
| 韦斯麦金 | Four hundred and thirty-three | 6.281316027769393 | 0.138094106149126 | 0.42942922072801465 | 2.0977965590099608 | 0.037036546490172004 | 0.1451353535074393 |
| zzzeek | Four hundred and thirty-nine | 2.8211085297046132 | 0.07212481675836008 | 0.21300619010180605 | 0.9293063391968137 | 0.01489369905806803 | 0.06292866185987782 |

标准化后，我们可以看到@cournape 和@teoliphant 在转发和收藏方面的平均值越来越高。

我们还可以看到每个用户每月的推文数量是如何随时间变化的。从下图我们可以看到例如 [@kennethreitz](https://dev.to/kennethreitz) 在 2017 年 9 月发了很多条推文(800 多条): [![monthly](img/b38ecc15f75470933af8d5f98f6d2700.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oyxlgG3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bk9lx56uecyupdpwjcnr.png)

或者我们甚至可以看到哪些工具是每个用户最常使用的推文: [![sources](img/cf333554cbf8d3d64accf748f18e4967.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nyVDKwTw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fj4jd4618hbsithnj6j4.png) 
我把很多不太常用的工具归到了“其他”下面(Twitter bot for IοS，Twitter for iPad，OS X，Instagram，Foursquare，脸书，LinkedIn，Squarespace，Medium，Buffer)。

最后，我们可以为每个用户构建一个 punchcard 图表，按照一周中的某一天和一天中的某几个小时来显示 tweets 日期的集合: [![punch](img/1d1b0c5d027191a2b1de122a7e309b82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8vdJbmI5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tki4ig3kewzvtz21mev5.png)

# 主题

但是列表中的开发者在谈论什么呢？

先来一个简单的可视化，一个词云。在对仅来自标准推文的文本进行一些基本预处理(标记化、词性标注、停用词移除、二元模型等)后，我按照用户名对推文进行了分组，并获得了每条推文的最常用词:

| 用户名 | 推文计数 | 最常见的 |
| --- | --- | --- |
| @asksol | six | [('python '，3)，(' enjoy '，1)，(' seeing '，1)，(' process '，1)，(' handle '，1)] |
| [@ benitoc](https://dev.to/benoitc) | Four hundred and eighty-eight | [('like '，40)，(' erlang '，33)，(' use '，31)，(' code '，30)，(' people '，30)] |
| @库尔纳普 | Forty-three | [('日本'，8)，('日本'，6)，(' #pyconjp '，6)，('涩谷'，4)，(' python '，4)] |
| [@kennethreitz](https://dev.to/kennethreitz) | One thousand one hundred and nine | [('pipenv '，157)，(' python '，84)，(' new '，77)，(' requests '，64)，(' released '，53)] |
| @光彦 | Three hundred and ninety-nine | [('锈'，53)，('象'，36)，('人'，27)，('新'，25)，('道'，20)] |
| @teoliphant | One hundred and thirteen | [('#pydata '，39)，(' #python '，36)，(' @anacondainc '，18)，(' great '，18)，(' new '，15)] |
| @wesmckinn | One hundred and twenty-nine | [(' @ Apache rrow '，32)，(' data '，21)，(' pandas '，16)，(' python '，12)，(' new '，10)] |
| @zzzeek | One hundred and seventy | [('like '，14)，(' years '，11)，(' python '，10)，(' time '，10)，(' use '，9)] |

然后我使用 [word_cloud](https://github.com/amueller/word_cloud) 为每个用户名创建了一个单词云。所有的人都在谈论 Python 或者他们的库(比如 pipenv，pandas，sqlalchemy 等等)；我们还可以发现其他一些编程语言，比如 erlang 和 rust。
T3![cloud](img/b7ecc97743b6fbdd9e8d9a712a1c0a7d.png)T5】

下一步是使用 Gensim 的 LDAmodel 确定真正的主题。我仍然使用了来自两个微博数量较多的账户的标准微博( [@kennethreitz](https://dev.to/kennethreitz) 和@mitsuhiko)，并且我执行了与 wordclouds 生成相同的预处理。
我使用两个动态值运行模型:

*   主题的数量(范围在 2 到 14 之间)
*   alpha 值(可能值为 0.2、0.3、0.4)。然后，我使用 Gensim 内置的一致性模型选择了最佳解决方案，使用 c_v 作为度量:最佳模型是具有 9 个主题且 alpha=0.2 的模型![coherence](img/c656c79d4791efa142c18978c47d2ab1.png)

以下是主题:

| 主题号 | 热门词汇 |
| --- | --- |
| Zero | (0，' 0.125* "方式"+ 0.094* "喜欢"+ 0.076* "特色"+ 0.067* "哦"+ 0.063* "觉得"') |
| one | (1，' 0.140*"pipenv" + 0.124* "已发布"+0.098 * " pipenv _ 已发布"+ 0.082* "想要"+ 0.073* "代码"') |
| Two | (2，' 0.271 * " python "+0.132 * " today "+0.093 * " people "+0.039 * " month "+0.036 * " Kenneth " ') |
| three | (3，' 0.183* "请求"+ 0.134* "爱"+ 0.081* "工作"+ 0.071*"html" + 0.057*"github " ') |
| four | (4，' 0.164* "象"+ 0.100* "锈"+ 0.098* "时"+ 0.058* "日"+ 0.047* "事"') |
| five | (5，' 0.297 * " pipenv "+0.062 * " support "+0.058 * " includes "+0.045 * " right "+0.044 * " making " ') |
| six | (6，' 0.271* "新"+ 0.076* "变得"+ 0.075* "更好"+ 0.058* "使用"+ 0.049* "照片"') |
| seven | (7，' 0.161* "好"+ 0.097* "去了"+ 0.092* "得了"+ 0.067* "开心"+ 0.058* "当前"') |
| eight | (8，' 0.114* "很棒"+ 0.091*"ipad" + 0.076* "终于"+ 0.066*"heroku" + 0.057* "正在工作"') |

我们可以使用 [pyLDAvis](https://github.com/bmabey/pyLDAvis) 查看主题间距离图和每个主题最相关的术语:您可以在我的 github 帐户中探索 jupyter 笔记本中的交互数据。【T2![ldavis](img/cea249436a6cb9dc3421e2e2852bb5be.png)

# 结论和未来步骤

在这篇文章中，我展示了如何从 Twitter APIs 获取数据，以及如何执行一些简单的分析，以便提前了解某个帐户的一些特性(例如，推文风格、推文统计、主题)。
您的里程可能会因初始账户列表和算法配置(尤其是话题检测)而异。

我在 github 上上传了一个 [jupyter 笔记本](https://github.com/eric-bonfadini/python-notebooks/blob/master/blog_posts/You_can_tell_a_man_by_his_tweets.ipynb)，里面有一些我用来写这篇博文的片段。

后续步骤:

*   使用词汇化和词干化改进预处理
*   使用 Gensim(如 AuthorTopicModel 或 LDAMallet)或 scikit-learn 尝试不同的主题检测算法
*   添加情感分析