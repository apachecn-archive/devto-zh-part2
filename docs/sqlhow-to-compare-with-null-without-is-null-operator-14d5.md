# SQL:如何在没有“IS NULL”运算符的情况下与 NULL 比较

> 原文：<https://dev.to/yuyabu/sqlhow-to-compare-with-null-without-is-null-operator-14d5>

我们用`IS NULL`代替`=`与 NULL 进行比较。这是因为如果您将 null 和值与`=`进行比较，会有许多 RDBMSs 变得未知。

*   将某些值和 null 与 false 进行比较
*   null 和 null 对 true 的比较

有时候我们想这样比较。在这种情况下，我们可以使用比较运算符`IS DISTINCT FROM`或`IS NOT DISTINCT FROM`。

| A | B | A = B | a 和 B 没有明显的区别 |
| --- | --- | --- | --- |
| Zero | Zero | 真实的 | 真实的 |
| Zero | one | 错误的 | 错误的 |
| Zero | 空 | 未知的 | 错误的 |
| 空 | 空 | 未知的 | 真实的 |

# 环境和版本

```
postgres=# SELECT version();
                                                             version                                                              
----------------------------------------------------------------------------------------------------------------------------------
 PostgreSQL 10.1 on x86_64-apple-darwin13.4.0, compiled by Apple LLVM version 6.0 (clang-600.0.57) (based on LLVM 3.5svn), 64-bit
(1 row) 
```

```
$ sqlite3 --version
3.22.0 2018-01-22 18:45:57 0c55d179733b46d8d0ba4d88e01a25e10677046ee3da1d5b1581e86726f2171d 
```

# PostgreSQL 上的例子

```
--compare null and value with '='( result is unknown)
postgres=# select null = 4;
 ?column? 
----------

(1 row)

--compare value and value (result is true/false)
postgres=# select 4 = 4;
 ?column? 
----------
 t
(1 row)

--compare null and value with 'IS DISTINCT FROM' operator (result is true/false)
postgres=# select null is distinct from  4;
 ?column? 
----------
 t
(1 row)

-- use 'IS NOT DISTINCT FROM' to check equal
postgres=# select null is not distinct from  4;
 ?column? 
----------
 f
(1 row)

-- You can also compare values and values using 'IS DISTINCT FROM'
postgres=# select 4 is distinct from  4;
 ?column? 
----------
 f
(1 row) 
```

# sqlite:用`IS`代替`IS DISTINCT FROM`

sqlite 不能用`IS DISTINCT FROM`。

你可以用`IS`来代替

```
sqlite> select 4 = 4;
1
sqlite> select 4 is null;
0
sqlite> select 4 is 4;
1
sqlite> select 4 is 5;
0
sqlite> select null is null;
1 
```

# 其他

MySQL 中的`IS DISTINCT FROM`运算符是`<=>`

# 引用

*   [https://modern-sql.com/feature/is-distinct-from](https://modern-sql.com/feature/is-distinct-from)
*   Joe Celko 的 SQL for Smarties:高级 SQL 编程(日文版)16。3 与运算符[不]不同