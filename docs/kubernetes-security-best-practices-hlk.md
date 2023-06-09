# Kubernetes 安全最佳实践

> 原文：<https://dev.to/pbnj/kubernetes-security-best-practices-hlk>

[![cargo ship](img/7e50b9cbf04b9a2e31cbfed6da6daf29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1wHwhagU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.logisticsmgmt.cimg/LM1710_SUP_Ocean_wideImage2.jpg)

## 目录

*   [概述](#overview)
*   [安全基线](#secure-baseline)
*   [认证](#authentication)
*   [授权](#authorization)
*   [准入控制](#admission-controls)
*   [冒名顶替](#impersonation)
*   [Pod 安全策略](#pod-security-policies)
*   [网络策略](#network-policies)
*   [附加安全措施](#additional-security)
*   [附加参考文献](#additional-references)
*   [结论](#conclusion)

## 

所以，你对容器技术了如指掌。您可能在本地机器上为您的应用程序构建了一个容器，并为数据库构建了另一个容器，但是接下来会发生什么呢？你需要系统来构建你的容器。您需要系统来部署您的容器。你需要系统来运行你的容器。你如何管理这种规模的集装箱？

进来吧，库伯内特斯！

Kubernetes 是新的应用服务器。Kubernetes 为开发团队、运营团队甚至安全团队提供了一个 API 接口，用于与应用程序和平台进行交互。

Kubernetes 是一个快速发展的开源项目，正在不断取得进展。因此，我在本文中的目标是涵盖我观察到的一些常见安全错误，并提供一些关于保护 Kubernetes 集群和工作负载的通用最佳实践。

**图例:**

| 图标 | 意义 |
| --- | --- |
| -好的 | 不推荐 |
| 🗒️ | 基本原理 |
| 981 号房 | 建议 |
| ⚠️ | 警告 |

## 

✅确保您的底层主机是加固和安全的。我建议将 CIS 基准作为起点。

✅确保 Docker 本身按照安全最佳实践进行配置。查看我以前的文章: [Docker 安全最佳实践](https://dev.to/petermbenjamin/docker-security-best-practices-45ih)

✅确保您从 Kubernetes 开始时有一个安全的基线。

*   互联网安全中心(CIS)免费维护[文档](https://downloads.cisecurity.org/)。
*   Aqua Security 的优秀人员还开源了一个基于 CIS 建议的自动检查器。来看看: [kube-bench](https://github.com/aquasecurity/kube-bench)

```
# recommended - on master node
$ kubectl run                         \
    --rm                              \
    -it                               \
    kube-bench-master                 \
    --image=aquasec/kube-bench:latest \
    --restart=Never                   \
    --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"hostPID\": true, \"nodeSelector\": { \"kubernetes.io/role\": \"master\" }, \"tolerations\": [ { \"key\": \"node-role.kubernetes.io/master\", \"operator\": \"Exists\", \"effect\": \"NoSchedule\" } ] } }"               \
    -- master                         \
    --version 1.8

# recommended - on worker nodes
$ kubectl run                         \
    --rm                              \
    -it                               \
    kube-bench-node                   \
    --image=aquasec/kube-bench:latest \
    --restart=Never                   \
    --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"hostPID\": true } }" \
    -- node                           \
    --version 1.8 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

与 Kubernetes 的大多数交互都是通过与控制平面对话来完成的，特别是控制平面的 **kube-apiserver** 组件。在请求被服务或拒绝之前，请求在 kube-apiserver 中经过 3 个步骤:身份验证、授权和准入控制。一旦请求通过了这三个步骤，kube-apiserver 就会通过网络与 [Kubelets](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) 进行通信。因此，Kubelets 还必须检查认证和授权。

通过使用某些命令行标志启动它们，可以控制或修改`kube-apiserver`和`kubelet`的行为。此处记录了受支持的命令行标志的完整列表:

*   [kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)
*   库伯莱

让我们检查一些常见的 Kubernetes 身份验证安全错误:

❌默认情况下，匿名身份验证处于启用状态

🗒️ Kubernetes 允许开箱即用的匿名认证。有一些`kube-apiserver`设置的组合可以使匿名认证变得安全，比如 [`--authorization-mode=RBAC`](#authorization) ，因为您需要明确地向`system:anonymous`用户和`system:unauthenticated`组授予 RBAC 特权。
**注**:授予`*`用户或`*`组的 RBAC 权限不包括匿名用户。

✅通过传递`--anonymous-auth=false`标志
来禁用匿名认证

```
# not recommended - on master node
$ kube-apiserver       \
    <... other flags>  \
    --anonymous-auth=true

# recommended - on master node
$ kube-apiserver       \
    <... other flags>  \
    --anonymous-auth=false

# not recommended - on worker nodes
$ kubelet             \
    <... other flags> \
    --anonymous-auth=true

# recommended - on worker nodes
$ kubelet             \
    <... other flags> \
    --anonymous-auth=false 
```

Enter fullscreen mode Exit fullscreen mode

* * *

❌带着`--insecure-port=<PORT>`跑`kube-apiserver`

🗒️在旧版本的 Kubernetes 中，你可以用一个没有任何保护的 API 端口运行`kube-apiserver`

✅通过传递`--insecure-port=0`标志来禁用不安全的端口。在最近的版本中，这已经被默认为禁用，目的是完全否定它

```
# not recommended
$ kube-apiserver         \
    <... other flags>    \
    --insecure-port=6443

# recommended
$ kube-apiserver      \
    <... other flags> \
    --insecure-port=0 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在对用户进行身份验证时，✅更喜欢基于 OpenID Connect 或 X509 客户端证书的身份验证策略

🗒️ Kubernetes 支持不同的认证策略:

*   X509 客户端证书:不错的认证策略，但是您必须定期更新和重新分发客户端证书
*   静态令牌:避免使用它们，因为它们不是短暂的
*   **引导令牌**:与上述静态令牌相同
*   **基本认证**:避免它们，因为凭证以明文形式在网络上传输
*   **服务帐户令牌**:不应该用于试图与 Kubernetes 集群交互的最终用户，但是它们是在 Kubernetes 上运行的应用&工作负载的首选身份验证策略
*   **OpenID Connect (OIDC)令牌**:OIDC 与您的身份提供商(如 AD、AWS IAM、GCP IAM)集成后，最终用户的最佳身份认证策略...等等)

```
# recommended - OIDC
$ kube-apiserver                                                        \
    <... other flags>                                                   \
    --oidc-issuer-url="https://domain/.well-known/openid-configuration" \
    --oidc-client-id="example"

# recommended - X509 cert
$ kube-apiserver                               \
    <... other flags>                          \
    --client-ca-file=/path/to/ca.crt           \
    --tls-cert-file=/path/to/server.crt        \
    --tls-private-key-file=/path/to/server.key 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

❌默认情况下，授权模式是总是授权所有请求给`kube-apiserver`

✅启用[基于角色的访问控制](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

🗒️ Kubernetes 支持不同的授权策略，但在 1.8 版中引入了**基于角色的访问控制** (RBAC)，它允许管理员定义用户和服务帐户可以或不可以在整个 Kubernetes 集群中或在特定的 Kubernetes [名称空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
中做什么

```
# not recommended
$ kube-apiserver      \
    <... other flags> \
    --authorization-mode=AlwaysAllow

# recommended
$ kube-apiserver      \
    <... other flags> \
    --authorization-mode=RBAC 
```

Enter fullscreen mode Exit fullscreen mode

* * *

❌默认情况下，`default`服务帐户会自动挂载到 Kubernetes 中所有容器的文件系统中

🗒️`default`服务帐户是一个有效的服务帐户令牌，可以用来查询 Kubernetes API 作为一个经过身份验证的工作负载。启用 RBAC 后，这仍然是一个问题，但没有启用 RBAC 时的问题大。没有 RBAC，这个令牌可以用来在 kubernetes 集群中做几乎任何事情

✅禁止自动安装`default`服务帐户令牌

```
# recommended
$ kubectl patch serviceaccount default -p "automountServiceAccountToken: false" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

准入控制器是在认证/授权过程的第三步，也是最后一步，拦截对 Kubernetes API 的请求的代码片段。许可控制选项的完整列表记录在此:

*   [准入控制器](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)

* * *

✅配置准入控制，通过在特权容器上启动交互式外壳或附加到特权容器来拒绝特权提升

🗒️:在一些有效的用例中，你需要运行特权容器来与底层主机的内核进行交互，但这意味着用户有可能`kubectl attach`或`kubectl exec`进入那些特权容器。

```
# recommended
kube-apiserver       \
    <...other flags> \
    --admission-control=...,DenyEscalatingExec 
```

Enter fullscreen mode Exit fullscreen mode

* * *

✅配置准入控制以启用 Pod 安全策略

🗒️ Pod 安全策略是 Pod 必须遵守的安全规则，以便在您的集群上被接受和安排。

⚠️ **确保你有 PodSecurityPolicy 对象(即 yaml 文件),一旦你打开它，你就可以应用，否则就不会安排任何 pod。**参见本文中的 [Pod 安全策略](#pod-security-policy)建议以获取示例。

```
# recommended
$ kube-apiserver      \
    <... other flags> \
    --addmission-control=...,PodSecurityPolicy

# example PodSecurityPolicy
$ kubectl create -f- <<EOF 
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: example
spec:
  privileged: false  # Don't allow privileged pods!
EOF

# let's try to request a privileged pod to be scheduled on our cluster
$ kubectl create -f- <<EOF
apiVersion: v1
kind: Pod
metadata:
  name:      pause
spec:
  containers:
    - name:  pause
      image: k8s.gcr.io/pause
EOF

# throws error as expected
Error from server (Forbidden): error when creating "STDIN": pods "privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

✅将准入控制配置为总是拉取图像

🗒️ Kubernetes 将缓存从任何注册表(私人或公共)提取的容器图像。任何缓存的容器映像都可以被集群上的任何 pod 重用。因此，pod 可能会获得对潜在敏感信息的未授权访问。强制 Kubernetes 总是获取图像将要求从私有注册中心获取图像所需的凭证由每个请求资源提供

```
# recommended
$ kube-apiserver      \
    <... other flags> \
    --admission-control=...,AlwaysPullImages 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

Kubernetes 有一个特性，允许任何用户模拟 Kubernetes 集群上的任何其他用户。这个特性对于调试来说很好，但是如果控制不当，可能会带来意想不到的安全隐患。

为了证明其含义:

```
$ kubectl drain test-node
Error from server (Forbidden): User "foo" cannot get nodes at the cluster scope. (get nodes test-node)

$ kubectl drain test-node --as=admin --as-group=system:admins
node "test-node" cordoned
node "test-node" drained 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里了解更多信息:

*   [用户模拟](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation)

✅限制了谁可以冒充以及作为被冒充的用户他们可以做什么

```
# not recommended
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: impersonator
rules:
# allows users to impersonate any "users", "groups", and "serviceaccounts"
- apiGroups: [""]
  resources: ["users", "groups", "serviceaccounts"]
  verbs: ["impersonate"]

# recommended
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: limited-impersonator
rules:
# Can impersonate the group "developers"
- apiGroups: [""]
  resources: ["groups"]
  verbs: ["impersonate"]
  resourceNames: ["developers"] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

Pod 安全策略定义了一组条件，pod 必须遵守这些条件才能被系统接受。

✅不允许特许集装箱

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  privileged: false 
```

Enter fullscreen mode Exit fullscreen mode

✅不允许共享主机进程 ID 名称空间
⚠️ **如果`hostPID`被设置为`true`并且容器被授予`SYS_PTRACE`能力，则有可能提升容器**之外的特权

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  hostPID: false 
```

Enter fullscreen mode Exit fullscreen mode

✅不允许共享主机 IPC 命名空间(即内存)

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  hostIPC: false 
```

Enter fullscreen mode Exit fullscreen mode

✅不允许共享主机网络堆栈(即访问回环、本地主机、窥探本地节点上的网络流量)

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  hostNetwork: false 
```

Enter fullscreen mode Exit fullscreen mode

✅白名单允许的卷类型

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  # It's recommended to allow the core volume types.
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    # Assume that persistentVolumes set up by the cluster admin are safe to use.
    - 'persistentVolumeClaim' 
```

Enter fullscreen mode Exit fullscreen mode

✅要求容器以非根用户身份运行

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  runAsUser:
    # Require the container to run without root privileges.
    rule: 'MustRunAsNonRoot'
  supplementalGroups:
    rule: 'MustRunAs'
    ranges:
      # Forbid adding the root group.
      - min: 1
        max: 65535 
```

Enter fullscreen mode Exit fullscreen mode

✅把`defaultAllowPrivilegeEscalation`设置为`false`到

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  defautlAllowPrivilegeEscalation: false 
```

Enter fullscreen mode Exit fullscreen mode

✅应用安全增强的 Linux ( `seLinux`)、`seccomp`或`apparmor`配置文件

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
  annotations:
    # applying default seccomp and apparmor profiles
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: 'docker/default'
    apparmor.security.beta.kubernetes.io/allowedProfileNames: 'runtime/default'
    seccomp.security.alpha.kubernetes.io/defaultProfileName:  'docker/default'
    apparmor.security.beta.kubernetes.io/defaultProfileName:  'runtime/default' 
```

Enter fullscreen mode Exit fullscreen mode

### 全 Pod 安全策略示例

```
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: 'docker/default'
    apparmor.security.beta.kubernetes.io/allowedProfileNames: 'runtime/default'
    seccomp.security.alpha.kubernetes.io/defaultProfileName:  'docker/default'
    apparmor.security.beta.kubernetes.io/defaultProfileName:  'runtime/default'
spec:
  privileged: false
  # Required to prevent escalations to root.
  defautlAllowPrivilegeEscalation: false
  # This is redundant with non-root + disallow privilege escalation,
  # but we can provide it for defense in depth.
  requiredDropCapabilities:
    - ALL
  # Allow core volume types.
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    # Assume that persistentVolumes set up by the cluster admin are safe to use.
    - 'persistentVolumeClaim'
  hostNetwork: false
  hostIPC: false
  hostPID: false
  runAsUser:
    # Require the container to run without root privileges.
    rule: 'MustRunAsNonRoot'
  seLinux:
    # This policy assumes the nodes are using AppArmor rather than SELinux.
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'MustRunAs'
    ranges:
      # Forbid adding the root group.
      - min: 1
        max: 65535
  fsGroup:
    rule: 'MustRunAs'
    ranges:
      # Forbid adding the root group.
      - min: 1
        max: 65535
  readOnlyRootFilesystem: false 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

Kubernetes 允许用户部署他们选择的网络附件。

✅选择一个允许你利用网络策略的网络附件，像 [Calico](https://docs.projectcalico.org/v3.1/introduction/) 或 [Canal](https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/flannel) ，它通过法兰绒为你提供网络，通过 Calico 提供网络策略。

```
# Canal Example 
$ kube-apiserver                 \
    <... other flags>            \
    --cluster-cidr=10.244.0.0/16 \
    --allocate-node-cidrs=true

# install RBAC
$ kubectl apply -f \
    https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/canal/rbac.yaml

# install Calico
$ kubectl apply -f \
    https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/canal/canal.yaml 
```

Enter fullscreen mode Exit fullscreen mode

## 

到目前为止，所有的建议都只是为了让你开始工作。在这一部分，我将介绍一些额外的安全措施！

✅·库伯内特[秘密](https://kubernetes.io/docs/concepts/configuration/secret/)对于一些有限的用例是有用的。我不会依赖它作为秘密管理解决方案。相反，考虑哈希公司[金库](https://www.vaultproject.io/)做这个。

✅用 [Anchore](https://anchore.com/) 或 [Clair](https://coreos.com/clair/docs/latest/) 持续扫描你的容器中的安全漏洞。

✅让您的基础设施在安全补丁上保持最新，或者在保持自身最新的操作系统上运行 Kubernetes(例如 CoreOS)

✅仅部署您已分析、扫描和签名的授权容器映像(即软件供应链安全)。[公证人](https://github.com/theupdateframework/notary)可以在这里帮忙。

✅限制对 Kubernetes 节点的直接访问。

✅避免吵闹的邻居问题。定义资源配额。

✅监视并记录普罗米修斯和格拉夫纳的一切。Sysdig Falco 将检测异常容器行为并发出警报，如容器中的外壳执行、容器权限提升、意外子进程的产生、安装敏感路径、进行网络连接的系统二进制文件...等等

* * *

## 

*   [保护集群](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/)
*   【Kubernetes 部署的安全最佳实践
*   [Kubernetes 安全最佳实践](https://github.com/freach/kubernetes-security-best-practice)

* * *

## 

容器和编排器本质上并不比传统虚拟化技术更安全或更不安全。如果有的话，我个人认为容器和编排器有可能彻底改变安全行业，并真正以 devo PS 的速度实现*安全。*

如果你觉得我错过了什么，弄错了一些细节，或者只是想打个招呼，请随时在下面留下评论或者在 GitHub 上联系我🐙，[推特🐦](https://twitter.com/petermbenjamin)，或 [LinkedIn🔗](https://www.linkedin.com/in/pmbenjamin)