# 您需要的每一个 SQL 连接

> 原文：<https://dev.to/katiekodes/every-sql-join-youll-ever-need-2hbd>

好...也许不是每个都加入*。但是这张小抄帮助我在数据库 101 期中考试中取得了 104%的成绩，所以它肯定是有价值的。*

> **注意:**在 dev.to 上有很多我不能做的标记。因此，尽管我让这篇文章完整地交叉发表，但如果没有我的“注意这一点！”背景突出显示、文本中的交叉链接、目录等。
> 
> 我推荐在这里阅读它，在我最初的博客上，而不是在 dev.to 上，然后回来 dev.to 讨论！

* * *

找到我以前的学校笔记花了点功夫，因为我甚至没有在标题里写上“加入”。相反，我把我的笔记叫做“SQL 中的集合操作”这才是真正的意义所在，因为无论您使用 SQL、Excel VLOOKUP、Python 还是纸笔来比较一个记录列表和另一个记录列表，都没有关系。将一组事物与另一组事物结合起来，作为一个商业问题，就是数学。更好的是，这是有趣的数学，而不是困难的数学！这是维恩图——让你像在小学一样涂鸦的那种数学。

当您的业务问题需要您将两组记录*(每个“记录”代表某个真实世界的实体，如一个人、一张发票或一个国家)*合并到一个单独的[关系数据库管理系统](https://en.wikipedia.org/wiki/Relational_database_management_system)*【RDBMS】*(通常简称为“数据库”)中时，SQL 代码非常有用所有这样的系统都具备使用 SQL 编程语言提取数据的能力。

*   如果你是一名 **Python** 数据分析师，一定要学习一些 SQL，这样你就可以利用它的能力来处理你的数据已经存在的数据库。

*   如果你是一名 **Salesforce** 分析师或系统管理员，你可能会遇到报告和 SOQL 的限制。也许您可以通过将两个表导出到 Excel，然后执行一个`VLOOKUP`操作来连接它们来解决这些问题。为了从那里升级，您可能想要探索 Python，而不是 SQL -除非您的 IT 部门已经将您的 Salesforce 组织的数据备份到传统数据库。然后你会想知道所有关于 SQL 的知识，因为它会节省你很多时间！

我在 Oracle 数据库中做了很多工作，所以我发现 Oracle 风格的 SQL 是输入速度最快的，我将用它作为例子。如果你注册了一个免费账户并试用了他们的 [Live SQL](https://livesql.oracle.com/) 工具，你就可以在线运行 Oracle SQL 代码。

要将这转换成另一种“风格”的 SQL，您可能需要 Google 一下我使用过的关键字，以及您的数据库名称。例如，当我的`full outer join`命令在 mysql 数据库中不起作用时，我搜索了“完全外连接‘MySQL’”，发现 MySQL 风格的 SQL 不包含`full outer join`命令。相反，你必须[聪明地组合更小的集合运算](#mysql-fake-full-outer-join) *(这就像小心翼翼地在你的维恩图中一次涂上一整圈，而不是用蜡笔一次涂遍整个图)*。

**Pro 提示:**将这篇文章加入书签，当作参考指南。

无论您对 SQL 了解多少，都可以使用本文顶部的[目录](#post-toc)一次阅读一个部分。

*   虽然简单，但这个指南很长，所以如果你正在学习，每天阅读一个“加入类型”。(除非你要坐很长时间的公交车！)
*   如果您知道诀窍，并且只需要相互比较命令，请直接跳到您感兴趣的命令。

## 我们的样本数据

### 表`A`内容(`CONVENTION`)

是参加会议的人员及其工作地点的列表。

(是的，这是一个非常八卦的事件，向参与者索要社会安全号码。)

| severely subnormal 智力严重逊常 | 名字 _lf | pH 值(溶液或物质的酸碱度) | 全身长的 | 公司 |
| --- | --- | --- | --- | --- |
| A1A1A1 | 阿努什·阿姆吉特 | One million one hundred and eleven thousand one hundred and eleven | [111@example.com](mailto:111@example.com) | 苹果 |
| B2B2B2 | 贝尼塔·博尔赫斯 | Two million two hundred and twenty-two thousand two hundred and twenty-two | [222@example.com](mailto:222@example.com) | 他们喝酒了 |
| ccc333 | 凯西·库姆斯 | Three million three hundred and thirty-three thousand three hundred and thirty-three | [333@example.com](mailto:333@example.com) | CVS |
| D4D4D4 | 达尔维什·达赫尔 | Four million four hundred and forty-four thousand four hundred and forty-four | [444@example.com](mailto:444@example.com) | （里面或周围有树的）小山谷 |
| E5E5E5 | 埃兹拉·埃利斯 | Five million five hundred and fifty-five thousand five hundred and fifty-five | [555@example.com](mailto:555@example.com) | （EconomicDevelopmentFoundation）欧洲开发基金 |
| F6F6F6 | 弗朗西丝·富尔维亚 | Six million six hundred and sixty-six thousand six hundred and sixty-six | [666@example.com](mailto:666@example.com) | 燧石 |

### 表`B`内容(`RESTAURANT`)

是一家餐馆的常客名单，他们最喜欢的食物，以及他们的年龄。

是的，这是一家非常爱管闲事的餐馆，向常客索要社会保险号。)

*   请注意 Cathy Combs 的电子邮件地址是“ccc”，而不是“333”。
*   请注意弗朗西斯·富尔维亚的社会保险号中的错别字。

| 社会的 | 名字 _fl | 电话 | 电子邮件 | 最喜欢的食物 | 年龄 |
| --- | --- | --- | --- | --- | --- |
| ccc333 | 凯西·库姆斯 | Three million three hundred and thirty-three thousand three hundred and thirty-three | [ccc@example.com](mailto:ccc@example.com) | 胡萝卜 | Thirty-three |
| D4D4D4 | 达尔维什·达赫尔 | Four million four hundred and forty-four thousand four hundred and forty-four | [444@example.com](mailto:444@example.com) | 多利多 | forty-four |
| E5E5E5 | 以斯拉·埃利斯 | Five million five hundred and fifty-five thousand five hundred and fifty-five | [555@example.com](mailto:555@example.com) | 菊苣 | Fifty-five |
| FFF666 | 弗朗西丝·富尔维亚 | Six million six hundred and sixty-six thousand six hundred and sixty-six | [666@example.com](mailto:666@example.com) | 炸薯条 | Sixty-six |
| G7G7G7 | Grace Gao | Seven million seven hundred and seventy-seven thousand seven hundred and seventy-seven | [777@example.com](mailto:777@example.com) | 大蒜 | Seventy-seven |
| H8H8H8 | 海伦·霍普 | Eight million eight hundred and eighty-eight thousand eight hundred and eighty-eight | [888@example.com](mailto:888@example.com) | 鹰嘴豆泥 | Eighty-eight |

### 代码创建这个数据(“DDL”&“DML”)

下面是在 Oracle 数据库中创建`CONVENTION`和`RESTAURANT`表并使用我们的示例数据填充它们的代码，如果您想自己尝试的话。

```
drop table CONVENTION;
create table CONVENTION ( 
    ssn     varchar2(6) not null 
    , name_lf   varchar2(30) not null 
    , ph        varchar2(7) not null 
    , em        varchar(15) not null 
    , company   varchar(15) not null 
);
insert into CONVENTION values ('A1A1A1', 'Amjit, Anush', '1111111', '111@example.com', 'Apple');
insert into CONVENTION values ('B2B2B2', 'Borges, Benita', '2222222', '222@example.com', 'Boiron');
insert into CONVENTION values ('C3C3C3', 'Combs, Cathy', '3333333', '333@example.com', 'CVS');
insert into CONVENTION values ('D4D4D4', 'Daher, Darweesh', '4444444', '444@example.com', 'Dell');
insert into CONVENTION values ('E5E5E5', 'Ellis, Ezra', '5555555', '555@example.com', 'EDF');
insert into CONVENTION values ('F6F6F6', 'Fulvia, Frances', '6666666', '666@example.com', 'Firestone');
drop table RESTAURANT;
create table RESTAURANT ( 
    social      varchar2(6) not null 
    , name_fl   varchar2(30) not null 
    , phone     varchar2(7) not null 
    , email     varchar2(15) not null 
    , fav_food  varchar2(10) not null 
    , age       int not null 
);
insert into RESTAURANT values ('C3C3C3', 'Cathy Combs', '3333333', 'ccc@example.com', 'Carrots', 33);
insert into RESTAURANT values ('D4D4D4', 'Darweesh Daher', '4444444', '444@example.com', 'Doritos', 44);
insert into RESTAURANT values ('E5E5E5', 'Ezra Ellis', '5555555', '555@example.com', 'Endives', 55);
insert into RESTAURANT values ('FFF666', 'Frances Fulvia', '6666666', '666@example.com', 'Fries', 66);
insert into RESTAURANT values ('G7G7G7', 'Grace Gao', '7777777', '777@example.com', 'Garlic', 77);
insert into RESTAURANT values ('H8H8H8', 'Helen Hopper', '8888888', '888@example.com', 'Hummus', 88); 
```

### 谁重叠了又如何

假设我们是一家大公司。我们的规模如此之大，以至于我们经营大会*和*我们拥有餐厅。我们想要寻找我们的两个客户列表之间的“重叠”,并相应地进行分析。

实际上，这就是我给出的关于*“为什么”*我选择查询和输出的所有背景。它们完全没有商业意义。我选择它们来演示不同风格的 SQL 查询能做什么和不能做什么！

但是回到我们组合电视的话题上来:

像往常一样，当比较来自两个不同数据集的“人”记录时，必须决定什么“证明”两个记录代表同一个“真实世界的人”你的名字匹配吗？邮件？社保号*(记住，人死了以后都是重复使用的)*？它们的某种组合？

对于我们的例子，我们将看看两种不同的方法来组合我们的数据，只是为了展示我们的代码肌肉。

在阅读代码之前，请务必查看`CONVENTION`和`RESTAURANT`，并注意:

1.  当我们加入`A.ssn=B.social`时，凯茜-以斯拉(3-5)比赛
    *   Anush、Benita 和“Frances w/ `ssn` F6F6F6”仍然是`CONVENTION`独有的
    *   "弗朗西斯 w/ `ssn` FFF666，"格蕾丝，&海伦保持着对`RESTAURANT`的独特
2.  当我们在`A.ph=B.phone and A.em=B.email`加入时，达威什-弗朗西斯(4-6)比赛
    *   阿努什、贝尼塔和《凯西 w/`em`[333 @](mailto:333@)……”保持对`CONVENTION`的唯一性
    *   `email`凯茜 w/[CCC @](mailto:ccc@)...，&格蕾丝，`RESTAURANT`海伦保持独特

你的任务已经完成了！让我们来看看 5 种“连接类型”中的第一种

* * *

## 加入类型:“在`A`”

[![Venn diagram with the left circle shaded, including the overlap](../Images/9654ab596b950ff6b9811291b9773c34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CU-ANfG3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.com/images/venn-a.png)

对于这种连接类型，我们期望 6 行输出(Anush-Frances - all people from `CONVENTION`)。

在某种程度上，这些人“也存在于”`RESTAURANT` *(根据“[匹配标准”](#who-overlaps-and-how)【我们选择】*的定义)，我们期望从`RESTAURANT`看到关于他们的细节。

### `LEFT OUTER JOIN`

#### SQL 查询，`left outer join`示例 1/4

首先，我们将匹配社会保险号，因此 Cathy 有来自“表 B”的详细信息，但 Frances 没有。

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
left outer join RESTAURANT B on A.ssn = B.social; 
```

#### 查询输出，`left outer join`例 1/4

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 阿努什·阿姆吉特 | 苹果 |  |  |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |  |  |
| 凯西·库姆斯 | CVS | 凯西·库姆斯 | 胡萝卜 |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 | 达尔维什·达赫尔 | 多利多 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 |  |  |

#### SQL 查询，`left outer join`例 2/4

接下来，我们将在电话和电子邮件上进行匹配，因此 Frances 有来自“表 B”的详细信息，但 Cathy 没有。)

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
left outer join RESTAURANT B on A.ph = B.phone and A.em = B.email; 
```

#### 查询输出，`left outer join`例 2/4

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 阿努什·阿姆吉特 | 苹果 |  |  |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |  |  |
| 凯西·库姆斯 | CVS |  |  |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 | 达尔维什·达赫尔 | 多利多 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 | 弗朗西丝·富尔维亚 | 炸薯条 |

#### SQL 查询，`left outer join`例 3/4

接下来，我们将尝试*(不正确)*来隐藏小于 50 岁的人的`name_fl`和`fav_food`细节。

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
left outer join RESTAURANT B on A.ssn = B.social
where B.age >= 50; 
```

#### 查询输出，`left outer join`例 3/4

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |

#### 注

尽管“来自`B`的行”在我们的连接中是可选的，但在添加对`B`的过滤器时，我们已经有效地将这个“左连接”变成了一个“内连接”，其中我们的数据[必须出现在两个表](#join-type--in-both-a-and-b)的**中。**

我们*实际上*所做的是过滤掉所有没有年龄*(因为他们不在表格`B` )* 或者小于 50 岁的人的全部记录。

这是一个常见的错误。我们将在下一个示例中修复它。

在数学中，当你将数字相减时，相减的顺序很重要。

同样，在使用 SQL 解决业务问题时，考虑一下“连接”和“过滤”数据的顺序*也很重要。*

#### SQL 查询，`left outer join`示例 4/4

现在，我们实际上会隐藏 50 岁或更小的人的`name_fl`和`fav_food`细节。

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
left outer join (select * from RESTAURANT where age >= 50) B on A.ssn = B.social; 
```

#### 查询输出，`left outer join`例 4/4

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 阿努什·阿姆吉特 | 苹果 |  |  |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |  |  |
| 凯西·库姆斯 | CVS |  |  |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 |  |  |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 |  |  |

#### 注

在我们修正的查询中，我们*在将* `RESTAURANT`视为`B`之前，根据年龄对其进行了预过滤，这解决了我们上一个示例中的问题。

这是第一个“连接类型”我们来看看第二个。

* * *

## 连接类型:“在`A`而不在`B`

[![Venn diagram with only the portion of the left circle that does not overlap shaded](../Images/01b2d01940e69064a7ad7323c8ad89b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cLacCWGH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.com/images/venn-difference.png)

对于这种连接类型，我们期望 3 行输出 *(Anush+Benita+Cathy 或 Anush+Benita+Frances，取决于我们选择的"[匹配标准](#who-overlaps-and-how))*。

根据定义，表`B`不是您业务问题的最终输出的一部分，所以请记住，下面的所有方法将只包括表`A`中的“细节”。

### `NOT IN`

#### SQL 查询，`not in`例 1/3

当你只匹配一列时，这是很棒的。

```
select A.name_lf, A.company
from CONVENTION A
where A.ssn not in ( select B.social from RESTAURANT B ); 
```

#### 查询输出，`not in`例 1/3

| 名字 _lf | 公司 |
| --- | --- |
| 阿努什·阿姆吉特 | 苹果 |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |
| 弗朗西丝·富尔维亚 | 燧石 |

#### SQL 查询，`not in`例 2/3

当你需要在多个列上进行匹配时会变得不确定——你必须将多个列连接成一个列，并负责任地处理空值。

对于多列匹配，**不是首选**。用 [`NOT EXISTS`](#not-exists) 代替。

这个查询只是做做样子！

```
select A.name_lf, A.company
from CONVENTION A
where nvl(A.ph,'')||'~'||nvl(A.em,'') not in ( select nvl(B.phone,'')||'~'||nvl(B.email,'') from RESTAURANT B ); 
```

#### 查询输出，`not in`例 2/3

| 名字 _lf | 公司 |
| --- | --- |
| 阿努什·阿姆吉特 | 苹果 |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |
| 凯西·库姆斯 | CVS |

#### SQL 查询，`not in`例 3/3

我们的第三个`not in`例子甚至不是真正的表间连接:注意`RESTAURANT`甚至不在查询中。

我只是想指出，这与您在向查询添加手写值过滤器时可以使用的`not in`的输入方式相同。

```
select A.name_lf, A.company
from CONVENTION A
where A.ssn not in ( 'B2B2B2','XYZZY','E5E5E5' ); 
```

#### 查询输出，`not in`例 3/3

| 名字 _lf | 公司 |
| --- | --- |
| 阿努什·阿姆吉特 | 苹果 |
| 凯西·库姆斯 | CVS |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 |
| 弗朗西丝·富尔维亚 | 燧石 |

### `<> ALL`

这是*和`not in`一模一样的*，只是你输入的是`<> all`。关于`not in`一节的每个例子都可以用`<> all`来写。

#### SQL 查询

```
select A.name_lf, A.company
from CONVENTION A
where A.ssn <> all ( select B.social from RESTAURANT B ); 
```

#### 查询输出

| 名字 _lf | 公司 |
| --- | --- |
| 阿努什·阿姆吉特 | 苹果 |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |
| 弗朗西丝·富尔维亚 | 燧石 |

### `MINUS`

使用`minus`，我们不能输出不在我们的“匹配标准”中的列。

也就是说，如果我们的业务问题是一个简单的“伪检查”列表，即“所有只出现在`A`中的值”，那么这样有限的输出可能就足够了

#### SQL 查询，`minus`例 1/2

```
select A.ssn
from CONVENTION A
minus
select B.social
from RESTAURANT B; 
```

#### 查询输出，`minus`例 1/2

| severely subnormal 智力严重逊常 |
| --- |
| A1A1A1 |
| B2B2B2 |
| F6F6F6 |

#### SQL 查询，`minus`例 2/2

```
select A.ph, A.em
from CONVENTION A
minus
select B.phone, B.email
from RESTAURANT B; 
```

#### 查询输出，`minus`例 2/2

| pH 值(溶液或物质的酸碱度) | 全身长的 |
| --- | --- |
| One million one hundred and eleven thousand one hundred and eleven | [111@example.com](mailto:111@example.com) |
| Two million two hundred and twenty-two thousand two hundred and twenty-two | [111@example.com](mailto:111@example.com) |
| Three million three hundred and thirty-three thousand three hundred and thirty-three | [111@example.com](mailto:111@example.com) |

### `NOT EXISTS`

**在复杂的查询中，如果需要在`A`中做“而不是在`B`中做多列“匹配条件”的话，可以使用这个**！

`where not exists (...)`是**编写包含多列匹配的复杂查询的最有用的语法**。

这种方法支持对`A`的“进一步过滤”——只是将它们作为负责“匹配”操作的`where`子句之外的附加`and`和`or`子句。

如果其余的过滤器中有`or`,确保使用圆括号来确保不会意外地使`where not exists (...)`成为可选的。

#### SQL 查询

```
SELECT A.name_lf, A.company
  FROM CONVENTION A
WHERE NOT EXISTS (
  SELECT NULL
    FROM RESTAURANT B
  WHERE A.ph = B.phone and A.em = B.email
); 
```

#### 查询输出

| 名字 _lf | 公司 |
| --- | --- |
| 阿努什·阿姆吉特 | 苹果 |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |
| 凯西·库姆斯 | CVS |

### `LEFT OUTER JOIN ... B.... IS NULL`

我想不出在实践中使用“`LEFT OUTER JOIN`加`B.joinColumn IS NULL`语法的任何理由。完全过度紧张了。

**但是**...这在概念上与`A`或`B`中的[的常用方法相关，但不是两个都是](#join-type--in-a-or-b-but-not-both)，所以这是一个有趣的心理练习，以确保你理解。

当你遇到没有`NOT EXISTS`语法的报告编写工具时，这也是一个很好的“欺骗”模式，比如 [Cognos Report Studio](https://www.ibm.com/developerworks/community/forums/html/threadTopic?id=77777777-0000-0000-0000-000014635183#77777777-0000-0000-0000-000014636392) 或 [Python+Pandas](https://stackoverflow.com/questions/32652718/) 。

使用这种方法，**至关重要的是**使用`IS NULL`过滤器来过滤`B`中涉及到`LEFT OUTER JOIN`操作的所有列。

否则，如果您的数据集中有大量的`NULL`值，您可能会得到意想不到的结果。

这种方法支持对`A`的“进一步过滤”——只是将它们作为负责“匹配”操作的`where`子句之外的附加`and`和`or`子句。

如果其余的过滤器中有`or`，一定要使用圆括号来确保不会意外地使`where ... is null`过滤器成为可选的。

#### SQL 查询

```
select A.name_lf, A.company
from CONVENTION A
left outer join RESTAURANT B on A.ph = B.phone and A.em = B.email
where B.phone is null
and B.email is null; 
```

#### 查询输出

| 名字 _lf | 公司 |
| --- | --- |
| 阿努什·阿姆吉特 | 苹果 |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |
| 凯西·库姆斯 | CVS |

恭喜你通过了第二个“连接类型”到第三个了！

* * *

## 联接类型:“在`A`、`B`或两者中”

[![Venn diagram with both circles and their overlap shaded](../Images/c8724488e72c15a7f019ddfa312215a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---fxCQK17--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.com/images/venn-union.png)

对于这种连接类型，我们期望 9 行输出 *(Anush-Helen，Cathy 或 Frances 根据我们选择的[匹配标准](#who-overlaps-and-how))*。

### `UNION`

#### SQL 查询，`union`例 1/2

```
select A.ssn
from CONVENTION A
union
select B.social
from RESTAURANT B; 
```

#### 查询输出，`union`例 1/2

| severely subnormal 智力严重逊常 |
| --- |
| A1A1A1 |
| B2B2B2 |
| ccc333 |
| D4D4D4 |
| E5E5E5 |
| F6F6F6 |
| FFF666 |
| G7G7G7 |
| H8H8H8 |

*   你看到双倍的“弗朗西斯”数据了吗？

#### 注

与`minus`一样，我们不能输出不在我们的“匹配标准”中的列。

如果我们的业务问题是一个简单的“来自任一表的所有可能值”的“虚拟检查”列表，那么一列输出就足够了

#### SQL 查询，`union`例 2/2

```
select A.ph, A.em
from CONVENTION A
union
select B.phone, B.email
from RESTAURANT B; 
```

#### 查询输出，`union`例 2/2

| pH 值(溶液或物质的酸碱度) | 全身长的 |
| --- | --- |
| One million one hundred and eleven thousand one hundred and eleven | [111@example.com](mailto:111@example.com) |
| Two million two hundred and twenty-two thousand two hundred and twenty-two | [222@example.com](mailto:222@example.com) |
| Three million three hundred and thirty-three thousand three hundred and thirty-three | [333@example.com](mailto:333@example.com) |
| Three million three hundred and thirty-three thousand three hundred and thirty-three | [ccc@example.com](mailto:ccc@example.com) |
| Four million four hundred and forty-four thousand four hundred and forty-four | [444@example.com](mailto:444@example.com) |
| Five million five hundred and fifty-five thousand five hundred and fifty-five | [555@example.com](mailto:555@example.com) |
| Six million six hundred and sixty-six thousand six hundred and sixty-six | [666@example.com](mailto:666@example.com) |
| Seven million seven hundred and seventy-seven thousand seven hundred and seventy-seven | [777@example.com](mailto:777@example.com) |
| Eight million eight hundred and eighty-eight thousand eight hundred and eighty-eight | [888@example.com](mailto:888@example.com) |

*   你看到双倍的“Cathy”数据了吗？

### `FULL OUTER JOIN`

#### SQL 查询，`full outer join`例 1/2

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
full outer join RESTAURANT B on A.ssn = B.social; 
```

#### 查询输出，`full outer join`例 1/2

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 阿努什·阿姆吉特 | 苹果 |  |  |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |  |  |
| 凯西·库姆斯 | CVS | 凯西·库姆斯 | 胡萝卜 |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 | 达尔维什·达赫尔 | 多利多 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 |  |  |
|  |  | 弗朗西丝·富尔维亚 | 炸薯条 |
|  |  | Grace Gao | 大蒜 |
|  |  | 海伦·霍普 | 鹰嘴豆泥 |

*   你看到双倍的“弗朗西斯”数据了吗？

#### SQL 查询，`full outer join`例 2/2

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
full outer join RESTAURANT B on A.ph = B.phone and A.em = B.email; 
```

#### 查询输出，`full outer join`例 2/2

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 阿努什·阿姆吉特 | 苹果 |  |  |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |  |  |
| 凯西·库姆斯 | CVS |  |  |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 | 达尔维什·达赫尔 | 多利多 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 | 弗朗西丝·富尔维亚 | 炸薯条 |
|  |  | 凯西·库姆斯 | 胡萝卜 |
|  |  | Grace Gao | 大蒜 |
|  |  | 海伦·霍普 | 鹰嘴豆泥 |

*   你看到双倍的“Cathy”数据了吗？

### MySQL 山寨`FULL OUTER JOIN`

这是我之前提到的[聪明的诡计](https://stackoverflow.com/questions/4796872/how-to-do-a-full-outer-join-in-mysql)，以弥补 MySQL 风格的 SQL 没有`full outer join`命令的事实。

#### SQL 查询

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
left outer join RESTAURANT B on A.ssn = B.social
union
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
right outer join RESTAURANT B on A.ssn = B.social; 
```

#### 查询输出

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 阿努什·阿姆吉特 | 苹果 |  |  |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |  |  |
| 凯西·库姆斯 | CVS | 凯西·库姆斯 | 胡萝卜 |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 | 达尔维什·达赫尔 | 多利多 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 |  |  |
|  |  | 弗朗西丝·富尔维亚 | 炸薯条 |
|  |  | Grace Gao | 大蒜 |
|  |  | 海伦·霍普 | 鹰嘴豆泥 |

*   你看到双倍的“弗朗西斯”数据了吗？

在半路上！请继续阅读，了解第四种“连接类型”

这本书有很多选项，所以读起来会有点长。

* * *

## 加入类型:“在`A`和`B`都有”

[![Venn diagram with only the overlap of the two circles shaded](../Images/360b1a997ec31ba23fb9523856a81b02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BBwxZq0S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.com/images/venn-intersect.png)

对于这种连接类型，我们期望 3 行输出 *(Cathy-Ezra 或 Darweesh-Frances，取决于我们选择的"[匹配标准](#who-overlaps-and-how))*。

### `IN`

#### SQL 查询，`in`例 1/3

```
select A.name_lf, A.company
from CONVENTION A
where A.ssn in ( select B.social from RESTAURANT B ); 
```

#### 查询输出，`in`例 1/3

| 名字 _lf | 公司 |
| --- | --- |
| 凯西·库姆斯 | CVS |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 |

#### 注

*   `in`是伟大的时候:
    *   你只匹配一列，而且
    *   您不需要查看或进一步过滤`B`中的任何细节

#### SQL 查询，`in`例 2/3

```
select A.name_lf, A.company
from CONVENTION A
where nvl(A.ph,'')||'~'||nvl(A.em,'') in ( select nvl(B.phone,'')||'~'||nvl(B.email,'') from RESTAURANT B ); 
```

#### 查询输出，`in`例 2/3

| 名字 _lf | 公司 |
| --- | --- |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 |
| 弗朗西丝·富尔维亚 | 燧石 |

#### 注

*   当你需要在多个列上进行匹配时会变得不确定——你必须将多个列连接成一个列，并负责任地处理空值。在多个列上匹配时，**不是首选**。用`exists`代替。这只是作秀！

#### SQL 查询，`in`例 3/3

```
select A.name_lf, A.company
from CONVENTION A
where A.ssn in ( 'B2B2B2','XYZZY','E5E5E5' ); 
```

#### 查询输出，`in`例 3/3

| 名字 _lf | 公司 |
| --- | --- |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 |

#### 注

*   这实际上不是真正的表间连接:注意`RESTAURANT`甚至不在查询中。我只想指出，在向查询添加手动值过滤器时，您可以使用相同的`in`。

### `= ANY`

#### SQL 查询

```
select A.name_lf, A.company
from CONVENTION A
where A.ssn = any ( select B.social from RESTAURANT B ); 
```

#### 查询输出

| 名字 _lf | 公司 |
| --- | --- |
| 凯西·库姆斯 | CVS |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 |

#### 注

这个*和`in`的*一模一样，只是你输入的是`= any`。来自`in`的 3 个例子都适用。

### `INTERSECT`

#### SQL 查询，`intersect`例 1/2

```
select A.ssn
from CONVENTION A
intersect
select B.social
from RESTAURANT B; 
```

#### 查询输出，`intersect`例 1/2

| severely subnormal 智力严重逊常 |
| --- |
| ccc333 |
| D4D4D4 |
| E5E5E5 |

#### 注

与`minus`和`union`一样，我们不能输出不在我们的“匹配标准”中的列

如果我们的业务问题是“两个表中出现的所有值”的简单“虚拟检查”列表，那么一列输出可能就足够了

#### SQL 查询，`intersect`例 2/2

```
select A.ph, A.em
from CONVENTION A
intersect
select B.phone, B.email
from RESTAURANT B; 
```

#### 查询输出，`intersect`例 2/2

| pH 值(溶液或物质的酸碱度) | 全身长的 |
| --- | --- |
| Four million four hundred and forty-four thousand four hundred and forty-four | [444@example.com](mailto:444@example.com) |
| Five million five hundred and fifty-five thousand five hundred and fifty-five | [555@example.com](mailto:555@example.com) |
| Six million six hundred and sixty-six thousand six hundred and sixty-six | [666@example.com](mailto:666@example.com) |

### `EXISTS`

#### SQL 查询

```
SELECT A.name_lf, A.company
  FROM CONVENTION A
WHERE EXISTS (
  SELECT NULL
    FROM RESTAURANT B
  WHERE A.ph = B.phone and A.em = B.email
); 
```

#### 查询输出

| 名字 _lf | 公司 |
| --- | --- |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 |
| 弗朗西丝·富尔维亚 | 燧石 |

#### 注

*   当您的“匹配条件”涉及多个列时，`exists`比`in`更好。
*   像`in`，`exists`不让你看到或者进一步过滤来自`B`的细节。

### `INNER JOIN`

#### SQL 查询

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
inner join RESTAURANT B on A.ph = B.phone and A.em = B.email; 
```

#### 查询输出

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 | 达尔维什·达赫尔 | 多利多 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 | 弗朗西丝·富尔维亚 | 炸薯条 |

### `=`{ #等于}

简单的`=`连接表的方法非常普遍，通常在用 SQL 连接表的教程中首先教授。

它支持“进一步的过滤器”——只是将它们作为负责“匹配”操作的`where`子句之外的附加`and`和`or`子句。

如果您的其余过滤器中有`or`，请确保使用括号来确保您不会意外地使您的“连接过滤器”成为可选的。

避免这样的担心是我稍微喜欢`inner join`而不是`=`方法的一个原因。

当我需要试验来确定我想要写什么查询时，我几乎总是使用显式的`inner join`命令。它帮助我在玩的时候避免粗心的退格错误。

#### SQL 查询

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A, RESTAURANT B
where A.ph = B.phone
and A.em = B.email; 
```

#### 查询输出

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 达尔维什·达赫尔 | （里面或周围有树的）小山谷 | 达尔维什·达赫尔 | 多利多 |
| 埃兹拉·埃利斯 | （EconomicDevelopmentFoundation）欧洲开发基金 | 以斯拉·埃利斯 | 菊苣 |
| 弗朗西丝·富尔维亚 | 燧石 | 弗朗西丝·富尔维亚 | 炸薯条 |

如此接近，你可以品尝它。下面是第五种也是最后一种“连接类型”

* * *

## 联接类型:“在`A`或`B`中，但不能同时在两者中”

[![Venn diagram with the unique portions of the circles shaded, but not the overlap](../Images/6c3ee273931e668394b4352d87156569.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GUaRwm1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.com/images/venn-xor.png)

对于这种连接类型，我们期望 6 行输出*(Anush+Benita+cathy 1+cathy 2+Grace+Helen 或 Anush+Benita+frances 1+frances 2+Grace+Helen，取决于"[匹配标准](#who-overlaps-and-how)"我们选择)*。

没有任何 SQL 命令专用于这种连接类型，但是仔细看看代表它的维恩图。你能看到在`A`、`B`或两个连接中，原本是一个[的“中间部分”吗？你的任务是编写代码，去掉“中间的一点”](#join-type--in-a-b-or-both)

*(旁注:**很难将维恩图制作成一块糖饼干，在你的数据库 101 课程期末考试时不会碎。在 SQL 中制作要容易得多！)***

### 选择自己的冒险

通过根据以下架构设计一个查询，将重叠的圆“切掉中间”:

1.  使用您最喜欢的方法在`A`、`B`或两者中构建一个查询，并将其视为您的**新** `A`。
2.  使用您最喜欢的方法在`A`和`B`查询中构建一个[，并将其视为您的**新** `B`。](#join-type--in-both-a-and-b)
3.  用你最喜欢的`A`中的[，而不是`B`](#join-type--in-a-but-not-in-b)方法，将你的新`A`和新`B`缝合在一起。

### `FULL OUTER JOIN ... A.... IS NULL OR B.... IS NULL`

使用这种方法，对`A`的所有列和`FULL OUTER JOIN`操作中涉及的`B`的所有列使用`IS NULL`过滤器是**至关重要的**。

否则，如果您的数据集中有大量的`NULL`值，您可能会得到意想不到的结果。

当您这样做时，“`AND`”一起过滤来自同一个表的列，而“`OR`”一起过滤来自不同表的列，如下面的示例代码所示。

这种方法支持“进一步的过滤器”——只是将它们作为负责“匹配”操作的`where`子句之外的附加`and`和`or`子句。

确保将负责“从中间取出一点”的整个`... or ...`过滤器放在它们自己的括号中，以避免意外的行为。

如果其余的过滤器中有`or`,也要用圆括号将它们括起来，以确保您不会意外地使您的“从中间咬出”过滤器成为可选的。

#### SQL 查询

```
select A.name_lf, A.company, B.name_fl, B.fav_food
from CONVENTION A
full outer join RESTAURANT B on A.ph = B.phone and A.em = B.email
where 
    (
    A.ph is null
    and A.em is null
    )
or
    (
    B.phone is null
    and B.email is null
    ); 
```

#### 查询输出

| 名字 _lf | 公司 | 名字 _fl | 最喜欢的食物 |
| --- | --- | --- | --- |
| 阿努什·阿姆吉特 | 苹果 |  |  |
| 贝尼塔·博尔赫斯 | 他们喝酒了 |  |  |
| 凯西·库姆斯 | CVS |  |  |
|  |  | 凯西·库姆斯 | 胡萝卜 |
|  |  | Grace Gao | 大蒜 |
|  |  | 海伦·霍普 | 鹰嘴豆泥 |

你成功了！你能感觉到你有多聪明吗？

* * *

## 更多资源

如果你喜欢这篇文章，你可能会感兴趣的其他链接是:

*   [SQL 教程](https://community.modeanalytics.com/sql/)按模式分析
*   海伦·安德森著
*   如何实现从 Excel 到 SQL 的跨越
*   [通过 Periscope 数据仅连接 SQL](https://www.periscopedata.com/blog/4-ways-to-join-only-the-first-row-in-sql) 中第一行的 4 种方法
*   Alex Petralia 的 SQL 中更危险的连接子条目
*   SQL、Python 和 R 的连接
*   [Python 和 SQL 重叠](https://blog.modeanalytics.com/bridge-the-gap-python-sql-overlap/)大卫·华莱士