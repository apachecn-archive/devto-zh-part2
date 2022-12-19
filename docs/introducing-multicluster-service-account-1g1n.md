# 介绍多集群服务帐户

> 原文：<https://dev.to/admiralty/introducing-multicluster-service-account-1g1n>

[最初发表于金钟的博客。](https://admiralty.io/blog/introducing-multicluster-service-account/)

如果您操作多个 Kubernetes 集群，并且需要一个集群中的 pod 来调用另一个集群的 Kubernetes API 服务器，我们知道您的痛苦。到目前为止，您要么必须依赖 Kubernetes 之外的异构身份提供者，要么不得不重新利用远程服务帐户。

Kubernetes 集群通常使用两个或更多的认证模块来认证 API 客户端。一个模块认证集群中定义的 Kubernetes *服务账户*；其他模块验证集群外部定义的*用户*。

用户认证取决于您的 Kubernetes 发行版:它可以简单地基于包含密码、令牌和/或客户端证书列表的静态文件；它经常使用开放 ID 连接；它也可以使用其他方法(LDAP、SAML、Kerberos 等。)通过定制的 webhooks 和代理。即使有了通用的 Open ID Connect 模块，后台身份提供者也依赖于您的发行版:Google Cloud IAM、Azure Active Directory 等等。

因此，在混合和/或多云架构中，Kubernetes 用户认证可以是异构的。尝试以“用户”身份调用其他集群的 API 服务器的集群中的 pod 可能必须登录到多个身份提供者。即使您跨集群使用单个身份提供者，您仍然要负责在 pod 内安装秘密— [client-go 的身份验证自动化](https://github.com/kubernetes/client-go/tree/master/plugin/pkg/client/auth)是为集群外的、面向人的工具而设计的。为了简单起见，我们需要为 pod 商定一个通用的、自动化的多集群身份提供者。

Kubernetes 很可能就是我们要找的身份提供者。它已经为 pod 提供了身份:每个 pod 使用其名称空间中的[服务帐户](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/)向其本地 Kubernetes API 服务器进行身份验证。Kubernetes 甚至自动化了令牌生成和 pod 配置:

1.  服务帐户控制器确保每个名称空间中至少存在一个“默认”服务帐户，尽管可以创建更多帐户。
2.  令牌控制器为每个服务帐户生成一个令牌，并将其放在同一个名称空间的秘密中。
3.  服务帐户准入控制器在每个 pod 中自动安装服务帐户密码，可以是“默认”服务帐户，也可以是 pod 规范中指定的帐户。

结合[基于角色的访问控制(RBAC)](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) ，服务帐户是 Kubernetes API 的一个强大的身份和访问管理(IAM)解决方案。然而，尽管服务帐户的令牌可以在其命名空间和群集之外使用，但是自动令牌生成和卷装载仅在服务帐户的命名空间中起作用；您有责任获取最新的令牌并相应地配置您的客户端。

一些多集群工具已经重新利用 kubeconfig 文件来存储远程服务帐户令牌，因为它们也可以存储远程 API 服务器 URL 和根证书，并且它们受 client-go 支持。当我们开源多集群控制器时，这是我们最初使用的[，但这有点不太好。Kubeconfig 文件被设计成供 Kubernetes 客户端(如 kubectl)在集群外使用，以对实际用户进行身份验证，但是没有什么可以阻止我们将它们挂载到 pods 中以对远程服务帐户进行身份验证。然而，这只是解决了问题的一部分，kubeconfig 文件仍然必须生成并安装在 pod 中。](https://admiralty.io/blog/introducing-multicluster-controller/)

**[今天，我们开源了多集群服务账户](https://github.com/admiraltyio/multicluster-service-account)** ，利用服务账户作为多集群身份，实现了所有必要的自动化:

1.  ServiceAccountImport 自定义资源定义(CRD)和控制器，用于导入远程服务帐户(及其机密)；
2.  一个动态许可的 webhook，用于自动挂载服务帐户，在带注释的 pods 中导入机密，就像常规服务帐户在 pods 中自动挂载一样；
3.  一个帮助器方法的 Go 库，用于从服务帐户导入中生成 client-go 配置(以及用于回退到 kubeconfig 上下文和常规服务帐户的通用方法)。

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png)/[多集群服务账户](https://github.com/admiraltyio/multicluster-service-account)

### 导入并自动装载远程 Kubernetes 服务帐户

<article class="markdown-body entry-content container-lg" itemprop="text">

# 多集群服务帐户

Multicluster-service-account 使得集群中的 pods 可以很容易地调用其他集群的 Kubernetes APIs。它将远程服务帐户令牌导入到本地机密中，并在带注释的 pods 中自动挂载它们。

Multicluster-service-account 可用于从另一个集群运行任何 Kubernetes 客户端。它还可以用于构建跨多个集群控制 Kubernetes 资源的操作符，例如使用 [multicluster-controller](https://github.com/admiraltyio/multicluster-controller) 。

为什么？查看英国海军部介绍多集群服务账户的博客文章。

## 它是如何工作的

多集群服务帐户包括:

1.  用于引导集群的二进制文件`kubemcsa`，允许它们从彼此导入服务帐户机密
    *   在[将](https://raw.githubusercontent.com/admiraltyio/multicluster-service-account/master/#step-1-installation)multi cluster-service-account 安装在 cluster1 中之后，如果您在单个默认的 kubeconfig 文件中处理多个上下文，那么允许 cluster1 从 cluster2 导入服务帐户秘密就像运行

        ```
        kubemcsa bootstrap --target-context cluster1 --source-context cluster2
        ```

        一样简单，或者如果您在多个 kubeconfig 文件中处理多个上下文，那么运行

        ```
        kubemcsa bootstrap --target-kubeconfig cluster1 --source-kubeconfig cluster2
        ```

        。
2.  一个服务帐户导入自定义…

</article>

[View on GitHub](https://github.com/admiraltyio/multicluster-service-account)

查看[自述文件](https://github.com/admiraltyio/multicluster-service-account/blob/master/README.md)了解更多细节，包括设置说明。[贡献](https://github.com/admiraltyio/multicluster-service-account/blob/master/CONTRIBUTING.md)、[特性请求和 bug 报告](https://github.com/admiraltyio/multicluster-service-account/issues)总是受欢迎的。

*注意:multicluster-service-account 的问题空间仅仅是对 Kubernetes 客户的认证。对于服务对服务多集群认证，我们推荐使用 [Istio 多集群](https://istio.io/docs/setup/kubernetes/multicluster-install/)。*