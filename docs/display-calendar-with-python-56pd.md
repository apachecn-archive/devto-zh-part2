# 用 python 显示日历

> 原文：<https://dev.to/hasansajedi/display-calendar-with-python-56pd>

> Python 有一个内置函数“日历”来处理与日期相关的任务。通过下面的例子，你将学会显示给定日期的日历。

```
 import calendar

yy = 2018
mm = 8

print(calendar.month(yy, mm)) 
```

Enter fullscreen mode Exit fullscreen mode

在程序的顶部，我们导入了日历模块。模块中的内置函数 month()接受年和月，并显示一年中该月的日历。

## 输出:

```
 August 2018
Mo Tu We Th Fr Sa Su
       1  2  3  4  5
 6  7  8  9 10 11 12
13 14 15 16 17 18 19
20 21 22 23 24 25 26
27 28 29 30 31 
```

Enter fullscreen mode Exit fullscreen mode