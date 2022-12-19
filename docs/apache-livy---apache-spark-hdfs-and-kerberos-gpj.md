# Apache Livy - Apache Spark、HDFS 和 Kerberos

> 原文：<https://dev.to/risdenk/apache-livy---apache-spark-hdfs-and-kerberos-gpj>

### 概述

[Apache Livy](https://livy.incubator.apache.org/) 提供了一个 [REST 接口](https://livy.incubator.apache.org/docs/latest/rest-api.html)用于与 [Apache Spark](https://spark.apache.org/) 交互。当使用 Apache Spark 与使用 [Kerberos](https://web.mit.edu/kerberos/) 保护的 [Apache Hadoop HDFS](https://hadoop.apache.org/) 进行交互时，需要获得一个 Kerberos 令牌。由于令牌委托，这往往会带来一些问题。

`spark-submit` [为这个](https://issues.apache.org/jira/browse/SPARK-12279)提供了一个解决方案，当任务提交时，它会代表您获得一个委托令牌。为此，Hadoop 配置和 JAR 文件必须在`spark-submit`类路径中。具体哪些配置的 JAR 文件在参考文献[这里](https://risdenk.gitbooks.io/hadoop_book/content/examples/spark_and_hdfs.html)有解释。

在 HDP 中使用 Livy 时，Hadoop JAR 文件和配置已经在`spark-submit`的类路径中。这意味着对于通过 Livy 提交的 Spark 作业，对 HDFS 的读/写没有任何特殊要求。

如果你想用 [Apache HBase](https://hbase.apache.org/) 做类似的事情，请看这篇[的帖子](///2018/03/05/apache-livy-spark-hbase-kerberos.html)。

### 假设

*   [阿帕奇安巴里](https://ambari.apache.org/)负责管理[阿帕奇火花](https://spark.apache.org/)和[阿帕奇李维](https://livy.incubator.apache.org/)
*   [阿帕奇诺克斯](https://knox.apache.org/)在[前面阿帕奇李维](https://livy.incubator.apache.org/)用 [Kerberos](https://web.mit.edu/kerberos/) 保护
*   [Apache Hadoop HDFS](https://hadoop.apache.org/) 用 [Kerberos](https://web.mit.edu/kerberos/) 保护

### `run.sh`

```
curl \
  -u ${USER} \
  --location-trusted \
  -H 'X-Requested-by: livy' \
  -H 'Content-Type: application/json' \
  -X POST \
  https://localhost:8443/gateway/default/livy/v1/batches \
  --data "{
    \"proxyUser\": \"${USER}\",
    \"file\": \"hdfs:///user/${USER}/SparkHDFSKerberos.jar\",
    \"className\": \"SparkHDFSKerberos\",
    \"args\": [
      \"hdfs://PATH_TO_FILE\"
    ]
  }" 
```

Enter fullscreen mode Exit fullscreen mode

### `SparkHDFSKerberos`

```
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;

public class SparkHDFSKerberos {
  public static void main(String[] args) {
    SparkConf sparkConf = new SparkConf().setAppName(SparkHDFSKerberos.class.getCanonicalName());
    JavaSparkContext jsc = new JavaSparkContext(sparkConf);

    JavaRDD<String> textFile = jsc.textFile(args[0]);
    System.out.println(textFile.count());

    jsc.stop();
    jsc.close();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode