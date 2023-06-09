# 论库本内斯·RBAC 的违约

> 原文：<https://dev.to/mhausenblas/on-some-defaults-in-kubernetes-rbac-270l>

在 Kubernetes 中，基于角色的访问控制(RBAC)方法定义了许多默认角色和绑定。在本文中，我们将仔细研究其中的一些，并讨论在哪里以及如何使用它们。

让我们从面向用户的集群角色开始:

```
$ kubectl get clusterroles | grep -v '^system'
NAME                                                                   AGE
admin                                                                  33h
cluster-admin                                                          33h
edit                                                                   33h
view                                                                   33h 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们发现在这个设置(Kubernetes 1.11 安装)中，默认情况下有四个集群角色可用:`admin`、`cluster-admin`、`edit`和`view`。你能用它们做什么？使用诸如`kubectl get clusterrole/admin -o yaml`这样的命令揭示了这些角色的含义:

*   `cluster-admin`和`admin`都给予对所有(分别是许多)资源的读写访问权。一般来说，如果您想让某人成为超级用户或集群根用户，那么使用带有集群角色绑定的`cluster-admin`集群角色。如果你想做那件事，好好想想。如果您的组织拥有名称空间管理员的职责，您可以将`cluster-admin`和`admin`与角色绑定一起使用，这样这个人就可以使用角色绑定所在的名称空间中的所有(或几乎所有)资源做任何事情。`cluster-admin`和`admin`之间唯一真正的区别是，当涉及到某些资源(如名称空间本身)时，后者更具限制性。
*   `edit`角色允许用户创建、更新、删除许多常规核心资源，如部署、服务或配置映射，但是不允许操作 RBAC 权限。
*   `view`角色是对大多数资源(除了机密)的只读访问。

对于人类用户来说，这些默认角色通常是一个很好的起点。对于应用程序，实际上你可能最终会使用像 [audit2rbac](https://github.com/liggitt/audit2rbac) 这样的工具，根据具体的访问模式来计算定制的权限。

接下来，我们来看看唯一的默认集群绑定`cluster-admin`，在我的安装中定义如下(为了清楚起见，删除了不相关的字段):

```
$ kubectl get clusterrolebindings/cluster-admin -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: cluster-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters 
```

Enter fullscreen mode Exit fullscreen mode

似乎集群角色绑定`cluster-admin`赋予了`system:masters`组超能力；知道这个很好，但不是直接超级有用。

好了，让我们继续讨论服务账户(非人类用户的身份，如 Kubernetes 组件，如节点、控制器或您自己的应用程序):

```
$ kubectl get sa --all-namespaces
NAMESPACE     NAME                                 SECRETS   AGE
default       default                              1         1d
kube-public   default                              1         1d
kube-system   attachdetach-controller              1         1d
kube-system   bootstrap-signer                     1         1d
kube-system   certificate-controller               1         1d
kube-system   clusterrole-aggregation-controller   1         1d
kube-system   coredns                              1         1d
kube-system   cronjob-controller                   1         1d
kube-system   daemon-set-controller                1         1d
kube-system   default                              1         1d
kube-system   deployment-controller                1         1d
kube-system   disruption-controller                1         1d
kube-system   endpoint-controller                  1         1d
kube-system   expand-controller                    1         1d
kube-system   generic-garbage-collector            1         1d
kube-system   horizontal-pod-autoscaler            1         1d
kube-system   job-controller                       1         1d
kube-system   kube-proxy                           1         1d
kube-system   namespace-controller                 1         1d
kube-system   nginx-ingress                        1         1d
kube-system   node-controller                      1         1d
kube-system   persistent-volume-binder             1         1d
kube-system   pod-garbage-collector                1         1d
kube-system   pv-protection-controller             1         1d
kube-system   pvc-protection-controller            1         1d
kube-system   replicaset-controller                1         1d
kube-system   replication-controller               1         1d
kube-system   resourcequota-controller             1         1d
kube-system   service-account-controller           1         1d
kube-system   service-controller                   1         1d
kube-system   statefulset-controller               1         1d
kube-system   storage-provisioner                  1         1d
kube-system   token-cleaner                        1         1d
kube-system   ttl-controller                       1         1d 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，有大量的控制平面组件，每个组件都有自己的服务帐户和臭名昭著的`default`服务帐户，你不应该使用，[总是创建一个专用的](https://itnext.io/the-abc-of-kubernetes-access-control-e7d280af5c88)。

如果您怀疑某个实体，即用户或服务帐户是否被允许执行某个操作(比如创建一个秘密)，您可以使用`kubectl auth can-i`命令来检查，例如:

```
# let's create a dedicated namespace called secdemo:
$ kubectl create ns secdemo
kubectl create ns secdemo

# let's create a service account nsadmin:
$ kubectl --namespace=secdemo create sa nsadmin
serviceaccount/nsadmin created

# checking if that service account is allowed to create secrets:
$ kubectl --namespace=secdemo auth can-i create secrets \ 
          --as=system:serviceaccount:secdemo:nsadmin
no

# let's give the service account nsadmin the permissions:
$ kubectl --namespace=secdemo create rolebinding nsadmin \
          --clusterrole=admin \
          --serviceaccount=secdemo:nsadmin 
rolebinding.rbac.authorization.k8s.io/nsadmin created

# … and now the service account nsadmin is allowed to 
# create secrets in the namespace secdemo:
$ kubectl --namespace=secdemo auth can-i create secrets \ 
          --as=system:serviceaccount:secdemo:nsadmin
yes 
```

Enter fullscreen mode Exit fullscreen mode

好了，就这样，如果你想了解更多关于 RABC 的信息，我推荐你观看这个精彩的 KubeCon 2017 演讲:

[https://www.youtube.com/embed/Nw1ymxcLIDI](https://www.youtube.com/embed/Nw1ymxcLIDI)

如果你想了解更多关于 RBAC 用法的信息，请查看:

*   [一个只读的 Kubernetes 仪表板](http://blog.cowger.us/2018/07/03/a-read-only-kubernetes-dashboard.html)
*   [Kubernetes 和 RBAC:限制用户访问一个名称空间](https://jeremievallee.com/2018/05/28/kubernetes-rbac-namespace-user.html)
*   [库贝克特尔在行动——RBAC](https://mhausenblas.info/kubectl-in-action/#rbac)

感谢您的入住，下次再见。