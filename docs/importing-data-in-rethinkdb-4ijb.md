# 在 RethinkDB 中导入数据

> 原文：<https://dev.to/ulisesac4/importing-data-in-rethinkdb-4ijb>

## 简介

有时候，您需要在不同的数据库软件之间移动数据。幸运的是，RethinkDB 可以从 MySQL 或 PostgreSQL 等其他服务导出的 JSON 和 CSV 文件中获取数据。

## Requeriments

*   正在运行的 RethinkDB 安装。
*   用`pip`把 python 包`rethinkdb`安装到你的机器上。执行数据导入的`import`工具捆绑在那个包中。建议使用 Python 3 版本包，因为 Python 2 很快就要寿终正寝了。

## 准备数据

对于本教程，你需要两个文件，一个`json`和一个`csv`。最好放在你的`home`文件夹里。需要注意的重要一点是，您不需要已经创建了目标表，因为 RethinkDB 会处理它。

第一个文件叫做`temp_data1.json`，应该包含以下内容:

```
[
  {
    "id": 1, "day": "monday", "max": 23, "min": 12
  },
  {
    "id": 2, "day": "tuesday", "max": 22, "min": 12
  },
  {
    "id": 3, "day": "wednesday", "max": 23, "min": 12
  },
  {
    "id": 4, "day": "thursday", "max": 24, "min": 12
  },
  {
    "id": 5, "day": "friday", "max": 25, "min": 12
  },
  {
    "id": 6, "day": "saturday", "max": 26, "min": 13
  },
  {
    "id": 7, "day": "sunday", "max": 27, "min": 13
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

第二个名为`temp_data2.csv`，内容如下:

```
id, day, max, min
8, 'monday', 24, 12
9, 'tuesday', 21, 11
10, 'wednesday', 26, 14
11, 'thursday', 24, 13
12, 'friday', 25, 12
13, 'saturday', 25, 10
14, 'sunday', 22, 14 
```

Enter fullscreen mode Exit fullscreen mode

注意，建议使用逗号作为第二个文件的分隔符。还要检查您是否让 RethinkDB 实例以您更适应的方式运行。

## 导入 JSON 文件

创建文件后，您就可以导入数据了。您将使用 RethinkDB 的`import`命令，该命令带有文件的名称和将存储数据的表的名称。在这种情况下，数据将存储在`test`数据库中。

```
rethinkdb import -f temp_data1.json --table test.TempData1 
```

Enter fullscreen mode Exit fullscreen mode

然后，您将收到一条消息，显示导入到表中的行数。

## 导入 CSV 文件

导入 CSV 文件就像导入 JSON 文件一样简单，你只需要用`--format`选项指定文件格式:

```
rethinkdb import -f temp_data2.csv --table test.TempData2 --format csv 
```

Enter fullscreen mode Exit fullscreen mode

同样，您将获得导入到表中的行数。请注意，这次您将导入到不同的表中。

## 把东西包起来

成功地将数据导入数据库后，就该查询数据以便查看了。在位于`Data Explorer`部分
的`localhost:8080`的网络面板中分别运行每个命令

```
r.db('test').table('TempData1')
r.db('test').table('TempData2') 
```

Enter fullscreen mode Exit fullscreen mode

两个查询的结果有所不同，CSV 中的所有内容都以字符串形式导入，您必须转换 RethinkDB 中已经导入的数据，或者使用专门的工具将 CSV 转换为 JSON。

要完成这一部分，请记住，如果您已经在数据库中创建了目标表，只需在命令中添加`--force`选项，强制将数据写入这些表中。

## 结论

RethinkDB 为导入数据提供了一个快速而有用的工具。幸运的是，在编程语言及其各自绑定的帮助下，本教程中使用的工具可以实现自动化。有了它，我们可以轻而易举地整合来自外部服务的数据。