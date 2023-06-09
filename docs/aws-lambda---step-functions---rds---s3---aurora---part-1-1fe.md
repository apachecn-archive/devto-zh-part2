# 自动气象站 Lambda -阶跃函数- RDS - S3 -极光-第 1 部分

> 原文：<https://dev.to/diegonalvarez/aws-lambda---step-functions---rds---s3---aurora---part-1-1fe>

这是第一篇关于将步骤功能集成到特定工作流的文章。这是我的第一个 python 代码的一个公共回购，我也在学习这个。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [迪戈纳·阿尔瓦雷斯](https://github.com/diegonalvarez) / [双阶跃函数](https://github.com/diegonalvarez/bi-step-functions)

<article class="markdown-body entry-content container-lg" itemprop="text">

## 它是如何工作的

关于它如何工作的解释可以在这里找到:

*   [https://dev.to/diegonalvarez/aws-lambda-步骤-功能- rds - s3 -极光-零件-1-1fe](https://dev.to/diegonalvarez/aws-lambda---step-functions---rds---s3---aurora---part-1-1fe)

## 要求

*   圣杯 1.1.0
*   Python 2.7

## 唯一机器的 Json

```
{
  "Comment": "State machine to populate databases for reports."
  "StartAt": "MySql To S3 Files"
  "States": {
    "MySql To S3 Files": {
      "Type": "Task"
      "Resource": "ARN",
      "ResultPath": "$.guid",
      "Next": "S3 Data To Mysql",
      "Retry": [
        {
          "ErrorEquals": [
            "States.ALL"
          ],
          "IntervalSeconds": 1,
          "MaxAttempts": 3,
          "BackoffRate": 2
        }
      ]
    },
    "S3 Data To Mysql": {
      "Type": "Task",
      "Resource": "ARN",
      "InputPath": "$.guid",
      "End": true,
      "Retry": [
        {
          "ErrorEquals": [
            "States.ALL"
          ],
          "IntervalSeconds": 1,
          "MaxAttempts": 3,
          "BackoffRate": 2
        }
      ]
    }
  }
} 
```

### 并行机的 Json

```
{
  "Comment": "State machine to populate databases for reports.",
  "StartAt": "MySql To S3 Files",
  "States": {
    "MySql To S3 Files": {
      "Type": "Task",
      "Resource": "lambda-arn",
      "ResultPath": "$.guid",
      "Next": "InsertDataToMysql",
      "Retry": [
        {
          "ErrorEquals": [
            "States.ALL"
          ],
          "IntervalSeconds": 1,
          "MaxAttempts": 3,
          "BackoffRate": 2
        }
      ]
    },
```

…</article>

[View on GitHub](https://github.com/diegonalvarez/bi-step-functions)

## 目标:

这篇文章的目标是将数据从一个 MySQL 数据库转移到 S3，再从那里转移到另一个数据库。
我们正在使用的案例是从 Amazon RDS 到 Amazon Aurora，它当然可以是另一个数据库，我们的想法是将我们的新数据集与 Bussiness Inteligences 工具连接，而不影响原始数据集，并使用自定义结构。

第一个想法是使用 AWS 数据管道，但是我们需要定制某些行为。

这个想法是有三个或更多的帖子来达到目的，这就是:

1.  一个管理数据库来处理参数，在我们的例子中，我们使用:
    *   公司
    *   桌子
2.  将数据从 RDS 移动到 S3
3.  将数据从 S3 转移到奥罗拉
4.  运行功能的时间表
5.  一个自动创建信息的脚本，只设置一个客户(在我们的用例中是公司)
6.  脚本截断表，更改名称，并检测新数据是可用的。
7.  Lambdas 与大表的阶跃函数和参数并行工作。

在这篇文章中，我将讨论 1-2-3 和 4 点。

## 管理数据库

我将把我用于两个主表的结构以及我们如何使用这两个表:

### 公司

我调用了表 companies，但是您可以更改为 customer 或您需要的任何内容(有必要将代码更改为)。这个想法是，我们有多个公司，但只有少数需要商业智能，所以对于这些公司，我们设置了一个小的配置来获取我们的主数据库中的信息。

| 名字 | 描述 |
| --- | --- |
| 公司 id | **元素的唯一标识符** |
| 状态 | **状态表示是否需要报告** |
| 别名 | **用于创建 csv 和公司的桶** |

```
CREATE TABLE `companies` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `company_id` int(11) DEFAULT NULL,
  `status` int(11) DEFAULT '0',
  `alias` varchar(60) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1; 
```

Enter fullscreen mode Exit fullscreen mode

### 表格

该表旨在获取与需要迁移的信息相关的查询。

| 名字 | 描述 |
| --- | --- |
| 名字 | **表格名称，即用户** |
| 状态 | **逻辑状态如有必要获取信息** |
| 查询 _ 选择 | **获取信息的 SQL** |
| params_select | **选择查询的参数** |
| 查询 _ 插入 | **插入信息的 SQL** |
| params_insert | **插入查询的参数** |

```
CREATE TABLE `tables` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT '',
  `status` int(11) DEFAULT '0',
  `query_select` text,
  `params_select` text,
  `query_insert` text NOT NULL,
  `params_insert` text NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=latin1; 
```

Enter fullscreen mode Exit fullscreen mode

这是一个真实的例子，在未来的版本中，我想做一个更好的方法来处理参数。
相关的插入将是检测“用户”表，并使用**参数 _ 查询**值相应地获取数据**查询 _ 选择**的值，利用这些信息生成 CSV 并插入 S3。

当插入数据的函数运行时，它将执行字段 **query_insert** 中的插入，参数为 **params_insert** 。您可以在这里输入任何信息，并根据您的业务需求调整数据集的结构。

```
INSERT INTO `tables` (`id`, `name`, `status`, `query_select`, `params_select`, `query_insert`, `params_insert`)
VALUES
    (1, 'users', 1, 'SELECT id, username FROM users WHERE company = %s', 'company[1]', 'REPLACE INTO users(id, username) VALUES ', '\'(\'+row[0] +\',\"\'+ row[1]+\'\")\''); 
```

Enter fullscreen mode Exit fullscreen mode

2018 年 12 月 6 日星期四变更:

我添加了从数据库配置字段获取的特性。我们需要定制的第一个字段是日期，所以我创建了两个表:

```
CREATE TABLE `configurations` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(192) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=latin1; 
```

Enter fullscreen mode Exit fullscreen mode

这里，第一条记录的值为“日期 Y-M-D”，以标识添加到下一个表中的配置:

```
CREATE TABLE `companies_configurations` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `retail_id` int(11) DEFAULT NULL,
  `configuration_id` int(11) DEFAULT NULL,
  `configuration_value` text,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1; 
```

Enter fullscreen mode Exit fullscreen mode

一个 insert 获取查询中最近 60 天的例子是:

```
INSERT INTO `companies_configurations` (`id`, `retail_id`, `configuration_id`, `configuration_value`)
VALUES
    (1, 1, 1, '60'); 
```

Enter fullscreen mode Exit fullscreen mode

## 配置文件

一个配置文件在**中。chalice/.config.sample.json**

将文件重命名为 **config.json** 并完成参数设置。所有的参数自己解释，不要忘记添加 IAM 角色。

## Lambdas

有两个主要函数，在本回购中用作 lambdas:

1.  mysql_csv_to_s3
2.  s3_to_mysql

### mysql_csv_to_s3

这个 Lambda 从表中获取信息，执行选择查询并将数据插入 S3。

### s3_to_mysql

这里，数据是从 S3 收集的，通过海关查询进行插入。

## 步进功能【状态机定义】

在最后一步，从 AWS Step 函数创建一个新的状态机，并添加以下 json。你需要为每个新的 lambda 修改 ARN，并运行。

```
{  "Comment":  "State machine to populate databases for reports.",  "StartAt":  "MySql To S3 Files",  "States":  {  "MySql To S3 Files":  {  "Type":  "Task",  "Resource":  "ARN",  "ResultPath":  "$.guid",  "Next":  "S3 Data To Mysql",  "Retry":  [  {  "ErrorEquals":  [  "States.ALL"  ],  "IntervalSeconds":  1,  "MaxAttempts":  3,  "BackoffRate":  2  }  ]  },  "S3 Data To Mysql":  {  "Type":  "Task",  "Resource":  "ARN",  "InputPath":  "$.guid",  "End":  true,  "Retry":  [  {  "ErrorEquals":  [  "States.ALL"  ],  "IntervalSeconds":  1,  "MaxAttempts":  3,  "BackoffRate":  2  }  ]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 云观察规则

最后，您需要在 cloudwatch 中创建一个与机器状态相关的规则来生成一个时间表，这只是一个有帮助的文档

*   [https://docs . AWS . Amazon . com/Amazon cloud watch/latest/Events/Create-cloud watch-Events-Scheduled-rule . html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Scheduled-Rule.html)

希望得到一些反馈，我会在第二部分更新帖子。