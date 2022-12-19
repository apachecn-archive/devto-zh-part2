# 数据库开发人员的 BeckDesignRules:揭示意图——命名

> 原文：<https://dev.to/pesse/beckdesignrules-for-database-developers-reveals-intention--naming-4ce9>

在本系列的[上一篇文章](https://dev.to/pesse/beckdesignrules-for-database-developers-part-1-passes-the-tests-2och)中，我写了了解你的问题领域、理解需求和定义要通过的测试的重要性。

我很感激我得到的所有积极的反馈，但是一些读者觉得它太长了，很难吸收。所以我打算放弃我最初的想法，为每个设计规则写一篇文章。

通过将我的想法和对 Beck 设计规则的解释分割成更小的块和子主题，我也有可能更深入地探索这些规则，这是我所期待的。

今天，我将深入探讨马丁·福勒所表述的肯特·贝克关于简单软件设计的第二条规则:“揭示意图”。

这个短语简短，切中要点，容易记忆。然而，值得看看参与制定这些规则的人在讨论最初的 C2 维基页面上的想法时写了些什么:

> “通过测试后，下一个最重要的事情是——教育。注意]代码尽可能容易理解，因此我们需要确保它表达了我们需要清楚表达的每一个想法”
> 
> *—叶锦添*

重要的是要记住，我们不是为机器写代码。如果我们这样做了，我们就可以(也应该)写出二进制代码。因为我们编写代码是为了不被他人使用和维护，所以我们不这样做，因为我们人类天生不能理解 0 和 1 的巨大块的含义。

我们编写的任何代码都有两个目的:

1.  让机器按照我们想要的方式运转
2.  告诉读者我们预期的行为是什么

虽然第一个目的似乎显而易见，但我们经常忘记第二个目的，尽管如果我们的目标是低维护成本和长期成功，第二个目的同样重要。

那么，我们如何才能使我们的代码可读和可理解呢？我们有哪些可能性，尤其是在数据库开发方面？

## 富有表现力的命名

> "计算机科学中只有两个难题:缓存失效和事物命名."
> 
> *—菲尔·卡尔顿*

命名是可读源代码的一个关键元素，这就是为什么它被所有经验层次的许多开发人员视为一项艰巨任务的原因。表、视图、变量和方法的名称是人们与你的代码进行交互的主要点，可以帮助或阻碍对代码的理解。

理想情况下，您的代码应该能够讲述自己的故事，基本上没有注释。

如果你阅读你的代码，它会流动吗？有意义吗？如果不是，是名称的问题还是结构的问题？你是否发现自己被事物的命名方式所困扰？

### 做的事和被做的事

Steven Feuerstein 提出了一个我想学习的强有力的想法:为了能够讲述我们的故事，我们必须区分真实存在的事物。

事情是:

*   桌子
*   视图
*   列
*   变量
*   类型
*   记录
*   目标

做的事情:

*   程序
*   功能
*   扳机
*   乔布斯

甚至还有第三类，我称之为有组织的事物:

*   程序包(在 Oracle 中)
*   模式(在 SQL Server 中，不要与 Oracle 中的模式混淆)
*   类(在许多面向对象的语言中)
*   名称空间(也在许多其他语言中)

这些可以用来给出更多的上下文，并揭示一些关于其内容的一般情况。

## 就事论事，让代码说话

让我们来看一些命名如何极大地提高可读性的例子:

#### 次优方法

```
create table soldier_grp (
  id int not null primary key,
  name varchar2(256)
);

[...]

create table planet_x_sold_grp (
  sold_grp_id integer primary key,
  planet_id integer not null,
  ...
); 
```

#### 可读的方法

```
create table soldier_groups (
  id int not null primary key,
  name varchar2(256)
);

[...]

create table planet_garrison (
  soldier_group_id integer primary key,
  planet_id integer not null,
  ...
); 
```

我们很少使用快捷方式。快捷方式需要大量的上下文——你用得越多，你需要未来的读者和代码维护者了解的知识就越多(可能非常具体)。

我们还使用了与业务领域相关的术语(“planet_garrison”而不是技术术语“planet_x_sold_grp”)，并提供了许多上下文和含义。

#### 次优方法

```
create or replace package planet_util as
  procedure update_grp_planet(
    a simple_integer,
    b simple_integer );

  function get_planet(
    a simple_integer )
    return planets.id%type;
end; 
```

#### 可读的方法

```
create or replace package soldier_group_action as
  procedure move_to(
    i_group_id simple_integer,
    i_planet_id simple_integer );

  function current_planet_id(
    i_group_id simple_integer )
    return planets.id%type;
end; 
```

有几件事可以提高第二种方法的可读性:

1.  这个包给出了有意义的上下文。在第一个例子中，它甚至是误导性的，因为它集中在行星上而不是士兵群体上。
2.  在第二种方法中，我们更关注**什么**(“移动 _ 到”)而不是**如何**(“更新 _ 组 _ 行星”)。这揭示了意图而不是技术，这对于读者和方法的潜在用户来说都不太有趣。
3.  我们从名字中删除了尽可能多的“不必要”信息，并利用了语言语法的细节:返回当前星球的函数不是[“get _ planet”](https://twitter.com/sfonplsql/status/1034879083073556480)–PLSQL 总是期望一个函数返回值的赋值目标，所以我们已经从语法中知道它是一个函数。

通过查看调用我们的方法的代码，我们还可以注意到，通过这些更改，我们的公共 API 变得更加可读:

#### 次优方法

```
begin
    planet_util.update_grp_planet( 1,  1 );

    dbms_output.put_line( 'Current planet: ' ||
      to_char(planet_util.get_planet( 1 )) );
end; 
```

#### 可读的方法

```
begin
    soldier_group_action.move_to(
        i_group_id => 1,
        i_planet_id => 1 );

    dbms_output.put_line( 'Current planet: ' ||
      to_char(soldier_group_action.current_planet_id( 1 )) );
end; 
```

如果我们正确地命名参数，我们还可以看到参数符号的使用如何提供额外的信息。

让我们再看一个例子:

#### 次优方法

```
create or replace view soldier_groups_1 as
    select
        ... --inner-join on planets
;
create or replace view soldier_groups_2 as
    select
        ... --outer-join on planets 
```

#### 可读的方法

```
create or replace view soldier_groups_stationed as
    select
        ... --inner-join on planets
;
create or replace view soldier_groups_all as
    select
        ... --outer-join on planets 
```

通过拒绝使用数字作为区别，我们在名称中添加了更多的 **what** ，让读者非常清楚两种视图之间的区别。

我在 Oracle 的 LiveSQL 上提供了这两种方法的完整示例:

[次优](https://livesql.oracle.com/apex/livesql/s/g7s06h9202em7ftr5xugnkz30)与[可读](https://livesql.oracle.com/apex/livesql/s/g7s06h93hbi0fi1ootlbuj3ww)

## 一般建议

你认为第二种方法可读性更强吗？你能更容易地阅读和理解它吗？

如果是这样，太好了，继续读下去！如果不是，我很乐意与你交谈，不要犹豫使用评论功能或通过[推特](https://twitter.com/Der_Pesse)或[电子邮件](mailto:derpesse@gmail.com)联系我！

最后，我提供一些一般性的建议来帮助你擅长命名:

*   无论你做什么，都要保持一致。如果您在参数前加上“I ”,那么在任何地方都要这样做。如果您将您的表称为“行星”，请在整个数据库应用程序中使用该术语，而不要开始将它们称为“天体”。
*   注意不要用同一个术语来描述不同的事物:绝地/西斯环境中的“原力”和赛车模拟中的“原力”是完全不同的
*   Trivadis AG 为 PL/SQL 和 SQL 发布了一组很棒的[命名约定](https://trivadis.github.io/plsql-and-sql-coding-guidelines/2-naming-conventions/naming-conventions/)
*   关于命名的话题，凯夫林·亨尼[对](https://www.youtube.com/watch?v=CzJ94TMPcD8) [有很多话要说](https://vimeo.com/97329157)
*   看别人的代码。应用与你阅读自己的规则相同的规则。什么有用，什么没用？

## 留心命名

我们生活的世界是有偏见的。我想这是我们人性不可分割的一部分——软件开发也是如此。乍看之下，软件似乎是非性别、非种族、非偏见的，但软件是由人类编写的，因此反映了他们的斗争、愿望和偏见。

当开发人员通读您的代码时，您不希望他们感到不舒服或者甚至受到侮辱。

例如，我避免在我的软件中使用“主/从”术语。这些术语太多了，没有用。相反，我选择“父/子”或“主/副本”。

当然，即使意识到你写的东西可能会冒犯到别人，这也是很棘手的。但你至少可以遵循这两条规则:

*   如果你听到你脑海中有一个小小的声音在表达你的担忧，倾听那个声音，改变你正在做的事情。
*   如果你想提高自己意识到什么可能会冒犯别人的能力，就去倾听那些受到影响的人。

有一个由 [@mzbat](https://twitter.com/mzbat) 发起的 [Twitter 帖子](https://twitter.com/mzbat/status/995368613765935104)，她被要求更改她幻灯片中关于“黑帽黑客”和“白帽黑客”的幻灯片。这是一个很棒的线程，有许多深思熟虑的答案，但给我留下深刻印象的是她的结论(POC =有色人种):

> 我不知道答案是什么，但我知道我没有资格告诉一个 POC 什么是或不是种族不敏感。如果观众中有一个有色人种的孩子被他们不理解的牛仔比喻所伤害，那就太多了。和平与爱你们。[![❤](img/a38aacb9bf3a6d875b78a506f0bf990e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qo6T5Tti--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/2764.png)
> 
> —bat(@ mzbat)[2018 年 5 月 12 日](https://twitter.com/mzbat/status/995443494805270529?ref_src=twsrc%5Etfw)

如果我们都能表现出那种同理心和同情心，那么我们的世界一定会变得更加美好。