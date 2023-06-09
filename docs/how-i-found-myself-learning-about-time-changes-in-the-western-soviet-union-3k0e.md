# 我发现自己是如何了解苏联西部的时间变化的

> 原文：<https://dev.to/nylas/how-i-found-myself-learning-about-time-changes-in-the-western-soviet-union-3k0e>

[![Nylas_Blog_TimeChange_V1](img/3be8242f16190cc1b3dcab335319347c.png)T2】](https://www.nylas.com/blog/how-i-found-myself-learning-about-time-changes-in-the-western-soviet-union)

在 [Nylas](https://www.nylas.com/) 这里，我们正在构建一个 API 来帮助[开发者在电子邮件、日历&联系人之上构建应用](https://dev.to/nylas/nylas-api-contacts-crud-now-supports-icloud-2ch3)。事实证明，这样做会让你学到很多关于前苏联时间变化的知识。几个月前，我加入了 Nylas，并且很早就了解到在日历中设置事件的一个重要方面是了解事件所在的时区，相对于事件的创建者以及任何其他参与者。自然，这涉及到大量的时区数学。大部分工作是由 [pytz](https://pypi.org/project/pytz/) 库完成的，这对我们来说已经足够好了。为什么要做别人已经做过的事，对吗？

输入微软。许多公司使用 Microsoft Exchange 处理电子邮件和工作日历。历史上，Exchange 一直使用一种称为 Exchange 活动同步(EAS)的协议。与任何微软协议一样，他们对日历 API 的态度似乎是，“让我们提供所有的原始数据，让用户用它做一些有用的事情。”他们提供的是:

*   与 UTC 的时差，以分钟为单位
*   该日历年中时区转换为夏令时的日期
*   该日历年中时区转换为标准时间的日期
*   夏令时期间的偏移量

…您将从中获得一个友好的名称来显示给用户。这就像如果我问你时间，你会告诉我地球的顺转和倾斜，地球轨道的偏心率，月球对旋转速度的潮汐效应，以及如何调整闰秒。我是说，当然，但是你就不能告诉我已经 5:30 了吗？*

Pytz 有一个相当稀疏的公共 API(其中“公共”是使用没有下划线前缀的类和方法的 Python 习语定义的)。作为程序员，你从类似于`US/Eastern`的友好字符串中实例化一个 timezone 对象，你可以使用`localize`、`normalize`和`utcoffset`方法来处理它。Pytz 负责跟踪 DST 转换和偏移。从一个普通用户的角度来看，这是有意义的。Pytz 的整个价值主张是，抽象出处理时区、官僚主义和改变补偿的政治的痛苦。好吧，雷德蒙，游戏开始。

我们决定使用 pytz internals 构建一个查找表来存储这些内容。我们需要使用从微软获得的信息作为键，时区名称作为值。对于每个时区，pytz 存储一个属性`_utc_transition_times`

```
>>> tz = pytz.timezone('US/Eastern')
>>> pprint(tz._utc_transition_times[-5:])
[datetime.datetime(2035, 11, 4, 6, 0),
 datetime.datetime(2036, 3, 9, 7, 0),
 datetime.datetime(2036, 11, 2, 6, 0),
 datetime.datetime(2037, 3, 8, 7, 0),
 datetime.datetime(2037, 11, 1, 6, 0)] 
```

哦，酷，所以我们可以用它来建立查找表。，对吧？我们将给定年份的第一个转换作为到 Dst 的转换，给定年份的第二个转换作为到 ST 的转换，选择 DST 之外的一个时间并查找 UTC 偏移量，并将这个元组存储为我们的表的键！

```
>>> daylight_time = tz._utc_transition_times[n]
>>> standard_time = tz._utc_transition_times[n+1]
>>> utc_offset = tz.utc_offset(date_not_in_dst)
>>> timezone_table[(daylight_time, standard_time, utc_offset)] = timezone_name 
```

这工作得很好，直到我们得到一个错误报告，奥克兰的日历事件丢失了时区信息。对此，我们的查找表似乎有相反的`daylight_time`和`standard_time` 条目。这怎么可能？事实证明，当我们假设一年的第一次过渡是向夏令时的过渡时，我们做了一个非常糟糕的假设。因为南半球的夏季是从 12 月到 3 月，所以它们在 11 月左右进入 DST，在第二年的 4 月左右退出。也许社会平等组织的[制图师是对的！我们没有意识到南北半球之间的这种差异，并因此而存在缺陷。回到绘图板。](https://youtu.be/OH1bZ0F3zVU)

## 挖掘 Pytz

好吧，那么对 Pytz 的深入研究是有保证的。这个图书馆到底是怎么运作的？事实证明，这真的很吸引人。与代码库捆绑在一起的是 Olson 数据库，也称为 *Zoneinfo* 或 *tz* 数据库。整个数据库是以 Unix 风格编写的，明文文件描述了每个地理区域的时区名称和历史偏移量。但比实际数据更突出的是作为评论散布在各处的所有历史轶事和背景。有些是大的，有些是小的，有些似乎只是维护者之间的内部玩笑。

例如，在阿拉斯加:

来自保罗·埃格特(2017-06-15):

...许多阿拉斯加居民不知道美国获得了阿拉斯加，更不知道任何日历或时间的变化。然而，阿拉斯加受俄罗斯影响的部分确实遵守了俄罗斯时间，对此建模比忽略它更准确。数据库格式需要准确的转换时间；使用俄罗斯礼炮作为阿拉斯加控制权正式转移的任意时间。西特卡的 UTC 时差是-9:01:13；将其 15:30 调整到阿拉斯加其他地方的当地时间，以便它们同时改变。

来自保罗·埃格特(2014-07-18):

关于上世纪 80 年代初阿拉斯加时区和夏令时之争的一种观点，出现在朱诺号机场墙上的涂鸦上:

“欢迎来到朱诺号。请把你的手表拨回到 19 世纪。”

见:特纳 w 阿拉斯加的四个时区现在两个。纽约时报 1983-11-01[http://www . nytimes . com/1983/11/01/us/Alaska-s-four-time-zones-now-two . html](http://www.nytimes.com/1983/11/01/us/alaska-s-four-time-zones-now-two.html)

在密歇根:

出自保罗·埃格特(1999 年 3 月 31 日):

香克斯写道，密歇根州于 1885 年 9 月 18 日开始使用标准时间，但豪斯写道(第 124-125 页，指的是 1901-01 年的《大众天文学》)，底特律一直保持当地时间，直到 1900 年市议会颁布法令，时钟应拨回中央标准时间 28 分钟。全城一半人服从，一半人拒绝。经过激烈的辩论后，这个决定被取消了，这个城市恢复了太阳时间。一个在市政厅前竖立日晷的嘲笑提议被提交给了下水道委员会。然后，在 1905 年，城市投票决定采用中央时间。

这个故事太有趣了，不可能是假的，所以去看看《豪斯医生》吧。

时区是政治性的——谁知道呢？玩笑归玩笑，我想看看皮兹究竟是如何*处理*时间的。这发生在 timezone 类的`localize`方法中。`localize`试图涵盖所有怪异时区转换的基础。为此，它将`_utc_transition_times`一分为二，找出给定时间在转换列表中的位置。然后，它从`_transition_info`检索关于转换的信息。

```
>>> tz = pytz.timezone('US/Eastern')
>>> pprint(tz._utc_transition_times[:5])
[datetime.datetime(1, 1, 1, 0, 0),
 datetime.datetime(1901, 12, 13, 20, 45, 52)
 datetime.datetime(1918, 3, 31, 7, 0),
 datetime.datetime(1918, 10, 27, 6, 0),
 datetime.datetime(1919, 3, 30, 7, 0)]
>>> pprint(tz._transition_info[:5])
[(datetime.timedelta(-1, 68640), datetime.timedelta(0), 'LMT'),
 (datetime.timedelta(-1, 68400), datetime.timedelta(0), 'EST'),
 (datetime.timedelta(-1, 72000), datetime.timedelta(0, 3600), 'EDT'),
 (datetime.timedelta(-1, 68400), datetime.timedelta(0), 'EST'),
 (datetime.timedelta(-1, 72000), datetime.timedelta(0, 3600), 'EDT')]
```

这讲述了一个更好的故事。对于`_utc_transition_times`中的每个条目，在`_transition_info`中都有相应的条目。该条目中的 DST 偏移量告诉我们它是向 ST(如果为零)还是 DST(如果不为零)过渡。此外，UTC 偏移可以直接使用，而不需要参考时间来测试。

那么这里有哪些边缘案例呢？事实证明，当您使用时区信息来识别姓名时，可能会有多个条目。当您得知代表莫斯科和土耳其的时区有不同的名称时，您可能不会感到惊讶。但如果不小心，很容易将“欧洲/莫斯科”或“欧洲/伊斯坦布尔”(这个时区的常见表示)改写为“W-SU”(代表西苏)。

此外，仅仅看一下`localize`方法就可以看到大量的边缘情况。有一个看似显而易见的例子，试图处理一个过渡中的时间，例如，从夏令时过渡到夏令时的凌晨 1:30 是在过渡之前还是之后？但也有其他情况，当国家在夏令时转换之外将时钟回拨。这可能会给任何试图在时区之间进行实时转换的人带来问题。

幸运的是，这正是我们可以依靠 pytz 的事情。虽然并不完美，但 pytz 在处理最糟糕的编程问题时，尽可能多地涵盖了边缘情况。虽然我们需要使用时区转换的更精细的点来破译交换事件，但我们通常可以在其他地方使用精细的 pytz API。

如果你想了解更多关于 Nylas 日历 API 的信息，你可以通过[在这里](https://www.nylas.com/developers/)注册获得一个免费的 API 密匙。

* * *

我特意打了这个比方，这样我就可以瞪着第一个问我现在是几点半的人。