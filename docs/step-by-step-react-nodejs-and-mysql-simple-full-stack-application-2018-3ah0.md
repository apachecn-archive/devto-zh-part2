# 步步 React，NodejS 和 MySQL 简单全栈应用 2018(第一部分)

> 原文：<https://dev.to/kmaryam27/step-by-step-react-nodejs-and-mysql-simple-full-stack-application-2018-3ah0>

#### 在这篇文章中，我描述了数据库及其类型，并安装了 MySQL 数据库进行后端开发。

#### *什么是数据库？

数据库是具有规则结构的信息集合，易于访问、管理和更新。

#### *什么是数据库元素？

表是数据库的元素。数据库表由保存数据的记录和字段组成。表格也称为数据表。数据库中的每个表保存不同但相关的主题的数据。

![](../Images/af9b182cb7bfa0d271256a1870f320df.png)

#### *有哪些不同类型的数据库？

1- Relational databases(RDBMS) or SQL data structure: * databases like MySQL, Oracle, PostgreSQL use structured query language (SQL) for defining and manipulating data. * It is a safe choice and especially great for complex queries, but it can be restrictive. * It uses predefined schemas to determine the structure of your data before you work with it and all of your data must follow the same structure.
2- non-relational #NoSQL data structure: * A non-relational database like MongoDB, BigTable, Redis, RavenDB Cassandra, HBase, Neo4j and CouchDB have dynamic schema for unstructured data, and data is stored in many ways: it can be column-oriented, document-oriented, graph-based or organized as a KeyValue store

#### 如何开始使用 MySQL 数据库？

*   安装:

    *   打开[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)链接，下载 mysql: ![](../Images/29de49316c894cbfca4cb760b4ac8634.png)
*   下载并开始安装后，在选择安装类型时使用自定义项，因为我们只需要安装 MySQL 作为数据库:

    [![](../Images/2acc89f2f4a815c1f847f61fb4741d30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H9xtEEvh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yykkrwax1ztr5btite9c.png)

    [![](../Images/763219c7395695e3b3c89152e6573f2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lGyVr487--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w0n7o18vx1a8359sfz2g.png)

*   确定你的配置应该像下面的信息:

    [![](../Images/9c61be45dad0324c9cd615acd28ba5a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---aDU4J3g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cr99c6ah2bunz8d8gvo8.png)

*   对于身份验证方法，插入您选择的密码并按下添加用户按钮:

*   打开下面链接下载安装 MySQL workbench 拥有可视化 MySQL 数据库:【https://dev.mysql.com/downloads/workbench/】

    [![](../Images/b774c7d9d3729a8ac82d5a14383b02c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o3aQHtAp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/phhtws61kec4bf7idzkg.png)

*   安装完成后，从开始菜单的 MySQL 文件夹中打开 MySQL 8.0 命令行客户端，输入您的 MySQL 密码，然后按回车键

    [![](../Images/1ec38c58c6484eeaf1df768b6fac1602.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_5tz6ZMU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2vwosjrelf5bocxkm0a7.png)

*   运行 MySQL Workbench 在灰色方块中按下本地实例 MySQL，插入数据库密码:

    [![](../Images/43f9e00fe718d5d48122b8daf69ab3bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7YFNaq27--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rv2vwgr3kp808ptz09a3.png) 

    现在 MySQL 正在运行，我们准备制作数据库和表格。

2018 年 10 月 31 日
资源:

*   [http://www . data versity . net/review-pros-consenses-different-databases-relational-vs-non-relational/](http://www.dataversity.net/review-pros-cons-different-databases-relational-versus-non-relational/)
*   [https://drive . Google . com/drive/folders/18 gas 0 zvtkclhd _ 83mQ _ Q1 TFF xerxa 20k？usp =共享](https://drive.google.com/drive/folders/18gas0zVtkClhD_83mQ_Q1TFFxERxa20k?usp=sharing)
*   [https://www . cengage . com/school/corp view/regular features/database tutorial/db _ elements/db _ elements 2 . htm](https://www.cengage.com/school/corpview/RegularFeatures/DatabaseTutorial/db_elements/db_elements2.htm)
*   [https://dev.mysql.com](https://dev.mysql.com)