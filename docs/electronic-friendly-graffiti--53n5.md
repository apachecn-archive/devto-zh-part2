# 电子友好涂鸦

> 原文：<https://dev.to/terceranexus6/electronic-friendly-graffiti--53n5>

不久前，我向我年轻的学生解释如何用铅笔或纯石墨制作导电图。总之，我最喜欢的当地艺术商店宣布他们的网站上有石墨粉。石墨粉是石墨的最纯的形式，具有极好的导电性，并且容易混合，所以我跑去买了一瓶石墨粉。在路上，我还买了一个旅行迷你喷雾和丙烯酸黑漆。

一回到家，我就拿出 voltimeter 开始搅拌。首先，一些石墨粉和水，画一点，测量。答对了。我可以得到一点电位差。虽然，它不是很大，画也不是很强。我加了一点丙烯颜料...它看起来非常好，再次油漆和测量，仍然工作，添加了一点额外的石墨更好的传导，完美！
[![](img/51c17a78ba8713d2bb2e7abd17d28fe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7NJRo8pP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/klgwkronjuyclwookwkr.jpg) 
这个测量看起来很不错，所以我试着在绘画上放一个 led，并在笔触的另一边使用+5V 的电阻线。答对了。它亮了。
[![](img/98de7afc9e640ef98c55dd1d1a9cea3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7YVih4m---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wixueyk7hnvtmkki79tw.jpg) 
没有直接连接时那么强，但这是因为油漆本身会产生一些电阻，因为不是每个部件都是同等导电的。这里更好的选择是使用低欧姆的电阻，比如 100 欧姆而不是 220 欧姆(我用的是 220 欧姆)。或者也可以将绘画图案设计成并联电阻而不是连续电阻。

连续:

```
 {LED}-[---brushstroke---]-(resistance)-5V 
```

Enter fullscreen mode Exit fullscreen mode

并行:

```
 _[---brushstroke---] 
       /                    \
{LED}-<                      >--5V
        \_(resistance)------/ 
```

Enter fullscreen mode Exit fullscreen mode

总之，一旦我得到了配方，我就创造了更多的东西来适应迷你喷雾罐，并在里面添加了一个 Molotow(我最喜欢的涂鸦品牌之一)迷你球，用于在摇动时混合(当你摇动油漆罐时会发出声音的小球)。用有毒的绿色闪电装饰喷雾，完成了，一个导电喷雾罐。

[![](img/df5dfbb1d7a6b5787e673cf95edc147e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W9AHBl-8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cvbqj7smemw2rc2v0iw7.jpg)
[![](img/5ac7b6100f979838e9ec9c03739a3ce4.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--YWz3lKJM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/efdikj2q4vegwzwak3km.jpg)