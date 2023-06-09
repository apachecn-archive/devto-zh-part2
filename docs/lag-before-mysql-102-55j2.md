# MySQL 10.2 之前的 Lag()

> 原文：<https://dev.to/ladvien/lag-before-mysql-102-55j2>

最近我被要求使用 MySQL 10.0。我惊讶地发现 MySQL 10.2 及更低版本不支持一些常见的[窗口函数](https://drill.apache.org/docs/sql-window-functions-introduction/)，具体来说就是[值函数](https://drill.apache.org/docs/value-window-functions/)和[秩函数](https://drill.apache.org/docs/ranking-window-functions/)。

好吧，真倒霉。我真的很需要它们。

最重要的是，我只有对数据库的读取权限，没有创建存储过程的能力。出于绝望，我发现自己正在研究使用 MySQL [用户变量](https://dev.mysql.com/doc/refman/5.7/en/user-variables.html)创建自己的函数的可能性。

稍微调整但丁，“抛弃一切正常，叶谁进入这里。”用户变量很奇怪。

我花了一些时间在网上研究，我找到的关于这个主题的最好的文章是:

[高级 MySQL 用户变量技术](https://www.xaprb.com/blog/2006/12/15/advanced-mysql-user-variable-techniques/)

它专注于从用户变量中获得期望的行为，而不是理解它们。这篇文章将坚持同样的理念——我不需要一定理解他们，但是，我希望能够预测他们的行为。

一个警告的话，在这篇文章之前，上面的文章并不是真正的建议阅读——它更多的是必读。如果您不知道如何在需要时强制评估用户变量，那么结果是不可预测的。

TL；DR 版本:在用户变量中，操作顺序非常重要*,将用户变量包装在子查询或函数中以强制求值。*

 *在这篇文章的底部，我已经包括了本文中使用的数据。您可以将它插入到 MySQL 或 MariaDB 数据库中，然后继续操作。目标是将这些数据转换成`start_date`和`stop_date`，这将*极大地*减少存储需求。

对于`id` 1，`start_date`和`stop_date`的等价物看起来像:

| 身份证明（identification） | 日期 |  |
| --- | --- | --- |
| one | **2012 年 9 月 10 日** | 开始 _1 |
| one | 09/11/12 |  |
| one | 09/12/12 |  |
| one | 09/13/12 |  |
| one | **2012 年 9 月 14 日** | 停止 _1 |
| one | **2012 年 10 月 11 日** | 开始 _2 |
| one | 10/12/12 |  |
| one | **2012 年 10 月 13 日** | 停止 _2 |

我们希望最终得到如下的表格。

| 身份证明（identification） | 开始日期 | 结束日期 |
| --- | --- | --- |
| one | 09/10/12 | 09/14/2012 |
| one | 10/11/22 | 10/13/2012 |

要将数据转换到这个表中，知道用户变量可以保存从一行到下一行的值是很重要的。

```
SELECT
    id, date, @row_number:=@row_number + 1 row_num
FROM
    attendance
        CROSS JOIN
    (SELECT @row_number:=0) r; 
```

Enter fullscreen mode Exit fullscreen mode

这将生成下表:

| 身份证明（identification） | 日期 | 行编号 |
| --- | --- | --- |
| one | 2012-09-10 | one |
| one | 2012-09-10 | Two |
| one | 2012-09-11 | three |
| five | 2013-02-07 | four |
| five | 2013-02-07 | five |
| five | 2013-02-07 | six |
| five | 2013-02-07 | seven |
| five | 2013-02-07 | eight |
| five | 2013-02-07 | nine |
| ... | ... | ... |

很酷，对吧？现在，只要我们能在`id`改变时让`row_number`复位就好了。别担心，让我们使用另一个变量来存储前一行的`id`,这样我们就可以将它与当前的进行比较。

```
SELECT 
    id,
    date,
    @row_number:=@row_number + 1 row_number,
    IF(@previous_id = id,
        @row_number,
        @row_number:=0) calc1,
    @previous_id:=id cacl2
FROM
    attendance
        CROSS JOIN
    (SELECT @row_number:=0, @previous_id:=0) r; 
```

Enter fullscreen mode Exit fullscreen mode

这将为我们提供以下输出:

| 身份证明（identification） | 日期 | 行数 | calc1 | 氯化钙 |
| --- | --- | --- | --- | --- |
| one | 2012-09-10 | one | Zero | one |
| one | 2012-09-10 | one | one | one |
| one | 2012-09-11 | Two | Two | one |
| five | 2013-02-07 | three | Zero | five |
| five | 2013-02-07 | one | one | five |
| ... | ... | ... | ... | ... |

注意，`calc1`和`calc2`不是您需要的值。它们仅仅是用于在`id`改变时重置`row_number`的计算。嗯，这很有趣——希望你能看到它有很多可能性。

现在，让我们回过头来再多考虑一下我们的问题。

| 身份证明（identification） | 日期 |
| --- | --- |
| one | **2012 年 9 月 10 日** |
| one | 09/11/12 |
| one | 09/12/12 |
| one | 09/13/12 |
| one | **2012 年 9 月 14 日** |
| one | **2012 年 10 月 11 日** |
| one | 10/12/12 |
| one | **2012 年 10 月 13 日** |

我们可以将一行的值保存到下一行。因此，可以通过比较当前行的`date`值和前一行的`date`值来检测出勤日期范围内的休息。如果前一行大于当前行减一，那么我们知道在该范围内有一个中断。

```
SELECT 
    id, date, range_selector
FROM
    (SELECT DISTINCT
        id,
            date,
            IF(@previous_id = id, @range_selector, @range_selector:=0) calc1,
            IF(DATEDIFF(@previous_date, date) = 1, @range_selector, @range_selector:=@range_selector + 1) range_selector,
            @previous_id:=id calc2,
            @previous_date:=DATE(date) calc3
    FROM
        (SELECT DISTINCT
        *
    FROM
        attendance
    ORDER BY id DESC , date DESC) order_sub
    CROSS JOIN (SELECT 
        @id_selector:=0,
            @previous_date:=0,
            @range_selector:=0,
            @previous_id:=0
    ) variable_initialization
    ORDER BY id , date DESC) date_ranges; 
```

Enter fullscreen mode Exit fullscreen mode

此*应*给出下表:

| 身份证明（identification） | 日期 | 范围 _ 索引 |
| --- | --- | --- |
| one | 2012-10-13 | one |
| one | 2012-10-12 | one |
| one | 2012-10-11 | one |
| one | 2012-09-14 | Two |
| one | 2012-09-13 | Two |
| one | 2012-09-12 | Two |
| one | 2012-09-11 | Two |
| one | 2012-09-10 | Two |
| Two | 2012-09-23 | one |
| Two | 2012-08-22 | Two |
| Two | 2012-08-17 | three |
| Two | 2012-08-12 | four |
| Two | 2012-08-11 | four |
| Two | 2012-08-10 | four |
| Two | 2012-08-09 | four |
| four | 2012-11-22 | one |
| four | 2012-11-03 | Two |
| four | 2012-11-02 | Two |
| four | 2012-11-01 | Two |
| four | 2012-10-04 | three |
| four | 2012-10-03 | three |
| four | 2012-10-02 | three |
| four | 2012-10-01 | three |
| five | 2013-02-07 | one |
| five | 2013-02-06 | one |
| five | 2013-02-05 | one |
| five | 2013-02-04 | one |
| five | 2013-02-03 | one |
| five | 2013-02-02 | one |
| five | 2013-01-28 | Two |
| five | 2013-01-24 | three |
| five | 2013-01-23 | three |
| five | 2012-12-07 | four |
| five | 2012-12-06 | four |
| five | 2012-12-05 | four |
| five | 2012-12-04 | four |
| five | 2012-12-03 | four |
| five | 2012-12-02 | four |
| five | 2012-12-01 | four |
| five | 2012-11-01 | five |

我之所以说“应该”，是因为如果你修改了用户变量的顺序，就会出错。如果你换一下`ORDER BY`，它就会坏掉。如果你加上一个`WHERE`或`HAVING`条款，它就会失效。几乎可以说，这是一个非常脆弱的查询。

然而，聪明的一群人可能明白我们在做什么。现在，它只是简单地把`date`的`MIN()`和`MAX()`以及`id`和`range_index`的`group by`拿走。

```
SELECT 
    id, min(date) start_date, max(date) end_date
FROM
    (SELECT DISTINCT
        id,
            date,
            IF(@previous_id = id, @range_selector, @range_selector:=0) calc1,
            IF(DATEDIFF(@previous_date, date) = 1, @range_selector, @range_selector:=@range_selector + 1) range_selector,
            @previous_id:=id calc2,
            @previous_date:=DATE(date) calc3
    FROM
        (SELECT DISTINCT
        *
    FROM
        attendance
    ORDER BY id DESC , date DESC) order_sub
    CROSS JOIN (SELECT 
        @id_selector:=0,
            @previous_date:=0,
            @range_selector:=0,
            @previous_id:=0
    ) r
    ORDER BY id , date DESC) date_ranges
    GROUP BY id, range_selector; 
```

Enter fullscreen mode Exit fullscreen mode

这将为我们提供如下输出:

| 身份证明（identification） | 开始日期 | 结束日期 |
| --- | --- | --- |
| one | 2012-10-11 | 2012-10-13 |
| one | 2012-09-10 | 2012-09-14 |
| Two | 2012-09-23 | 2012-09-23 |
| Two | 2012-08-22 | 2012-08-22 |
| Two | 2012-08-17 | 2012-08-17 |
| Two | 2012-08-09 | 2012-08-12 |
| four | 2012-11-22 | 2012-11-22 |
| four | 2012-11-01 | 2012-11-03 |
| four | 2012-10-01 | 2012-10-04 |
| five | 2013-02-02 | 2013-02-07 |
| five | 2013-01-28 | 2013-01-28 |
| five | 2013-01-23 | 2013-01-24 |
| five | 2012-12-01 | 2012-12-07 |
| five | 2012-11-01 | 2012-11-01 |

我们开始吧。不太令人惊讶，但我无法通过谷歌找到这个答案，所以我想我会把它添加到伟大的维基天空。

```
CREATE TABLE attendance(
   id   INTEGER  NOT NULL
  ,date DATE  NOT NULL
);
INSERT INTO attendance(id,date) VALUES (1,'2012-09-10');
INSERT INTO attendance(id,date) VALUES (1,'2012-09-11');
INSERT INTO attendance(id,date) VALUES (1,'2012-09-12');
INSERT INTO attendance(id,date) VALUES (1,'2012-09-13');
INSERT INTO attendance(id,date) VALUES (1,'2012-09-14');
INSERT INTO attendance(id,date) VALUES (1,'2012-10-11');
INSERT INTO attendance(id,date) VALUES (1,'2012-10-12');
INSERT INTO attendance(id,date) VALUES (1,'2012-10-13');
INSERT INTO attendance(id,date) VALUES (2,'2012-08-09');
INSERT INTO attendance(id,date) VALUES (2,'2012-08-10');
INSERT INTO attendance(id,date) VALUES (2,'2012-08-11');
INSERT INTO attendance(id,date) VALUES (2,'2012-08-12');
INSERT INTO attendance(id,date) VALUES (2,'2012-08-17');
INSERT INTO attendance(id,date) VALUES (2,'2012-08-22');
INSERT INTO attendance(id,date) VALUES (2,'2012-09-23');
INSERT INTO attendance(id,date) VALUES (4,'2012-10-01');
INSERT INTO attendance(id,date) VALUES (4,'2012-10-02');
INSERT INTO attendance(id,date) VALUES (4,'2012-10-03');
INSERT INTO attendance(id,date) VALUES (4,'2012-10-04');
INSERT INTO attendance(id,date) VALUES (4,'2012-11-01');
INSERT INTO attendance(id,date) VALUES (4,'2012-11-02');
INSERT INTO attendance(id,date) VALUES (4,'2012-11-03');
INSERT INTO attendance(id,date) VALUES (4,'2012-11-22');
INSERT INTO attendance(id,date) VALUES (5,'2012-11-01');
INSERT INTO attendance(id,date) VALUES (5,'2012-12-01');
INSERT INTO attendance(id,date) VALUES (5,'2012-12-02');
INSERT INTO attendance(id,date) VALUES (5,'2012-12-03');
INSERT INTO attendance(id,date) VALUES (5,'2012-12-04');
INSERT INTO attendance(id,date) VALUES (5,'2012-12-05');
INSERT INTO attendance(id,date) VALUES (5,'2012-12-06');
INSERT INTO attendance(id,date) VALUES (5,'2012-12-07');
INSERT INTO attendance(id,date) VALUES (5,'2013-01-23');
INSERT INTO attendance(id,date) VALUES (5,'2013-01-24');
INSERT INTO attendance(id,date) VALUES (5,'2013-01-28');
INSERT INTO attendance(id,date) VALUES (5,'2013-02-02');
INSERT INTO attendance(id,date) VALUES (5,'2013-02-03');
INSERT INTO attendance(id,date) VALUES (5,'2013-02-04');
INSERT INTO attendance(id,date) VALUES (5,'2013-02-05');
INSERT INTO attendance(id,date) VALUES (5,'2013-02-06');
INSERT INTO attendance(id,date) VALUES (5,'2013-02-07'); 
```

Enter fullscreen mode Exit fullscreen mode*