# 如何使用 VMware Workstation 在 windows 上使用 Docker(无 hyper-v)

> 原文：<https://dev.to/ans_ashkan/how-to-use-docker-on-windows-using-vmware-workstation-without-hyper-v-eib>

我使用 docker for windows 有一段时间了，但是它的网络适配器不适合我。导致 DNS 问题和....
所以我尝试在不使用 hyper-v 的情况下使用 docker，并产生了将 Docker 与 vmware workstation 配合使用的想法。

这是如何设置一个简单的环境:

1.  安装 VMware Workstation
2.  从[https://download.docker.com/win/](https://download.docker.com/win/)下载 Docker 二进制文件
3.  从[https://github.com/docker/machine/releases](https://github.com/docker/machine/releases)下载 Docker 机器二进制文件
4.  下载 Docker Machine Vmware Workstation 驱动程序表单[https://github . com/pecigonzalo/Docker-Machine-Vmware Workstation/releases](https://github.com/pecigonzalo/docker-machine-vmwareworkstation/releases)
5.  将下载的文件提取并移动到路径中的一个目录下
6.  在 cmd 中执行`docker-machine create --driver=vmwareworkstation dev`创建一个机器
7.  在 cmd 中执行`docker-machine env dev > dockerdev.bat`,创建一个名为 dockerdev.bat 的批处理文件，并将它复制/移动到您的路径中
8.  执行步骤 7 中创建的批处理文件(dockerdev)
9.  照常使用`docker`