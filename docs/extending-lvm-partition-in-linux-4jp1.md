# 在 Linux 中扩展 LVM 分区？

> 原文：<https://dev.to/jjmpsp/extending-lvm-partition-in-linux-4jp1>

我试图将我的 ubuntu 虚拟机的根分区上的磁盘空间从 1.5TB 扩展到 3.4TB。我认为这是一个简单的任务，但我很难找到如何在操作系统层面上做到这一点。下面的控制台输出解释了我当前的设置(没什么特别的，只是一个有大量存储空间的分区):

* * *

```
root@ubuntu-samba:/home/joel# fdisk -l
Disk /dev/sda: 3.4 TiB, 3758096384000 bytes, 7340032000 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: dos
Disk identifier: 0xbad8753d

Device     Boot   Start        End    Sectors  Size Id Type
/dev/sda1  *       2048     999423     997376  487M 83 Linux
/dev/sda2       1001470 3147825151 3146823682  1.5T  5 Extended
/dev/sda5       1001472 3147825151 3146823680  1.5T 8e Linux LVM

Partition 2 does not start on physical sector boundary.

Disk /dev/mapper/ubuntu--samba--vg-root: 1.5 TiB, 1606879805440 bytes, 3138437120 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes

Disk /dev/mapper/ubuntu--samba--vg-swap_1: 4 GiB, 4290772992 bytes, 8380416 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes 
```

* * *

```
root@ubuntu-samba:/home/joel# vgdisplay
  --- Volume group ---
  VG Name               ubuntu-samba-vg
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  10
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               1.47 TiB
  PE Size               4.00 MiB
  Total PE              384133
  Alloc PE / Size       384133 / 1.47 TiB
  Free  PE / Size       0 / 0   
  VG UUID               RZUZwh-xTB4-Dh90-vrYA-Ej1e-kUiM-az4y2k 
```

* * *

```
root@ubuntu-samba:/home/joel# lvs
  LV     VG              Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root   ubuntu-samba-vg -wi-ao---- 1.46t                                                    
  swap_1 ubuntu-samba-vg -wi-ao---- 4.00g 
```

* * *

```
root@ubuntu-samba:/home/joel# vgs
  VG              #PV #LV #SN Attr   VSize VFree
  ubuntu-samba-vg   1   2   0 wz--n- 1.47t    0 
```

* * *

```
root@ubuntu-samba:/home/joel# pvs
  PV         VG              Fmt  Attr PSize PFree
  /dev/sda5  ubuntu-samba-vg lvm2 a--  1.47t    0 
```

* * *

```
root@ubuntu-samba:/home/joel# df -h
Filesystem                          Size  Used Avail Use% Mounted on
udev                                2.0G     0  2.0G   0% /dev
tmpfs                               395M   11M  385M   3% /run
/dev/mapper/ubuntu--samba--vg-root  1.5T  1.4T   55G  97% /
tmpfs                               2.0G     0  2.0G   0% /dev/shm
tmpfs                               5.0M     0  5.0M   0% /run/lock
tmpfs                               2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/sda1                           472M  310M  138M  70% /boot
tmpfs                               395M     0  395M   0% /run/user/1000 
```

* * *

*问题:*
我如何扩展物理卷“/dev/sda5”来使用 100%的“/dev/sda”的存储(3.4TB)？

我做了很多尝试，但毫无进展。许多在线教程声称这就像扩展逻辑卷一样简单，但这对我不起作用，因为没有检测到额外的存储空间。

```
lvextend -L +100%FREE /dev/mapper/ubuntu--samba--vg-root 
```

```
 New size (383110 extents) matches existing size (383110 extents) 
```

任何指示都将不胜感激，因为我已经在这上面浪费了好几天，并且非常需要额外的存储空间:(