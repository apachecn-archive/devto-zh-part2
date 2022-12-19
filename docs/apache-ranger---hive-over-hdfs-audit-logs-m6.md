# HDFS 审计日志上的 Apache Ranger - Hive

> 原文：<https://dev.to/risdenk/apache-ranger---hive-over-hdfs-audit-logs-m6>

### 概述

[Apache Ranger](https://ranger.apache.org/) 允许对 [Apache Hadoop](https://hadoop.apache.org/) 及相关技术进行集中授权和审计。Ranger 审计可以存储在多个位置，包括 [Apache Solr](https://lucene.apache.org/solr/) 和 [HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 。由于 HDFS 出于合规性目的存储审计日志，我们需要一种方法来查询这些日志。Apache Hive 提供了毫不费力地查询 HDFS 数据的能力。这在[之前已经写过，这里](*%20https://community.hortonworks.com/articles/60802/ranger-audit-in-hive-table-a-sample-approach-1.html)也是。下面的帖子概述了一个类似的方法，只是细节略有不同。

### HDFS 游侠审核日志格式

目前，Apache Ranger 在 HDFS 以标准的 JSON 格式存储审计日志。这里的详细描述了审计模式[。以前我写过如何压缩这些日志](https://cwiki.apache.org/confluence/display/RANGER/Ranger+Audit+Schema)[这里](///2018/02/26/apache-ranger-hdfs-audit-logging-compression.html)。利用 [RANGER-1837](https://issues.apache.org/jira/browse/RANGER-1837) ，格式可以改变以使下面的过程更容易。

### 在 HDFS 的 Ranger 审计日志上创建 Hive 表

由于 HDFS 上的数据是 JSON 格式的，有几个选项可以选择使用什么样的服务器。内置的 [Hive JSON SerDe](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-RowFormats&SerDe) 在数据格式错误时会出现问题，根据经验，Ranger 审计数据可能会出现这种情况。@rcongiu 有一个 Hive JSON SerDe-[https://github.com/rcongiu/Hive-JSON-Serde](https://github.com/rcongiu/Hive-JSON-Serde)可以轻松处理格式错误的 JSON 数据。

HDFS 上的 Ranger 审计日志格式规定文件夹结构包括`component`和`evtDate`。这种文件夹结构使得定义一个[分区的 Hive 表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterPartition)成为可能，当查看特定的日期范围或组件时，[可以提高查询性能](https://blog.cloudera.com/blog/2014/08/improving-query-performance-using-partitioning-in-apache-hive/)。

```
ADD JAR hdfs:///INSERT_PATH_TO/json-serde-VERSION-jar-with-dependencies.jar;

DROP TABLE IF EXISTS ranger_audit;
CREATE EXTERNAL TABLE ranger_audit (
  resource string,
  resType string,
  reqUser string,
  evtTime TIMESTAMP,
  policy int,
  access string,
  result int,
  reason string,
  enforcer string,
  repoType int,
  repo string,
  cliIP string,
  action string,
  agentHost string,
  logType string,
  id string
)
PARTITIONED BY (component String, evtDate String)
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe' WITH SERDEPROPERTIES ( "ignore.malformed.json" = "true"); 
```

Enter fullscreen mode Exit fullscreen mode

对于每个分区(`component`和`evtDate`)，您需要修改表来添加分区。我编写了这个脚本，以便根据需要自动添加新的分区。对一个分区这样做的例子:

```
ALTER TABLE ranger_audit ADD IF NOT EXISTS PARTITION (component='COMPONENT_NAME', evtDate='DATE') LOCATION 'DATE_FOLDER'; 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 Hive 查询 HDFS 上的 Ranger 审计日志

在创建了 Hive 表之后，可以跨 Ranger 审计日志发出 [Hive SQL 查询](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Select)。下面是一些查询示例:

```
-- Get total number of events
SELECT count(1) FROM ranger_audit;

-- Get total number of events for HDFS
SELECT count(1) FROM ranger_audit WHERE component='HDFS';

-- Get total number of events on 2018-03-23
SELECT count(1) FROM ranger_audit WHERE evtDate='2018-03-23';

-- Get total number of events for HDFS on 2018-03-23
SELECT count(1) FROM ranger_audit WHERE component='HDFS' and evtDate='2018-03-23'; 
```

Enter fullscreen mode Exit fullscreen mode

这些查询可以在多种不同的工具中运行，包括可以生成图表的 [Apache Zeppelin](https://zeppelin.apache.org/) 。此外，在创建了 Hive 表之后，可以很容易地通过 JDBC/ODBC 公开它。