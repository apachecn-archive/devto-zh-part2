# pgexercise -成员

> 原文：<https://dev.to/stanzheng/pgexercise---members-4c74>

一直在做 postgres 练习；有些是直截了当的，有些则有多种解决方案。这方面的一个例子如下。

点击查看至少预订过一次
[的会员人数](https://pgexercises.com/questions/aggregates/members1.html)

更无效的方法是。(我的第一关)

```
select count(1) from cd.members 
where memid 
IN (select memid from cd.bookings 
    where slots is not null) 
```

Enter fullscreen mode Exit fullscreen mode

通过检查不同的计数并执行以下操作，可以更容易地对它们进行分组。(distinct 是一个子查询)

`select count(distinct memid) from cd.bookings`。