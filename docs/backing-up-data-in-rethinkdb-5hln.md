# 在 RethinkDB 中备份数据

> 原文：<https://dev.to/ulisesac4/backing-up-data-in-rethinkdb-5hln>

## 简介

数据库管理员的一项经常性任务是创建和应用备份。在本教程中，您将学习如何在 RethinkDB 环境中完成这项任务。

## Requeriments

*   正在运行的 RethinkdB 安装。
*   用`pip`把 python 包`rethinkdb`安装到你的机器上。`import`工具被捆绑在那个包中。建议使用 Python 3 版本包，因为 Python 2 很快就要寿终正寝了。

## 设置您的数据

在创建备份之前，设置 RethinkDB 服务器很重要，只需快速完成以下步骤:

1.  打开 Web 面板，它位于`localhost:8080`
2.  转到`Tables`部分，添加一个名为`FinancialXchanges`的数据库。
3.  在数据库`FinancialXchanges`中创建一个名为`Dollar`的表

现在转到`Data Explorer`部分，运行下面的查询:

```
r.db('FinancialXchanges').table('Dollar').insert( [
{
"week": 26, "id": 1, "cad": 1.28837, "jpy": 109.89
},
{
"week": 27, "id": 2, "cad": 1.30979, "jpy": 109.89
},
{
"week": 28, "id": 3, "cad": 1.25588, "jpy": 106.751
},
{
"week": 29, "id": 4, "cad": 1.2483, "jpy": 104.709
},
{
"week": 30, "id": 5, "cad": 1.27785, "jpy": 108.165
},
{
"week": 31, "id": 6, "cad": 1.27459, "jpy": 107.137
},
{
"week": 32, "id": 7, "cad": 1.26657, "jpy": 108.987
}
] ) 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经有了学习本教程所需的数据

## 做备份

创建备份的命令是`dump`，默认情况下，它对所有服务器进行备份。要限制其范围，请使用`-e`选项，您可以使用`-f`选项选择备份的名称:

```
rethinkdb dump -e FinancialXchanges.Dollar -f backup1.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

这样的文件将存储在你的终端会话的实际文件夹中，你可以`ls`查看你的备份:

```
YOUR_USER@YOUR_DOMAIN:~# ls
backup1.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

在此之后，转到您的 Web 面板并删除`FinancialXchanges`数据库，因为您将在下一部分进行恢复。

## 恢复备份

恢复备份的工具是`restore`。Restore 是一个强大的工具，它让我们可以通过安全隧道应用备份，甚至指向特定的数据库和表。在您的例子中，您只备份了一个简单的数据库，所以您可以运行 clean 命令，而不需要任何额外的选项:

```
rethinkdb restore backup1.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

RethinkDB 将告诉您导入到服务器的数据量。

为了解决问题，在 Web 面板的`Data Explorer`部分运行这个查询，列出您恢复的表和数据库的内容:

```
r.db('FinancialXchanges').table('Dollar') 
```

Enter fullscreen mode Exit fullscreen mode

您应该能够看到之前步骤中创建的原始数据。

## 结论

如您所见，在 RethinkDb 中管理备份相当容易。在本教程之后，建议使用`dump`和`retore`命令的不同选项，这样你就可以理解这些命令可以提供给你的不同选项。