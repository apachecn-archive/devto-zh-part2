# 在虚拟机上设置您自己的 kubernetes 集群

> 原文：<https://dev.to/pongsatt/setup-your-own-kubernetes-cluster-on-vms-5ln>

我的要求是，我想要一个我可以玩的服务器。我尝试了 Docker 和 Jenkins 的 CD，感觉棒极了。然后 Docker swarm 和 Kubernetes 来了，所以我想把我的 Docker 环境升级到集群。问题是我只有一台电脑，我不想为我的游戏环境付费。然后我发现我可以用一个叫 kubeadm 的工具和一些虚拟机来实现这个。我想分享我的环境和如何设置它，以及我使用的步骤记录。

我把这些步骤分成了一系列的帖子，这样就足够简单明了了。

**要求**

*   基于 kubeadm 的 kubernetes 集群
*   支持基于 NFS 的动态存储配置
*   支持自动获取加密证书的入口
*   能够使用 Jenkin CD 管道为后端和前端部署应用程序

你可以遵循下面每个帖子中的每个步骤。

**设置详情:**

1.  [使用 Virtualbox 和 Ubuntu 准备虚拟机](https://dev.to/pongsatt/prepare-virtual-machine-using-virtualbox-and-ubuntu-558j)
2.  [使用 kubeadm 设置 kubernetes 集群(1 主 3 节点)](https://dev.to/pongsatt/setup-kubernetes-cluster-using-kubeadm-hpb)
3.  [基于 NFS 设置动态存储类供应器](https://dev.to/pongsatt/setup-dynamic-storage-class-provisioner-based-on-nfs-1d4i)
4.  [设置 nginx 入口和证书管理器自动应用 letsencrypt 证书](https://dev.to/pongsatt/set-up-nginx-ingress-with-letsencrypt-certificate-on-vms-or-bare-metal-kubernetes-cluster-j25)
5.  [使用带有自动 https 证书的 Jenkins CD 和 minio 将静态前端应用程序部署到 kubernetes 集群](https://dev.to/pongsatt/deploy-static-front-end-application-to-kubernetes-cluster-using-jenkins-cd-and-minio-with-automatic-https-cert-44gk)
6.  [使用 Jenkins CD 将带有数据库的后端 api 应用程序部署到 kubernetes 集群](https://dev.to/pongsatt/deploy-back-end-api-application-with-database-to-the-kubernetes-cluster-with-jenkins-cd-51j7)