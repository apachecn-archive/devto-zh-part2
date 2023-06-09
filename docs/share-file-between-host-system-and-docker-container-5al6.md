# 在主机系统和 Docker 容器之间共享文件。

> 原文：<https://dev.to/umeshdhakar/share-file-between-host-system-and-docker-container-5al6>

**1。在主机系统中创建一个文件夹。**
`C:\\<directory-name>`
(如 C:\ \隧道)

**2。将此文件夹添加到虚拟机的共享文件夹列表中。**
`Go to : Machine > Settings > Shared Folder`
(此处添加文件夹。)

**3。在虚拟机中创建文件夹。**
`mkdir /<directory-name>`
(如 mkdir /vm_tunnel)

**4。在虚拟机目录中挂载共享文件夹。**
`mount -t vboxsf <host-directory-name> /<vm-directory-name>`
(例如 mount -t vboxsf Tunnel /vm_tunnel)

**5。通过在 tunnel 或/vm_tunnel 中创建文件并签入
/vm_tunnel 或 Tunnel 来测试共享。**

**6。在主机和容器之间共享文件。**
`docker cp <container>:/path/to/file/in/container /path/of/vm-direcory/where/host-folder/is/mounted`
(例如 docker CP foo:/data/bar . txt/VM _ tunnel/)

感谢阅读！😊