# Apache Knox -代理 Apache NiFi

> 原文：<https://dev.to/risdenk/apache-knox---proxying-apache-nifi-pfe>

### TL；速度三角形定位法(dead reckoning)

有一个 Apache Knox 和 Apache NiFi 的工作示例，Docker [在这里](https://github.com/risdenk/knox_nifi_testing)。关于如何设置和调试信息的所有细节，请继续阅读。

## 概述

[Apache Knox](https://knox.apache.org/) 是一个反向代理，它简化了 Kerberos 安全 [Apache Hadoop](https://hadoop.apache.org/) 集群和其他相关组件前面的安全性。但是，使用 Knox 并不要求必须有 Apache Hadoop。Apache Knox 的一个用例是为不同的组件和 ui 提供单一的入口点。Apache Knox 可以代理这些组件和 ui。

前几天在 [Apache Knox 用户邮件列表](https://mail-archives.apache.org/mod_mbox/knox-user/)上，有一个关于使用 Apache Knox 连接 [Apache NiFi](https://nifi.apache.org/) 的[有趣帖子](http://mail-archives.apache.org/mod_mbox/knox-user/201803.mbox/%3CCACSRyKLMP9H--uFRwcGYO4Y0x-hHcgZPe1hc8kgp5Gn8SWi2%3Dw%40mail.gmail.com%3E)。消息作者在 Apache Knox 和 Apache NiFi 之间遇到了多个 TLS/SSL 问题。这篇文章描述了如何成功设置 Knox 来代理 Apache NiFi。

## Apache Knox 支持 Apache NiFi

[Apache Knox](https://knox.apache.org) 在 0.14.0 版本中凭借 [KNOX-970](https://issues.apache.org/jira/browse/KNOX-970) 获得了对 [Apache NiFi](https://nifi.apache.org) 的支持。Knox 0.14.0 用户指南没有关于如何设置代理 NiFi 的 Knox 的信息。事先了解 Apache Knox 的工作方式将有助于解决集成问题。还有一些来自 [Hortonworks](https://hortonworks.com/) 的[Horton works 数据流(HDF)文档](https://docs.hortonworks.com/HDPDocuments/HDF3/HDF-3.1.1/bk_security/content/configure_knox_for_nifi.html)，用于使用 Apache NiFi 设置 Apache Knox。

## 设置 Apache Knox 和 Apache NiFi

安装 Apache Knox 和 Apache NiFi 涉及多个组件。Apache NiFi TLS 工具包帮助创建适用于 Apache NiFi 以及集成 Apache Knox 的 TLS/SSL 证书。Apache NiFi 本身将需要设置。最后，Apache Knox 项目将被用作演示 LDAP 服务器和代理服务器。下面的指南介绍了如何设置受 TLS 保护的 Apache NiFi，如何设置 Apache Knox，以及如何集成 Apache Knox 和 Apache NiFi。

### 假设

*   为浏览器运行 macOS
*   Linux/macOS 上的 Apache NiFi 1.5.0
*   Linux/macOS 上的 Apache Knox 1.0.0

### 使用 Apache NiFi TLS 工具包生成 SSL/TLS 证书

#### 下载并解压 Apache NiFi TLS 工具包

[下载 Apache NiFi TLS 工具包](https://www.apache.org/dyn/closer.lua?path=/nifi/1.5.0/nifi-toolkit-1.5.0-bin.tar.gz)，解压`.tar.gz`文件。

```
tar zxvf nifi-toolkit-1.5.0-bin.tar.gz
cd nifi-toolkit-1.5.0 
```

Enter fullscreen mode Exit fullscreen mode

#### 为 Apache NiFi 主机、Apache Knox 主机和初始管理员用户生成证书

确保填写`NIFI_FQDN_HOSTNAME`和`KNOX_FQDN_HOSTNAME`。

```
NIFI_FQDN_HOSTNAME=
./bin/tls-toolkit.sh standalone --hostnames $NIFI_FQDN_HOSTNAME --isOverwrite --trustStorePassword truststore --keyStorePassword nifi --keyStoreType jks

KNOX_FQDN_HOSTNAME=
./bin/tls-toolkit.sh standalone --hostnames $KNOX_FQDN_HOSTNAME --isOverwrite --trustStorePassword truststore --keyStorePassword knox --keyStoreType jks

./bin/tls-toolkit.sh standalone --isOverwrite --clientCertDn CN=nifi-admin,OU=NIFI --clientCertPassword nifi-admin 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**以上密码不安全。这是为了演示的目的。移除`--keyStorePassword`、`--trustStorePassword`和`--clientCertPassword`选项将使 TLS 工具包生成随机密码。

#### 将生成的证书复制到正确的主机上

对于为其生成 TLS/SSL 证书的每台主机，将 FQDN 主机名文件夹复制到节点上的`/opt/certs/`。

**注意:**如果您想使用不同的位置，稍后跟踪即可。

#### 为 Apache NiFi TLS/SSL 认证配置浏览器

因为 Apache NiFi 使用双向 SSL，所以您浏览器必须配置为提供客户端 SSL 证书。这是上面作为 TLS 工具包步骤的一部分生成的。

以下步骤假设您使用的是 macOS。如果你没有，那么在谷歌上搜索“导入 ssl 证书浏览器”。

*   打开“钥匙串访问”
*   单击文件->导入项目
*   导入`CN=nifi-admin_OU=NIFI.p12`文件
*   出现提示时，输入密码`nifi-admin`

### 设置 Apache NiFi

#### 下载并解压 Apache NiFi

[下载 Apache NiFi](https://www.apache.org/dyn/closer.lua?path=/nifi/1.5.0/nifi-1.5.0-bin.tar.gz) 并解压`.tar.gz`文件。

```
tar zxvf nifi-1.5.0-bin.tar.gz
cd nifi-1.5.0 
```

Enter fullscreen mode Exit fullscreen mode

#### 为 TLS/SSL 配置 Apache NiFi

在`conf/nifi.properties`中设置以下属性，用节点的正确值替换`NIFI_FQDN_HOST`:

```
nifi.web.http.port=
nifi.web.https.port=9091
nifi.remote.input.secure=true
nifi.cluster.protocol.is.secure=false
nifi.security.keystore=/opt/certs/NIFI_FQDN_HOSTNAME/keystore.jks
nifi.security.keystoreType=JKS
nifi.security.keystorePasswd=nifi
nifi.security.truststore=/opt/certs/NIFI_FQDN_HOSTNAME/truststore.jks
nifi.security.truststoreType=JKS
nifi.security.truststorePassword=truststore
nifi.security.needClientAuth=true
nifi.web.proxy.context.path=gateway/sandbox/nifi-app 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**如果您让 Apache NiFi TLS 工具包生成随机密码，那么它们应该在这里指定。如果您选择了不同于`/opt/certs`的位置，也将它放在这里。

在`conf/authorizers.xml`中设置以下

```
# Line 52
<property name="Initial User Identity 1">CN=nifi-admin, OU=NIFI</property>

# Line 247
<property name="Initial Admin Identity">CN=nifi-admin, OU=NIFI</property> 
```

Enter fullscreen mode Exit fullscreen mode

**注:**

*   “，OU”之间的空格非常重要。Apache NiFi 对这个空白非常敏感。
*   如果您在启动 Apache NiFi 之后更改了初始管理员身份，您必须删除生成的`conf/users.xml`和`conf/authorizations.xml`。

#### 启动 Apache NiFi 并检查 UI

启动 Apache NiFi

```
./bin/nifi.sh start 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中打开 Apache NiFi UI

*   `https://NIFI_HOST:9091/nifi`
    *   `NIFI_HOST` -这应该是`NIFI_HOST`的全限定域名
    *   这应该提示输入客户端证书，选择`CN=nifi-admin,OU=NIFI`证书。

**注意:**Apache NiFi 可能需要一两分钟才能启动。查看`logs`目录了解更多详情。

您应该看到这个:

[![](../Images/042356d29a4efdd8a5e0655d7a579a0c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xZ7YYUzp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui.png)

#### 为 Apache Knox 配置 Apache NiFi

为了使 Apache Knox 能够代理对 Apache NiFi 的请求，Apache NiFi 中需要有一个 Apache Knox 用户和一个授权策略。

对于下面的每个步骤，用 Apache Knox 主机的正确值替换`KNOX_FQDN_HOSTNAME`。

**将 Apache Knox 用户添加到 Apache NiFi**

|打开 Apache NiFi 汉堡菜单| [![](../Images/bb27c60b7d78b821dc6c81e072cffd4a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--AQl4dZPy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_hamburger.png) |
|点击“用户”| [![](../Images/ae7ed481d5674ba95ef1f7750ec50273.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--QtkRNZKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_users.png) |
|点击“添加用户”图标|[![](../Images/6ba99af427ded9cd207ec46286f06b90.png)|](https://res.cloudinary.com/practicaldev/image/fetch/s--LJIUb7xp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_users_add.png)|
|在“身份”框中输入`CN=KNOX_FQDN_HOSTNAME, OU=NIFI`，点击“确定”|[|![](../Images/bb242cf5e013b4fdbf454e3bde91aee2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O9jQAX78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_user_add_KNOX_FQDN_HOSTNAME.png)|
|用“X”按钮关闭“NiFi 用户”对话框。|

**添加 Apache NiFi 策略，允许 Apache Knox 用户代理请求**

|打开 Apache NiFi 汉堡菜单| [![](../Images/bb27c60b7d78b821dc6c81e072cffd4a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--AQl4dZPy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_hamburger.png) |
|单击“策略”| [![](../Images/d2c933dbc143521a9cd9d473ef99b61a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ocfcIjIr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_policies.png) |
|从策略下拉列表中选择“代理用户请求”，客户端“创建”一个新策略|[![](../Images/c6d54f551b9401cd2706a9150c092ba2.png)|](https://res.cloudinary.com/practicaldev/image/fetch/s--2XslhPaw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_policy_proxy_user_create.png)|
|确保“代理用户请求”被选中，然后单击“添加用户”图标|[|![](../Images/b798d39768da75af140a6cf1ffc7f305.png)|](https://res.cloudinary.com/practicaldev/image/fetch/s--eB-7CZ5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_policy_proxy_user_add.png)|
|添加用户`CN=KNOX_FQDN_HOSTNAME, OU=NIFI`，然后单击“添加”| [|](https://res.cloudinary.com/practicaldev/image/fetch/s--5fFSU-zK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_policy_proxy_user_add_KNOX_FQDN_HOSTNAME.png)

**将`admin`用户添加到 Apache NiFi，以便与 Apache Knox 一起使用**

|打开 Apache NiFi 汉堡菜单| [![](../Images/bb27c60b7d78b821dc6c81e072cffd4a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--AQl4dZPy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_hamburger.png) |
|点击“用户”| [![](../Images/ae7ed481d5674ba95ef1f7750ec50273.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--QtkRNZKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_users.png) |
|点击“添加用户”图标|[![](../Images/6ba99af427ded9cd207ec46286f06b90.png)|](https://res.cloudinary.com/practicaldev/image/fetch/s--LJIUb7xp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_users_add.png)|
|在“身份”框中输入`admin`，点击“确定”|[|![](../Images/6646c1e238fcfdf0c7013c43e45961db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--auVmdoua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_user_add_admin.png)|
|用“X”按钮关闭“NiFi 用户”对话框。|

**添加 Apache NiFi 策略，允许`admin`用户查看用户界面**

|打开 Apache NiFi 汉堡菜单| [![](../Images/bb27c60b7d78b821dc6c81e072cffd4a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--AQl4dZPy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_hamburger.png) |
|点击“策略”| [![](../Images/d2c933dbc143521a9cd9d473ef99b61a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ocfcIjIr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_policies.png) |
|确保“查看用户界面”被选中，点击“添加用户”图标| [![](../Images/63067ff27bf8137e3662ade9a4e004b4.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--VyAkv3XW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_policy_view_interface_add.png) |
|添加用户`admin`，点击“添加”|[|![](../Images/217fa895fb5c18558d54709b333552dc.png)|](https://res.cloudinary.com/practicaldev/image/fetch/s--KiDvOQTv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/nifi_ui_policy_view_interface_add_admin.png)|
|用“X”按钮关闭“访问策略”对话框。|

### 设置 Apache Knox

#### 下载并解压 Apache Knox

[下载 Apache Knox 1.0.0](http://www.apache.org/dyn/closer.cgi/knox/1.0.0/knox-1.0.0.zip) 并解压`.zip`文件

```
unzip knox-1.0.0.zip
cd knox-1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

#### 启动 Apache Knox 演示 LDAP 服务器

```
./bin/ldap.sh start 
```

Enter fullscreen mode Exit fullscreen mode

#### 启动 Apache Knox

```
./bin/knoxcli.sh create-master 
./bin/gateway.sh start 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**跟踪 Apache Knox 主机密，因为您以后会需要它。

#### 检查阿帕奇诺克斯

在浏览器中打开 Apache Knox

*   `https://KNOX_HOST:8443/gateway/manager/admin-ui/index.html`
    *   `KNOX_HOST` -这应该是`KNOX_HOST`的全限定域名
    *   用户名= `admin`
    *   密码= `admin-password`

您应该看到这个:

[![](../Images/1fcffa3359cb974d219e796b43456c8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RkwNhzRt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/knox_manager_example.png)

#### 为 Apache NiFi 设置 Apache Knox

**向 Apache Knox 添加双向 NiFi TLS/SSL 证书**

确保输入您在上面使用的`KNOX_MASTER_SECRET`值。

```
KNOX_MASTER_SECRET=
keytool -importkeystore -destkeypass $KNOX_MASTER_SECRET -srckeystore /opt/certs/knox/keystore.jks -destkeystore data/security/keystores/gateway.jks -deststoretype JKS -srcstorepass keystore -deststorepass $KNOX_MASTER_SECRET -noprompt
keytool -importkeystore -srckeystore /opt/certs/knox/truststore.jks -destkeystore data/security/keystores/gateway.jks -deststoretype JKS -srcstorepass truststore -deststorepass $KNOX_MASTER_SECRET -noprompt

# Restart Apache Knox to pickup new certificates
./bin/gateway.sh stop
./bin/gateway.sh start 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**如果您让 Apache NiFi TLS 工具包生成随机密码，那么它们应该在这里指定。如果您选择了不同于/opt/certs 的位置，也在这里输入。

**将 Apache NiFi 添加到 Apache Knox 沙盒拓扑中**

将以下内容添加到`</topology>`之前的末尾`conf/topologies/sandbox.xml`。将`NIFI_FQDN_HOSTNAME`替换为 Apache NiFi 主机的正确值。

```
<service>
  <role>NIFI</role>
  <url>https://NIFI_FQDN_HOSTNAME:9091/</url>
  <param name="useTwoWaySsl" value="true" />
</service> 
```

Enter fullscreen mode Exit fullscreen mode

### 检查 Apache NiFi 的 Apache Knox 代理

*   `https://KNOX_HOST:8443/gateway/sandbox/nifi-app/nifi`
    *   `KNOX_HOST` -这应该是`KNOX_HOST`的全限定域名
    *   用户名= `admin`
    *   密码= `admin-password`

您应该看到这个:

[![](../Images/be1fa68de22b4fae1deb56c74b204fb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W8acLMGR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://risdenk.github.io/images/posts/2018-03-18/knox_nifi_ui.png)

## 结论

到现在为止，您应该已经有了一个有效的 [Apache Knox](https://knox.apache.org/) 代理 [Apache NiFi](https://nifi.apache.org/) 设置。按照配置，`admin`用户只有查看 Apache NiFi 界面的权限。您需要添加更多的策略让`admin`用户做更多的事情。Apache Knox 还有`guest`、`sam`和`tom`用户，您可以使用它们来查看不同的用户如何被传递到 Apache Knox。至于接下来的步骤，配置 [Apache NiFi LDAP 授权器](https://nifi.apache.org/docs/nifi-docs/html/administration-guide.html#ldap_login_identity_provider)指向 Apache Knox LDAP 服务器对于简化权限可能是有用的。

如果您使用的是 [Hortonworks 数据流(HDF)](https://hortonworks.com/products/data-platforms/hdf/) 和 [Hortonworks 数据平台(HDP)](https://hortonworks.com/products/data-platforms/hdp/) ， [Apache Ambari](https://ambari.apache.org) 管理上述许多设置。您可以根据这里的建议检查文件值，但是您需要使用 Ambari 来配置不同的设置。

如果您还有其他问题没有在这里找到答案，请联系 Apache Knox 用户邮件列表。

## Apache Knox 和 Apache NiFi 故障排除

以下故障诊断步骤旨在根据上述配置回答有关 Apache Knox 和 Apache NiFi 的问题。在很大程度上，它们对于任何 TLS/SSL 调试都足够通用，并且有望为您指明正确的方向。

### Apache NiFi 日志

Apache NiFi 日志位于您提取了`tar.gz`文件的`logs`目录中。`nifi-app.log`将有 Apache NiFi 应用程序日志。`nifi-user.log`会有 Apache NiFi 用户相关日志。由于 TLS/SSL 配置错误而导致的大多数错误将出现在`nifi-app.log`中。

### Apache Knox 日志

Apache Knox 日志位于您提取了`.zip`文件的`logs`目录中。`gateway.log`会有大部分 TLS/SSL 相关错误。注意，默认情况下，Apache Knox 只记录`ERROR`消息，不会显示更多细节。如果您想了解更多细节，请在`conf/gateway-log4j.properties`中将`log4j.rootLogger=ERROR, drfa`更改为`log4j.rootLogger=INFO, drfa`。

### 浏览器——“您的连接不是私人的”

```
Your connection is not private
Attackers might be trying to steal your information from HOSTNAME (for example, passwords, messages, or credit cards). 
```

Enter fullscreen mode Exit fullscreen mode

您的浏览器不信任 Apache NiFi TLS Toolkit CA。通常，您可以安全地忽略此警告。要修复该警告，您需要让浏览器测试 Apache NiFi TLS Toolkit CA 证书。

### 浏览器-“此站点无法提供安全连接”

```
This site can’t provide a secure connection 
```

Enter fullscreen mode Exit fullscreen mode

当您尝试连接到不支持 SSL/TLS 的端点时，通常会发生此错误。请注意，此消息不会显示“登录证书”。如果消息显示“登录证书”,请参见下面的错误。对于此错误，请参见上面的“为 TLS/SSL 配置 Apache NiFi”中的详细信息。

### 浏览器-“本站无法提供安全连接”-“登录证书”

```
This site can’t provide a secure connection
HOSTNAME didn’t accept your login certificate, or one may not have been provided. 
```

Enter fullscreen mode Exit fullscreen mode

当您的服务(如 Apache NiFi)需要双向 SSL，而浏览器没有客户端证书时，通常会出现这种错误。请参阅上面的“为 Apache NiFi TLS/SSL 身份验证配置浏览器”中的详细信息。

另一个原因可能是 Apache NiFi 不信任所提供的客户端证书。在`nifi.properties`中配置的 Apache NiFi 信任库无法信任提供的证书。在`nifi-app.log`中没有任何表明这一点的错误。请参阅上面的“为 TLS/SSL 配置 Apache NiFi”中的详细信息。

### 阿帕奇 NiFi -启动失败- `... no truststore properties are configured.`

```
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'flowService': FactoryBean threw exception on object creation; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'flowController': FactoryBean threw exception on object creation; nested exception is org.apache.nifi.framework.security.util.SslContextCreationException: Need client auth is set to 'true', but no truststore properties are configured. 
```

Enter fullscreen mode Exit fullscreen mode

如果没有在`nifi.properties`中配置信任库，就会发生这种情况。请参阅上面的“为 TLS/SSL 配置 Apache NiFi”中的详细信息。

### 阿帕奇 NiFi -启动失败- `Keystore was tampered with, or password was incorrect`

```
2018-03-17 12:58:13,580 WARN [main] org.apache.nifi.web.server.JettyServer Failed to start web server... shutting down.
java.io.IOException: Keystore was tampered with, or password was incorrect 
```

Enter fullscreen mode Exit fullscreen mode

当信任库或密钥库的`nifi.properties`中存在错误密码时，会出现此错误。请参阅上面的“为 TLS/SSL 配置 Apache NiFi”中的详细信息。

### Apache NiFi -身份未知的用户- `CN=nifi-admin, OU=NIFI`

```
Unknown user with identity 'CN=nifi-admin, OU=NIFI'. Contact the system administrator. 
```

Enter fullscreen mode Exit fullscreen mode

很可能意味着`conf/authorizers.xml`中有一个错别字。“，OU=”之间的空格非常重要。请参阅上面的“为 TLS/SSL 配置 Apache NiFi”中的详细信息。

### Apache NiFi -权限不足-代理不可信

```
Insufficient Permissions
Untrusted proxy CN=KNOX_FQDN_HOSTNAME, OU=NIFI 
```

Enter fullscreen mode Exit fullscreen mode

这是由于 Apache NiFi 没有允许 Apache Knox 充当可信代理的用户或策略。请参阅上面的“为 Apache Knox 配置 Apache NiFi”中的详细信息。

### Apache NiFi -身份未知的用户- `CN=admin, OU=NIFI`

```
Unknown user with identity 'CN=admin, OU=NIFI'. Contact the system administrator. 
```

Enter fullscreen mode Exit fullscreen mode

很可能意味着用户`admin`没有在 Apache NiFi 中设置。请参阅上面的“将管理员用户添加到 Apache NiFi 以与 Apache Knox 一起使用”中的详细信息。

### Apache NiFi -权限不足-无法查看用户界面

```
Insufficient Permissions
Unable to view the user interface. Contact the system administrator. 
```

Enter fullscreen mode Exit fullscreen mode

很可能意味着您错过了将`admin`用户添加到`view user interface` Apache NiFi 策略。请参阅上面的“添加 Apache NiFi 策略以允许管理员用户查看用户界面”中的详细信息。

### Apache Knox -浏览器-重复登录框

这意味着您输入了错误的用户名/密码，或者 Apache Knox LDAP 服务器没有启动。请参阅上面的“启动 Apache Knox 演示 LDAP 服务器”中的详细信息。

### 阿帕奇诺克斯——启动失败——`UnrecoverableKeyException: Cannot recover key`

```
2018-03-17 12:49:02,873 FATAL knox.gateway (GatewayServer.java:main(163)) - Failed to start gateway: java.security.UnrecoverableKeyException: Cannot recover key 
```

Enter fullscreen mode Exit fullscreen mode

当`gateway.jks`中有一个别名没有将`keypassword`设置为 Apache Knox 主机密时，就会出现这种情况。当您从像`KNOX_FQDN_HOSTNAME/keystore.jks`这样的密钥库中导入证书，并且没有将`-destkeypass`设置为 Apache Knox 主秘密时，就会发生这种情况。请参阅上面的“将双向 NiFi TLS/SSL 证书添加到 Apache Knox”中的详细信息。

### 阿帕奇 Knox -浏览器- 500 错误

```
HTTP ERROR 500
Problem accessing /gateway/sandbox/nifi-app/nifi. Reason:
Server Error 
```

Enter fullscreen mode Exit fullscreen mode

在 Apache Knox 中，这种错误可以通过多种方式表现出来。您需要在`logs/gateway.log`查看 Apache Knox 日志，并查找具体的错误。一些错误如下:

#### Apache Knox -主机不可达？

```
2018-03-17 12:53:39,558 WARN knox.gateway (DefaultDispatch.java:executeOutboundRequest(147)) - Connection exception dispatching request: https://nifi/?user.name=admin java.net.UnknownHostException: nifi: nodename nor servname provided, or not known
java.net.UnknownHostException: nifi: nodename nor servname provided, or not known 
```

Enter fullscreen mode Exit fullscreen mode

当您在 Apache Knox 拓扑 xml 文件中为 Apache NiFi 服务设置了错误的`<url></url>`时，就会出现此错误。请参阅上面的“将 Apache NiFi 添加到 Apache Knox 沙箱拓扑”中的详细信息。

#### 阿帕奇诺克斯- `PKIX path building failed`

```
2018-03-17 12:35:46,420 WARN knox.gateway (DefaultDispatch.java:executeOutboundRequest(147)) - Connection exception dispatching request: https://NIFI_FQDN_HOSTNAME:9091/nifi?user.name=admin javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target 
```

Enter fullscreen mode Exit fullscreen mode

当 Apache Knox 不信任 Apache NiFi 的证书时，会出现错误“PKIX 路径构建失败”和“无法找到有效的证书路径”。很可能 Apache NiFi TLS 工具包为 Apache Knox 生成的`truststore.jks`证书没有导入到`gateway.jks`中。请参阅上面的“将双向 NiFi TLS/SSL 证书添加到 Apache Knox”中的详细信息。

发生这种情况的第二个原因是 Apache NiFi 服务的 Apache Knox 拓扑 xml 文件中缺少`<param name="useTwoWaySsl" value="true" />`。要检查并修复这个问题，请参阅上面的“将 Apache NiFi 添加到 Apache Knox 沙盒拓扑”中的详细信息。

#### 阿帕奇诺克斯- `Received fatal alert: bad_certificate`

```
2018-03-17 12:41:39,413 WARN knox.gateway (DefaultDispatch.java:executeOutboundRequest(147)) - Connection exception dispatching request: https://NIFI_FQDN_HOSTNAME:9091/nifi?user.name=admin javax.net.ssl.SSLHandshakeException: Received fatal alert: bad_certificate
javax.net.ssl.SSLHandshakeException: Received fatal alert: bad_certificate 
```

Enter fullscreen mode Exit fullscreen mode

这很可能是由于 Apache NiFi TLS 工具包 Apache Knox 密钥库没有导入到`gateway.jks`中。要检查并修复此问题，请参阅上面的“向 Apache Knox 添加双向 NiFi TLS/SSL 证书”中的详细信息。

另一个原因可能是 Apache NiFi 不信任所提供的客户端证书。在`nifi.properties`中配置的 Apache NiFi 信任库无法信任提供的证书。在`nifi-app.log`中没有任何表明这一点的错误。请参阅上面的“为 TLS/SSL 配置 Apache NiFi”中的详细信息。

#### 阿帕奇诺克斯- `SSLPeerUnverifiedException`

```
javax.net.ssl.SSLPeerUnverifiedException: Certificate for <NIFI-IP-ADDR> doesn't match any of the subject alternative names: [NIFI-IP-ADDR] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的博客中，我不推荐使用 IP 地址，因为它们不适合 TLS/SSL 证书。Apache NiFi TLS 工具包[不支持为 IP 地址](https://github.com/apache/nifi/blob/master/nifi-toolkit/nifi-toolkit-tls/src/main/java/org/apache/nifi/toolkit/tls/util/TlsHelper.java#L226)生成证书。上面的错误是因为证书认为 IP 地址实际上是 DNS 条目，不匹配。

### 没有提到的事情？

如果您还有其他问题没有在这里找到答案，请联系 Apache Knox 用户邮件列表。