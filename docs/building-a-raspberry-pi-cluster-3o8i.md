# 构建 Raspberry Pi 集群

> 原文：<https://dev.to/spino327/building-a-raspberry-pi-cluster-3o8i>

我看到了 Ray Tsang 等人的一个演示，展示了一个带有 Docker 和 Kubernetes 支持 <sup id="fnref1">[1](#fn1)</sup> 的 Raspberry Pi 集群。拥有一个个人的 Raspberry Pi 集群来探索分布式系统的概念和创建概念原型的证明是非常有用的。这篇文章将更新我关于如何创建和维护 Raspberry Pi 集群的经验，所以我会随着时间的推移添加内容。

### 1。部件

我的集群是基于 Raspberry Pi 3 Model B 的，我试着按照 <sup id="fnref1">[1](#fn1)</sup> 中呈现的部件列表。下面我列出了亚马逊(美国)的部分和每个部分的链接。

| 部分 | 统一资源定位器 |
| --- | --- |
| 树莓 Pi 3 型号 B 主板 | [链接](https://www.amazon.com/gp/product/B01CD5VC92/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1) |
| Anker 60W 6 端口 USB 壁式充电器 | [链接](https://www.amazon.com/gp/product/B00P936188/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1) |
| 三星 32GB 95MB/s (U1) MicroSD EVO Select 存储卡 | [链接](https://www.amazon.com/gp/product/B06XWN9Q99/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1) |
| 6 类以太网电缆 5 英尺(5 包)扁平互联网网线 | [链接](https://www.amazon.com/gp/product/B017R12IJA/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1) |
| NETGEAR N300 Wi-Fi 路由器，带高功率 5dBi 外置天线(WNR2020v2) | [链接](https://www.amazon.com/NETGEAR-Router-External-Antennas-WNR2020v2/dp/B00MRVJYEI/ref=sr_1_1?ie=UTF8&qid=1526432473&sr=8-1&keywords=netgear+wnr2020) |
| 金士顿数字 USB 3.0 便携式读卡器的 SD，microSD。 | [链接](https://www.amazon.com/gp/product/B00KX4TORI/ref=oh_aui_detailpage_o08_s01?ie=UTF8&psc=1) |

我用文件[树莓集群框架](https://www.thingiverse.com/thing:2371586)3D 打印了集群架。

[![setup](img/46f0a8b62c1de88112848a26eb0c6926.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pdRvq2Dm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://spino327.github.io/assets/img/c_pi_setup_small.jpg)

### 2。操作系统

我选择 Hypriot OS 是因为它已经支持 docker。此外，在 HypriotOS 1.7 及更高版本中，可以使用 cloud-init 在首次引导时自动更改一些设置。它似乎支持的`cloud-init`版本是 [v0.7.9](https://cloudinit.readthedocs.io/en/0.7.9/index.html) 。

由于我的集群基于 Raspberry PI 3 model B，其中包括一个*四核 1.2 GHz Broadcom BCM 2837 64 位 CPU* <sup id="fnref2">[2](#fn2)</sup> ，因此我使用 github repo `DieterReuter/image-builder-rpi64` (64 位分发)提供的 Hypriot OS 版本，而不是 repo `hypriot/image-builder-rpi` (32 位分发) <sup id="fnref3">[3](#fn3)</sup> 提供的版本。

#### 2.1。刷新操作系统

首先，我决定使用 hypriot 开发并托管在 github `hypriot/flash` <sup id="fnref4">[4](#fn4)</sup> 上的命令行脚本。这比更简单的方法如`sudo dd if=image.img of=/dev/rdisk2 bs=1m`更有优势。按照`hypriot/flash`报告中的描述安装完依赖项后，我在`$HOME/bin`中安装了命令行实用程序。

```
$ # get the release
$ curl -O https://raw.githubusercontent.com/hypriot/flash/master/flash
$ # add executable permissions
$ chmod +x flash
$ # move to ~/bin
$ mv flash $HOME/bin/
$ # export $HOME/bin to the path
$ export PATH=$HOME/bin:$PATH 
```

二、我从`DieterReuter/image-builder-rpi64` <sup id="fnref5">[5](#fn5)</sup> 拿到的 OS 镜像:

```
$ wget https://github.com/DieterReuter/image-builder-rpi64/releases/download/v20180429-184538/hypriotos-rpi64-v20180429-184538.img.zip
$ wget https://github.com/DieterReuter/image-builder-rpi64/releases/download/v20180429-184538/hypriotos-rpi64-v20180429-184538.img.zip.sha256
$ # Verify the image with sha-256
$ shasum -a 256 hypriotos-rpi64-v20180429-184538.img.zip 
```

刷新 sd 卡:

```
$ # Setting nodeX and with the user-data.yml
$ flash --hostname nodeX --userdata ./user-data.yml hypriotos-rpi64-v20180429-184538.img 
```

你可以找到一个 [user-data.yml](https://gist.github.com/spino327/d514212f9f782d786cedf0487854c6f9) 的例子

#### 2.1。网络配置(使用路由器)

我决定使用静态 IP 来配置网络接口。想法是修改位于`sudo vim /etc/network/interfaces.d/eth0`的`eth0`的文件。例如，这是*节点 1* 的设置。

```
allow-hotplug eth0
iface eth0 inet static
    address 192.168.2.11
    network 192.168.2.0
    netmask 255.255.255.0
    broadcast 192.168.2.255
    gateway 192.168.2.1 
```

我有一个专用于 Pi 集群的小型 Netgear WNR2020 无线路由器，它连接到另一个为它提供互联网的路由器。我发现通过添加 google DNS 服务器来修改 DNS 路由很有用。因此，我修改了文件`/etc/resolv.conf`，看起来像这样(您需要`sudo`来写入它):

```
$ cat /etc/resolv.conf
  nameserver 8.8.8.8
  nameserver 8.8.4.4
  nameserver 192.168.2.1 
```

##### 2.1.1 网络配置(使用带有 NAT 的交换机和头节点)

最近，我通过移除路由器来更新集群。因此，现在配置使用交换机与集群的节点进行内部通信。
另外，我将头节点配置为一个 NAT。基本上，头节点使用`wlan0`连接到我的无线网络，并通过以太网接口`eth0`充当 NAT。

###### 2.1.1.1 头节点

首先，通过`echo 1 > /proc/sys/net/ipv4/ip_forward`或者改变文件`/etc/sysctl.conf`中的行`net.ipv4.ip_forward=1`来告诉内核启用 IP 转发。

第二，让头节点充当 NAT:

```
$ sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
$ sudo iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
$ sudo iptables -A FORWARD -i eth0 -o wlan0 -j ACCEPT 
```

我们可以通过以下方式保存 iptable 规则并在启动时恢复它们:

```
$ sudo iptables-save > /etc/cluster_ip_rules.fw 
```

第三，我为无线接口添加了如下配置:

```
$ cat /etc/network/interfaces.d/wlan0 
auto wlan0
iface wlan0 inet dhcp
    wpa-ssid "Your ssid"
    wpa-psk "your password"
    gateway 192.168.1.1
    post-up iptables-restore < /etc/cluster_ip_rules.fw 
```

###### 2.1.1.2。从属节点

在其他节点中，我通过添加头节点 IP 作为网关来更改`eth0`配置。所以看起来像是:

```
$ cat /etc/network/interfaces.d/eth0
allow-hotplug eth0
#iface eth0 inet dhcp
iface eth0 inet static
    address 192.168.2.13
    network 192.168.2.0
    netmask 255.255.255.0
    broadcast 192.168.2.255
    gateway 192.168.2.11 
```

确保文件`/etc/resolv.conf.head`有行`nameserver 192.168.1.1`或者指向你的 wifi 路由器的 IP 地址。

#### 2.2。设置无密码

我按照马蒂亚斯·凯特纳 <sup id="fnref6">[6](#fn6)</sup> 的指导，设置了无密码的 ssh 登录。基本上，这是一个两部分的过程。首先，在本地机器上创建密钥。

```
$ # generating a key that has not the default filename
$ ssh-keygen -t rsa
 Enter file in which to save the key (<user_path>/.ssh/id_rsa): <user_path>/.ssh/pic_id_rsa
 Enter passphrase (empty for no passphrase):
 Enter same passphrase again:
 Your identification has been saved in <user_path>/.ssh/pic_id_rsa.
 Your public key has been saved in <user_path>/.ssh/pic_id_rsa.pub.
 The key fingerprint is:
 ... 
```

其次，在远程主机上设置公钥。

```
$ # Create .ssh folder in remote machine
$ ssh ruser@remote 'mkdir -p ~/.ssh'
 ruser@remote's password:
$ # Send key.pub to remote machine
$ cat ~/.ssh/pic_id_rsa.pub | ssh ruser@remote 'cat >> ~/.ssh/authorized_keys'
 ruser@remote's password:
$ # Changing permissions in the remote machine of both .ssh and .ssh/authorized_keys
$ ssh ruser@remote 'chmod 700 ~/.ssh'
$ ssh ruser@remote 'chmod 640 ~/.ssh/authorized_keys' 
```

现在，你可以不用密码 ssh 到`remote`。我使用的是 Mac 笔记本电脑，所以我必须在`$ ssh-add $HOME/.ssh/pic_id_rsa`之前将密钥添加到 ssh 代理中。

### 3。码头工人

在 [docker 注册表](https://hub.docker.com/u/arm64v8/)中有多个 **arm64v8** 的 docker 图像。

### 4。库伯内特斯

在下一节中，我假设您以 root 用户身份登录。如果没有，那么您需要在命令中插入`sudo`。最终结果应该是一样的。

首先，为包添加加密密钥

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - 
```

第二，添加存储库

```
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" >> /etc/apt/sources.list.d/kubernetes.list 
```

第三，在每个节点安装 kubernetes

```
apt-get install -y kubelet kubeadm kubectl kubernetes-cni 
```

#### 4.1。配置主节点

```
$ kubeadm init --pod-network-cidr 10.244.0.0/16 --apiserver-advertise-address 192.168.2.11 
```

要开始使用您的集群，您需要以普通用户的身份运行以下命令:

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config 
```

#### 4.2。用法兰绒配置网络

[法兰绒](https://github.com/coreos/flannel)是一种配置为 Kubernetes 设计的第 3 层网络结构的简单易行的方法。\
您只需要使用`kubectl` :
将它应用到您的集群中

```
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml 
```

#### 4.3。配置工作节点

我们只需要使用`kubeadm join`命令并传递由`kubeadm init`提供的
令牌和发现令牌-ca-cert-hash 来加入我们的工作节点。

```
$ kubeadm join 192.168.2.11:6443 --token=<token> --discovery-token-ca-cert-hash <sha256:...> 
```

#### 4.4。访问集群

要使用`kubectl`从远程机器访问集群，您可以使用`kubectl proxy`。在我的例子中，raspberry pi
集群中的主节点有两个网络接口，我想从外部网络中的一台机器进行连接(外部网络是`192.168.1.0/24`，内部网络是`192.168.2.0/24`)。因此，在主节点中运行`kubectl proxy`，并传递参数以使用主节点的
外部 ip(在我的例子中是`--address=192.168.1.32`)，并传递外部网络中 IP 允许的主机列表(`--accept-hosts="^192.168.1"` ):

```
$ kubectl proxy --port=8080 --address=192.168.1.32 --accept-hosts="^192.168.1" 
```

然后，您可以创建以下信息或将其添加到您想要的 KUBECONFIG 文件中:

```
apiVersion: v1
clusters:
- cluster:
    server: http://192.168.1.32:8080
  name: rpi-cluster
contexts:
- context:
    cluster: rpi-cluster
  name: rpi
current-context: rpi
kind: Config
preferences: {} 
```

### 参考文献

* * *

1.  R. Tsang 等人创建了一个运行购物清单 Kubernetes 的 Raspberry Pi 集群(第 1 部分)。Kubernetes 博客。2015\. [↩](#fnref1)

2.  [树莓 PI 3 型号 B 规格。](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) [↩](#fnref2)

3.  使用 Cloud-Init 和 HypriotOS (64 位)引导云。 [↩](#fnref3)

4.  flash:命令行脚本，用于刷新任何类型的 SD 卡映像。 [↩](#fnref4)

5.  [image-builder-rpi 64 版本。](https://github.com/DieterReuter/image-builder-rpi64/releases) [↩](#fnref5)

6.  凯特纳先生。SSH 登录无需密码。 [↩](#fnref6)