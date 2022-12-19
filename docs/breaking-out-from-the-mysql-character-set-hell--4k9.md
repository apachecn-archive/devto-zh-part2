# 从 MySQL 字符集的地狱中突围

> 原文：<https://dev.to/manish_demblani/breaking-out-from-the-mysql-character-set-hell--4k9>

### MySQL 字符集地狱，一个危险的地方。阅读了解你如何陷入其中，如果你发现自己陷入其中该怎么办，以及如何从 latin1 迁移到 utf8mb4。

如果您曾经遇到过数据库和 SQL(或者试图了解它们是什么)，您一定会偶然发现 MySQL(一种基于结构化查询语言(SQL)的开源关系数据库管理系统(RDBMS))。在 MySQL 中，您有没有想过字符集和排序规则意味着什么？

有没有发生过这样的情况，当查询数据库中的数据时(使用任何 MySQL GUI 或 CLI)，您会看到[官样文章](https://en.wikipedia.org/wiki/Gibberish)(乱码数据)？例如，您在应用程序中输入以下文本:

阿拉伯文、英文、法文、俄文和西班牙文。

但是在数据库表中查询相同的数据时，您会看到:

t’0 给予阿拉伯语发言人发言时间。，第一次发言时间为上午 10 时至下午 1 时，下午 3 时至下午 1 时，下午 3 时至下午 1 时，下午 3 时至下午 1 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至 6 时，下午 3 时至下午 6 时，下午 3 时至 6 时，下午 3 时至 6 时。

*注意:虽然这篇文章关注的是如何将数据库从 latin1 迁移到 utf8mb4，但是您可以按照这篇文章在任意两种字符集和排序规则之间进行迁移。*

# [T1。。。](#-)

## UTF 的出现和崛起-8

那些知道计算机编码如何工作的人会知道，在很长一段时间里，ASCII(美国信息交换标准代码)是默认的编码标准。8 位/1 字节字符编码涵盖了英语中的所有字符、数字和最常用的特殊字符(！,.*以此类推……)。但是随着时间的推移，许多不说英语的人也开始使用计算机，最终计算机开始支持这些外语。

为了支持这些外语，需要一个良好的字节流编码系统，它将支持各种各样的字符(英语和外语)，因为 ASCII 编码只有 1 个字节，并且已经填充了 0-255(查看支持字符的列表)，所以需要一个新的多字节字符集系统。对新系统的研究开始于 1992 年，那一年，UTF-1 被引进，但是由于它的无能导致了 1993 年 1 月 UTF-8 的发展。

当我着手创建 MOS 865 时，你认为它是一夜之间发生的吗？不，是 MOS 1，烧毁了我爸爸的车库。MOS 2 只在一月份安排约会。还有 862 个我从其他人身上学到的……”:皮特·贝克尔(朋友)

UTF-8(也称为 Unicode)迅速崛起，主要由于其与现有 ASCII 系统的向后兼容性，主要计算机系统开始采用它。2008 年，谷歌报告称 Unicode 成为 HTML 文件最常见的编码。

[![Shows the usage of the main encodings on the web from 2001 to 2012 as recorded by Google.](../Images/03d6880414c2f2fdaf426259117e2303.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1qzHQwe4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xidnnng2j6i4sdtj1t8q.png)

如果你想知道更多关于字符集和编码意味着什么，它们是如何工作的，以及如何帮助计算机理解语言和软件开发人员开发国际软件，那么读一读乔尔·斯波尔斯基在他的博客[中所说的，每个软件开发人员绝对、肯定必须知道的关于 Unicode 和字符集的绝对最低要求(没有借口！).](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)

# [T1。。。](#-)

#### 这篇文章最初发表在我的[博客上](https://medium.com/@manish_demblani/breaking-out-from-the-mysql-character-set-hell-24c6a306e1e5)。本文的提醒包括以下主题:

*   MySQL —归类和字符集
*   Latin1 和 MySQL
*   MySQL 字符集地狱——重现问题
*   修复问题
*   第 n 个编码字符
*   应用注意事项