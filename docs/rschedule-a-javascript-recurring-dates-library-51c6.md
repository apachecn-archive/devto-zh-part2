# rSchedule:一个 javascript 循环日期库

> 原文：<https://dev.to/johncarroll/rschedule-a-javascript-recurring-dates-library-51c6>

rSchedule 是一个 javascript 库，用 typescript 编写，用于处理循环日期和 ICal 循环。规则可以以 [iCalendar RFC 5545](https://tools.ietf.org/html/rfc5545) 格式导入/导出，规则对象本身遵守 [javascript 迭代器协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)。

用法示例:

```
const rule = new Rule({
  frequency: 'YEARLY',
  byMonthOfYear: [2, 6],
  byDayOfWeek: ['SU', ['MO', 3]],
  start: new Date(2010,1,7),
})

let index = 0;
for (const { date } of rule.occurrences()) {
  date.toISOString()
  index++

  if (index > 10) break;
}

rule.occurrences({
  start: new Date(2010,5,7),
  take: 5
})
  .toArray()
  .map(date => date.toISOString()) 
```

Enter fullscreen mode Exit fullscreen mode

rSchedule 使用了一个相当简单的`DateAdapter`包装器对象，它从单个的数据库实现中抽象出来，使得 rSchedule 与数据库无关。如果您选择的`DateAdapter`支持时区，rSchedule 支持时区。

目前存在的`StandardDateAdapter`、`LuxonDateAdapter`、`MomentDateAdapter`和`MomentTZDateAdapter`包为标准 javascript `Date`对象以及 [`moment`](https://momentjs.com) 、 [`moment-timezone`](https://momentjs.com) 和 [luxon `DateTime`](https://moment.github.io/luxon/) 对象提供了`DateAdapter`兼容包装器。此外，为自己喜欢的库创建自己的`DateAdapter`应该很容易。

rSchedule 已经从头开始编码，以便于创建复杂的循环时间表，其中包含了很多内容。[要获得完整的概述，请查看 Gitlab 上的项目](https://gitlab.com/john.carroll.p/rschedule)。

### 出现流运算符

在不深入研究这个库的情况下，我想调用它的一个非常酷的特性:事件流操作符。

事件流操作符受 rxjs 管道操作符的启发，它们允许您组合和操作来自不同对象的事件流。

我自己的应用程序中的一个例子:

> 我想获得特定志愿者的志愿者时间表。志愿者时间表是个人报名时间表和相关志愿者机会发生时间表的*交集*。
> 
> 换句话说，志愿者机会有自己的发生时间表。比方说:每隔**个**周二以及每周五。另外，有些人可能会在“每周二”以及几个特定的周五报名成为志愿者。个人*实际上*要去做志愿者的日子，是这两个时间表的交集。

下面是如何使用 rSchedule:
建立这个新的时间表

```
declare const volunteerAvailability: Schedule[];
declare const opportunitySchedule: Calendar;

const volunteerSchedule = new Calendar().pipe(
    add(...volunteerAvailability),
    unique(),
    intersection({
      streams: opportunitySchedule
    })
  ); 
```

Enter fullscreen mode Exit fullscreen mode

分解这个例子:

*   当志愿者表示有空时，包含一系列不同的时间表。
*   `opportunitySchedule`包含描述志愿者机会实际发生时间的日历。

我们想要创建一个日历，其中包含志愿者有空参加*这个志愿者机会*(`volunteerSchedule`)的时间。

```
const volunteerSchedule =

  // create a new calendar
  new Calendar().pipe(

    // add all times that the volunteer is available, in general
    add(...volunteerAvailability),

    // filter to get only unique times
    unique(),

    // get the intersection of these times with the
    // volunteer opportunity's schedule
    intersection({
      streams: opportunitySchedule
    })
  ); 
```

Enter fullscreen mode Exit fullscreen mode

可以使用`volunteerSchedule.occurrences()`对结果`volunteerSchedule`进行迭代。我还可以使用`volunteerSchedule.collections({granularity: 'MONTHLY'})`很容易地按月分组事件，并遍历各个月。

考虑到每个报名的志愿者都有自己的时间表，我可以将这些时间表组合在一起，创建一个新的日历，包含每个人计划参加志愿者活动的日期。

例如:

```
declare const volunteerSchedules: Calendar[];

const scheduleOfAllVolunteers =
  new Calendar({ schedules: volunteerSchedules }); 
```

Enter fullscreen mode Exit fullscreen mode

我想大多数用户不需要如此强大的工具，对他们来说，他们可以使用 ICAL 规范中提供的实现了`RRULE`、`EXRULE`、`RDATE`和`EXDATE`的`Schedule`对象。

例如:

```
const schedule = new Schedule({
  rrules: [
    {
      frequency: 'WEEKLY',
      start: new Date(2012, 5, 24),
      end: new Date(2012, 11, 31)
    },
    {
      frequency: 'DAILY',
      start: new Date(2011, 9, 2)
    }
  ],
  data: 'Holds anything I want',
})

schedule
  .occurrences({take: 10})
  .toArray()
  .map(date => date.toISOString()) 
```

Enter fullscreen mode Exit fullscreen mode

# [欲了解更多，请查看 rSchedule repo。](https://gitlab.com/john.carroll.p/rschedule)