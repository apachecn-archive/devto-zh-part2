# 笔记本电脑上的本地 hadoop 用于实践

> 原文：<https://dev.to/tomerbendavid/local-hadoop-on-laptop-for-practice-3nap>

## 简介

以下是我上周对 hadoop 安装的了解:

**Hadoop 听起来像是一件非常大的事情，复杂的安装、集群、数百台机器、万亿级(如果不是千万亿次)的数据，但实际上，您可以下载一个简单的 jar 并在笔记本电脑上运行带有 hdfs 的 Hadoop，对于实践来说，这非常简单！**

## 我们的计划

1.  Setup JAVA_HOME (hadoop 构建在 JAVA 之上)。
2.  下载 Hadoop tar.gz。
3.  提取 Hadoop tar.gz
4.  设置 hadoop 配置
5.  开始并格式化 hdfs
6.  将文件上传到 hdfs。
7.  对这些上传的文件运行 hadoop 作业。
8.  回来打印结果！

听起来像个计划！

## 设置 JAVA_HOME

正如我们所说的 hadoop 是建立在 java 之上的，所以我们需要 JAVA_HOME set。

```
➜  hadoop$ ls /Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home
➜  hadoop$ echo $JAVA_HOME
/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home 
```

Enter fullscreen mode Exit fullscreen mode

## 下载 Hadoop tar.gz

接下来我们下载 hadoop，不错:)

```
➜  hadoop$ curl http://apache.spd.co.il/hadoop/common/hadoop-3.1.0/hadoop-3.1.0.tar.gz --output hadoop.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  1  310M    1 3581k    0     0   484k      0  0:10:57  0:00:07  0:10:50  580k 
```

Enter fullscreen mode Exit fullscreen mode

## 提取 Hadoop tar.gz

现在我们的笔记本电脑上有了 tar.gz，让我们提取它。

```
➜  hadoop$ tar xvfz ~/Downloads/hadoop-3.1.0.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 HDFS

现在让我们在笔记本电脑上配置 HDFS:

```
➜  hadoop$ cd hadoop-3.1.0
➜  hadoop-3.1.0$
➜  hadoop-3.1.0$ vi etc/hadoop/core-site.xml 
```

Enter fullscreen mode Exit fullscreen mode

配置应该是:

```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们配置了 hdfs 端口，让我们配置我们需要多少个副本，我们在笔记本电脑上，我们只需要一个副本来存储我们的数据:

```
➜  hadoop-3.1.0$ vi etc/hadoop/hdfs-site.xml: 
```

Enter fullscreen mode Exit fullscreen mode

上面的`hdfs-site.xml`是副本配置的站点，下面是它应该具有的配置(提示:1):

```
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

## 启用 SSHD

Hadoop 通过 ssh 连接到节点，因此让我们在 mac 笔记本电脑上启用它:

[![http://cdn.osxdaily.com/wp-content/uploads/2011/09/enable-sftp-server-mac-os-x-lion.jpg](img/8e94c66c910044b1bbcb1eb5c340b3cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kQvHu2eM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://cdn.osxdaily.com/wp-content/uploads/2011/09/enable-sftp-server-mac-os-x-lion.jpg)

你应该可以不通过 ssh:

```
➜  hadoop-3.1.0 ssh localhost
Last login: Wed May  9 17:15:28 2018
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode

如果做不到这一点:

```
 $ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
  $ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
  $ chmod 0600 ~/.ssh/authorized_keys 
```

Enter fullscreen mode Exit fullscreen mode

## 开始 HDFS

接下来，我们开始在笔记本电脑上格式化 HDFS:

```
bin/hdfs namenode -format

➜  hadoop-3.1.0$ bin/hdfs namenode -format
WARNING: /Users/tomer.bendavid/tmp/hadoop/hadoop-3.1.0/logs does not exist. Creating.
2018-05-10 22:12:02,493 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = Tomers-MacBook-Pro.local/192.168.1.104

➜  hadoop-3.1.0$ sbin/start-dfs.sh
Starting namenodes on [localhost]
Starting datanodes 
```

Enter fullscreen mode Exit fullscreen mode

## 在 hdfs 上创建文件夹

接下来，我们在笔记本电脑的 HDFS 上创建示例输入文件夹:

```
➜  hadoop-3.1.0$ bin/hdfs dfs -mkdir /user
2018-05-10 22:13:16,982 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
➜  hadoop-3.1.0$ bin/hdfs dfs -mkdir /user/tomer
2018-05-10 22:13:22,474 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
➜  hadoop-3.1.0$ 
```

Enter fullscreen mode Exit fullscreen mode

## 上传测试数据到 HDFS

现在我们已经在笔记本电脑上安装并运行了 HDFS，让我们上传一些文件:

```
➜  hadoop-3.1.0$ bin/hdfs dfs -put etc/hadoop input
2018-05-10 22:14:28,802 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
put: `input': No such file or directory: `hdfs://localhost:9000/user/tomer.bendavid/input'
➜  hadoop-3.1.0$ bin/hdfs dfs -put etc/hadoop /user/tomer/input
2018-05-10 22:14:37,526 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
➜  hadoop-3.1.0$ bin/hdfs dfs -ls /user/tomer/input
2018-05-10 22:16:09,325 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Found 1 items
drwxr-xr-x   - tomer.bendavid supergroup          0 2018-05-10 22:14 /user/tomer/input/hadoop 
```

Enter fullscreen mode Exit fullscreen mode

## 运行 hadoop 作业

我们的笔记本电脑上有 hdfs 文件，让我们在上面运行一个作业，你觉得怎么样？

```
➜  hadoop-3.1.0$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.0.jar grep /user/tomer/input/hadoop/*.xml /user/tomer/output1 'dfs[a-z.]+'
➜  hadoop-3.1.0$ bin/hdfs dfs -cat /user/tomer/output1/part-r-00000
2018-05-10 22:22:29,118 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
1   dfsadmin
1   dfs.replication 
```

Enter fullscreen mode Exit fullscreen mode

**我们设法在 HDFS 本地安装了 hadoop 进行测试！并运行测试作业！太棒了！。**

## 总结

我们设法下载 hadoop，启动 hdfs，将文件上传到这个 hdfs，运行 hadoop 作业，并从 hdfs 获得结果，所有这些都在我们的笔记本电脑上的单个目录中完成！太酷了。

此外，这里没有什么新东西，我只是遵循了 [hadoop 安装文档](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)中的直接指导。对自己做了一些小的修改和一些小的更新解释，这样当我将来看它作为参考时会更清楚。

如果你想看更多我上周学到的东西，我总是在 https://tomer-Ben-David . github . io