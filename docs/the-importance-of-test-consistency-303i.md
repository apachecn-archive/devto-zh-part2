# 测试一致性的重要性

> 原文：<https://dev.to/lloople/the-importance-of-test-consistency-303i>

最近，我发现在我的应用程序中包含特性和单元测试的好处，这有助于我更快地找到 bug。

今天，我在我们团队最大的测试套件中发现了一个小错误，直到今天我才意识到。

这个测试套件包含许多与日期相关的测试，比如酒店预订或每日价格变化。今天当月份从六月变成七月时，我发现了这个问题，让我解释一下。

用户需要能够每周修改价格，但只在某些天，例如九月的每个周末。
因此请求接收一个`date_begin`、一个`date_end`和一个`weekdays` :
数组

```
/** @test */
public function only_weekdays_selected_are_created()
{
    // ... Preparing required variables and data on the database

    $this->actingAs($user)->post('/prices/create', [
        'price' => 46,
        'date_begin' => Carbon::now()->addMonth()->firstOfMonth(Carbon::MONDAY)->format('Y-m-d'),
        'date_end' => Carbon::now()->addMonth()->firstOfMonth(Carbon::FRIDAY)->format('Y-m-d'),
        'weekdays' => ['wed']
    ]);

    $this->assertDatabaseHas('price_days', [
        'day' => Carbon::now()->addMonth()->firstOfMonth(Carbon::WEDNESDAY)->format('Y-m-d'),
        'price' => 46
    ]);
} 
```

你发现漏洞了吗？好吧，反正我要写下来强迫自己记住。

代码将第一个*完整周*的第一个星期三插入数据库，即 2018 年 8 月 8 日，
断言将寻找第一个星期三，即 2018 年 8 月 1 日。

经过一点重构，问题解决了，但是它迫使我从另一个角度来处理这种断言。由于我们是一个开发人员的小团队，我们所有人都需要对这个项目编写测试，我想过创建
某种类型的`helper`(它已经成为编程中一个容易使用的词，你不觉得吗？)来为我们处理约会的事情。

我决定在 tests 文件夹中创建一个类，并将其命名为`TestCarbon`。顺便说一下，这个名字一点也不确定，但它是我想到的第一个名字，因为它只用于测试，而且与碳有关。

而这个类看起来和这个很像:

```
 namespace Tests;

class TestCarbon
{

    public function today()
    {
        return Carbon::now();
    }

    public function firstMondayOfNextMonth()
    {
        return self::today()->addMonth()->firstOfMonth(Carbon::MONDAY);
    }

    public function firstWednesdayOfNextMonth()
    {
        return self::firstMondayOfNextMonth()->addDays(2);
    }

    public function firstFridayOfNextMonth()
    {
        return self::firstMondayOfNextMonth()->addDays(4);
    }

} 
```

然后在重构测试之后，看起来又是这样的:

```
/** @test */
public function only_weekdays_selected_are_created()
{
    // ... Preparing required variables and data on the database

    $this->actingAs($user)->post('/prices/create', [
        'price' => 46,
        'date_begin' => TestCarbon::firstMondayOfNextMonth()->format('Y-m-d'),
        'date_end' => TestCarbon::firstFridayOfNextMonth()->format('Y-m-d'),
        'weekdays' => ['wed']
    ]);

    $this->assertDatabaseHas('price_days', [
        'day' => TestCarbon::firstWednesdayOfNextMonth()->format('Y-m-d'),
        'price' => 46
    ]);
} 
```

我知道我知道，`firstWednesdayOfNextMonth`在说谎(至少在 2018 年 8 月)，但是`firstWednesdayInsideFirstFullWeekOfNextMonth`
对我来说似乎太长了😅。

我向团队展示了代码，他们认为这是对我们测试套件的一个很好的补充。他们开始在自己的
测试中使用它，并添加返回他们最常用日期的方法。原来我有几个日期相近的测试
，但和`TestCarbon`用的三个不一样。但是考虑到这个类，而不是
为新的一天创造新的方法，我修改了那些测试来匹配这个日期，它们仍然通过。所以可以说，我能够从代码中删除一些`magic dates`。

后来，我想到了[魔法弦问题](https://en.wikipedia.org/wiki/Magic_string)，我想起了
中的一个应用，我对它进行了一些测试，测试中它一直在与弦交互。这是关于颜色的，它们是通过`yellow`、`green`、`orange`这样的请求传递的
...然而，这一次，当我在这个测试套件中创建这个`TestColors`类
时，我意识到并想到

> 为什么我不把这个也用在主应用上？

所以我把那个类移到了一个名为`helpers`的文件夹中，并重构了应用程序和测试中的代码。现在是
使用这个类，它包含了我需要的所有颜色作为常量，比如`const YELLOW = 'yellow';`。这让
我从所有的应用程序中删除了许多神奇的字符串，现在一切看起来更加一致了。