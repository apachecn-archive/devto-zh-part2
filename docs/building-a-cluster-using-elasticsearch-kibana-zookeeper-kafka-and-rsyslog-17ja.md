# 使用 Elasticsearch、Kibana、Zookeeper、Kafka 和 Rsyslog 构建集群

> 原文：<https://dev.to/guthakiran/building-a-cluster-using-elasticsearch-kibana-zookeeper-kafka-and-rsyslog-17ja>

在本教程中，你将学习如何使用 elasticsearch、kibana、zookeeper、kafka 和 rsyslog 构建一个集群。开发人员在构建集群时会面临许多困难的情况，这里我们清楚地解释了创建集群的一步一步的过程。

环境困难:
1。开发者无法登录到服务器
2。每个系统都有日志，日志数据比较分散，难以查找
3 日志数据量大，查询繁忙，不能实时

环境要求:
1。日志需要标准化

搭建这个集群需要的软件:
logstash-2.0.0.tar.gz
Elasticsearch-2.1.0.tar.gz
Jdk-8u 25-x64 . rpm
Kafka _ 2.11-0 . 11 . 0 . 1 . tgz
Kibana-4.3.2-linux-x64.tar.gz

以上软件可从官网下载:[https://www.elastic.co/downloads](https://www.elastic.co/downloads)

## 部署步骤:

1.  ES 集群安装和配置
2.  Rsyslog 客户端配置
3.  Kafka (zookeeper)集群配置
4.  基巴纳部署 5。案例:nginx 日志收集和消息日志收集
5.  基巴纳报告基本用途

## 预部署操作:

关闭防火墙，关闭 se [linux](https://mindmajix.com/linux-training) (生产环境根据需要关闭或开启)
同步服务器时间，选择公网 ntpd 服务器或自建 ntpd 服务器

[root@es1 ~]# crontab -l #方便直接使用公网服务器

# 更新时间

*/5 * * * */usr/bin/rdate-s time-b.nist.gov & >/dev/null

## 1。ES 集群安装和配置

### 1 安装 jvm 依赖环境

elk 运行时需要 jvm 环境。2.x 版本需要 oracale JDK 1.7 或 open-jdk1.7.0 版本。五号。x 版本需要 oracale JDK 1.8 或 open-jdk1.8.0 版本。多节点 JDK 版本确保它始终包含次要版本。不可以，否则可能是加入集群时出错，这也是小弟没有用百胜安装 JDK 的原因。

[root @ es1 ~]# rpm-IVH JDK-8u 25-x64 . rpm #因为 5。x 版本需要 1.8，以便升级以后直接安装 1.8
准备中...## # # # # # # # # # # # # # # # # # # # # # # # # # # # 100%]
1:JDK 1 . 8 . 0 _ 131 # # # # # # # # # # # # # # # # # # # # 100%】

### 设置 Java 环境

[root @ es1 ~]# cat/etc/profile . d/java . sh #编辑 Java 环境配置文件
导出 JAVA_HOME=/usr/java/latest
导出 class PATH = $ Java _ HOME/lib/tools . jar
导出路径= $ Java _ HOME/bin:$ PATH
[root @ es1 ~]#。/etc/profile . d/Java . sh

【root @ es1 ~ 】# Java-version #确认配置
java 版本" 1.8.0_131"
Java(TM) SE 运行时环境(build 1 . 8 . 0 _ 131-b11)
Java HotSpot(TM)64 位服务器 VM (build 25.131-b11，混合模式)

## 2。安装和配置 elasticsearch

[root @ es1 ~]# tar xf elasticsearch-2.1.0.tar.gz-c/usr/local/
[root @ es1 ~]# CD/usr/local/
[root @ es1 local]# ln-SV elastic search-2 . 1 . 0 elastic search
" elastic search "->" elastic search-2 . 1 . 0 "
[root @ es1 local]# CD elastic search
[root @ es1 elastic search]# vim config/elastic search . yml 集群内唯一
path . Data:/Data/es/Data #数据目录
path . logs:/Data/es/logs #日志目录
bootstrap . mlockall:true
network . host:10 . 1 . 1 . 243
http . port:9200
discovery . Zen . ping . unicast . hosts:[" 10 . 1 . 1 . 243 "、" 10.1.1.244"]
discovery。

## 3。创建相关目录

[root @ es1 elastic search]# Mkdir-PV/Data/es/{ Data，logs}
Mkdir:已创建目录"/Data "
mkdir:已创建目录"/Data/es "
mkdir:已创建目录"/Data/es/data "
mkdir:已创建目录"/Data/es/logs

### 4。出于安全原因，Elasticsearch 不允许 root 启动。创建一个新用户，并使用该用户进行相关操作。

创建一个普通用户并建议添加适当的 sudo 权限
【root @ es1 elastic search】# useradd elastic search
【root @ es1 elastic search】# chown-R elastic search:elastic search/Data/es/
【root @ es1 elastic search】# chown-R elastic search:elastic search/usr/local/elastic search-2 . 1 . 0/

### 5。配置其他环境参数(必须，否则将在出错后启动)

[root @ es1 elastic search]# echo " elastic search hard no file 65536 " > >/etc/security/limits . conf
[root @ es1 elastic search]# echo " elastic search soft no file 65536 ">>/etc/security/limits . conf
[root @ es1 elastic search]# sed-I ' s/1024/2048/g '/etc/security/limits . d/90-nproc . conf

[root @ es1 elastic search]# grep " ES _ HEAP _ SIZE = " " bin/elastic search # Set[elastic search](https://mindmajix.com/elasticsearch-training)内存大小原则上是越大越好，但不要超过 32G
导出 ES_HEAP_SIZE =100m #测试环境内存有限

其他节点的配置与其他节点的配置相同。

网络主机:10.1。1.243 #本地 IP 地址

Node.name: pwb -node1 #指定的节点名

### 6。开始弹性搜索

[root @ es1 elastic search]# su-elastic search
[elastic search @ es1 ~]$ CD/usr/local/elastic search
[elastic search @ es1 elastic search]$ bin/elastic search&

通过输出，您可以看到服务启动，并通过自动发现在集群中添加其他节点。

检查服务是否正常

[root@es1 ~]# netstat -tnlp
活跃互联网连接(仅限服务器)
Proto Recv-Q Send-Q 本地地址外来地址状态 PID/程序名
Tcp 0 0 0 . 0 . 0 . 0:22 0 . 0 . 0 . 0:* list EN 944/sshd
Tcp 0 0::ffff:10.1。1.243 : 9300 :::* LISTEN 3722 / java 之间的通信# ES 节点
Tcp 0 0:::22:::* list EN 944/sshd
Tcp 0 0::ffff:10.1。1.243:9200:::* list EN 3722/Java # ES 节点和外部通信使用
[root @ es1 ~]# curl http://10 . 1 . 1 . 243:9200/#如果出现以下消息，说明安装和配置成功
{
" name " : " pwb-node1 "，
" cluster_name " : " pwb-cluster "，
" version ":{
" number ":" 2
" build _ timestamp ":" 2015-11-18t 22:40:03Z "，
"build_snapshot" : false，
" Lucene _ version ":" 5 . 3 . 1 "
}，
"tagline ":"你懂的，为了搜索"
}

### 7。安装 elasticsearch 常用插件

[root @ es1 ~]#/usr/local/elastic search/bin/plugin 安装 mobz/elasticsearch-head

安装完成后，访问网址[http://10 . 1 . 1 . 243:9200/_ plugin/head/](http://10.1.1.243:9200/_plugin/head/)。由于集群中暂时没有数据，所以显示为空(五角星表示主节点，圆点表示数据节点)。

其他常用的插件安装方法(这里不演示，有兴趣自己安装)

。/bin/plugin 安装 lukas-vlcek/bigdesk # 2 .0 版本变更命令
。/ bin / plugin 安装 hlstudio/bigdesk # 2 .0 或更高版本使用此命令安装
，/bin/plugin 安装 lmenezes/elastic search-head/version

## 2。日志存储集群安装和配置

Logstash 需要依赖 java 环境，所以这里我们仍然需要安装 JVM，这一步就省略了

### 1。安装日志存储

[root @ logstash 1 ~]# tar xf logstash-2.0.0.tar.gz-C/usr/local/
[root @ logstash 1 ~]# CD/usr/local/
[root @ logstash 1 local]# ln-SV logstash-2 . 0 . 0 logstash
" logstash "->" logstash-2 . 0 . 0 "
[root @ logstash 1 local]# CD logstash
[root @ logstash 1 logstash]# grep " LS _ HEAP _ SIZE " bin/log

### 2。测试日志将日志写到 elasticsearch，以系统消息文件为例

**(1)编写一个 logstash 配置文件**

[root @ Logstash 1 log stash]# cat conf/messages . conf
input {
File { # data input 使用输入文件插件，从消息文件中读取
path =>"/var/log/messages "

}
}
output {
elastic search { # data output points to ES cluster
Hosts =>[" 10 . 1 . 1 . 243:9200 "、" 10.1.1.244

**(2)写一些文件给消息，我们安装一些软件**

[root @ logstash 1 log]# yum install httpd-y

检查消息文件中的更改

[root @ logstash 1 log]# tail/var/log/messages
10 月 24 日 13:44:25 localhost 内核:ata2.00:为 UDMA 配置/33
10 月 24 日 13:44:25 localhost 内核:ata2: EH 完成
10 月 24 日 13:49:34 localhost rz[3229]:[root]logstash-2.0.0.tar.gz/ZMODEM:错误:zgethdr 返回 16
10 月 24 日 13:49:34 localhost rz 1830344 BPS
11 月 8 日 22:50:49 localhost yum[3697]:已安装:apr-util-LDAP-1 . 3 . 9-3 . el6 _ 0.1 . x86 _ 64
11 月 8 日 22:50:50 localhost yum[3697]:已安装:httpd-tools-2 . 2 . 15-60 . el6 . centos . 6 . x86 _ 64
11 月 8 日 22:51:07

访问 elasticsearch head 插件的网页。我们已经看到，logstash 可以正常地写入到 elasticsearch 集群中，并且 logstash 配置已经完成(其他节点的配置相同)。

## 3。Kafka+Zookeeper 集群安装和配置

构建 kafka 集群时，需要提前安装 zookeeper 集群。你可以单独安装它，也可以使用 kafka 来安装 zookeeper 程序。选择 kafka 自带的 zookeeper 程序。

### 1。提取安装包

[root @ Kafka 1 ~]# tar xf Kafka _ 2.11-0 . 11 . 0 . 1 . tgz-C/usr/local/
[root @ Kafka 1 ~]# CD/usr/local/
[root @ Kafka 1 local]# ln-SV Kafka _ 2.11-0 . 11 . 0 . 1 Kafka
【Kafka】->“Kafka _ 2.11-0 . 11 . 0 . 1”
[root @ Kafka

### 2。配置 zookeeper 集群并修改配置文件

[root @ Kafka 1 Kafka]# grep " ^[a-z]" config/zookeeper . properties
datadir =/data/zookeeper
client port = 2181
maxclientcnxns = 0
tick time = 2000
init limit = 20
sync limit = 10
server . 1 = 10 . 1 . 1 . 247:2888:3888
server . 2 = 10 . 1 . 1 . 1 . 1 . 248:28888

# 描述:

tickTime:这个时间被用作 ZooKeeper 服务器维护的心跳之间或者客户端和服务器之间的时间间隔。也就是说，每隔一段时间就发送一次心跳。

端口 2888:表示该服务器与群集中的领导服务器交换信息的端口。

port:表示如果集群中的 leader 服务器挂起，则需要重新选择一个端口，并选择新的 leader。此端口是服务器在选举期间相互通信的端口。

### 3。创建 zookeeper 需要的目录和 myid 文件

[root @ Kafka 1 Kafka]# Mkdir-PV/Data/zookeeper
Mkdir:created directory "/Data "
Mkdir:created directory "/Data/zookeeper "
[root @ Kafka 1 Kafka]# echo " 1 ">/Data/zookeeper/myid # myid 文件，文件内容为标识主机的数字，如果该文件不存在，zookeeper 将无法启动

## 4。卡夫卡式结构

[root @ Kafka 1 Kafka]# grep " ^[a-z]" config/server . properties
broker . id = 1 # unique
in listeners = plaintext://10 . 1 . 1 . 247:9092 #服务器 IP 地址和端口
num . network . threads = 3
num . io . threads = 8
socket . send . buffer . bytes = 102400
socket。receive . buffer . bytes = 102400
socket . request . max . bytes = 104857600
log . dirs =/Data/Kafka-logs 需要提前创建#
Num.partitions = 10 #需要配置得更大，分片会影响读写速度
num . recovery . threads . per . Data . dir = 1
offsets . topic . replication . factor = 1
transaction . state . log . replication . factor = 1【T121.247 : 2181 , 10.1 .1.248 : 2181 # zookeeper 服务器 IP 和端口
zookeeper . connection . time out . ms = 6000
group . initial . rebalance . delay . ms = 0

除了以下几点之外，其他节点具有相同的配置:

(1 ) Zookeeper 配置
echo“x”>/Data/Zookeeper/myid # Unique
(2)Kafka
broker . id = 1 # Unique
host . name = local IP 的配置

## 5。启动动物园管理员和卡夫卡

### 1)启动 zookeeper

[root @ Kafka 1 Kafka]#/usr/local/Kafka/bin/zookeeper-server-start . sh/usr/local/Kafka/config/zookeeper . properties &

以下两个站执行相同的操作。在启动过程中，会出现以下错误消息

java.net.ConnectException:连接被拒绝
at Java . net . plainsocketimpl . socket connect(Native Method)
at Java . net . abstractplainsocketimpl . doc connect(abstractplainsocketimpl . Java:339)
at Java . net . abstractplainsocketimpl . connecttoaddress(abstractplainsocketimpl . Java:200)
at Java . net . abstractplainsocketimpl . connect(abstractplainsocketimpl . Java:182)
at Java

由于启动了 zookeeper 集群，每个节点都尝试连接到集群中的其他节点。第一次启动当然不会连接到以下节点。因此，日志前面部分中的异常可以忽略不计。从后面的部分可以看出，集群在选出一个领导者后，最终稳定下来。
其他节点也可能有类似情况，这是正常的

### 动物园管理员服务检查

[rootkafka1 ~]# netstat -nlpt | grep -E "2181|2888|3888"
tcp 0 0 :::2181 :::* LISTEN 33644/java
tcp 0 0 ::ffff:10.1.1.247:3888 :::* LISTEN 33644/java

[root @ Kafka 2 ~]# netstat-nlpt | grep-E " 2181 | 2888 | 3888 "
Tcp 0 0:::2181:::* list EN 35016/Java
Tcp 0 0::ffff:10.1。1.248:2888:::* list EN 35016/Java #哪个是领袖，那么他有 2888 个端口
TCP 0 0::ffff:10 . 1 . 1 . 248:3888:::* list EN 35016/Java

### 2)开始卡夫卡

/usr/local/Kafka/bin/Kafka-server-start . sh/usr/local/Kafka/config/server . properties &

如果您有以下错误

[2018-06-05 23:52:30，323]错误处理器得到未捕获的异常。(Kafka . network . processor)
Java . lang . exception iniinitializererror
at Kafka . network . Request channel $ Request。(request channel . Scala:124)
at Kafka . network . processor $ $ anon fun $ processCompletedReceives $ 1 . apply(socket server . Scala:518)
at Kafka . network . processor $ $ anon fun $ processCompletedReceives $ 1 . apply(socket server . Scala:511)
at Scala . collection . iterator $ class . foreach(iterator . Scala:891)
at Scala . collection . abstract iterator . fore(RequestChannel.scala:43)
在 kafka.network.RequestChannel$。
...10 更多
原因:Java . net . Unknown hostexception:Kafka 2 . example . com:Java . net . inet 6 address impl . lookupallhostaddr(Native Method)
at Java . net . inet address＄1 . lookupallhostaddr(inet address . Java:901)
at Java . net . inet address . getaddresssfromnameservice(inet address . Java:1293)【T20...还有 12 个

编辑 hosts 文件添加 127.0.0.1 解析当前主机名
[root @ Kafka 1 ~]# cat/etc/hosts
127 . 0 . 0 . 1 kafka1.example.com 本地主机 localhost . local domain localhost 4 localhost 4 . local domain 4
:1 localhost localhost . local domain localhost 6 . local domain 6

在其他节点上启动 zookeeper 和 Kafka
启动完成后，进行一些测试

### ①创造一个主题

[root @ Kafka 1 ~]#/usr/local/Kafka/bin/Kafka-topics . sh-create-zookeeper localhost:2181-replication-factor 2-partitions 1-topic summer #注意:因子大小不能超过代理的数量，否则会出现错误。当前集群代理值是 2
创建的主题“summer”。

### (2)检查已经创建了哪些主题

[root @ Kafka 1 ~]/usr/local/Kafka/bin/Kafka-topics . sh-list-zookeeper 10.1。1.247 : 2181 #集群列表所有话题
夏天

### (3)查看话题详情

[root @ Kafka 1 ~]#/usr/local/Kafka/bin/Kafka-topics . sh-describe-zookeeper 10 . 1 . 1 . 247:2181-Topic summer
Topic:summer Partition count:1 replication factor:2 config:
Topic:summer Partition:0 Leader:2 replica:2，1 Isr: 2，1

# 科目名称:夏天

# 分区:只有一个，从 0 开始

# 领导:id 为 2 的经纪人

# Replicas 副本存在于代理 id 2，1 之上。

# Isr:活跃的经纪人

### (4)发送消息，这里是生产者角色

[root Kafka 1 ~]#/bin/bash/usr/local/Kafka/bin/Kafka-console-producer . sh-broker-list 10.1。1.247 : 9092 -话题夏天

> 你好，约翰先生#输入东西，输入

### (5)接收消息，这里是消费者角色

[root @ Kafka 2 Kafka]#/usr/local/Kafka/bin/Kafka-console-consumer . sh-zookeeper 10 . 1 . 1 . 247:2181-topic summer-from-beginning
对旧消费者使用 ConsoleConsumer 已被否决，并将在未来的主要版本中删除。考虑通过传递[bootstrap-server]而不是[zookeeper](https://mindmajix.com/hadoop-training)来使用新的消费者。你好，约翰先生

如果您可以像上面那样接收到来自生产者的消息，那么基于 kafka 的 zookeeper 集群就是成功的。

## 6。配置客户端 rsyslog

### 1。查看当前的 rsyslog 版本

[root @ log-client 1 ~]# rsyslogd-v
rsyslogd 5 . 8 . 10，编译时使用:

Rsyslog 对 kafka 的支持是在 v8.7.0 发布后提供的，所有系统都需要升级 rsyslog 版本

### 2。升级 rsyslog

wget[http://rpms.adiscon.com/v8-stable/rsyslog.repo](http://rpms.adiscon.com/v8-stable/rsyslog.repo)-O/etc/Yum . repos . d/rsy slog . repo #下载百胜源码
T3】百胜更新 rsyslog -y
百胜安装 rsyslog -kafka -y #安装 rsyslog -kafka 模块
Ll/lib 64/rsy slog/omkafka . so #检查模块是否安装
/etc/init.d/rsyslog 重启#重启服务

检查更新版本

[root @ log-client 1 yum . repos . d]# wrsyslogd-v
rsyslogd 8 . 30 . 0，编译时使用:

### 3。下面的测试通过 rsyslog 将 nginx 日志传递给 Kafka(nginx 是预先安装的)

**(1)编辑 rsyslog 配置文件**

[root @ log-client 1 yum . repos . d]# cat/etc/rsyslog . d/engine _ Kaka . conf 中的根@ log-client 1 yum . repos . d]# cat/etc/rsyslog . d/engine _ Kaka . conf

# 加载 omkafka 和 imfile 模块

模块(load="omkafka")
模块(load="imfile ")

# nginx 模板

template(name = " nginxAccessTemplate " type = " string " string = " % hostname %% syslogtag %% msg % \ n ")

# 规则集

rule set(name = " engine-kafca "){
# log forwarding kafca]
action(
type = " omkaka "
template = " engine access replace "
confparam =[" compression . codec = snappy "，" queue . buffer . max . messages = 400000 "]
partitions . number = " 4 "
topic = " test

# 定义源并设置相关动作

输入(type="imfile" Tag="nginx，AWS " File = "/var/log/nginx/access . log " Ruleset = " nginx-Kafka ")

测试配置文件的语法错误

[root @ log-client 1 yum . repos . d]# rsy slogd-N 1
rsy slogd:版本 8.30.0，配置验证运行(级别 1)，主 config/etc/rsy slog . conf
rsy slogd:配置验证运行结束。再见。

测试完成后，重新启动 rsyslog。否则，配置不会生效。

[root @ log-client 1 yum . repos . d]#/etc/init . d/r syslog restart

启动 nginx，添加两个测试页面，访问

切换到 kafka 集群服务器并检查主题列表

[root @ localhost ~]#/usr/local/Kafka/bin/Kafka-topics . sh-list-zookeeper 10 . 1 . 1 . 247:2181
summer
Test _ nginx

您可以看到，除了前面测试创建的 summer 之外，还配置了一个额外的 test_nginx 主题。

### 7。Logstash 将 kafka 数据传输到 elasticsearch

[root @ logstash 1 ~]# cat/usr/local/log stash/conf/test _ nginx . conf
input {
Kafka {
ZK _ connect =>" 10 . 1 . 1 . 247:2181，10 . 1 . 1 . 248:2181 " # Kafka
group _ id =>" log stash "
topic _ id =>" test _ nginx "
reset _ beging

测试语法

[root @ logstash 1 ~]#/usr/local/log stash/bin/log stash-f/usr/local/log stash/conf/test _ nginx . conf-t
配置正常

启动服务，其余节点也启动服务

[root @ Logstash 1 log]#/usr/local/log stash/bin/log stash-f/usr/local/log stash/conf/test _ nginx . conf
使用的默认设置:筛选工作程序:1
Logstash 启动完成

切换到 ES 群集节点 es1.example.com 以查看:

[root @ es1 ~]# curl-XGET ' 10 . 1 . 1 . 243:9200/_ cat/indexes？v&pretty'
健康状态指数 pri rep docs . count docs . deleted store . size pri . store . size
绿开 log stash-2017 . 11 . 08 5 1 4 0 40.7 kb 20.3 kb
绿开测试-nginx-2017-11 5 1 1 0 12.5kb 6.2kb

如您所见，test-nginx 索引已经有了。
在 web 界面访问头部插件，重试测试，使用强制刷新。访问头部插件网页界面，最新访问的两个记录已经出来了。

### 8。部署基巴纳

在 ES 集群节点上安装 kibana

[root @ es1 ~]# tar xf kibana-4.2.1-linux-x64.tar.gz-c/usr/local/
[root @ es1 ~]# CD/usr/local/
[root @ es1 local]# ln-SV 基巴纳-4.2.1-linux-x64/基巴纳
"基巴纳"- >"基巴纳-4 . 2 . 1-Linux-x64/"
[root @ es1 local]# CD 基巴纳
[root@es1 基巴纳]# grep

现在让我们打开 kibana，配置一个索引并创建一个视图来测试数据。

结论:到此为止，在这里我们学习了如何使用麋鹿、动物园管理员、[卡夫卡](https://mindmajix.com/apache-kafka-training)和 Rsyslog 构建一个集群。让我们把手弄脏，如果你对这篇文章有任何问题，请在下面的评论区留下你的评论。快乐学习。

作者简介:Kiran Gutha

作者在各种技术平台(如大数据、AWS、数据科学、机器学习、Linux、Python、SQL、JAVA、Oracle、数字营销等)拥有超过 6 年的企业经验。他是一个技术迷，喜欢通过博客为各种开放平台做出贡献。他目前与领先的专业培训提供商 [Mindmajix Technologies INC](https://mindmajix.com/) 合作。并努力通过个人博客、研究和创新思想为有志之士和专业人士提供知识。