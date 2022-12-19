# 向 VMware CentOS 7 虚拟机添加硬盘

> 原文：<https://dev.to/juttayaya/add-hard-disk-to-vmware-centos-7-vm-22m8>

为 VMware CentOS 7 虚拟机添加硬盘有两个简单的步骤。

1.  在 VMware vSphere 中分配新硬盘
2.  在 CentOS 中配置新硬盘

## 在 vSphere 中分配硬盘

1.  在 VMware vSphere 中，右键单击虚拟机并选择**“编辑设置...”**
    [![VMware edit settings](../Images/ffa02486eb708f4cb7e2f8f63f954c72.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--mek0FTvr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/vmware_add_disk1.png)

2.  在**“新设备:”**区域，选择**“SCSI 控制器”**，点击**“确定”**按钮。请注意，每个虚拟机有 4 个 SCSI 控制器的硬性限制。
    [![VMware SCSI Controller](../Images/ed115d202a8c1268176d027cc32b4ac6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3hFUR_8I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/vmware_add_disk_2.png)

3.  右击**“编辑设置...”再次选择**并选择**【新硬盘】**
    [![VMware New Hard Disk](../Images/eda7e33b5130f083d8761609cefa9de9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZUDFh8qH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/vmware_add_disk_03.png)

4.  输入新硬盘的字节大小(在本例中为 5 GB)。在**“虚拟设备节点”**部分，分配新的 SCSI 控制器号。点击**【确定】**按钮。
    [![VMware Assign SCSI](../Images/f9e868a65057236ca1d4fafdc6b96ef1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kMzQKt4l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/vmware_add_disk_04.png)

## 以 CentOS 为单位配置新硬盘

*   SSH 进入 CentOS VM 和 sudo 到 root
    T1】sudo su-T3】

*   列出数据块设备以查看新分配的 vSphere 硬盘。
    *lsblk*
    [![CentOS lsblk](../Images/5e66e2f3b305bf220003012d524b62f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VbgsASWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/centos_add_disk_lsblk.png)
    如果看不到 vSphere 硬盘，请强制重新扫描

```
for host in $(ls -1d /sys/class/scsi_host/*); do echo "- - -" > ${host}/scan ; done
for device in $(ls -1d /sys/class/scsi_disk/*); do echo "1" > ${device}/device/rescan ; done 
```

*   格式化磁盘分区。从前面的 *lsblk* 输出
    T3】fdisk/dev/sdbT5】中获取设备名称查看截图您应该选择的选项
    *(新分区)
    *p* (主)
    *(按 ENTER)* (使用默认分区号)
    *(按 ENTER)* (使用默认第一个扇区)
    *(按 ENTER)【回车】**

     **t* (改变分区类型)
    *8e* (Linux LVM)

    *w* (写)

    [![CentOS fdisk](../Images/1cce9b7a073e0e023ef3e030a297ed53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yhc2FIGo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/centos_add_disk_fdisk.png)T3】* 
**   再次列出块设备以显示新的磁盘分区/dev/sdb1
    [![CentOS lsblk2](../Images/40d4181810135af0c6d6866482ff7ad3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xEXjxbCb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/centos_add_disk_lsblk02.png) 

    *   初始化物理卷
    *pvcreate/dev/SD B1*
    *PVS*(显示新的物理卷)
    [![CentOS pv](../Images/09e53ea4ba0422e3968c4cc6cfdcaf91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cF-IKTJI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/centos_add_disk_pv.png) 

    *   创建卷组
    *vgcreate vgBackup/dev/SD B1*(vgBackup 只是这个示例的一个名称。卷组名可以随便)
    *vgs* (显示卷组)

    *   为卷组
    *创建逻辑卷 lvcreate-n LV backup-l+100% FREE vgBackup*(LV backup 只是这个例子的一个名称。逻辑卷名可以随便)
    *lvs* (显示逻辑卷)
    [![CentOSlv](../Images/83806bafb209af6326611b71358b1b39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--suB6tN3g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/centos_add_disk_lv.png) 

    *   在新的逻辑卷
    *mkfs . xfs/dev/vgBackup/LV backup*
    [![CentOS mkfs](../Images/6b32a48abe3a535e379d888b7d280161.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CXyPJNK0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/centos_add_disk_mkfs.png)
    上构建一个 XFS 文件系统

    *   在逻辑卷上挂载一个 Unix 目录
    编辑文本文件/etc/fstab，并在下面添加一行:
    **/dev/vgBackup/LV backup/backup xfs defaults 1 2**

    *mkdir-p/backup*
    *mount/backup*
    *df-h*(显示新目录)
    [![CentOS fstab](../Images/a19fd9520ae3de8a8a6cee5963a05ef7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gj9uWxpr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/juttayaya/stackoverflow/master/vmware-disk-img/centos_add_disk_fstab.png)* 

 *要了解更多关于 Unix 逻辑卷的信息，请访问官方的 [RedHat LVM 文档](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/lvm_definition)*