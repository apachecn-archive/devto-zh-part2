# 在堆栈溢出开发人员调查中，您可能忽略了一些事情

> 原文：<https://dev.to/remotesynth/some-things-you-may-have-missed-in-the-stack-overflow-developer-survey-40lo>

是的，我知道你在想什么，“堆栈溢出调查已经是上个月的新闻了！”没错。但是有太多的数据需要消化，老实说，这需要很多时间来处理。这意味着许多早期的帖子专注于标题信息，如[最受欢迎/恐惧/想要的语言](https://insights.stackoverflow.com/survey/2018/#most-loved-dreaded-and-wanted)。现在我有一点时间来筛选所有的信息，我想分享一些让我觉得重要的事情，即使它们不一定是最大的头条。

我应该注意，在下面的所有情况下，当我提到开发者时，我指的是回答者。显然，尽管有超过 100，000 人参加了调查，但它可能存在一些偏差，因为它[严重偏向于至少有点常规的 StackOverflow 用户](https://insights.stackoverflow.com/survey/2018/#community-visiting-stack-overflow)。

## 我们一点也不像多样化的人！

总的来说，人口统计部分令人失望。事实上，相当多的开发人员[认为多样性不是优先考虑的事情](https://insights.stackoverflow.com/survey/2018/#work-how-do-developers-assess-potential-jobs)(包括[的男性，令人惊讶的是，还有女性](https://insights.stackoverflow.com/survey/2018/#work-differences-in-assessing-jobs-by-gender)),这并没有帮助我们。最终结果是，典型的开发人员是一个 35 岁以下的白人男性，没有孩子。

### 我们几乎都是男人

遗憾的是，开发人员是一个非常同质的群体——几乎 93%是男性(T1)，超过 74%是白人(T3)，超过 93%是异性恋(T5)。相比之下，总人口中女性比例明显略高于 50%。

所有让更多女性进入该行业的多元化努力似乎都没有以任何真正引人注目的方式改变事情。只看学生，数字变化不到百分之一。唯一的潜在亮点是，在工作经验不到 5 年的开发人员中，女性所占的比例越来越大。

### 我们大多是白人

种族划分尤其令人失望，因为 63%的受访者来自北美和欧洲。尽管如此，超过 74%的开发者认为自己是白人或欧洲后裔。地理上的细分导致一个结论，在北美和欧洲，细分将明显比整体百分比更白。这一点在评论中得到了证实，评论指出，“美国 7.4%的专业开发人员认为自己是黑人、西班牙裔或拉丁裔或印第安人”，而在所有受访者中，这一比例超过了 30%。从积极的方面来看，当减少到只有学生时，这个数字略有改善——所以这方面正在发生变化，但非常缓慢。

### 我们大多年轻，没有孩子

几乎 [73%的开发者年龄在 35 岁以下](https://insights.stackoverflow.com/survey/2018/#developer-profile-age)。就我个人而言，我正处于 45 岁或更老的人群中，这代表了不到 7%的开发人员——我可以证明，在过去 5 年中，我参加的大多数开发人员活动中，我的年龄都在上升。略高于 [71%的人没有孩子](https://insights.stackoverflow.com/survey/2018/#developer-profile-children-and-other-dependents)，这与 35 岁以下人群非常接近。

## 网络技术统治一切...

### ..但是薪水呢

JavaScript、HTML 和 CSS 是开发人员使用的顶级语言[，占受访者的三分之二以上，其中 JavaScript 是三者中最流行的(如果只过滤专业开发人员，这三种语言的数字甚至略有上升)。此外，](https://insights.stackoverflow.com/survey/2018/#technology-programming-scripting-and-markup-languages)[有 64%的人在使用 Angular 或 React](https://insights.stackoverflow.com/survey/2018/#technology-frameworks-libraries-and-tools)(Angular 看起来稍微大一点)，将近 50%的人说他们在使用 Node.js

然而，受欢迎并不能转化为报酬。网络技术处于薪酬等级的底部[(无论是在全球还是在美国，只有 JavaScript 位列倒数第二)。如果你想赚钱，那就用不太常用的后端语言，比如 Erlang、Scala、Ocaml 和 F#。前端开发人员作为一个头衔，总体来说位于](https://insights.stackoverflow.com/survey/2018/#technology-what-languages-are-associated-with-the-highest-salaries-worldwide)[工资清单](https://insights.stackoverflow.com/survey/2018/#salary)的中间。

### 移动仍然是一个(大)小众领域

本地移动队伍没有我预期的那么大。Swift 为 8.3%，Objective-C 仍为 7.3%，当记住这一类别允许重叠时，这意味着总体上很可能不到 10%的人在进行 iOS 开发(这符合仅占 10%左右的 [XCode 数字](https://insights.stackoverflow.com/survey/2018/#technology-most-popular-development-environments)和[相关技术](https://insights.stackoverflow.com/survey/2018/#correlated-technologies)部分显示确实有一些重叠)，尽管总体上有 15%的人说他们正在为 iOS 平台进行开发。Android 在语言部分没有脱离 Java，但 [IDE 部分](https://insights.stackoverflow.com/survey/2018/#technology-most-popular-development-environments)显示 Android Studio 为 19.3%，平台显示为 29%，鉴于缺乏本地 iOS 开发者，这有点不寻常。由于超过 50%的受访者使用移动设备，我想知道是否会对 StackOverflow 的观众有一点 Java/Android 的偏见。

其他专注于移动的开发技术也相对较少，Xamarin 和 Cordova 显示了合计 15%(因为我假设两者之间的重叠最小)。React Native 或 NativeScript 等工具没有上榜。对 Xamarin 和 Cordova 来说不幸的是，他们都[在最可怕的工具/框架列表](https://insights.stackoverflow.com/survey/2018/#technology-most-loved-dreaded-and-wanted-languages)中名列前茅。

### 后端开发遍地都是

说到后端开发，Java 仍然是王者，但是 Python 和 C#也没有落后太多。很多人(31%)仍然使用 PHP，而且不仅仅是 WordPress(不到 16%的人使用 Wordpress)。相当多的开发人员使用 C 和/或 C++，而很少但不可忽视的百分比使用 Ruby 和 Go。这些数字表明许多开发人员使用多种后端语言。

### Rust 和 Kotlin 的开发人员非常热爱他们的语言

Rust 和 Kotlin 开发人员显然是一个小型但充满激情的团队。Rust 甚至没有出现在语言列表中，Kotlin 仅占 4.5%，但在使用它的开发人员希望继续使用它的方面，它们明显超过了大多数语言。铁锈知识在[全球薪酬等级中处于相当高的位置，所以这可能是一个因素。](https://insights.stackoverflow.com/survey/2018/#technology-what-languages-are-associated-with-the-highest-salaries-worldwide)

总的来说，开发人员通常希望学习 Python 或 JavaScript/TypeScript，并希望摆脱一些旧的。特别是. NET 技术。然而，Python 和 JavaScript 都是收入最低的语言。

## 开发人员普遍喜欢他们的工作

这是一个[亮点](https://insights.stackoverflow.com/survey/2018/#work-how-do-developers-feel-about-their-careers-and-jobs)。超过 70%的开发人员至少对他们的职业稍感满意，大多数(55%)中度或极度满意。就对当前工作的满意度而言，这些数字基本上没有变化。然而，相当多的开发人员[在去年](https://insights.stackoverflow.com/survey/2018/#work-how-long-ago-did-developers-last-change-jobs)换了工作，大多数人(56%)在过去两年内换了工作。所以，也许这个工作满意度数字是短暂的。

也许开发人员高兴的部分原因是这份工作总体来说报酬很高。即使在美国，按开发人员类型计算，工资最低的[类别也比拥有四年大学学位的](https://insights.stackoverflow.com/survey/2018/#work-salary-by-developer-type)[平均工资](https://smartasset.com/retirement/the-average-salary-by-education-level)高大约 2 万美元，尽管大约 75%的[专业开发人员拥有大学学位](https://insights.stackoverflow.com/survey/2018/#developer-profile-educational-attainment)。