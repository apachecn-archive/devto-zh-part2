# 使用 Hadoop、Spark 和 R Shiny 进行大数据分析

> 原文：<https://dev.to/awwsmm/big-data-analysis-with-hadoop-spark-and-r-shiny-4il1>

*阿帕奇软件基金会*的 [*Hadoop*](https://github.com/apache/hadoop) 和 [*Spark*](https://github.com/apache/spark) 项目构成了当今可能是最受欢迎的开源大数据分析管道的核心。Hadoop 的分布式文件系统 HDFS 将文件分成块，并在商用硬件上复制这些块，从而形成一个廉价、可扩展、容错的数据存储解决方案。Spark 然后可以就地分析这些数据块，减少网络和 I/O 延迟，与 Hadoop 的内置分析框架 *MapReduce* 相比，速度提高了 100 倍。

但有时，您希望呈现一个漂亮的图形用户界面(GUI ),以便不精通技术的用户也可以访问和分析您的大数据存储。这有各种各样的应用——实时数据分析和制造过程建模；发现消费者或用户数据中的每日、每周或季节性趋势；或者利用 [`plotly`](https://plot.ly/r/) 分析科学数据制作现场互动剧情。在本文中，我将向您展示如何使用 Spark 读取和分析存储在 HDFS 上的分布式数据，并以 r。

(注意:这篇文章假设你已经在你的机器上运行了 HDFS / Spark。如果没有，看看我之前的帖子[在 Windows](https://dev.toawwsmm/installing-and-running-hadoop-and-spark-on-windows-33kc) 上安装和运行 Hadoop 和 Spark。)

# 火花外壳

使用`SparkR` shell，让 R 通过 Spark 与 HDFS 对话非常容易。从命令中，只需运行:

```
C:\Users\andrew>sparkR

R version 3.5.1 (2018-07-02) -- "Feather Spray"
Copyright (C) 2018 The R Foundation for Statistical Computing
Platform: x86_64-w64-mingw32/x64 (64-bit)

...

Spark package found in SPARK_HOME: C:\Spark\spark-2.3.2-bin-hadoop2.7
Launching java with spark-submit command C:\Spark\spark-2.3.2-bin-hadoop2.7/bin/spark-submit2.cmd   "sparkr-shell"  C:\Users\andrew\AppData\Local\Temp\RtmpuMdeVg\backend_port2664d055020
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).

 Welcome to
    ____              __
   / __/__  ___ _____/ /__
  _\ \/ _ \/ _ `/ __/  '_/
 /___/ .__/\_,_/_/ /_/\_\   version  2.3.2
    /_/

 SparkSession available as 'spark'.
> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个可以进入 HDFS 的 R shell。尝试运行一些 R 命令:

```
>  x  <-  c(1,2,3)  >  x  [1]  1  2  3 
```

Enter fullscreen mode Exit fullscreen mode

## 文本文件

如果您的 HDFS 设置正确，您应该能够使用`read.df()`方法:
立即访问纯文本文件

```
>  txt  <-  read.df("hdfs://localhost:9000/example_data/example_text.md",  "text")  >  txt  SparkDataFrame[value:string]  >  head(txt)  value  1  # Apache Spark  2  3  Spark  is  a  fast  and  general  cluster  computing  system  for  Big  Data.  It  provides  4  high-level  APIs  in  Scala,  Java,  Python,  and  R,  and  an  optimized  engine  that  5  supports  general  computation  graphs  for  data  analysis.  It  also  supports  a  6  rich  set  of  higher-level  tools  including  Spark  SQL  for  SQL  and  DataFrames,  > 
```

Enter fullscreen mode Exit fullscreen mode

请注意，该文件位于我的 HDFS 上的`/example_data/example_text.md`。这可以通过在`cmd`提示符下运行`hadoop fs -ls`来确认:

```
C:\Users\andrew>hadoop fs -ls /example_data
Found 5 items
-rw-r--r--   1 andrew supergroup       2848 2018-11-19 16:23 /example_data/example_csv.csv
-rw-r--r--   1 andrew supergroup       7882 2018-11-19 16:23 /example_data/example_sql_linux.txt
-rw-r--r--   1 andrew supergroup      13958 2018-11-19 16:23 /example_data/example_sql_windows.txt
-rw-r--r--   1 andrew supergroup       3809 2018-11-16 16:35 /example_data/example_text.md
-rw-r--r--   1 andrew supergroup      14381 2018-11-19 16:24 /example_data/example_xlsx.xlsx 
```

Enter fullscreen mode Exit fullscreen mode

主机名(`localhost`对我来说)和端口号(`9000`对我来说)是特定于您的 HDFS 设置的，但是这些是[独立安装](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)的当前默认设置。

## CSV 文件

CSV 文件也可以用`read.df()`读取，但是[你必须设置`source`等于`"csv"`](http://finzi.psych.upenn.edu/R/library/SparkR/html/read.df.html) ，而不是`"text"` :

```
>  csv  <-  read.df("hdfs://localhost:9000/example_data/example_csv.csv",  "csv")  >  csv  SparkDataFrame[_c0:string,  _c1:string,  _c2:string,  _c3:string,  _c4:string]  >  head(csv)  _c0  _c1  _c2  _c3  1  Time  dissolvedO2  pH  Temperature  2  2018-01-01  15:00:10  49.56497432219166  7.056500932431841  36.952017501071516  3  2018-01-01  15:00:40  49.04355394077128  7.056606732537641  36.94562695468097  ... 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们上面的列标题没有被正确解释。为了解决这个问题，`read.df()`还有另一个选项，叫做`header`，我们需要将它设置为`TRUE`(或者简称为`T`):

```
>  csv  <-  read.df("hdfs://localhost:9000/example_data/example_csv.csv",  "csv",  header=T)  >  csv  SparkDataFrame[Time:string,  dissolvedO2:string,  pH:string,  Temperature:string,  AgitatorSpeed:string]  >  head(csv)  Time  dissolvedO2  pH  Temperature  1  2018-01-01  15:00:10  49.56497432219166  7.056500932431841  36.952017501071516  2  2018-01-01  15:00:40  49.04355394077128  7.056606732537641  36.94562695468097  3  2018-01-01  15:01:10  49.7866879539053  7.056590932521841  36.98149607055008  ...  > 
```

Enter fullscreen mode Exit fullscreen mode

另外，请注意`csv`和`txt`都是`SparkDataFrame`对象。A `SparkDataFrame`是数据 的 [*分布式集合，`SparkR`可以在 HDFS 集群上以分布式形式*访问和分析*。这意味着数据不会被读入 R 会话，而是被完全按照在 Spark shell 中运行 Scala 或 Java 命令的方式处理。*](https://spark.apache.org/docs/latest/sparkr.html)

## SQL 表

### 将 SQL 表导入 HDFS

[在之前的一篇文章](https://dev.toawwsmm/installing-and-running-hadoop-and-spark-on-windows-33kc)中，我解释了如何使用`hadoop fs -put`命令将数据文件从本地文件系统复制到 HDFS，但是导入 SQL 表有点复杂。首先，我假设您已经安装了 MySQL，并且在某个数据库中有一个可用的表:

```
C:\Users\andrew>mysql -u root -p
Enter password: ***********
...

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| siemens            |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> use siemens;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_siemens |
+-------------------+
| simulate          |
+-------------------+
1 row in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 [Apache Sqoop](http://sqoop.apache.org/) 将`simulate`表导入到 HDFS:

1.  [下载 Sqoop 的最新版本](http://www.apache.org/dyn/closer.lua/sqoop/1.4.7)。(我下载了二进制`*.tar.gz`文件。)解压文件并将其移动到`C:\Sqoop`中。
2.  更新你的系统环境变量，添加`SQOOP_HOME`作为你刚刚解压到`C:\Sqoop`的目录(应该是类似`C:\Sqoop\sqoop-1.4.7.bin__hadoop-2.6.0`):

    [![](img/e26b525f6994c0213e84cd6bfa9d6a6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aQdUleJs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cnmqszpz5z8ubosjqh21.PNG)

    并通过追加`%SQOOP_HOME%\bin`将 Sqoop 添加到您的`%PATH%`:

    [![](img/139c2e79ca9516a324b90866a912e475.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mnD-HuqN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kz1b1angvafizzb3w8e3.PNG)

3.  通过打开一个新的`cmd`窗口并键入
    来验证安装

```
C:\Users\andrew>sqoop version
...
2018-11-21 13:20:29,408 INFO sqoop.Sqoop: Running Sqoop version: 1.4.7
Sqoop 1.4.7
git commit id 2328971411f57f0cb683dfb79d19d4d19d185dd8
Compiled by maugli on Thu Dec 21 15:59:58 STD 2017 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在，我们需要确保 Sqoop 可以通过 Java 数据库连接 API [(JDBC)](https://en.wikipedia.org/wiki/Java_Database_Connectivity) 与 MySQL 对话。为此，我们需要[下载 MySQL 的官方 JDBC 驱动程序 *Connector/J*](https://dev.mysql.com/downloads/connector/j/5.1.html) 。我下载了与平台无关的`*.zip`文件(目前是`mysql-connector-java-5.1.47.zip`)并解压到`C:\Program Files\MySQL\`中保存。然后，你需要将`C:\Program Files\MySQL\mysql-connector-java-5.1.47\mysql-connector-java-5.1.47.jar`复制到`C:\Sqoop\sqoop-1.4.7.bin__hadoop-2.6.0\lib\`(确保考虑到版本号，版本号可能会不同)。

最后要做的是下载 Cloudera Sqoop Java 库。(我从[这里](http://www.java2s.com/Code/Jar/s/Downloadsqoop142hadoop20jar.htm)得到我的。)这个应该叫做`sqoop-1.4.2-hadoop20.jar`之类的东西。把这个`jar`放到`C:\Spark\spark-2.3.2-bin-hadoop2.7\jars`里。然后，打开一个有管理员权限的`cmd`窗口。现在，您应该能够将 SQL 表导入 HDFS 了。

我将把这个 SQL 表作为`Parquet File`导入，但是[还有其他选项可用](https://sqoop.apache.org/docs/1.4.2/SqoopUserGuide.html#id385732)，包括作为序列文件、Avro 数据文件导入，或者只是作为纯文本导入。导入命令很长:

```
C:\Users\andrew> sqoop import --connect "jdbc:mysql://localhost:3306/Siemens" --table "simulate" --username "root" --password "<password here>" -m 1 --target-dir "hdfs://localhost:9000/big_data/example_SQL" --as-parquetfile --bindir sqoop_jars 
```

Enter fullscreen mode Exit fullscreen mode

注意，我的 MySQL 数据库也托管在`localhost`上，端口是默认的 MySQL 端口号`3306`。在我的例子中，数据库被称为`Siemens`，表，如上所述，是`simulate`。我以`root`的身份连接到 MySQL，输入我的 MySQL 密码，设置`-m 1`(一个并行导入的“映射”任务的数量)。我希望数据库在我的 HDFS 中的`/big_data/example_SQL`下显示为一个拼花文件，并且我希望任何生成的`*.class`或`*.jar`文件都被转储到`C:\Users\andrew\sqoop_jars`。

> 请注意，[您还可以使用一个“选项”文件](https://sqoop.apache.org/docs/1.4.2/SqoopUserGuide.html#_using_options_files_to_pass_arguments)将参数传递给`sqoop import`，这样您就不必将您的密码作为明文写入终端(这是不安全的)。

我们可以使用下面的命令来验证该表是否被正确地导入到 HDFS 系统中:

```
 C:\Users\andrew>hadoop fs -ls -R /big_data
    ...
    drwxr-xr-x - andrew supergroup          0 2018-11-19 17:46 /big_data/example_SQL
    drwxr-xr-x - andrew supergroup          0 2018-11-19 17:46 /big_data/example_SQL/.metadata
    -rw-r--r--   1 andrew supergroup        174 2018-11-19 17:46 /big_data/example_SQL/.metadata/descriptor.properties
    -rw-r--r--   1 andrew supergroup       2350 2018-11-19 17:46 /big_data/example_SQL/.metadata/schema.avsc
    drwxr-xr-x - andrew supergroup          0 2018-11-19 17:46 /big_data/example_SQL/.metadata/schemas
    -rw-r--r--   1 andrew supergroup       2350 2018-11-19 17:46 /big_data/example_SQL/.metadata/schemas/1.avsc
    drwxr-xr-x - andrew supergroup          0 2018-11-19 17:46 /big_data/example_SQL/.signals
    -rw-r--r--   1 andrew supergroup          0 2018-11-19 17:46 /big_data/example_SQL/.signals/unbounded
    -rw-r--r--   1 andrew supergroup     118032 2018-11-19 17:46 /big_data/example_SQL/684c9b31-892c-48c5-8574-9dd61a9d7e78.parquet 
```

Enter fullscreen mode Exit fullscreen mode

漂亮！最后，我们可以从 HDFS 读取这个 SQL 表。注意，作为一个 Parquet 文件导入意味着我们有`schemas`。这是相对于导入其他文件类型的一个优势——Parquet 文件维护了一个包含数据类型等的数据库模式，因此我们不需要在以后重新解析这些文件，这样可以节省时间。

### 从 HDFS 读取 SQL 表

假设您已经将 SQL 表放入 HDFS，如前一小节所述，我们现在可以从 SparkR shell 中读取它。简单运行:

```
C:\Users\andrew>sparkR  ...  >  sql  <-  read.df("hdfs://localhost:9000/big_data/example_SQL/684c9b31-892c-48c5-8574-9dd61a9d7e78.parquet",  "parquet")  >  sql  SparkDataFrame[Time:bigint,  dissolvedO2:double,  pH:double,  outletN2:double,  outletCO2:double,  outletO2:double,  outletAR:double,  AgitatorSpeed:double,  Temperature:double,  Mannose:double,  Osmolality:double,  Yield:double,  Afuco_glycans:double,  Galacto_glycans:double,  Viability:double]  >  head(sql)  Time  dissolvedO2  pH  outletN2  outletCO2  outletO2  outletAR  1  1.514819e+12  49.56497  7.056501  60.14372  10.32145  28.80335  0.7314709  2  1.514819e+12  49.26843  7.056541  60.13668  10.32402  28.82944  0.7305011  3  1.514819e+12  49.50221  7.056421  60.09855  10.31529  28.77520  0.7297820  ...  > 
```

Enter fullscreen mode Exit fullscreen mode

这与处理纯文本文件和 CSV 文件的方式完全相同，只是我们必须指定`source="parquet"`。

## 任何其他种类的文件

使用 R 包`curl`，几乎可以从 HDFS 读取任何其他类型的文件。我发现自己想要阅读 HDFS 的 Excel 文件。现在我做到了。首先，包括阅读文件所需的任何库，以及`curl`库:

```
>  library(curl)  >  library(xlsx) 
```

Enter fullscreen mode Exit fullscreen mode

使用`http://`获取文件。请注意，这里您的端口不同于上面用于`hdfs://`的端口，实际上是可以在浏览器中用于管理 HDFS 集群的相同端口:

[![](img/bdeff966cd89da0a7828c57e1e04d7c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0t3aRfMl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eix577e6txsa0rqousyk.PNG)

通过`http://`在 HDFS 上访问的任何文件都必须附加有`/webhdfs/v1`。所以，举例来说，`/example_data/example_xlsx.xlsx`会变成`/webhdfs/v1/example_data/example_xlsx.xlsx`。最后，我们需要将`?op=OPEN`添加到 URI 的末尾，这样`curl`就知道我们想要打开并读取这个文件:

```
>  curlfile  <-  curl_fetch_memory("http://localhost:9870/webhdfs/v1/example_data/example_xlsx.xlsx?op=OPEN")  >  tmpfile  <-  tempfile("name_of_temp_file")  >  tt  <-  file(tmpfile,  "wb")  >  writeBin(curlfile$content,  tt)  >  close(tt) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们现在有了一个临时文件(在 Windows 上，这些文件保存在`C:\tmp`的某个子目录中)，我们可以使用合适的阅读器
来读取它

```
>  xlsx  <-  createDataFrame(read.xlsx(tmpfile,  sheetIndex=1))  >  xlsx  SparkDataFrame[Time:timestamp,  dissolvedO2:double,  pH:double,  outletN2:double,  outletCO2:double,  outletO2:double,  outletAR:double,  AgitatorSpeed:double,  Temperature:double,  Mannose:double,  Osmolality:double,  Yield:double,  Afuco_glycans:double,  Galacto_glycans:double,  Viability:double]  >  head(xlsx)  Time  dissolvedO2  pH  outletN2  outletCO2  outletO2  1  2018-01-01  15:00:00  49.56497  7.056501  60.14372  10.32145  28.80335  2  2018-01-01  15:00:30  49.04355  7.056607  60.21205  10.38589  28.80089  3  2018-01-01  15:01:00  49.78669  7.056591  60.24343  10.37360  28.84372 
```

Enter fullscreen mode Exit fullscreen mode

再次注意，SQL Parquet 文件和这个 Excel 文件都是作为`SparkDataFrame`对象导入的！(注:据我[所知](https://github.com/tidyverse/readxl/issues/518)的[最好](https://github.com/rstudio/sparklyr/issues/1777)，似乎没有办法直接从 HDFS 把 Excel 文件读入 SparkR。它们必须作为临时文件下载到本地文件系统。但是假设您的大部分数据是 CSV、文本或 SQL-as-Parquet 文件，您仍然可以分布您的大部分分析任务。)

# r studio 中的火花

您还可以在普通的 R shell 甚至 RStudio 中执行上述任何命令。你要做的就是告诉 R`SparkR`库在哪里。这应该在您的`SPARK_HOME`目录中。对我来说，这就是:

```
C:\Users\andrew>echo %SPARK_HOME%
C:\Spark\spark-2.3.2-bin-hadoop2.7 
```

Enter fullscreen mode Exit fullscreen mode

所以我可以打开普通的 R shell，从上面用:
得到我的 SQL 表

```
C:\Users\andrew>R  --no-restore  --no-save  ...  >  library(SparkR,  lib.loc  =  c(file.path(Sys.getenv("SPARK_HOME"),  "R",  "lib")))  ...  >  sparkR.session(master  =  "local[*]",  sparkConfig  =  list(spark.driver.memory="4g"))  Spark  package  found  in  SPARK_HOME:  C:\Spark\spark-2.3.2-bin-hadoop2.7  Launching  java  with  spark-submit  command  C:\Spark\spark-2.3.2-bin-hadoop2.7/bin/spark-submit2.cmd  --driver-memory  "64g"  sparkr-shell  ...  >  sql  <-  read.df("hdfs://localhost:9000/big_data/example_SQL/684c9b31-892c-48c5-8574-9dd61a9d7e78.parquet",  "parquet")  >  sql  SparkDataFrame[Time:bigint,  dissolvedO2:double,  pH:double,  outletN2:double,  outletCO2:double,  outletO2:double,  outletAR:double,  AgitatorSpeed:double,  Temperature:double,  Mannose:double,  Osmolality:double,  Yield:double,  Afuco_glycans:double,  Galacto_glycans:double,  Viability:double]  >  head(sql)  Time  dissolvedO2  pH  outletN2  outletCO2  outletO2  outletAR  1  1.514819e+12  49.56497  7.056501  60.14372  10.32145  28.80335  0.7314709  2  1.514819e+12  49.26843  7.056541  60.13668  10.32402  28.82944  0.7305011  3  1.514819e+12  49.50221  7.056421  60.09855  10.31529  28.77520  0.7297820 
```

Enter fullscreen mode Exit fullscreen mode

一模一样！那么我们如何利用它来帮助构建 R 中的数据分析 GUI 呢？

## R 闪亮

让我们设置一个简单的 R Shiny 应用程序来连接这个数据库。创建两个名为`server.R`和`ui.R`的文件，并将以下代码粘贴到其中:

### `server.R`

```
library(shiny)  # Connect to Spark outside the shinyServer() method  library(SparkR,  lib.loc  =  c(file.path(Sys.getenv("SPARK_HOME"),  "R",  "lib")))  sparkR.session(master  =  "local[*]",  sparkConfig  =  list(spark.driver.memory="4g"))  # Define server logic  shinyServer(function(input,  output)  {  output$myTable  <-  renderDT(  # get the SparkDataFrame from HDFS; collect() converts SparkDataFrame -> data.frame  collect(read.df(paste("hdfs://localhost:9000/",  input$path,  sep=""),  input$type))  )  }) 
```

Enter fullscreen mode Exit fullscreen mode

### `ui.R`

```
library(shiny)  library(DT)  # Define UI for application that draws a data table  shinyUI(fluidPage(  # Application title  titlePanel("Simple SparkDataFrame Example"),  # Sidebar with a text input for the filename  sidebarLayout(  sidebarPanel(  textInput("path",  "HDFS File Path:",  "/big_data/example_SQL/684c9b31-892c-48c5-8574-9dd61a9d7e78.parquet"  ),  textInput("type",  "HDFS File Type:",  "parquet"  )  ),  # Show the table  mainPanel(  dataTableOutput("myTable")  )  )  )) 
```

Enter fullscreen mode Exit fullscreen mode

在 RStudio 中运行该应用程序，您将获得...

[![](img/49ce5d4be1b8ce006f1a78f390a1b466.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QQqmurq2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wv2teaobae8iu4lb9fd5.PNG)

哒哒！它只是工作。我们所要做的就是在`server.R`中设置`sparkR.session`,我们可以访问之前在普通 R shell 中运行的相同命令。**注意，使用`collect()`将`SparkDataFrame`转换为`data.frame`意味着[已经从 HDFS](https://markobigdata.com/2016/03/26/sparkr-and-r-dataframe-and-data-frame/) 收集了数据，并保存在内存中供 R 使用！**有时，这可能是您想要的(但通常不是，如果您正在处理巨大的数据集)。在转换为 R `data.frame`之前，最好尽可能多地使用[操作`SparkDataFrame`。](https://spark.apache.org/docs/latest/sparkr.html#sparkdataframe-operations)

* * *

所以你有它！一个 Hadoop/Spark 大数据后端，前端漂亮 R 闪亮。非常适合分析您的数据湖，并向最终用户发送漂亮、完美的结果，让 Spark 的所有功能都支持您的分析！

* * *

### 注:

在 R 中，一个有用的包是 [`sos`](https://cran.r-project.org/web/packages/sos/sos.pdf) ，它提供了`findFn()` :
，用于查找哪个包保存了您正在调用的特定函数

```
>  findFn("read.df")  found  3  matches  Downloaded  3  links  in  1  packages.  Ignoring  template. 
```

Enter fullscreen mode Exit fullscreen mode

...这将在您的浏览器中弹出如下窗口:

[![](img/16eefac53a518283b3f0a82c31735565.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I7CLd9WC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yaxodu6m5jew8yno2m7e.PNG)