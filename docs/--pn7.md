# 求日历的行数

> 原文：<https://dev.to/kentama7/--pn7>

求日历的行数。

算出方法は以下を参考. . .
[https://biz.hino-tama.com/creative/blog0012/](https://biz.hino-tama.com/creative/blog0012/)T3】

```
int calendarRows(LocalDate date) {
    if (date == null) {
        throw new IllegalArgumentException();
    }

    LocalDate firstDay = date.with(TemporalAdjusters.firstDayOfMonth());
    DayOfWeek dow = firstDay.getDayOfWeek();
    Month month = firstDay.getMonth();

    if (DayOfWeek.SATURDAY.equals(dow)) {
        return Month.FEBRUARY.equals(month) ? 5 : 6;
     }

    if (DayOfWeek.FRIDAY.equals(dow)) {
        return firstDay.lengthOfMonth() == 31 ? 6 : 5;
    }

    if (DayOfWeek.SUNDAY.equals(dow) && Month.FEBRUARY.equals(month)) {
        return firstDay.isLeapYear() ? 5 :4;
    }

    return 5;
} 
```

Enter fullscreen mode Exit fullscreen mode