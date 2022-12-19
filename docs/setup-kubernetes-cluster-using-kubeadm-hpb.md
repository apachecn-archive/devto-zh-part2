# 使用 kubeadm 设置 kubernetes 集群

> 原文：<https://dev.to/pongsatt/setup-kubernetes-cluster-using-kubeadm-hpb>

从[上一篇文章](https://dev.to/pongsatt/prepare-virtual-machine-using-virtualbox-and-ubuntu-558j)开始，你准备了许多虚拟机，在这篇文章中，我们将安装 kubeadm 并设置它作为一个集群一起工作。

这是[在虚拟机](https://dev.to/pongsatt/setup-your-own-kubernetes-cluster-on-vms-5ln)系列上设置您自己的 kubernetes 集群的一部分。

**注**

> 这是一个自以为是的 kubernetes 集群设置，所以对初学者来说很容易。意思是，有些东西已经为你选择好了，但是如果你想的话，你可以改变它。
> 这篇文章基于[这份 kubernetes 官方文件](https://kubernetes.io/docs/setup/independent/install-kubeadm/)

## 先决条件:

*   至少 2 台安装了 Ubuntu 并连接了网络的虚拟机

# 切换到 root 用户(所有机器)

运行以下命令并输入 root 密码(设置虚拟机时的密码)

```
sudo su 
```

[![](../Images/9270a5542c07a892d9b8b24f6543cf00.png "Switch to root user")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iVEwm1Jo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kwomwjpa4pay1thg5zqd.png)

# 安装 Docker(所有机器)

按照以下步骤将 docker 安装到所有机器上。

### 1。安装 Docker

```
# Install Docker from Ubuntu's repositories:
apt-get update
apt-get install -y docker.io 
```

### 2。配置 docker

如果您想使用自己的 docker 私有注册表，请遵循以下内容，否则请遵循“下面没有私有注册表”

*注:*“192 . 168 . 1 . 105:8082”是我的 docker 私有注册表。

```
# Setup daemon.
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",

  "insecure-registries": [
    "192.168.1.105:8082"
  ],
  "disable-legacy-registry": true
}
EOF

mkdir -p /etc/systemd/system/docker.service.d

# Restart docker.
systemctl daemon-reload
systemctl restart docker 
```

没有私人注册。

```
# Setup daemon.
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

mkdir -p /etc/systemd/system/docker.service.d

# Restart docker.
systemctl daemon-reload
systemctl restart docker 
```

### 3。(可选)安装映像清理作业

我建议安装这项工作，以清理未使用的 docker 图像，否则他们会消耗你所有的磁盘。

```
docker run -d --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock:rw \
  -v /var/lib/docker:/var/lib/docker:rw \
  -e "CLEAN_PERIOD=86400" \
  meltwater/docker-cleanup:latest 
```

### 4。检查是否一切正常

运行下面的命令，看看是否一切正常。

```
docker ps 
```

您应该在所有机器上看到类似下面的内容，您可以继续了。

[![](../Images/7427d69a36be0f291f73baecc5a4daee.png "Docker ps")](https://res.cloudinary.com/practicaldev/image/fetch/s--iD_AApP6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xy6w99f0qe9jp97jtyt5.png)
[![](../Images/2c7164319a9645c032626a4f46115541.png "Docker ps")T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--LHIVAlCy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8swpt8056k7eek65qyco.png)

# 安装 Kubeadm(所有机器)

下一步是安装 kubernetes 集群工具。

### 1。禁用交换

这是 kubeadm 的先决条件。

```
# disable temporarily
swapoff -a 
```

要永久禁用，请编辑/etc/fstab 和命令 swap line out 并保存。

```
vi /etc/fstab 
```

[![](../Images/839b396f0b5ad785ef699c4e1ace64a4.png "Disable swap")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_wuoRrje--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bisgoywyxdq5gg0w6gal.png)

**VI newby 注意:**

*   将光标移动到最后一行的开头
*   键入“I”以插入
*   键入“#”，然后键入“esc”
*   键入':wq '，然后回车保存

### 2。在所有计算机上运行以下命令

```
apt-get update && apt-get install -y apt-transport-https curl 
```

然后

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl nfs-common 
```

和

```
apt-mark hold kubelet kubeadm kubectl 
```

这些命令将安装:

*   kubriet:kuble node runtime(kuble 节点运行时)
*   kubeadm : kubernetes 集群设置工具
*   kubectl : kubernetes 集群命令行接口
*   nfs-common: nfs 客户端库能够连接到 nfs 服务器

# 设置集群(仅限主节点)

这一步，我们将通过运行命令初始化主节点。

```
# init master
kubeadm init 
```

*注意:*仅在主节点上运行

根据您的互联网连接，有时您需要等待。

如果一切正常，你会看到的。

[![](../Images/bd723019a19ffb9358f2c2a91e1f4d43.png "kubeadm init")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0w0W7b7P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n0hu2jrhy2cmkjz8yo9p.png)

**注:**
请抄最后一行。它将在下一步中用于加入工作节点。

# 设置集群(仅限工作节点)

在所有工作节点上运行从上一步复制的 join 命令。

下面是一个例子(不要使用这个命令，它对你不起作用)

```
kubeadm join 192.168.1.109:6443 --token srzyez.wjnqsmt2gcohxtp4 --discovery-token-ca-cert-hash sha256:ac8d21e46aeaba3664c4f6060072de03d906d0032f9731b021eeb8d54a876e35 
```

在 worker 节点上，如果 join 命令成功，您将看到。

[![](../Images/b642f0eb1489660081ef06ca0bcb6149.png "Worker join result")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V_fOBp7H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e097gdwx4i58rssq8x3p.png)

# 安装后(仅主节点)

### 1。设置群集连接配置

切换回普通用户。

```
exit 
```

运行以下命令并输入 root 密码。

```
# setup kube config
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config 
```

### 2。安装 Pod 网络(weavenet)

kubernetes 集群需要 pod 网络。要安装 weavenet(几个 pod 网络提供商之一)，在主节点上运行命令，如下所示。

```
sudo sysctl net.bridge.bridge-nf-call-iptables=1
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')" 
```

[![](../Images/6c18235924871c1e2b9c03a1d72b3562.png "Pod network installed")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oAlqN02H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gb116tqc17jfsw9vwy4a.png)

### 3。检查结果

在主节点上，运行下面的命令。

```
kubectl get nodes 
```

如果你看到下面这样的东西，祝贺你！你有你的集群。

[![](../Images/11167498ab2bb21b643f3f024c6b4e1d.png "Get nodes result")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uvQXbl5a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j51gqqdto5a8wb0j8go6.png)

# 安装仪表板(可选)

为了能够看到集群的全貌，您需要一个仪表板。

### 1。安装仪表板

在主节点上，运行以下命令。

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml 
```

### 2。设置访问仪表板的权限

为简单起见，我们将授予任何人访问 dashboard 管理员权限。您不会在实际的集群中这样做。

```
echo "apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system" | kubectl apply -f - 
```

### 3。运行代理命令

打开代理，以便可以从外部访问群集。

```
kubectl proxy --address='0.0.0.0' --accept-hosts='.*' 
```

### 4。打开仪表板 UI

打开 url

您应该会看到登录页面。

[![](../Images/219b219a38f8dcd2374cafd1fdc7697d.png "Dashboard login")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8kw4rAe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qwqtpnfl244pnb5svlpw.png)

点击“跳过”按钮，您将看到概述页面。

[![](../Images/a16f8bc80b7cf4b71def288293f9de36.png "Dashboard overview")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zRHWbqCd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uukarje7wh7y56vijbxk.png)

恭喜你！您有一个仪表板来查看和管理您的工作负载。

# 总结

我们有一个在虚拟机上运行的 kubernetes 集群。[下一步](https://dev.to/pongsatt/setup-dynamic-storage-class-provisioner-based-on-nfs-1d4i)将设置集群存储，这样我们就可以构建存储东西的应用程序。