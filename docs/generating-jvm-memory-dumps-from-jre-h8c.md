# 从 JRE 生成 JVM 内存转储

> 原文：<https://dev.to/jarekprzygodzki/generating-jvm-memory-dumps-from-jre-h8c>

# 从 Linux/Windows/OSX 上的 JRE 生成 JVM 内存转储

用 JDK 生成 JVM 堆内存转储很简单，因为几乎每个 Java 开发人员都知道 JDK 附带的 jmap 和 jcmd 工具。但是 JRE 呢？

有些人认为你[需要 JDK](https://stackoverflow.com/questions/24213491/how-do-i-produce-a-heap-dump-with-only-a-jre) ，或者至少[的一部分](https://medium.com/@chamilad/extracting-memory-and-thread-dumps-from-a-running-jre-based-jvm-26de1e37a080)，但那不是真的。答案就在 [jattach](https://github.com/apangin/jattach) 里，一个通过 JVM 黑客 Andrei Pangin([@ Andrei Pangin](https://twitter.com/andreipangin))创造的动态附加机制向 JVM 发送命令的工具。它很小(24KB)，只与 JRE 一起工作，并支持 Linux 容器。

## 用法

大多数时候，它归结为下载一个文件

```
wget -L -O /usr/local/bin/jattach \
    https://github.com/apangin/jattach/releases/download/v1.5/jattach && \
    chmod +x /usr/local/bin/jattach 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以发送`dumpheap`命令做 JVM 进程

```
jattach PID-OF-JAVA dumpheap <path to heap dump file> 
```

Enter fullscreen mode Exit fullscreen mode

例如

```
java_pid=$(pidof -s java) && \
    jattach $java_pid dumpheap /tmp/java_pid$java_pid-$(date +%Y-%m-%d_%H-%M-%S).hprof 
```

Enter fullscreen mode Exit fullscreen mode

## 它是如何工作的？

像 jmap 和 jstack 这样的内置 JDK 工具有两种执行模式:协作和强制。在正常协作模式下，这些工具使用动态连接机制连接到目标虚拟机。然后，目标虚拟机在其自己的进程中执行所请求的命令。该模式由 jattach 使用。

强制模式(jmap -F，jstack -F)的工作方式不同。该工具挂起目标进程，然后使用可服务性代理读取进程内存。详见[本](https://stackoverflow.com/questions/26140182/running-jmap-getting-unable-to-open-socket-file/35963059#35963059)。

## 码头工人

在 Java 10 jmap 之前，jstack 和 jcmd 不能从主机上的进程连接到 Docker 容器中运行的 JVM，因为连接机制与 pid 和 mount 名称空间的交互方式不同。Java 10 [通过容器中的 JVM 在根名称空间中找到它的 PID 并使用它来监视 JVM 附件来修复这个](https://bugs.openjdk.java.net/browse/JDK-8179498)。

Jattach 支持容器，并且与 JVM 的早期版本兼容——我们所需要的只是主机 PID 名称空间中的进程 id。怎么才能得到呢？

如果 JVM 是容器(PID 1)的主进程，那么所需的信息包含在`docker inspect`输出
中

```
cid=<container name or id>
host_pid=$(docker inspect --format {{.State.Pid}} $cid) 
```

Enter fullscreen mode Exit fullscreen mode

如果不是呢？然后事情就变得更有趣了。我所知道的最简单的方法是使用/proc/PID/sched - kernel 调度统计数据。

```
cid=<container name or id>
docker exec -it $cid bash -c 'cat /proc/$(pidof -s java)/sched'

java (8251, #threads: 127)
------------------------------------------------------------------------
se.exec_start                                :        275669.207074
se.vruntime                                  :            80.606203
se.sum_exec_runtime                          :            57.897264
nr_switches                                  :                  157
nr_voluntary_switches                        :                  149
nr_involuntary_switches                      :                    8
se.load.weight                               :                 1024
se.avg.load_sum                              :              8883079
se.avg.util_sum                              :                 4424
se.avg.load_avg                              :                  181
se.avg.util_avg                              :                   90
se.avg.last_update_time                      :         275669207074
policy                                       :                    0
prio                                         :                  120
clock-delta                                  :                   52
mm->numa_scan_seq                            :                    0
numa_migrations, 0
numa_faults_memory, 0, 0, 1, 0, -1
numa_faults_memory, 1, 0, 0, 0, -1 
```

Enter fullscreen mode Exit fullscreen mode

我们感兴趣的是输出的第一行(在[kernel/sched/debug . c # L877](https://github.com/torvalds/linux/blob/v4.18/kernel/sched/debug.c#L877)中定义的格式)。想要的 PID 可以通过一点点 shell 脚本来提取

```
docker exec -it $cid sh -c 'head -1 /proc/$(pidof -s java)/sched | grep -P "(?<=\()\d+" -o' 
```

Enter fullscreen mode Exit fullscreen mode

当目标容器是空的(没有外壳、没有猫、什么都没有)时，nsenter 是`docker exec`
的一个可能的替代

```
host_pid=$(docker inspect --format {{.State.Pid}} <container name or id>)
nsenter --target $host_pid  --pid --mount  sh -c 'cat /proc/$(pidof -s java)/sched' 
```

Enter fullscreen mode Exit fullscreen mode

## 会出什么差错？

项目发布页面上的 Jattach 链接到了 glibc，所以[很可能](https://wiki.alpinelinux.org/wiki/Running_glibc_programs)不能在 Alpine Linux 上工作。但是让它发挥作用并不太难。