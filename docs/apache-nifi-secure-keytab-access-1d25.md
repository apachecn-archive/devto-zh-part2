# Apache NiFi -安全密钥表访问

> 原文：<https://dev.to/bbende/apache-nifi-secure-keytab-access-1d25>

在多租户环境中，一种典型的方法是为每个团队创建一个流程组，并应用限制访问的安全策略，这样一个给定的团队只能访问它自己的组。

在一个流程组中，一个团队可能需要与一个由 Kerberos 保护的外部系统进行交互。例如，假设有一个 Kerberized 化的 HDFS 集群，有两个团队，每个团队都有一个访问该集群的密钥表。

为了创建将数据发送到 Kerberized 化的 HDFS 集群的 PutHDFS 处理器，该处理器必须配置有主体和密钥表，并且密钥表必须位于 NiFi JVM 可以访问的文件系统上。此外，启动 NiFi JVM 的操作系统用户必须能够读取 keytab。

[![](img/363afde6ab6f5d637c45cc2092b59e80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rhH6Exou--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-keytab-isolation/01-nifi-keytab-setup-before.png)

这里的问题是 keytab 属性是一个自由格式的文本字段，因此没有办法阻止团队 1 输入团队 2 的 Keytab。

为了解决这个问题，1.6.0 版本引入了一个 Keytab 控制器服务，以及更细粒度的受限组件，它们可以一起用于正确保护对 Keytab 的访问。

### 按键控制器服务

保护 keytab 访问的第一步是创建一个控制器服务，在其中可以定义主体和 keytab，然后更新所有具有自由格式属性的处理器，使其也具有 keytab 服务的属性。

注意:为了给每个人一个迁移到新方法的机会，在这个版本中没有删除自由格式的属性，但是我们将在后面详细讨论这个问题。

使用控制器服务的好处是，我们可以通过安全策略限制哪些用户能够使用该服务。在我们上面的例子中，一个管理员用户可以为团队 1 创建一个指向团队 1 的 keytab 的 Keytab 服务，然后创建一个只允许团队 1 访问该服务的策略。管理员用户可以对团队 2 做同样的事情。

[![](img/d076eba722511b24c108e8f2211e75bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_pHi_m2T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-keytab-isolation/02-nifi-keytab-setup-after.png)

这使我们非常接近我们想要的，但是如果团队 1 中的某个人忽略了为他们创建的服务，只是创建了另一个指向团队 2 的 keytab 的 Keytab 服务怎么办？

我们需要一种方法来限制谁可以创建 Keytab 控制器服务，以便只有 admin 用户可以创建它们，而团队 1 或团队 2 中的任何用户都不能创建。

### 粒状限制成分

NiFi 1.0.0 版本引入了“受限组件”的概念。我们的想法是，一些组件可以做一些我们不希望所有用户都做的事情，比如访问本地文件系统，或者执行代码。这些类型的组件要求用户拥有额外的“受限”权限才能创建组件。

1.6.0 版本引入了更精细的受限组件类别:

*   读取文件系统
*   写文件系统
*   执行代码
*   访问键标签
*   导出-nifi-详细信息

受限权限由右上角的全局策略菜单控制:

[![](img/cf78ec7bf5e3a2bdc33c80e4a0733904.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SRSHKpqU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-keytab-isolation/03-nifi-restricted-permissions.png)

在我们前面的例子中，如果我们只给 admin 用户“access-keytab”权限，那么团队 1 和团队 2 中的用户将不能创建 keytab 控制器服务，从而迫使他们使用他们被授予权限的 Keytab 服务。

### 防止自由格式的 Keytab 属性

这个难题的最后一部分是防止使用旧的自由格式的 keytab 属性，这些属性是为了向后兼容而留下的。这可以通过在 nifi-env.sh:
中配置环境变量来实现

```
export NIFI_ALLOW_EXPLICIT_KEYTAB=true 
```

将此值设置为 *false* 将在任何输入自由格式 keytab 属性的组件中产生验证错误，这意味着除非组件使用 Keytab 控制器服务，否则无法启动该组件。

### 总结

总之…

*   只有管理员用户拥有“access-keytab”权限
*   管理员用户创建 Keytab 服务并分配适当的权限
*   普通用户可以使用他们有权访问的 Keytab 服务，但不能创建新的服务
*   通过 NIFI_ALLOW_EXPLICIT_KEYTAB 环境变量，不允许使用传统的自由格式属性