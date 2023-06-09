# 针对有经验者的 6 个 MSSQL 面试问题

> 原文：<https://dev.to/sharadtricks/6-mssql-interview-questions-for-experienced-bdm>

## 1。如何在 Mssql 中收缩一个数据库？

在 MSSQl 中，可以使用 SQL Server Management Studio 或 Transact-SQL 收缩数据库。我们已经在下面描述了这两种方法

### 使用 SQL Server Management Studio

在对象资源管理器中，连接到 SQL Server 数据库引擎的一个实例，然后展开该实例。

展开数据库，然后右键单击要收缩的数据库。

指向任务，指向收缩，然后单击数据库。

数据库
显示所选数据库的名称。

当前分配的空间
显示所选数据库已用和未用的总空间。

可用空闲空间
显示所选数据库的日志和数据文件中空闲空间的总和。

在释放未使用的空间之前重组文件
选择此选项相当于执行 DBCC 收缩数据库并指定目标百分比选项。清除此选项等效于使用 TRUNCATEONLY 选项执行 DBCC SHRINKDATABASE。默认情况下，当对话框打开时，不选择此选项。如果选择了此选项，用户必须指定目标百分比选项。

收缩后文件中的最大可用空间
输入数据库收缩后数据库文件中剩余的最大可用空间百分比。允许值介于 0 和 99 之间。

单击确定。

### 使用 Transact-SQL

连接到数据库引擎。

在标准栏中，单击新建查询。

将以下示例复制并粘贴到查询窗口中，然后单击执行。该查询使用 DBCC 收缩数据库来减小 UserDB 数据库中数据和日志文件的大小，并在数据库中留出 10%的可用空间。

DBCC SHRINKDATABASE (UserDB，10)；
GO
来源:[https://docs . Microsoft . com/en-us/SQL/relational-databases/databases/shrink-a-database？view=sql-server-2017](https://docs.microsoft.com/en-us/sql/relational-databases/databases/shrink-a-database?view=sql-server-2017)

## 2。如何检查 mssql 的数据库版本？

识别 SQL Server 版本的步骤

登录到承载 SQL Server 数据库的 Windows Server 系统。
打开命令提示符窗口。
执行以下命令:
SQLCMD-S servername \ instancename
其中 servername 是您的服务器名，instance name 是 SQL 实例名。
回车选择@ @版本并按下。
在 2 >提示符下，键入 go 并按键。
服务器上运行的 SQL 版本显示在对话框中。
对每个附加实例重复步骤 3 到步骤 5。
来源:[https://support . solarwinds . com/Success _ Center/Orion _ Platform/knowledge base _ Articles/How _ to _ identify _ your _ SQL _ Server _ version _ and _ edition](https://support.solarwinds.com/Success_Center/Orion_Platform/Knowledgebase_Articles/How_to_identify_your_SQL_Server_version_and_edition)

## 3。如何在 mssql 中转义单引号和通配符？

要在 MS SQL 中转义单引号( ' )，只需添加两个单引号而不是一个。

示例:

插入到 name_column ([name])值(' R"Rams ')

要转义通配符，您可以使用默认的方括号“[]”，如- [%]或[_]，或者通过在 where 子句的末尾使用关键字 escape 来定义您自己的转义字符。

## 4。解释 MSSQL 中的魔表？

SQL Server 允许您定义一个神奇的表。魔表是隐形表或者虚拟表。您只能通过 SQL Server 中的帮助触发器看到它们。幻表是那些允许您在 SQL Server 中对表执行插入、删除和更新 DML 操作时保存插入、删除和更新的值的表。

这是两张神奇的桌子:

插入的
删除的
通常魔表是不可见的表，我们只能在 SQL Server 中借助触发器才能看到它们。

来源:[https://www . c-sharp corner . com/UploadFile/rohatash/magic-tables-in-SQL-server-2012/](https://www.c-sharpcorner.com/UploadFile/rohatash/magic-tables-in-sql-server-2012/)

## 5。列出 SQL Server 中的逻辑运算符？

逻辑运算符主要用于测试条件的真实性。

ALL–如果所有操作集都为真，则返回真。
AND–如果布尔表达式为真，则返回真。
IN–如果操作数等于表达式列表之一，则返回 true。
ANY–如果一组比较中的任何一个为真，则返回 true。
BETWEEN–如果操作数在一个范围内，则返回 true。
存在–如果子查询包含任何行，则返回 true。
LIKE–如果操作数匹配某个模式，则返回 true
否则，将反转任何布尔运算符的值。
OR–如果布尔表达式之一为真，则返回真。
SOME–如果一组比较中的某些为真，则返回 true。
来源:[https://www . onlineinterviewquestions . com/MSSQL-interview-questions/](https://www.onlineinterviewquestions.com/mssql-interview-questions/)

## 6。mssql 使用的默认端口是什么？

SQL server 的默认端口是 1433。