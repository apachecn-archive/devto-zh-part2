# 在 MySQL (1m 行)中搜索全名的最好方法是什么？

> 原文：<https://dev.to/auct/what-is-the-best-way-to-search-full-name-in-mysql-1m-rows-338h>

你好。我需要一些帮助。目前我有弹性搜索，但它非常烦人(需要大量内存，每次都关机)，所以我想摆脱它，回到 mysql 搜索。我有一张 1m 长的桌子。
可搜索列:姓名，姓名原件(ofthen 空白)。两者都是各 2-3 字，varchar 255。

你将如何组织搜索？