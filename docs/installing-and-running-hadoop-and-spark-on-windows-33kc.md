# 在 Windows 上安装和运行 Hadoop 和 Spark

> 原文：<https://dev.to/awwsmm/installing-and-running-hadoop-and-spark-on-windows-33kc>

# 在 Windows 上安装运行 Hadoop 和 Spark

我们最近有一台**大型**新服务器在运行 Hadoop 和 Spark (H/S ),用于对我们为生物制药行业编写的一些软件进行概念验证测试，我在尝试让 H/S 在 Windows Server 2016 / Windows 10 上运行时遇到了一些困难。我在这里一步一步地记录了我是如何设法直接在 Windows `cmd`提示符下安装和运行这对 Apache 产品的，而不需要任何 Linux 仿真。

> ***更新 2019 年 12 月 16 日:**软件版本号已更新，文本已澄清。*

# 获取软件

第一步是下载 Java、Hadoop 和 Spark。Spark 似乎无法与较新版本的 Java 兼容，所以我现在坚持使用 Java 8:

*   [Java(使用版本:8u230+)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
*   [Hadoop(使用版本:3.1.3)](https://hadoop.apache.org/releases.html)
*   [Spark(使用版本:3.0.0 预览版)](https://spark.apache.org/downloads.html)

> 我不能保证本指南适用于较新版本的 Java。如果你有问题，请尝试使用 Java 8。此外，对于新的 Oracle 许可结构(2019+)，您可能需要创建一个 Oracle 帐户来下载 Java 8。为了避免这种情况，只需[从 AdoptOpenJDK 下载，而不是](https://adoptopenjdk.net/)。

对于 Java，我下载的是 AdoptOpenJDK HotSpot JVM 的“Windows x64”版本(`jdk8u232-b09`)；对于 Hadoop，v3.1.3 的二进制(`hadoop-3.1.3.tar.gz`)；对于 Spark，v3.0.0“为 Apache Hadoop 2.7 及更高版本预建”(`spark-3.0.0-preview-bin-hadoop2.7.tgz`)。从现在开始，我将这些版本统称为`hadoop-<version>`和`spark-<version>`；在本教程的剩余部分，请用您的版本号替换它们。

> 尽管目前有更新版本的 Hadoop 和 Spark，[Windows 上的 Hadoop 3.2.1 存在一个导致安装失败的 bug](https://issues.apache.org/jira/browse/HDFS-14890)。在该修补版本可用之前(3.3.0 或 3.1.4 或 3.2.2)，您必须在 Windows 上使用早期版本的 Hadoop。

接下来，[下载 7-Zip](https://www.7-zip.org/) 解压`*gz`档案。请注意，您可能需要提取两次(一次从`*gz`移动到`*.tar`文件，然后第二次到“untar”)。一旦它们被提取出来(Hadoop 需要一些时间)，你就可以删除所有的`*.tar`和`*gz`文件。您现在应该有两个目录，并且在您的下载目录中有 JDK 安装程序:

[![](../Images/04d8564ac9ac01c8a35e9367ca13d60b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ilp1ubt0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sn274lipt5r1dq60q4y2.PNG)

注意——如上所示——“Hadoop”目录和“Spark”目录各包含一个`LICENSE`、`NOTICE`和`README`文件。对于特定版本的 Hadoop，您可能会提取并获得类似于
的目录结构

```
C:\Users\<username>\Downloads\hadoop-<version>\hadoop-<version>\... 
```

Enter fullscreen mode Exit fullscreen mode

...如果是这种情况，通过复制粘贴将*内层* `hadoop-<version>`目录的内容移动到外层`hadoop-<version>`目录，然后删除*内层* `hadoop-<version>`目录。例如，`LICENSE`文件的路径应该是:

```
C:\Users\<username>\Downloads\hadoop-<version>\LICENSE 
```

Enter fullscreen mode Exit fullscreen mode

...对于“Spark”目录也是类似的。

> ***警告**:如果你在 7-Zip 中看到类似“无法创建符号链接:客户端不拥有所需权限”的信息，[你**必须**在管理员模式下运行 7-Zip](https://superuser.com/a/1076531/728488)，**然后**解压目录。如果您跳过这些文件，可能会导致 Hadoop 安装失败。*

将 Spark 和 Hadoop 目录移动到`C:\`目录中(您可能需要在您的机器上拥有管理员权限来完成此操作)。然后，运行 Java 安装程序，但是将目标文件夹从默认的`C:\Program Files\AdoptOpenJDK\jdk-<version>\`改为`C:\Java`。(H/S 可能无法处理名称中包含空格的目录。)

[![](../Images/b92af98901f44e3db1ccb2d490de6a86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_5qz4Fgl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9gcxr91eej5cuc5kay8f.PNG)

安装完成后，您可以删除 Java `*.msi`安装程序。创建两个名为`C:\Hadoop`和`C:\Spark`的新目录，并将`hadoop-<version>`和`spark-<version>`目录分别复制到这两个目录中:

[![](../Images/31f3e6fad06c1fb35b0ef0f76ed0cc8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BrQ_u30Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3jarnzcyy5nczmfzwx7o.PNG)

如果您收到“名称太长”类型的警告，请跳过这些文件。这些只是`*.html`文件，对运行 H/S 并不重要。

# 设置你的环境变量

接下来，我们需要设置一些环境变量。转到`Control Panel > System and Security > System > Advanced System Settings > Environment Variables...`:

[![](../Images/4798b12f113e882044d705f954b8a44a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MLhmwD6O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gydxky0nm5t3pyyn6ls6.PNG)

...并添加新的系统变量(底部方框),名为:

*   `JAVA_HOME`->-`C:\Java`
*   `HADOOP_HOME`->-`C:\Hadoop\hadoop-<version>`
*   `SPARK_HOME`->-`C:\Spark\spark-<version>`

(根据你下载的 Hadoop 和 Spark 的版本进行调整。)

然后，编辑`Path`(还是在底部的系统变量框中)，添加那些附加了`\bin`的变量(对于 Hadoop 也是`\sbin`):

[![](../Images/01aab732af0d80868b238db8a8c4fce9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8ioyUzOX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p5gj8qs2sf8qq99beq57.PNG)

如果你在`cmd`中`echo %PATH%`，你现在应该在路径中间的某个地方看到这三个目录，因为用户路径被附加到了`%PATH`变量的系统路径上。现在您应该检查一下`java -version`、`hdfs -version`和`spark-shell --version`是否返回了版本号，如下所示。这意味着它们已被正确安装并添加到您的`%PATH%`:

[![](../Images/15a568323fc9fb9f22318b5ecb582cdd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WZbXb3u5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ohe2pkcd4o85odi4jk0l.PNG)

**请注意**如果您试图从路径中有空格的位置*运行上述命令，这些命令可能会失败。例如，如果您的用户名是“名字姓氏”，并且您试图检查 Hadoop 版本，您可能会看到类似于
的错误消息*

```
C:\Users\Firstname Lastname>hdfs -version
Error: Could not find or load main class Lastname 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个问题，只需移动到一个路径中没有任何空格的工作目录(正如我在上面的截图中所做的):

```
C:\Users\Firstname Lastname>cd ..

C:\Users>hdfs -version
openjdk version "1.8.0_232"
OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_232-b09)
OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.232-b09, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode

# 配置 Hadoop

接下来，转到`%HADOOP_HOME%\etc\hadoop`并编辑(或创建)文件`core-site.xml`，如下所示:

## [T1】core-site . XML](#coresitexml)

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

在同一目录下，编辑(或创建)`mapred-site.xml`以下内容:

## mapred-site.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，编辑(或创建)`hdfs-site.xml`:

## hdfs-site.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///C:/Hadoop/hadoop-<version>/namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///C:/Hadoop/hadoop-<version>/datanode</value>
  </property>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

...是的，它们应该是正斜杠，即使 Windows 使用反斜杠。这是由于 Hadoop 解释这些文件路径的方式。此外，一定要用适当的 Hadoop 版本号替换`<version>`。最后，编辑`yarn-site.xml`,使其显示为:

## yarn-site.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>  
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的最后一件事是创建我们在`hdfs-site.xml`中引用的目录:

[![](../Images/cbe091bc0abe31860ebcf7732548b587.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SuXnc43j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4zv6rhjo808tomijw4s4.PNG)

# 补丁 Hadoop

现在，您需要应用[一个由用户 cdarlint](https://github.com/cdarlint/winutils) 创建并发布到 GitHub 的补丁。(*注意，这个补丁是特定于你正在安装的 Hadoop 版本的，*但是如果确切的版本不可用，尝试使用所需版本之前的版本...这有时行得通。)

备份你的`%HADOOP_HOME%\bin`目录(复制到`\bin.old`或者类似的地方)，然后把打了补丁的文件(具体到你的 Hadoop 版本，从上面的 git repo 下载)复制到旧的`%HADOOP_HOME%\bin`目录，用新的替换旧的文件。

现在，如果您在`cmd`中运行`hdfs namenode -format`，您应该会看到:

[![](../Images/c4f142b1c2b12c79be2e5f1fa4606e1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KdgUgIf4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o1r4vh0ky329o5jg6dp1.PNG)

还有一件事要做:将`C:\Hadoop\hadoop-<version>\share\hadoop\yarn\timelineservice`中的`hadoop-yarn-server-timelineservice-<version>`复制到`C:\Hadoop\hadoop-<version>\share\hadoop\yarn`(父目录)。(这些是短版本号，像`3.1.3`，`JAR`文件名和目录名可能不匹配。)

# 开机 HDFS

最后，你可以通过运行`cmd`中的`start-dfs.cmd`和`start-yarn.cmd`来启动 HDFS:

[![](../Images/de1dda4b554224a51bcdb1c6bce98124.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p-uKiWJw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxs3insfi8dozupt41z3.PNG)

您应该使用`jps`命令验证`namenode`、`datanode`、`resourcemanager`和`nodemanager`都在运行:

[![](../Images/c0aff97e442009ab5c9e380e6f41f9ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jKUXqTOT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ar9irvm9sikggucetzov.PNG)

您还可以在浏览器中打开`localhost:8088`和`localhost:9870`来监控您闪亮的新 Hadoop 分布式文件系统:

[![](../Images/840772160f8946b5f32e573163391c25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N6ru4Dv2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b3d4dz32ynzeciu8sn71.PNG)

最后，通过运行`hadoop fs -mkdir /test`测试您是否可以编辑文件系统，这将在根目录中创建一个名为`test`的目录:

[![](../Images/fa54e8da84b2757d221ba6709261f504.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZHq2ldcE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mh4cw1ga0ev5lgyk8re7.PNG)

# 测试 Hadoop 和 Spark

我们现在知道了如何在 HDFS 中创建目录(`fs -mkdir`)并列出它们的内容(`fs -ls`)，那么创建和编辑文件呢？嗯，可以用`fs -put`把文件从本地文件系统复制到 HDFS。然后我们可以用`sc.textFile(...)`读取`spark-shell`中的文件:

[![](../Images/f3b345c88022d9fc35b48ba10d5c3836.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hK1bi8xk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nhowj0hgwqdw6edi8965.PNG)

请注意，您在`hdfs://localhost:9000/`而不仅仅是`hdfs://`从 HDFS 读取了一个文件。这是因为这是我们在`core-site.xml`中定义的`defaultFS`。

**如果你想停止 HDFS** ，你可以运行命令:

```
C:\Users> stop-dfs.cmd 
```

Enter fullscreen mode Exit fullscreen mode

和

```
C:\Users> stop-yarn.cmd 
```

Enter fullscreen mode Exit fullscreen mode

* * *

所以你有它！Spark 在 Windows 上运行，读取存储在 HDFS 的文件。这需要做一些工作，我非常感谢以前遇到过和我一样的错误的人，或者以前编写过我用作本演练框架的教程的人。以下是我用来构建本教程的博客、GitHub repos 和其他帖子:

*   [Muhammad Bilal Yar 的 Hadoop 2.8.0 演练](https://github.com/MuhammadBilalYar/Hadoop-On-Window/wiki/Step-by-step-Hadoop-2.8.0-installation-on-Window-10)
*   [Java . net . urisynctaxexception](https://stackoverflow.com/questions/38784134/hadoop-installation-issue-on-windows)
*   [Java . lang . unsatisfiedlinkerror](https://stackoverflow.com/questions/33211599/hadoop-error-on-windows-java-lang-unsatisfiedlinkerror)
*   [致命资源管理器。资源管理器](https://stackoverflow.com/questions/51118358/noclassdeffounderror-org-apache-hadoop-yarn-server-timelineservice-collector-tim)
*   [本地主机:50070 错误](https://stackoverflow.com/questions/19641326/http-localhost50070-does-not-work-hadoop)
*   [Kuldeep Singh 的演练和故障排除指南](https://dzone.com/articles/working-on-apache-spark-on-windows)
*   [雅克·拉尔斯基的《吉他》T1](https://jaceklaskowski.gitbooks.io/mastering-apache-spark/spark-tips-and-tricks-running-spark-windows.html)
*   [java.io.IOException:不兼容的 cluster id](https://stackoverflow.com/questions/22316187/datanode-not-starts-correctly)
*   [HDFS 基本命令](https://www.tutorialspoint.com/hadoop/hadoop_hdfs_operations.htm)
*   [火花基本命令](https://spark.apache.org/examples.html)