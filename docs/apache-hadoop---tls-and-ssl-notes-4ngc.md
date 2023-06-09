# Apache Hadoop - TLS 和 SSL 注释

> 原文：<https://dev.to/risdenk/apache-hadoop---tls-and-ssl-notes-4ngc>

### 概述

几年来，我一直在收集关于 TLS/SSL 的笔记。其中大部分与 [Apache Hadoop](https://hadoop.apache.org/) 相关，但其他的更为通用。当[狮子狗](https://en.wikipedia.org/wiki/POODLE)和[心脏出血](https://en.wikipedia.org/wiki/Heartbleed)漏洞发布时，我正在咨询。以下是 TLS/SSL 相关参考资料的集合。不能保证它们是最新的，但是在一个地方有参考资料是有帮助的。

### TLS/SSL 通用

*   TLS/SSL 的精彩讲解:[http://www.zytrax.com/tech/survival/ssl.html](http://www.zytrax.com/tech/survival/ssl.html)
*   SSL Linux 证书位置:[UNIX Linux 上的 http://serverfault.com/questions/62496/ssl 证书位置](http://serverfault.com/questions/62496/ssl%C2%ADcertificate%C2%ADlocation%C2%ADon%C2%ADunix%C2%ADlinux)
*   SSL vs TLS:[http://security.stackexchange.com/questions/5126/whats SSL TLS 和 https 的区别](http://security.stackexchange.com/questions/5126/whats%C2%ADthe%C2%ADdifference%C2%ADbetween%C2%ADssl%C2%ADtls%C2%ADand%C2%ADhttps)

### 证书类型

*   [http://unmitigatedrisk.com/?p=381](http://unmitigatedrisk.com/?p=381)
*   [http://www . cloud era . com/content/cloud era/en/documentation/core/latest/topics/cm _ SG _ guide _ SSL _ certs . html](http://www.cloudera.com/content/cloudera/en/documentation/core/latest/topics/cm_sg_guide_ssl_certs.html)

### 生成证书

*   [https://www . SSL shopper . com/article-most-common-OpenSSL-commands . html](https://www.sslshopper.com/article-most-common-openssl-commands.html)
*   [https://support . SSL . com/knowledge base/Article/View/19/0/der-vs-CRT-vs-cer-vs-PEM-certificates-and-how-to-convert-them](https://support.ssl.com/Knowledgebase/Article/View/19/0/der-vs-crt-vs-cer-vs-pem-certificates-and-how-to-convert-them)

### JKS 现有的证书和钥匙

*   [http://stackoverflow.com/questions/11952274/how 我可以从现有证书 abc crt 和 abc 密钥文件中创建密钥库吗](http://stackoverflow.com/questions/11952274/how%C2%ADcan%C2%ADi%C2%ADcreate%C2%ADkeystore%C2%ADfrom%C2%ADan%C2%ADexisting%C2%ADcertificate%C2%ADabc%C2%ADcrt%C2%ADand%C2%ADabc%C2%ADkey%C2%ADfil)

```
openssl pkcs12 ‐export ‐in abc.crt ‐inkey abc.key ‐out abc.p12
keytool ‐importkeystore ‐srckeystore abc.p12 \
        ‐srcstoretype PKCS12 \
        ‐destkeystore abc.jks \
        ‐deststoretype JKS 
```

### 信任 CA 证书

#### [OpenSSL](#openssl)

```
update‐ca‐trust force‐enable
cp CERT.pem /etc/pki/tls/source/anchors/
update‐ca‐trust extract 
```

#### OpenLDAP

`vi /etc/openldap/ldap.conf`

```
...
TLS_CAFILE /etc/pki/
# Comment out TLS_CERTDIR
... 
```

#### Java

```
/usr/java/JAVA_VERSION/jre/lib/security/cacerts
/etc/pki/ca‐trust/extracted/java/cacerts 
```

*   [https://bugzilla.redhat.com/show_bug.cgi?id=1056224](https://bugzilla.redhat.com/show_bug.cgi?id=1056224)

### 狮子狗 SSLv3

#### 什么是狮子狗？

*   [https://poodle.io/servers.html](https://poodle.io/servers.html)
*   [https://www.openssl.org/docs/apps/ciphers.html#SSL 3.0 版密码套件](https://www.openssl.org/docs/apps/ciphers.html#SSL%C2%ADv3.0%C2%ADcipher%C2%ADsuites)

#### 为狮子狗测试

*   [https://chrisburgess . com . au/how-to-test-for-the-SSL v3-poodle-vulnerability/](https://chrisburgess.com.au/how-to-test-for-the-sslv3-poodle-vulnerability/)

```
# Requires a relatively recent version of openssl installed
openssl s_client ‐connect HOST:PORT ‐ssl3
# ‐tls1 ‐tls1_1 ‐tls1_2
curl ‐v3 ‐i ‐X HEAD https://HOST:PORT 
```

### 为密码套件和协议配置 Hadoop

每个 Hadoop 组件都必须进行配置或拥有适当的版本，以禁用某些 SSL 协议和版本。

#### 安巴里

*   [https://docs . Horton works . com/HDP documents/HDP P3/HDP-3 . 0 . 1/configuring-advanced-security-options-for-ambari/content/ambari _ sec _ optional _ configure _ ciphers _ and _ protocols _ for _ ambari _ server . html](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-advanced-security-options-for-ambari/content/ambari_sec_optional_configure_ciphers_and_protocols_for_ambari_server.html)
    *   `security.server.disabled.ciphers=TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA`
    *   `security.server.disabled.protocols=SSL|SSLv2|SSLv3`

#### Hadoop

*   [https://issues.apache.org/jira/browse/HADOOP-11243](https://issues.apache.org/jira/browse/HADOOP-11243)
    *   TLSv1 的 Hadoop 2.5.2 + 2.6 补丁 SSLFactory
    *   `hadoop.ssl.enabled.protocols=TLSv1`
    *   (JDK6 可以用 TLSv1，JDK7+可以用 TLSv1，TLSv1.1，TLSv1.2)
*   [https://issues.apache.org/jira/browse/HADOOP-11218](https://issues.apache.org/jira/browse/HADOOP-11218)
    *   适用于 TLSv1.1 和 TLSv1.2 的 Hadoop 2.8 补丁 SSLFactory
    *   Java 6 不支持 TLSv1.1+。需要 Java 7。
*   [https://issues.apache.org/jira/browse/HADOOP-11260](https://issues.apache.org/jira/browse/HADOOP-11260)
    *   Hadoop 2.5.2 + 2.6 补丁 Jetty 禁用 SSLv3

#### HTTPFS

*   [https://issues.apache.org/jira/browse/HDFS-7274](https://issues.apache.org/jira/browse/HDFS-7274)
    *   Hadoop 2.5.2 + 2.6 在 HTTPFS 中禁用 SSLv3

#### 鼠标

*   [https://issues.apache.org/jira/browse/HIVE-8675](https://issues.apache.org/jira/browse/HIVE-8675)
    *   Hive 0.14 从支持的协议中删除了 SSLv3
    *   `hive.ssl.protocol.blacklist`
*   [https://issues.apache.org/jira/browse/HIVE-8827](https://issues.apache.org/jira/browse/HIVE-8827)
    *   Hive 1.0 将`SSLv2Hello`添加回支持的协议
    *   `hive.ssl.protocol.blacklist=SSLv2,SSLv3`

#### Oozie

*   [https://issues.apache.org/jira/browse/OOZIE-2034](https://issues.apache.org/jira/browse/OOZIE-2034)
    *   Oozie 4.1.0 禁用 SSLv3
*   [https://issues.apache.org/jira/browse/OOZIE-2037](https://issues.apache.org/jira/browse/OOZIE-2037)
    *   添加对 TLSv1.1 和 TLSv1.2 的支持
    *   Java 6 不支持 TLSv1.1+。需要 Java 7。取决于 OOZIE 2036

#### 水槽

*   [https://issues.apache.org/jira/browse/FLUME-2520](https://issues.apache.org/jira/browse/FLUME-2520)
    *   Flume 1.5.1 HTTPSource 禁用 SSLv3

#### 色相

*   [https://issues.cloudera.org/browse/HUE-2438](https://issues.cloudera.org/browse/HUE-2438)
    *   色调 3.8 禁用 SSLv3
    *   `/usr/lib/hue/desktop/core/src/desktop/lib/wsgiserver.py`的第 1670 行
    *   `ctx.set_options(SSL.OP_NO_SSLv2 | SSL.OP_NO_SSLv3)`
    *   `ssl_cipher_list = "DEFAULT:!aNULL:!eNULL:!LOW:!EXPORT:!SSLv2"`(默认)

#### 游侠

*   [https://issues.apache.org/jira/browse/RANGER-158](https://issues.apache.org/jira/browse/RANGER-158)
    *   Ranger 0.4.0 Ranger 管理员和用户验证禁用 SSLv3

#### 诺克斯

*   [https://issues.apache.org/jira/browse/KNOX-455](https://issues.apache.org/jira/browse/KNOX-455)
    *   Knox 0.5.0 禁用 SSLv3
    *   `ssl.exclude.protocols`

#### 风暴

*   [https://issues.apache.org/jira/browse/STORM-640](https://issues.apache.org/jira/browse/STORM-640)
    *   Storm 0.10.0 禁用 SSLv3

### 资源

*   [http://sys advent . blogspot . co . uk/2010/12/day-3-debugging-SSL ls-with-OpenSSL 1 . html](http://sysadvent.blogspot.co.uk/2010/12/day-3-debugging-ssltls-with-openssl1.html)
*   [https://gist.github.com/jankronquist/6412839](https://gist.github.com/jankronquist/6412839)