# 修复关系数据库错误:错误的键

> 原文：<https://dev.to/kspeakman/fixing-relational-db-mistakes-wrong-keys-3p4p>

我们有许多通过数据库集成的遗留应用程序。如果说一个[集成数据库反模式](https://martinfowler.com/bliki/IntegrationDatabase.html)还不够糟糕，那么缺乏经验会导致设计没有很好地建模。以下是我们修复的几个问题以及我们用来修复的过程。

## 我们的场景

第一个应用程序是作为会计系统的配套程序编写的。所以它直接使用会计数据库中的数据。随着它的发展，它有了自己的数据库，但仍然依赖于会计程序的数据。

今天，我们的情况是，新课程必须首先输入我们的会计系统，然后同步到我们的系统数据库。我们想改变这种依赖关系(在我们的系统中输入课程，然后将它同步到会计，或者更好的做法是将它作为生成的发票上的一个行项目)，但是会计数据库 ID 是我们使用的主键。每个引用课程的表也有自己的会计 ID。只有在添加新课程时，才能在会计系统内部生成会计 ID。所以不容易修复。我们将不得不切换到使用我们自己控制的 ID 列。

这种技术也适用于另一种情况:我们使用了错误的列作为表上的键。例如，有一个表，我们总是使用 5 列来连接它。在子表中，这些列仅用于匹配父表。可以很容易地用代理键来替换它，并且可以在子表中删除这些列。在另一种情况下，我们使用一个名称作为键，这个名称可能会改变。我们希望使用一个用户不可更改的 ID。(如果我们需要一个用户可更改的 ID，它将添加到我们的 ID 中。)

## 流程

该过程尽最大努力确保在每一步之后都可以部署变更，而不会出现中断。一次处理一个子表(或一系列表)是个好主意。此外，我建议使用一个测试环境(例如，一个开发人员的机器),其中有一份生产数据的副本和一份指向测试数据库的应用程序的副本。

我们实际上是在第 7 步，所以如果我们在第 7 步和第 8 步之间学到了任何重要的东西，我会更新这个指南。

> 🛡️和往常一样，在应用更改之前备份您的生产数据库。在您成功证明“备份”可以恢复之前，它不能算作备份过程。

愚蠢的法律东西，因为目光短浅，滥用的人存在:*这是按原样提供。没有保修。我对阅读本指南后(或同时)可能发生的数据丢失或量子奇异性(或任何其他将本指南放入你的大脑的方法)概不负责。*

### 第一步:创建新的 ID 列

#### 父表

假设您还没有可以用作新 ID 的列。您必须有一个用于标识每个实体的列。在我们的示例中，我们在课程表上创建了一个名为 CourseId 的 UUID 列。我们将其设置为`NOT NULL`，并使用一个`DEFAULT`约束，如果不存在约束，则生成一个新的 UUID。对于 MSSQL，该语句如下所示。

```
ALTER
TABLE Course
  ADD CourseId uniqueidentifier
      NOT NULL
      DEFAULT newid()
      CONSTRAINT UK_Course_CourseId UNIQUE
; 
```

Enter fullscreen mode Exit fullscreen mode

在我们完成转换之前，会计 ID 将一直作为主键。但是我们仍然希望保证 CourseId 在那之前是唯一的。

#### 子表

子表的情况略有不同。在我们添加列的时候，我们不知道应该在它们里面放什么 CourseId。所以我们现在必须允许空值。我们现在将跳过创建索引。这只是为 CourseId 提供了一个存储位置。MSSQL 语句如下所示。

```
ALTER
TABLE Registration
  ADD CourseId uniqueidentifier
; 
```

Enter fullscreen mode Exit fullscreen mode

> ⚠️💣如果您有任何通过索引访问列的应用程序，您可能希望仔细检查这不会破坏它们。如果使用 ORM，您可能还需要更新其配置以包含新列。

### 第二步:更改应用程序以填充新列

因此，在步骤 1 之后，我们用新的 id 填充了父表(Course)。但是 CourseId 在子表(注册)的每一行都是 NULL。我们可以在现有的注册记录上回填 CourseId。但是新记录仍然会被写入空值，因为应用程序还不知道这一点。因此，我们需要确保对子表的任何写入都填充到新的 CourseId 列中。

通常情况下，应用程序数据的读取频率要比写入频率高得多。在我们的例子中，我们只有少数几个地方创建注册，而有太多的地方阅读注册。所以这种变化的范围相对较小。

我们必须浏览应用程序并修改插入代码，还要设置 CourseId。在某些情况下，这些数据不是由客户提供的。所以我们还必须改变客户端来传递数据。这些变化通常很容易测试，风险也不大。但是您在应用程序中要做的事情可能会有所不同。

因此，在这一步结束时，数据实际上没有被用于任何用途。但我们只是确保它被任何写入子表的应用程序填充。

### 第 3 步:验证所有写入程序都在填充新列

可能是我们错过了一些写入子表的应用程序。例如，也许它是按照计划自动运行的，所以我们忘记了它。或者可能是与另一个系统的集成，我们已经 6 个月没有接触过了，所以忘记了。

所以我的建议是等待一段时间。监视子表，查看最近写入的记录中是否有空 id 列。如果是这样，找到向其写入数据的应用程序，然后重复步骤 2。

一旦您确信所有的作者都在填充新列，就继续执行步骤 4。

### 第四步:回填空值

此时，我们已经在将来填充了新列，但是许多过去的条目仍然为空。所以我们写了一个`UPDATE`语句来填充它们。这些记录当前由不正确的键(此处为 CourseAccountingId)链接，因此我们可以使用它来填充新的键。

```
UPDATE Registration
   SET CourseId = c.CourseId
  FROM Registration r
  JOIN Course c
    ON c.CourseAccountingId = r.CourseAccountingId
 WHERE r.CourseId IS NULL
; 
```

Enter fullscreen mode Exit fullscreen mode

### 第五步:在子表中的新列上添加约束

> ⚠️💣如果您错过了第 2 步中的任何应用程序，它们将在这一步后开始失败。因此，您可能希望执行最后一次检查，确保新列中没有空记录。

现在，我们有了将来要填充的列，所有的旧数据都被填充了。但是我们需要在以后实现新特性后保证数据的完整性。所以我们需要添加任何有意义的约束和索引。

在我们的例子中，我们首先使列成为必需的。

```
ALTER TABLE Registration
ALTER COLUMN CourseId uniqueidentifier NOT NULL
; 
```

Enter fullscreen mode Exit fullscreen mode

#### 一对多子表

我们需要添加一个索引，许多查询都需要这个索引，尤其是 JOIN 语句。

```
CREATE INDEX IX_Registration_CourseId ON Registration (CourseId); 
```

Enter fullscreen mode Exit fullscreen mode

#### 一对一子表

在 1 对-(0/1)子表的情况下，您希望使新列成为主键，而不是添加普通索引。以下是删除以前的主键的查询。

```
ALTER TABLE ChildTable
DROP CONSTRAINT PK_ChildTable
; 
```

Enter fullscreen mode Exit fullscreen mode

您可能希望在旧列上添加一个唯一的索引，以确保仍在使用该值进行查询的应用程序具有良好的性能。

下面是一个将新列更改为主键的语句。

```
ALTER TABLE ChildTable   
ADD CONSTRAINT PK_ChildTable PRIMARY KEY (ParentId)
; 
```

Enter fullscreen mode Exit fullscreen mode

如果您的子表有任何重复记录，添加该主键将失败。

#### 固定一对一的子表数据

如果您的子表应该具有 1 对-(0/1)的关系，但是您没有在旧的 id 上强制使用唯一/主键，那么很有可能在应该只有一个记录的地方有重复的记录。所以直到执行(危险！)修复。如何修复重复取决于你自己。例如，您可以删除所有内容，只保留一个，或者合并它们。我不能帮你，但是我可以提供一个查询来帮助在一个应该只有一个的表中找到重复项。

```
SELECT c.*
  FROM ChildTable c
  JOIN (

-- Get IDs of duplicates in child table
SELECT ParentId
  FROM ChildTable
 GROUP BY ParentId
HAVING COUNT(*) > 1

       ) d
    ON d.ParentId = c.ParentId
 ORDER BY c.ParentId
; 
```

Enter fullscreen mode Exit fullscreen mode

### 第六步:添加外键

现在我们已经保证了新列正在被填充(否则插入将会失败)。然而，仍然可能发生删除(父)课程的情况，这将在(子)注册表中留下孤立记录。所以我们需要创建一个外键来确保注册表不能包含孤儿。下面是一个在 MSSQL 中添加外键的示例语句。

```
ALTER TABLE Registration
ADD CONSTRAINT FK_Registration_Course
       FOREIGN KEY (CourseId)
    REFERENCES Course (CourseId)
; 
```

Enter fullscreen mode Exit fullscreen mode

如果子表有任何孤立记录，创建该外键将会失败。

#### 固定数据

如果您以前在旧的 id 上没有外键，那么很有可能在子表中有一些孤儿。因此，在实际添加外键之前，必须修复孤儿(删除或链接到新的父对象)。具体的流程会根据您的情况而有所不同。但是我将向您展示一个有助于在子表中查找孤儿的查询。这是 MSSQL 版本。

```
SELECT r.*
  FROM Registration r
  LEFT
  JOIN Course c
    ON c.CourseId = r.CourseId
 WHERE c.CourseId IS NULL
; 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，这个查询没有什么意义。但是现在我们从登记表开始。我们使用的是左连接，这样可以确保注册表中的每条记录都保留在结果中，即使 Course 表中没有匹配项。在没有课程匹配的情况下，课程表中的所有列都将为空。于是 WHERE 子句开始工作，只显示 CourseId 为空的记录(不匹配)。

### 步骤 7:修复应用程序以读取新列

对我们来说，这是大部分的过程。我们有许多应用程序和许多不同的数据查询。我们必须仔细检查并更改它们，以便从新列中读取。我们还需要确保不再引用任何旧的列。如果适用，记得更新 ORM 配置以删除旧列。

测试。测试。测试。

认识到有些应用程序(有时称为第 1 层)如果停机，将会对您的业务产生重大影响。花一些额外的时间来彻底检查这些。但也要明白，有些应用程序对您的业务影响有限(例如，电子邮件通知会在失败后发送)。请允许我说句实在话，对于非关键服务来说，部署它们并确切了解它们是如何失败的可能会更快。

更新所有受影响的应用程序后，验证没有任何应用程序意外访问旧列。一种方法是:在您的测试环境中，删除旧的列(即使您想在生产中保留它)并运行程序。除非您是故意使用旧列(在我们的例子中，我们现在只使用它来同步会计软件)，否则所有程序都应该在没有它的情况下正常运行。你可以忽略那些有意依赖该列的应用程序的失败。

### 第八步:删除或更改旧列

我们终于到了我们想去的地方。在我们的例子中，我们希望将旧列更改为不再是主键，并且可以为空。然后使我们的新列成为主键。这将使我们能够完全控制课程数据，并且只在我们将课程更改推入会计软件时填充会计 ID。

> ⚠️💣如果有任何应用程序没有被更新以使用新列，它们将在这一步之后开始失败。

#### 清理

删除子表中旧列的索引:

```
DROP INDEX IX_Registration_CourseAccountingId ON Registration; 
```

Enter fullscreen mode Exit fullscreen mode

从子表中删除旧 ID:

```
ALTER TABLE Registration
DROP COLUMN CourseAccountingId
; 
```

Enter fullscreen mode Exit fullscreen mode

#### 交换钥匙

从父表的旧 ID 中删除主键。

```
ALTER TABLE Course
DROP CONSTRAINT PK_Course
; 
```

Enter fullscreen mode Exit fullscreen mode

删除新 ID 上的 unique 约束(因为我们将把它改为主键)。

```
ALTER TABLE Course
DROP CONSTRAINT UK_Course_CourseId
; 
```

Enter fullscreen mode Exit fullscreen mode

在新 ID 上添加主键。

```
ALTER TABLE Course   
ADD CONSTRAINT PK_Course PRIMARY KEY (CourseId)
; 
```

Enter fullscreen mode Exit fullscreen mode

我在这里没有使用集群，因为这对于随机 uuid 来说是不好的。

使旧 ID 可为空。

```
ALTER TABLE Course
ALTER COLUMN CourseAccountingId varchar(255) NULL
; 
```

Enter fullscreen mode Exit fullscreen mode

在旧 ID 上添加一个唯一的索引，因为我们在会计集成中使用它。此 MSSQL 版本需要条件索引，但其他数据库可能会使用标准的 unique 约束。

```
CREATE UNIQUE INDEX UX_Course_CourseAccountingId
    ON Course (CourseAccountingId)
 WHERE CourseAccountingId IS NOT NULL
; 
```

Enter fullscreen mode Exit fullscreen mode

旧的会计集成继续工作，但现在我们已经在数据库中准备好了一切，可以从会计系统中获取课程所有权。

### 总结

我们一步一步地在数据库上执行大手术，试图改善整个系统的健康状况。我们一直试图以这样一种方式进行手术，使患者在每一步之后都能继续工作。这不容易，也不迅速。所以好处最好值得。在我们的例子中，必须首先在会计系统中输入数据是我们想要实现的许多用例的主要障碍。所以这个花费被认为是值得的。

我想在这里指出，如果事情经过深思熟虑，通过经验的镜头过滤，并在第一时间做出正确的决定，将会节省大量的时间。但是在系统启动的时候，经验(以及所需的预算)是不存在的。在缺乏经验的情况下，不幸的现实是学习是反复的——我们通常不知道更好，直到我们犯了错误，感受到痛苦。即使没有经验的人使用模式“因为一些聪明的人这么说”，他们真的不知道他们在做什么，并且可能非常错误地应用模式。所以我相信大多数错误都是无法避免的。事实上，由于增长，我们现在真的需要解决这个问题:“好问题。”