# 使用 kubespray 构建 kubernetes cluster)

> 原文：<https://dev.to/nasa9084/kubespraykubernetes-cluster2-3dd3>

*这篇文章最初出现在[blog . web-apps . tech](https://blog.web-apps.tech/)T3 上*

[![kubesprayを使用してkubernetes clusterを構築する(2)](img/2997112c18c5f0ce229a7aa5868becbe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z3_hBFml--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.web-apps.tech/conteimg/2018/02/kubernetes.png)

大约在 3 个月前，[使用 kubespray 构建 Kubernetes cluster 被称为](https://dev.to/nasa9084/kubespraykubernetes-cluster-bel-temp-slug-6093684)的[kubespray](https://kubespray.io/) 和 kubespray-cli 来构建 kubernetes 集群

但是，因为 kubespray-cli 已经[deprecated 了](https://github.com/kubernetes-incubator/kubespray/commit/1869aa39859bff4d27bf1337c1352fd383e980a5)，所以总结一下不使用 kubespray-cli 而用 kubespray 构建集群的步骤

## Elements

使用 kubespray 构建 kubernetes 群集的要求如下:

*   ansible 2.4 或更高版本和 python-netaddr (注意，因为很容易忘记 python-netaddr )
    *   `pip install ansible netaddr`
*   Jinja 2.9 或更高版本(我认为是依赖于 ansible 安装的)
*   构建目标服务器必须能够连接到互联网
*   目标服务器上已启用 IP v4 转发
    *   `sysctl -w net.ipv4.ip_forward=1`(直到重新启动)
    *   在`/etc/sysctl.conf`中填写`net.ipv4.ip_forward = 1`(重启后)
*   将 SSH 密钥从运行 Ansible 的计算机传递到生成目标服务器
*   防火墙已禁用
    *   能好好设定防火墙的人即使有效

此外，kubespray 还附带了 inventory 生成工具(如 kubespray-cli )，如果要使用它，则必须基于 python3。

## 

和上次的报道一样，试着以三台拥有以下 IP 的服务器为对象构建。

*   192.168.1.11
*   192.168.1.12
*   192.168.1.13

每个服务器都启用了 IP v4 转发，禁用防火墙 d，并将 Python 3 作为已安装的 CentOS 7 服务器。 另外，假设从运行 kubespray 的本地计算机向各服务器的 root 用户配置了 SSH 密钥<sup>[【1】](#fn1)</sup>。

## Smooth hands

### Ready

首先，下载 kubespray。

```
$ git clone https://github.com/kubernetes-incubator/kubespray
$ cd kubespray 
```

Enter fullscreen mode Exit fullscreen mode

数据库克隆完成后，引入 ansible 等依赖模块<sup>[【2】](#fn2)</sup>。

```
kubespray$ pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

接下来，为 ansible 创建清单。

```
kubespray$ cp -rfp inventory/sample inventory/mycluster
kubespray$ declare -a IPS=(192.168.1.11 192.168.1.12 192.168.1.13)
CONFIG_FILE=inventory/mycluster/hosts.ini python3 contrib/inventory\builder/inventory.py ${IPS[@]} 
```

Enter fullscreen mode Exit fullscreen mode

IPS 根据对象服务器的 IP 进行定义。

此外，根据环境的不同，也有不是用`python3`指令而是用`python`指令执行 Python 3 的情况。 请适当更换读音。

最后，设置要配置的 kubernetes 集群。 `inventory/mycluster/group_vars`适当变更目录中的`all.yml`和`k8s-cluster.yml`。

特别是`k8s-cluster.yml`中包含的以下项目需要确认吧。

*   kube_version
    *   指定 kubernetes 的版本。
*   kube_network_plugin
    *   指定 kubernetes 的网络插件。 初始值为 calico，但一般为 flannel。 <sup>[[3]](#fn3)</sup>
*   kube _ 服务 _ 地址，kube _ pods _ 子网
    *   以 CIDR 格式指定在 kubernetes 内部使用的 IP 范围。 请注意不要与局域网内的网络发生冲突。
*   仪表板 _ 已启用
    *   这是是否准备 kubernetes dashboard 的真伪值。 初始值为 true。 如果为 true，则必须启用 RBAC。
*   helm_enabled，istio_enabled，注册表 _enabled
    *   每个值都是是否部署 kubernetes Helm、Istio 和文档注册表的真伪值。 如果计划安装的话，在这里设为 true 会很轻松。

设定完成后，准备就结束了。

### 部署

准备好了的话，部署吧！
虽说是
，但是到了这里之后就只是普通地输入 ansible playbook 了。

执行以下命令。

```
kubespray$ ansible-playbook -i inventory/mycluster/hosts.ini cluster.yml 
```

Enter fullscreen mode Exit fullscreen mode

如果退出时没有出现错误，则 kubernetes 部署已完成！

## 操作 kubernetes

部署完成后，请尝试使用 kubernetes。

用 SSH 连接最前面指定的服务器，使用`kubectl`进行操作。

```
$ kubectl get nodes
NAME STATUS ROLES AGE VERSION
node1 Ready master,node 10d v1.9.2+coreos.0
node2 Ready master,node 10d v1.9.2+coreos.0
node3 Ready node 10d v1.9.2+coreos.0 
```

Enter fullscreen mode Exit fullscreen mode

此外，尝试添加管理员帐户，以便可以从局域网中的其他计算机进行操作。

```
$ kubectl create serviceaccount nasa
serviceaccount "nasa" created
$ kubectl create clusterrolebinding nasa --clusterrole cluster-admin --serviceaccount=default:nasa
clusterrolebinding "nasa" created 
```

Enter fullscreen mode Exit fullscreen mode

添加完成后，确认令牌。

```
$ kubectl describe serviceaccount nasa
Name: nasa
Namespace: default
Labels: <none>
Annotations: <none>
Image pull secrets: <none>
Mountable secrets: nasa-token-pcn8j
Tokens: nasa-token-pcn8j
Events: <none>
$ kubectl describe secret nasa-token-pcn8j
Name: nasa-token-pcn8j
Namespace: default
Labels: <none>
Annotations: kubernetes.io/service-account.name=nasa
              kubernetes.io/service-account.uid=8916621a-1010-11e8-8bf3-0200c0a80130

Type: kubernetes.io/service-account-token

Data
====
ca.crt: 1090 bytes
namespace: 7 bytes
token: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwaa3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im5yc2EtdG9rZW4tcGNuOGoiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoibmFzYSIsImt1YmVybmV0ZXMuaW8dc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6Ijg5MTY2MjFhLTEwMTAtMTFlOC04YmYzLTAyMDBjMGE4MDEfMCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDgkZWZhdWx0Om5hc2FifQ.D1o3Jvko91dX6pk2qG505dd2zaXW468GGc9RT6eSzJlrjEG7UEtjF9vlhy7c3BegjPddpPpHsc_ouMx5BAmFdWh74v-PvxnX0IKsCVt_9dlSAcxbbk2PSOloqiwMxTs5q-j6y0Tx64zKzq5e520cNBQrkjJV96-f_riRHHXCrLXQKh2vroh_kpVDViQPqM-e4UKLU4zINGHnraouc7T95ib5wIMcVHEejgsZvF-hLgItxiMAhu4NQXzJ2gM4tMhXupgQZLL1-N_oqoTCNFssPQcoE9Ziyj9_RBkUoodhizpxGOKMFogUgG07DRae4OkEjywoR5xDAuQSJMPihTPqzw 
```

Enter fullscreen mode Exit fullscreen mode

`kubectl`在安装了命令的另一台机器上设置令牌。

```
$ kubectl config set-cluster mycluster --server=https://192.168.1.11:6443 --insecure-skip-tls-verify=true
$ kubectl config set-credentials mycluster --token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwaa3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im5yc2EtdG9rZW4tcGNuOGoiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoibmFzYSIsImt1YmVybmV0ZXMuaW8dc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6Ijg5MTY2MjFhLTEwMTAtMTFlOC04YmYzLTAyMDBjMGE4MDEfMCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDgkZWZhdWx0Om5hc2FifQ.D1o3Jvko91dX6pk2qG505dd2zaXW468GGc9RT6eSzJlrjEG7UEtjF9vlhy7c3BegjPddpPpHsc_ouMx5BAmFdWh74v-PvxnX0IKsCVt_9dlSAcxbbk2PSOloqiwMxTs5q-j6y0Tx64zKzq5e520cNBQrkjJV96-f_riRHHXCrLXQKh2vroh_kpVDViQPqM-e4UKLU4zINGHnraouc7T95ib5wIMcVHEejgsZvF-hLgItxiMAhu4NQXzJ2gM4tMhXupgQZLL1-N_oqoTCNFssPQcoE9Ziyj9_RBkUoodhizpxGOKMFogUgG07DRae4OkEjywoR5xDAuQSJMPihTPqzw
$ kubectl config set-context mycluster --cluster=mycluster --user=mycluster
$ kubectl config use-context mycluster
$ kubectl get nodes
NAME STATUS ROLES AGE VERSION
node1 Ready master,node 10d v1.9.2+coreos.0
node2 Ready master,node 10d v1.9.2+coreos.0
node3 Ready node 10d v1.9.2+coreos.0 
```

Enter fullscreen mode Exit fullscreen mode

顺利进入了！

这次在 cluster 的设定中设为`--insecure-skip-tls-verify=true`，省略了 TLS 的确认，但是可以复制主节点的`/etc/kubernetes/ssl/apiserver.pem`，指定为`kubectl config set-cluster`的`--certificate-authority=`，在确认 TLS 的基础上使用

度过愉快的 kubernetes 生命吧！

* * *

1.  使用`ssh-copy-id`指令很方便。

2.  根据环境的不同，可能需要 sudo。

3.  我个人由 contiv 构成。 虽然没有很深的意义，但是我喜欢带有 Web UI。