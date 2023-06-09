# 使用名称空间和 Bash 创建容器

> 原文：<https://dev.to/nicolasmesa/container-creation-using-namespaces-and-bash-6g>

交叉发布自我的个人博客[https://blog.nicolasmesa.co](https://blog.nicolasmesa.co)。

几周前，我看到了一个很棒的视频，解释了 Docker 是如何在引擎盖下工作的(见下面的视频)。视频以一个演示结束，其中 jérme Petazzoni 仅使用 bash 创建了一个容器。我发现他使用的许多命令都很神秘，所以我决定解释他做了什么以及每个命令的目的。

## 视频

[https://www.youtube.com/embed/sK5i-N34im8](https://www.youtube.com/embed/sK5i-N34im8)

(演示大约在第 41 分钟开始)

## 术语

在开始演示之前，让我们先了解一些术语。

### 容器

容器是一些技术的组合，包括名称空间、cgroups 和功能。在本文中，我们将关注名称空间。

### 命名空间

来自操作中的[名称空间:](https://lwn.net/Articles/531114/)

> 每个命名空间的目的是将特定的全局系统资源包装在一个抽象中，使命名空间内的进程看起来拥有自己的全局资源的独立实例。

Linux 中有六种类型的名称空间:

1.  Pid:隔离进程标识符。例如，不同名称空间中的两个进程可以有相同的 *PID* 。
2.  用户:隔离用户 id 和组 id。两个不同用户名称空间中的两个用户可以有相同的用户 id。这非常有用，因为它允许将名称空间外的非特权用户 id 映射为名称空间内的根用户 id。
3.  Net:这个命名空间提供网络隔离。在单独的网络命名空间中运行的进程看不到其他命名空间的网络接口。
4.  Mnt:挂载名称空间允许容器拥有自己的挂载点，而不会污染全局名称空间。它还提供了一种对容器隐藏全局挂载点的方法。
5.  Uts:这允许一个容器拥有它自己的运行在容器中的进程的主机名。
6.  Ipc:赋予容器自己的进程间通信名称空间。

**注意**:名称空间的意义远不止于此。如果你想了解更多，看看操作中的[名称空间。](https://lwn.net/Articles/531114/)

### 偏置

来自 [Btrfs wiki](https://btrfs.wiki.kernel.org/index.php/Main_Page)

> Btrfs 是一个用于 Linux 的现代写时复制(CoW)文件系统，旨在实现高级特性，同时还侧重于容错、修复和易于管理。

## 设置

### 系统

在这个演示中，我使用了一台运行 Ubuntu 16.04 的电脑。更具体地说:

```
nmesa@desktop-nicolas:~/demos/containers$  uname -a
Linux desktop-nicolas 4.13.0-39-generic #44~16.04.1-Ubuntu SMP Thu Apr 5 16:43:10 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux 
```

我们还需要 *docker* 做两件事:

1.  来提取*阿尔卑斯*码头工人的图像。
2.  使用 *docker* 提供的网桥进行联网。

这是我安装的 *docker* 的版本:

```
root@desktop-nicolas:/btrfs#  docker version
Client:
 Version: 18.06.0-ce
 API version: 1.38
 Go version: go1.10.3
 Git commit: 0ffa825
 Built: Wed Jul 18 19:11:02 2018
 OS/Arch: linux/amd64
 Experimental: false

Server:
 Engine:
  Version: 18.06.0-ce
  API version: 1.38 (minimum version 1.12)
  Go version: go1.10.3
  Git commit: 0ffa825
  Built: Wed Jul 18 19:09:05 2018
  OS/Arch: linux/amd64
  Experimental: false 
```

### 系统设置

在我们开始跟随视频之前，我们需要将我们的环境设置为与杰罗姆的计算机相同的状态。我们需要在 */btrfs* 中安装一个 *btrfs* 文件系统。

#### 创建磁盘镜像

我们首先创建一个空的磁盘映像(从 [btrfs wiki](https://btrfs.wiki.kernel.org/index.php/Getting_started#Basic_Filesystem_Commands) 开始，映像的大小应该至少为 1GB)。

```
nmesa@desktop-nicolas:~/demos/containers$  dd if=/dev/zero of=disk.img bs=512 count=4194304
4194304+0 records in
4194304+0 records out
2147483648 bytes (2.1 GB, 2.0 GiB) copied, 4.60753 s, 466 MB/s nmesa@desktop-nicolas:~/demos/containers$  ls -alh disk.img
-rw-rw-r-- 1 nmesa nmesa 2.0G Aug 23 22:06 disk.img 
```

我们使用`dd`命令创建一个 2GB 的空映像。`dd`命令接收四个参数:

*   `if`:用作输入的文件/设备。在本例中，`/dev/zero`，它输出一串零。
*   `of`:输出文件/设备。这种情况下，`disk.img`。
*   `bs`:这是以字节为单位的块大小(本例中为 512 字节)。
*   `count`:从`/dev/zero`读取并写入`disk.img`的`bs`块数。在本例中，4194304 (2 * 1024 * 1024 * 1024 / 512)使图像为 2GB。

#### 格式化磁盘镜像

让我们使用`mkfs.btrfs`命令用 *btrfs* 文件系统格式化我们的新映像。

```
nmesa@desktop-nicolas:~/demos/containers$  mkfs.btrfs disk.img
btrfs-progs v4.4
See http://btrfs.wiki.kernel.org for more information.

Label: (null)
UUID: 772f0676-ec96-448d-b58c-31d4c10b5c3a
Node size: 16384
Sector size: 4096
Filesystem size: 2.00GiB
Block group profiles:
  Data: single 8.00MiB
  Metadata: DUP 110.38MiB
  System: DUP 12.00MiB
SSD detected: no
Incompat features: extref, skinny-metadata
Number of devices: 1
Devices:
   ID SIZE PATH
    1 2.00GiB disk.img 
```

#### 挂载磁盘镜像

太好了！我们已经格式化了文件系统！我们需要使用`mount`命令将映像挂载到 */btrfs* (注意，您必须是 **root** )。

```
root@desktop-nicolas:/home/nmesa/demos/containers#  mkdir /btrfs
root@desktop-nicolas:/home/nmesa/demos/containers#  mount -t btrfs disk.img /btrfs 
```

我们准备好跟随视频了！

## 视频击穿

在本节中，我们将跟随视频，我将描述每个命令的作用。大多数命令要求您以根用户的身份运行它们。

### 使坐骑私有

```
root@desktop-nicolas:/#  mount --make-rprivate / 
```

使用这个命令，我们可以使容器将要私有的挂载成为私有的。这可以防止我们的容器进行的任何装载被主机系统看到。`--make-rprivate`中的`r`代表*递归*，这意味着它使所有挂载点私有。

### 创建容器图像

让我们创建两个目录，一个保存源图像，一个保存容器图像。

```
root@desktop-nicolas:/btrfs#  mkdir images containers 
```

然后我们使用`btrfs subvol create`命令创建一个名为*阿尔卑斯山*的新图像。

```
root@desktop-nicolas:/btrfs#  btrfs subvol create images/alpine
Create subvolume 'images/alpine' root@desktop-nicolas:/btrfs#  ls images/alpine/
root@desktop-nicolas:/btrfs# 
```

`btrfs`命令用于控制 *btrfs* 文件系统。`subvolume`子命令用于管理 *btrfs 子卷*。我们使用`create`子命令在*图像*目录中创建一个名为 *alpine* 的子卷。

我们有图像，但它是空的。我们使用`docker`来得到*阿尔卑斯山*的图像。

```
root@desktop-nicolas:/btrfs#  CID=$(docker run -d alpine true)
root@desktop-nicolas:/btrfs#  echo $CID
9b290758c1734811c85445640c985fed9803121891d6604b4260e985c2647404 root@desktop-nicolas:/btrfs#  docker export $CID | tar -C images/alpine/ -xf-
root@desktop-nicolas:/btrfs#  ls images/alpine/
bin dev etc home lib media mnt proc root run sbin srv sys tmp usr var 
```

我们运行`docker run -d alpine true`并将它的输出(容器 id)分配给`CID`变量。下面是对`docker`命令的更好的解释:

*   `run`:运行一个容器。
*   `-d`:卸下容器，打印容器 id。我们的终端不等待容器完成它的执行。
*   `alpine`:容器应该使用的图像。
*   `true`:在容器中运行的命令。

我们使用`docker export`命令来导出图像。我们通过管道将图像传输到`tar`命令，该命令对其进行解压缩，并将文件放入 *images/alpine* 中。然后我们运行`ls images/alpine`来查看图像的内容。

下一步是拍摄这个图像的快照，并把它放在我们的容器文件夹中。这张快照的名字是[特百惠](https://en.wikipedia.org/wiki/Tupperware)。

```
root@desktop-nicolas:/btrfs#  btrfs subvol snapshot images/alpine/ containers/tupperware
Create a snapshot of 'images/alpine/' in 'containers/tupperware' root@desktop-nicolas:/btrfs#  ls containers/tupperware/
bin dev etc home lib media mnt proc root run sbin srv sys tmp usr var 
```

我们使用`snapshot`子命令在*容器*目录中创建名为*特百惠*的子卷 *alpine* 的快照。快照非常高效。它们不从源子卷复制数据，这使得它们能够高效利用空间并快速创建。如果修改了快照，原始子卷不会受到影响。我们可以在下面的例子中看到这一点:

```
root@desktop-nicolas:/btrfs#  touch containers/tupperware/NICK_WAS_HERE
root@desktop-nicolas:/btrfs#  ls containers/tupperware/
bin dev etc home lib media mnt NICK_WAS_HERE proc root run sbin srv sys tmp usr var root@desktop-nicolas:/btrfs#  ls images/alpine/
bin dev etc home lib media mnt proc root run sbin srv sys tmp usr var 
```

在本例中，我们将名为 *NICK_WAS_HERE* 的文件添加到快照中。然后我们证明它在快照中可见，但在原始的*阿尔卑斯山子体积*中不可见。关于`btrfs`命令的更多信息可以在[这篇文章](https://www.linux.com/learn/how-create-and-manage-btrfs-snapshots-and-rollbacks-linux-part-2)中找到。

### 使用 chroot 进行测试

让我们使用`chroot`命令在*特百惠快照*中运行`sh`。

```
root@desktop-nicolas:/btrfs#  chroot containers/tupperware/ sh
/ #  ls /
NICK_WAS_HERE dev home media proc run srv tmp var
bin etc lib mnt root sbin sys usr / #  apk
apk-tools 2.10.0, compiled for x86_64.

Installing and removing packages:
  add Add PACKAGEs to 'world' and install (or upgrade) them, while ensuring that all dependencies are met
  del Remove PACKAGEs from 'world' and uninstall them

System maintenance:
  fix Repair package or upgrade it without modifying main dependencies
  update Update repository indexes from all remote repositories
  upgrade Upgrade currently installed packages to match repositories
  cache Download missing PACKAGEs to cache and/or delete unneeded files from cache

Querying information about packages:
  info Give detailed information about PACKAGEs or repositories
  list List packages by PATTERN and other criteria
  dot Generate graphviz graphs
  policy Show repository policy for packages

Repository maintenance:
  index Create repository index file from FILEs
  fetch Download PACKAGEs from global repositories to a local directory
  verify Verify package integrity and signature
  manifest Show checksums of package contents Use apk <command>  --help for command-specific help.
Use apk --help --verbose for a full command listing.

This apk has coffee making abilities. / #  exit
root@desktop-nicolas:/btrfs# 
```

`chroot`用于改变一个程序的根目录。在上面的例子中，*/Bt rfs/container/tupperware*目录成为文件系统的根目录。我们执行`apk`，它只在*阿尔卑斯*可用，然后我们退出容器。

注意，我们没有在这个“容器”中使用名称空间让我们改变这一切！

### 使用名称空间

```
root@desktop-nicolas:/btrfs#  unshare --mount --uts --ipc --net --pid --fork bash
root@desktop-nicolas:/btrfs# 
```

我们使用`unshare`命令在不同的*挂载*、 *uts* 、 *ipc* 、 *net* 和 *pid* 命名空间中运行一个程序( *bash* )。我们还传入了`--fork`标志来创建一个新流程，作为`unshare`命令的子流程。命令输出看起来好像什么都没发生，但是我们使用了名称空间，并且与全局名称空间有些隔离。让我们更改*主机名*来证明我们在一个隔离的 *uts* 名称空间中。

```
root@desktop-nicolas:/btrfs#  hostname tupperware
root@desktop-nicolas:/btrfs#  exec bash
root@tupperware:/btrfs# 
```

注意，在执行了`bash`之后，我们的主机名切换到了*特百惠*。从另一个终端运行`hostname`显示全局名称空间仍然具有相同的*主机名*(在本例中为 *desktop-nicolas* )。

```
#  from a different terminal
nmesa@desktop-nicolas:~/demos/containers$  hostname
desktop-nicolas 
```

让我们用 *pid 名称空间*做一些实验。

```
root@tupperware:/btrfs#  ps
 PID TTY TIME CMD
24506 pts/26 00:00:00 bash
24551 pts/26 00:00:00 unshare
24552 pts/26 00:00:00 bash
24961 pts/26 00:00:00 ps
28780 pts/26 00:00:00 sudo
28781 pts/26 00:00:00 su
28782 pts/26 00:00:00 bash root@tupperware:/btrfs#  pidof unshare
24551 root@tupperware:/btrfs#  kill $(pidof unshare)
bash: kill: (24551) - No such process 
```

我们看到的流程比预期的多得多。*PID*也是主系统的 PID。这是怎么回事？原来是挂载了全局名称空间的*/proc*,`ps`命令使用 */proc* 来获取它的一些信息。(您可以通过运行`strace ps`来验证这一点。)

### 挂载程序

让我们挂载 */proc* 。

```
root@tupperware:/btrfs#  mount -t proc proc /proc
root@tupperware:/btrfs#  ps
 PID TTY TIME CMD
    1 pts/26 00:00:00 bash
   30 pts/26 00:00:00 ps root@tupperware:/btrfs#  umount /proc
root@tupperware:/btrfs# 
```

在我们挂载了 *proc* 之后，`ps`只显示了在我们的容器中运行的具有正确*PID*的进程。我们现在再次卸载 */proc* 。

我们以前用过`mount`。让我们来看看它是做什么的。

`mount`命令用于挂载文件系统，通常有以下语法:

```
mount -t <type>  <device> <directory> 
```

从[安装手册页](https://linux.die.net/man/8/mount)中:

> 这告诉内核将在`<device>`(类型为`<type>`)上找到的文件系统附加到目录`<directory>`中。

以下是我们具体案例的论点分类:

*   `-t proc`:要使用的文件系统类型是 *proc* 。
*   `proc`:*/proc*文件系统没有连接到设备。这里的任何关键字都可以。
    *   请注意，在视频中，杰罗姆使用 *none* 代替 *proc* 作为该值。我把它改成了 *proc* ，因为 [mount 手册页](https://linux.die.net/man/8/mount)不鼓励使用 *none* 来处理这个问题(“通常的选择 *none* 不太好:umount 的错误消息‘none busy’可能会令人困惑。”).
*   `/proc`:我们想要挂载 *proc* 文件系统的目录。

### 枢根

让我们使用`mount`和`pivot_root`将*/btrfs/containers/tupperware*作为我们文件系统的根。

```
root@tupperware:/btrfs#  mkdir /btrfs/containers/tupperware/oldroot
root@tupperware:/btrfs#  mount --bind /btrfs/containers/tupperware /btrfs
root@tupperware:/btrfs#  cd /btrfs/
root@tupperware:/btrfs#  ls
bin dev etc home lib media mnt NICK_WAS_HERE oldroot proc root run sbin srv sys tmp usr var root@tupperware:/btrfs#  pivot_root . oldroot
root@tupperware:/btrfs#  cd /
root@tupperware:/#  ls
NICK_WAS_HERE dev home media oldroot root sbin sys usr
bin etc lib mnt proc run srv tmp var root@tupperware:/#  ls oldroot/
bin cdrom hello lib media root srv usr
boot core home lib32 mnt run sys var
bt dev initrd.img lib64 opt sbin tes vmlinuz
btrfs etc initrd.img.old lost+found proc snap tmp vmlinuz.old 
```

让我们来分解一下:

1.  我们创建了一个名为 *oldroot* 的目录，我们当前的根文件系统将安装在这个目录中。正如杰罗姆在视频中解释的那样，为了能够成功运行`pivot_root`，您需要接近文件系统的顶部( */* )。
2.  我们使用`mount --bind`将容器文件系统获取到 */btrfs* 。(杰罗姆把这个分成两步，我当时没明白为什么。我决定用这种方法试试，看看是否可行，结果也确实如此。`mount --bind`命令对于在其他地方挂载一个目录很有用(在我们的例子中，我们挂载*/Bt rfs/containers/tupperware*到 */btrfs* )。
3.  我们转到 */btrfs* 并运行`ls`以确保我们的容器文件系统被挂载在那里。
4.  我们执行`pivot_root`，将当前目录( */btrfs* )切换为新的根目录，并将当前根目录挂载到*旧根目录*中。
5.  我们移动到新的根目录，并通过运行`ls`检查它是否被正确挂载。
6.  我们验证了 *oldroot* 具有旧的根挂载点。

**注意**:我不明白为什么我们需要做一个`pivot_root`而不是做一个`/`的绑定挂载点。莱纳斯·托沃兹本人的这封电子邮件帮助我理解了。简而言之，`/`指向**进程的文件系统**的根。通过对 root ( `/`)进行绑定挂载，这个过程仍然指向旧版本的`/`。

### 固定挂载点

让我们看看我们的挂载点。

```
root@tupperware:/#  mount -t proc proc /proc
root@tupperware:/#  mount | head
/dev/sda2 on /oldroot type ext4 (rw,relatime,errors=remount-ro,data=ordered)
/dev/sda2 on /oldroot type ext4 (rw,relatime,errors=remount-ro,data=ordered)
udev on /oldroot/dev type devtmpfs (rw,nosuid,relatime,size=16404692k,nr_inodes=4101173,mode=755)
devpts on /oldroot/dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /oldroot/dev/shm type tmpfs (rw,nosuid,nodev)
mqueue on /oldroot/dev/mqueue type mqueue (rw,relatime)
hugetlbfs on /oldroot/dev/hugepages type hugetlbfs (rw,relatime,pagesize=2M)
tmpfs on /oldroot/run type tmpfs (rw,nosuid,noexec,relatime,size=3286976k,mode=755)
tmpfs on /oldroot/run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
cgmfs on /oldroot/run/cgmanager/fs type tmpfs (rw,relatime,size=100k,mode=755) 
```

我们先挂载 */proc* ，因为`mount`依赖它。(您可以通过运行`strace mount`来验证这一点)。

**注意**:挂载点列表很漂亮，很大，所以我用管道把它传给`head`来显示前 10 行。

这些挂载中的大部分不应该是我们容器的一部分，所以让我们卸载所有的东西并再次挂载 */proc* 。

```
root@tupperware:/#  umount -a
umount: can't unmount /oldroot/btrfs: Resource busy
umount: can't unmount /oldroot: Resource busy
umount: can't unmount /oldroot: Resource busy root@tupperware:/#  mount -t proc proc /proc
root@tupperware:/#  mount
/dev/sda2 on /oldroot type ext4 (rw,relatime,errors=remount-ro,data=ordered)
/dev/sda2 on /oldroot type ext4 (rw,relatime,errors=remount-ro,data=ordered)
/dev/loop0 on /oldroot/btrfs type btrfs (ro,relatime,space_cache,subvolid=5,subvol=/)
/dev/loop0 on / type btrfs (ro,relatime,space_cache,subvolid=258,subvol=/containers/tupperware)
proc on /proc type proc (rw,relatime) 
```

由于资源繁忙， */oldroot* 的`umount`失败。这就是为什么当我们运行`mount`时 */oldroot* 仍然会出现。让我们解决这个问题。

```
root@tupperware:/#  umount -l /oldroot
root@tupperware:/#  mount
/dev/loop0 on / type btrfs (ro,relatime,space_cache,subvolid=258,subvol=/containers/tupperware)
/dev/loop0 on / type btrfs (ro,relatime,space_cache,subvolid=258,subvol=/containers/tupperware)
proc on /proc type proc (rw,relatime) 
```

`umount`命令中的`-l`标志代表懒惰。根据[卸载手册页](https://linux.die.net/man/8/umount)，这个标志将“立即从文件层次结构中分离文件系统，并且一旦文件系统不再繁忙，就清除对它的所有引用。”因此，当我们重新运行`mount`时，我们看不到任何对 */oldroot* 的引用。

### 联网

让我们验证一下我们是否无法访问网络。

```
root@tupperware:/#  ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
ping: sendto: Network unreachable root@tupperware:/#  ifconfig -a
lo Link encap:Local Loopback
          LOOPBACK MTU:65536 Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B) TX bytes:0 (0.0 B) 
```

很好！我们没有网络接入，只有环回接口。让我们解决这个问题。

**注意**:我们从一个单独的终端(不在我们的容器中)运行下面的命令。

我们需要容器的进程 id(`unshare`的 *PID* )。

```
root@desktop-nicolas:/home/nmesa#  CPID=$(pidof unshare)
root@desktop-nicolas:/home/nmesa#  echo $CPID
24551 
```

我们将 *PID* 分配给`CPID`变量，以便在您跟随时更容易复制/粘贴命令。让我们创建一对虚拟网络接口。

```
root@desktop-nicolas:/home/nmesa#  ip link add name h$CPID type veth peer name c$CPID 
```

`ip link add`命令创建两个类型为`veth`的接口(虚拟以太网接口)。一个名为 *h24551* ，另一个名为 *c24551* 。 *h* 代表主机， *c* 代表容器， *24551* 是我们容器的 *PID* 。

让我们将 *c24551* 接口移动到我们的容器中(注意这个命令也是从主机上执行的)。

```
root@desktop-nicolas:/home/nmesa#  ip link set c$CPID netns $CPID 
```

`ip link set`命令为接口设置配置属性。在这种情况下，它将 *netns* (网络名称空间)设置为与存储在 *$CPID* 中的进程 id 相同(在我们的例子中为 *24551* )。

让我们回到我们的容器终端，验证它是否有了新的接口。

```
root@tupperware:/#  ifconfig -a
c24551 Link encap:Ethernet HWaddr 96:18:F1:61:12:A2
          BROADCAST MULTICAST MTU:1500 Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B) TX bytes:0 (0.0 B)

lo Link encap:Local Loopback
          LOOPBACK MTU:65536 Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B) TX bytes:0 (0.0 B) 
```

太好了！我们的容器里有接口！让我们回到我们的主机终端，将 *h24551* 接口连接到 docker 桥( *docker0* )。

```
root@desktop-nicolas:/home/nmesa#  ip link set h$CPID master docker0 up
root@desktop-nicolas:/home/nmesa#  ifconfig docker0
docker0 Link encap:Ethernet HWaddr 02:42:ac:03:c8:91
          inet addr:172.17.0.1 Bcast:172.17.255.255 Mask:255.255.0.0
          inet6 addr: fe80::42:acff:fe03:c891/64 Scope:Link
          UP BROADCAST MULTICAST MTU:1500 Metric:1
          RX packets:77485 errors:0 dropped:0 overruns:0 frame:0
          TX packets:77624 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:6199951 (6.1 MB) TX bytes:164681776 (164.6 MB) 
```

我们使用`ip link set`命令将接口连接到 *docker0* 桥。我们还获取了我们的 *docker0* 接口的 ip 地址: *172.17.0.1* (记下它，因为稍后您会需要它)。

**注意**:杰罗姆没有完成这最后一步，我怀疑这就是为什么演示没有按他的预期进行。

让我们回到我们的集装箱码头，配置网络接口。

```
root@tupperware:/#  ip link set lo up
root@tupperware:/#  ip link set c24551 name eth0 up
root@tupperware:/#  ip addr add 172.17.42.3/16 dev eth0
root@tupperware:/#  ip route add default via 172.17.0.1
root@tupperware:/#  ifconfig
eth0 Link encap:Ethernet HWaddr 96:18:F1:61:12:A2
          inet addr:172.17.42.3 Bcast:0.0.0.0 Mask:255.255.0.0
          inet6 addr: fe80::9418:f1ff:fe61:12a2/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
          RX packets:35 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:3789 (3.7 KiB) TX bytes:936 (936.0 B)

lo Link encap:Local Loopback
          inet addr:127.0.0.1 Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING MTU:65536 Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B) TX bytes:0 (0.0 B) 
```

让我们来分解一下:

1.  我们打开环回接口。
2.  我们调出 *c24551* 接口，并将其重命名为 *eth0* 。请注意，此命令不能复制和粘贴，因为您的界面可能有不同的名称。
3.  我们将 ip 地址 *172.17.42.3* 分配给我们的 *eth0* 接口。注意，这个 ip 地址必须在我们从 *docker0* 接口得到的范围内(在我们的例子中是 172.17.0.0/16)。
4.  我们将容器的默认网关设置为 *172.17.0.1* (这应该是我们从 *docker0* 获得的 ip 地址)。
5.  我们快速运行`ifconfig`以确保一切设置正确。

现在，关键时刻到了。让我们乒*8.8.8.8*。

```
root@tupperware:/#  ping -c 3 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=121 time=11.045 ms
64 bytes from 8.8.8.8: seq=1 ttl=121 time=10.981 ms
64 bytes from 8.8.8.8: seq=2 ttl=121 time=10.508 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 10.508/10.844/11.045 ms 
```

我们的容器有网络连接！

### 让我们完成这项工作

正如杰罗姆在视频中指出的，我们仍然在容器内运行 *bash* ，但是 *alpine* 图像没有 *bash* 。到目前为止，我们一直在做容器运行时的工作。为了完成容器移交，我们运行下面的命令:

```
root@tupperware:/#  exec chroot / sh
/ # 
```

命令是 *bash* 中的内置工具(谁知道呢？).它用命令(在我们的例子中是 T1)替换 shell，而不启动新的进程。一旦这个命令被执行，我们就正式进入了一个容器。让我们运行几个命令。

```
/ #  apk
apk-tools 2.10.0, compiled for x86_64.

Installing and removing packages:
  add Add PACKAGEs to 'world' and install (or upgrade) them, while ensuring that all dependencies are met
  del Remove PACKAGEs from 'world' and uninstall them

System maintenance:
  fix Repair package or upgrade it without modifying main dependencies
  update Update repository indexes from all remote repositories
  upgrade Upgrade currently installed packages to match repositories
  cache Download missing PACKAGEs to cache and/or delete unneeded files from cache

Querying information about packages:
  info Give detailed information about PACKAGEs or repositories
  list List packages by PATTERN and other criteria
  dot Generate graphviz graphs
  policy Show repository policy for packages

Repository maintenance:
  index Create repository index file from FILEs
  fetch Download PACKAGEs from global repositories to a local directory
  verify Verify package integrity and signature
  manifest Show checksums of package contents Use apk <command>  --help for command-specific help.
Use apk --help --verbose for a full command listing.

This apk has coffee making abilities. / #  ls
NICK_WAS_HERE dev home media oldroot root sbin sys usr
bin etc lib mnt proc run srv tmp var 
```

## 清理

以下是一些清理步骤命令。

```
/ #  exit
root@desktop-nicolas:/btrfs#  exit
root@desktop-nicolas:/btrfs#  cd /
root@desktop-nicolas:/#  umount /btrfs
root@desktop-nicolas:/#  exit
exit nmesa@desktop-nicolas:~/demos/containers$  rm -f disk.img
nmesa@desktop-nicolas:~/demos/containers$ 
```

**注意**:我不需要删除我的 *veth* 接口。我不确定一旦网络名称空间消亡，它们是否会自动删除。

## 结论

大量工作在幕后进行，以提供容器！这只是名称空间部分。还有*群组*、*能力*、*设备*等。正如 jérme 建议的，不要创建自己的容器运行时，而是使用 [docker](https://www.docker.com/) 。

## 链接/进一步阅读

*   [原帖](https://blog.nicolasmesa.co/posts/2018/08/container-creation-using-namespaces-and-bash/)
*   [btr](https://btrfs.wiki.kernel.org/index.php/Main_Page)
*   【BTRFS 入门
*   [BTRFS 教程](https://www.linux.com/learn/how-manage-btrfs-storage-pools-subvolumes-and-snapshots-linux-part-1)
*   [挂载手册页](https://linux.die.net/man/8/mount)
*   [卸载手册页](https://linux.die.net/man/8/umount)
*   [码头工人](https://www.docker.com/)
*   [在纯 bash 和 C 中构建容器](https://containersummit.io/events/nyc-2016/videos/building-containers-in-pure-bash-and-c)
*   [操作中的名称空间](https://lwn.net/Articles/531114/)