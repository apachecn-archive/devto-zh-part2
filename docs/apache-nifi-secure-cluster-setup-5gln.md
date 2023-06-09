# Apache NiFi 安全集群设置

> 原文：<https://dev.to/bbende/apache-nifi-secure-cluster-setup-5gln>

设置一个安全的集群继续产生许多问题，所以尽管已经有几篇文章讨论了这个主题，我想我应该记录一下我在验证 Apache NiFi 1.8.0 候选版本时执行的步骤。

### 初始设置

可以为本地测试设置的最简单的集群是一个双节点集群，在第一个节点上运行嵌入式 ZooKeeper。

构建 1.8.0-RC3 的源代码后，解压二进制发行版并创建两个相同的目录:

```
unzip nifi-1.8.0-bin.zip
mv nifi-1.8.0 nifi-1
cp -R nifi-1 nifi-2 
```

生成的目录结构应该如下所示:

```
cluster
├── nifi-1
│   ├── LICENSE
│   ├── NOTICE
│   ├── README
│   ├── bin
│   ├── conf
│   │   ├── authorizers.xml
│   │   ├── bootstrap-notification-services.xml
│   │   ├── bootstrap.conf
│   │   ├── logback.xml
│   │   ├── login-identity-providers.xml
│   │   ├── nifi.properties
│   │   ├── state-management.xml
│   │   └── zookeeper.properties
│   ├── docs
│   └── lib
└── nifi-2
    ├── LICENSE
    ├── NOTICE
    ├── README
    ├── bin
    ├── conf
    │   ├── authorizers.xml
    │   ├── bootstrap-notification-services.xml
    │   ├── bootstrap.conf
    │   ├── logback.xml
    │   ├── login-identity-providers.xml
    │   ├── nifi.properties
    │   ├── state-management.xml
    │   └── zookeeper.properties
    ├── docs
    └── lib 
```

### [T1】配置 nifi.properties](#configure-nifiproperties)

编辑*nifi-1/conf/nifi . properties*并设置以下属性:

```
nifi.state.management.embedded.zookeeper.start=true

nifi.remote.input.secure=true

nifi.web.http.port=
nifi.web.https.host=localhost
nifi.web.https.port=8443

nifi.security.keystore=/path/to/keystore.jks
nifi.security.keystoreType=jks
nifi.security.keystorePasswd=yourpassword
nifi.security.keyPasswd=yourpassword
nifi.security.truststore=/path/to/truststore.jks
nifi.security.truststoreType=jks
nifi.security.truststorePasswd=yourpassword

nifi.cluster.protocol.is.secure=true

nifi.cluster.is.node=true
nifi.cluster.node.protocol.port=8088
nifi.cluster.flow.election.max.candidates=2

nifi.zookeeper.connect.string=localhost:2181 
```

编辑*nifi-2/conf/nifi . properties*并设置以下属性:

```
nifi.remote.input.secure=true

nifi.web.http.port=
nifi.web.https.host=localhost
nifi.web.https.port=7443

nifi.security.keystore=/path/to/keystore.jks
nifi.security.keystoreType=jks
nifi.security.keystorePasswd=yourpassword
nifi.security.keyPasswd=yourpassword
nifi.security.truststore=/path/to/truststore.jks
nifi.security.truststoreType=jks
nifi.security.truststorePasswd=yourpassword

nifi.cluster.protocol.is.secure=true

nifi.cluster.is.node=true
nifi.cluster.node.protocol.port=7088
nifi.cluster.flow.election.max.candidates=2
nifi.cluster.load.balance.port=6343

nifi.zookeeper.connect.string=localhost:2181 
```

注意:对于 nifi-1，我保留了 nifi.cluster.load.balance.port 的默认值，因为我们在同一台主机上运行两个节点，所以我们需要为 nifi-2 设置一个不同的值。

### 配置授权者. xml

两个节点的 authorizers.xml 配置应该相同，因此编辑*nifi-1/conf/authorizers . XML*和 _nifi-2/conf/authorizers.xml_ 并执行以下操作…

修改用户组提供者，为初始管理员和集群节点声明初始用户:

```
<userGroupProvider>
    <identifier>file-user-group-provider</identifier>
    <class>org.apache.nifi.authorization.FileUserGroupProvider</class>
    <property name="Users File">./conf/users.xml</property>
    <property name="Legacy Authorized Users File"></property>
    <property name="Initial User Identity 1">CN=bbende, OU=ApacheNiFi</property>
    <property name="Initial User Identity 2">CN=localhost, OU=NIFI</property>
</userGroupProvider> 
```

注意:在这种情况下，由于两个节点都运行在同一台主机上，并且使用同一个密钥库，因此只需要一个用户代表两个节点，但是在实际设置中会有多个节点身份。

注意:用户身份区分大小写和空白，所以要确保输入的身份是准确的，因为它们在发出请求时会被 NiFi 看到。

修改 accessPolicyProvider 以声明哪个用户是初始管理员，哪个是节点标识:

```
<accessPolicyProvider>
    <identifier>file-access-policy-provider</identifier>
    <class>org.apache.nifi.authorization.FileAccessPolicyProvider</class>
    <property name="User Group Provider">file-user-group-provider</property>
    <property name="Authorizations File">./conf/authorizations.xml</property>
    <property name="Initial Admin Identity">CN=bbende, OU=ApacheNiFi</property>
    <property name="Legacy Authorized Users File"></property>
    <property name="Node Identity 1">CN=localhost, OU=NIFI</property>
    <property name="Node Group"></property>
</accessPolicyProvider> 
```

### 配置状态管理. xml

两个节点的 state-management.xml 配置应该是相同的，因此编辑*nifi-1/conf/state-management . XML*和 _ nifi-2/conf/state-management . XML _ 并执行以下操作…

修改集群状态提供程序以指定 ZooKeeper 连接字符串:

```
<cluster-provider>
    <id>zk-provider</id>
    <class>org.apache.nifi.controller.state.providers.zookeeper.ZooKeeperStateProvider</class>
    <property name="Connect String">localhost:2181</property>
    <property name="Root Node">/nifi</property>
    <property name="Session Timeout">10 seconds</property>
    <property name="Access Control">Open</property>
</cluster-provider> 
```

### 配置 zookeeper.properties

两个节点的 zookeeper.properties 的配置应该是相同的，因此编辑*nifi-1/conf/zookeeper . properties*和 _ nifi-2/conf/zookeeper . properties _ 并执行以下操作…

指定属于 ZooKeeper ensamble 的服务器:

```
server.1=localhost:2888:3888 
```

### 创建 ZooKeeper myid 文件

因为嵌入式 ZooKeeper 只会在第一个节点上启动，所以我们只需要对 nifi-1 做如下操作:

```
mkdir nifi-1/state
mkdir nifi-1/state/zookeeper
echo 1 > nifi-1/state/zookeeper/myid 
```

### 启动集群

```
./nifi-1/bin/nifi.sh start && ./nifi-2/bin/nifi.sh start 
```

此时，假设您的浏览器中有初始管理员的客户端证书(p12)(“CN = bbende，OU=ApacheNiFi”)，那么您可以通过[https://localhost:8443/nifi](https://localhost:8443/nifi)或[https://localhost:7443/nifi](https://localhost:7443/nifi)访问您的集群。

### 常见问题

*嵌入式 ZooKeeper 无法创建目录*

```
 java.io.IOException: Unable to create data directory ./state/zookeeper/version-2
     at org.apache.zookeeper.server.persistence.FileTxnSnapLog.<init>(FileTxnSnapLog.java:85)
     at org.apache.nifi.controller.state.server.ZooKeeperStateServer.startStandalone(ZooKeeperStateServer.java:85)
     ... 51 common frames omitted 
```

嵌入式 ZooKeeper 经常发生这种情况，通常你可以尝试重新开始，它会自行解决。

*初始管理员身份或节点身份错误*

如果您在任何身份中输入了错误，并且需要编辑 authorizers.xml，则必须在重新启动之前从每个节点中删除 conf/users.xml 和 conf/authorizations.xml。

原因是只有当 NiFi 在没有其他用户、组或策略的情况下启动时，才会使用初始管理员和节点身份。如果您不删除用户和授权文件，那么您的更改将被忽略。