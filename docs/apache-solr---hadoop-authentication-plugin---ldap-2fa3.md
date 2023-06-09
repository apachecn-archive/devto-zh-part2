# Apache Solr - Hadoop 认证插件- LDAP

> 原文：<https://dev.to/risdenk/apache-solr---hadoop-authentication-plugin---ldap-2fa3>

### 概述

Apache Solr 是一个基于 [Apache Lucene](https://lucene.apache.org/solr/) 的全文搜索引擎。我过去被问到的一个问题是 LDAP 对 Apache Solr 认证的支持。虽然有商业上的附加产品增加了 LDAP 支持，如 [Lucidworks Fusion](https://lucidworks.com/products/fusion-server/) ，但是 Apache Solr 没有现成的 LDAP 认证插件。让我们探索一下 Apache Solr 的身份验证的当前状态。

### Apache Solr 和认证

Apache Solr 5.2 发布了一个来自 [SOLR-7274](https://issues.apache.org/jira/browse/SOLR-7274) 的可插拔认证模块。这为将来的认证实现铺平了道路，比如`BasicAuth` ( [SOLR-7692](https://issues.apache.org/jira/browse/SOLR-7692) )和 Kerberos ( [SOLR-7468](https://issues.apache.org/jira/browse/SOLR-7468) )。在 Apache Solr 6.1 中，委托令牌支持( [SOLR-9200](https://issues.apache.org/jira/browse/SOLR-9200) )被添加到 Kerberos 身份验证插件中。Apache Solr 6.4 增加了一个重要的特性，将 [Hadoop 认证框架](https://hadoop.apache.org/docs/current/hadoop-auth/Configuration.html)作为认证插件( [SOLR-9513](https://issues.apache.org/jira/browse/SOLR-9513) )直接挂接到 Solr 中。最近没有更多关于认证插件的工作。目前正在做一些工作来添加一个 JWT 认证插件( [SOLR-12121](https://issues.apache.org/jira/browse/SOLR-12121) )。每个 Solr 身份验证插件都提供了额外的 Solr 身份验证功能。

### Hadoop 认证、LDAP 和 Apache Solr

#### Hadoop 认证框架概述

[Hadoop 认证框架](https://hadoop.apache.org/docs/current/hadoop-auth/Configuration.html)提供了额外的功能，因为它增加了后端。后端目前包括 Kerberos、AltKerberos、LDAP、SignerSecretProvider 和 Multi-scheme。每个都可以配置为支持不同的身份验证需求。

#### Apache Solr 和 Hadoop 认证框架

由于 [SOLR-9513](https://issues.apache.org/jira/browse/SOLR-9513) 的工作，Apache Solr 6.4+支持 Hadoop 认证框架。 [Apache Solr 参考指南](https://lucene.apache.org/solr/guide/7_5/hadoop-authentication-plugin.html)提供了如何使用 Hadoop 认证插件的指导。所有必要的配置参数都可以传递给 Hadoop 认证框架。随着更多后端被添加到 Hadoop 认证框架中，Apache Solr 只需升级 Hadoop depdendency 即可获得支持。

#### Apache Solr 7.5 和 LDAP

Hadoop 2.8.0 中增加了对 Hadoop 认证框架的 LDAP 支持( [HADOOP-12082](https://issues.apache.org/jira/browse/HADOOP-12082) )。可悲的是，Apache Solr 7.5 的 Hadoop 依赖只存在于 [2.7.4](https://github.com/apache/lucene-solr/blob/branch_7_5/lucene/ivy-versions.properties#L156) 上。这意味着，当您尝试使用 LDAP 配置 HadoopAuthenticationPlugin `时，您将得到以下错误:

``
初始化 org . Apache . Solr . security . Hadoop auth plugin 时出错:
javax . servlet . servlet exception:Java . lang . classnotfoundexception:LDAP`

 ``

 `#### 手动升级 Apache Solr Hadoop 依赖

**注意:**我不建议在实验和看看有什么可能之外这样做。

我组装了一个[简单测试项目](https://github.com/risdenk/test-solr-hadoopauthenticationplugin-ldap)，用 2.9.1 jars“手动”替换 Hadoop 2 . 7 . 4 jar。这是为了测试是否可以用 LDAP 配置 Solr `HadoopAuthenticationPlugin`。我能够使用下面的`security.json`文件配置 Solr，以使用 Hadoop 2.9.1 LDAP 后端。

``
{
“认证”:{
“类”:“solr。HadoopAuthPlugin "，
" sysproprefix ":" Solr。"、
"type": "ldap "、
" authConfigs ":[
" LDAP . provider URL "、
"ldap.basedn "、
"ldap.enablestarttls"
]、
" default configs ":{
" LDAP . provider URL ":" LDAP://LDAP "、
"ldap.basedn": "dc=example，dc=org "，
。`

 ``

 `通过这种配置和 Hadoop 2.9.1 jars，Apache Solr 受到了 LDAP 的保护。应该进行更多的测试，以了解它如何处理多个节点和其他类型的集成。Hadoop 身份验证框架对 LDAP 的支持有限，但它应该适用于某些用例。

### 结论

从 7.5 开始，Apache Solr 目前对 Hadoop 认证框架的支持是有限的。这是由于对 Apache Hadoop 2.7.4 的依赖性。当 Hadoop 依赖关系在 Apache Solr 中得到更新( [SOLR-9515](https://issues.apache.org/jira/browse/SOLR-9515) )时，至少会有一些对 LDAP 集成的初始支持。

#### 参考文献

*   [https://lucene.apache.org/solr/guide/7_5/securing-solr.html](https://lucene.apache.org/solr/guide/7_5/securing-solr.html)
*   [https://Lucene . Apache . org/Solr/guide/7 _ 5/Hadoop-authentic ation-plugin . html](https://lucene.apache.org/solr/guide/7_5/hadoop-authentication-plugin.html)
*   [https://issues.apache.org/jira/browse/SOLR-9513](https://issues.apache.org/jira/browse/SOLR-9513)
*   [https://stack overflow . com/questions/50647431/LDAP-integration-with-Solr](https://stackoverflow.com/questions/50647431/ldap-integration-with-solr)
*   [https://community . Horton works . com/questions/130989/Solr-LDAP-integration . html](https://community.hortonworks.com/questions/130989/solr-ldap-integration.html)
*   [https://git hub . com/Apache/Lucene-Solr/blob/branch _ 7 _ 5/Lucene/ivy-versions . properties # L156](https://github.com/apache/lucene-solr/blob/branch_7_5/lucene/ivy-versions.properties#L156)
*   [https://issues.apache.org/jira/browse/HADOOP-12082](https://issues.apache.org/jira/browse/HADOOP-12082)