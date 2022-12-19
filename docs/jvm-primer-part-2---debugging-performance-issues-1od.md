# JVM 初级教程第 2 部分——调试内存问题

> 原文：<https://dev.to/rrampage/jvm-primer-part-2---debugging-performance-issues-1od>

这是我的 JVM 内存管理和调试系列的第 2 部分。点击此处阅读第 1 部分:

[![rrampage](../Images/bec6c5c5b1a1ae239588667c16549de7.png)](/rrampage) [## JVM 内存管理和故障排除入门- 1

### 偈纳克罗摩克里希南 5 月 15 日 184 分钟阅读

#java #jvm #kotlin #programming](/rrampage/a-primer-on-jvm-memory-management-and-troubleshooting---1-12b6)

在这篇文章中，我们将讨论基于 JVM 的应用程序的内存问题的症状，我们可以使用哪些工具来诊断它们，以及我们如何修复它们。

#### 症状

以下是内存问题的一些症状:

1.  较差的应用程序性能
2.  内存使用异常
3.  内存不足错误(OOME)

#### 应用程序性能差

1.  应用程序的性能没有达到预期水平
2.  响应时间长
3.  丢弃客户端请求
4.  卡住的螺纹
5.  服务不可用
6.  应用程序日志中的时间戳差距很大

#### 记忆出现问题的原因:

1.  错误配置的内存
    *   旧一代的内存空间比对象的活动集要小。这将触发一次大规模的垃圾收集(GC ),导致更大的暂停。
    *   **代码缓存**比生成的编译代码占用空间小
    *   **年轻一代**的规模不合适，导致对象过早升级
    *   **PermGen / Metaspace** 大小不正确，导致完全垃圾收集
2.  内存泄漏——无意中在内存空间中保留对象
    *   无意中引用堆中的对象集
    *   没有适当地取消对类加载器实例的引用
    *   没有适当地释放本地资源
3.  过度使用**终结器**
    *   带有终结器的对象可能会延迟自己的 GC
    *   在回收实例之前，终结器线程需要调用实例的 finalize()方法
    *   只能有一个终结器线程。如果它跟不上对象变得可用于终结的速度，JVM 将因 OOME 而失败
    *   挂起的终结器对象本质上是累积的垃圾
    *   Java 9 中不推荐使用终结器
4.  显式 GC 调用
    *   **`System.gc()`** 和诊断数据收集会导致长时间暂停
    *   **`-XX:+DisableExplicitGC`** 可以禁用 System.gc()调用
    *   **`-XX:+PrintClassHistogram`** 在接收到`kill -3`信号时也调用显式 GC

#### 出内存错误

*   层级:`Throwable`->-`Error`->-`VirtualMachineError`->-`OutOfMemoryError`(未勾选异常)
*   当 JVM 耗尽了各种内存空间中的空间或者无法继续执行进程时抛出。一些可能性:
    *   堆空间已满
        *   JVM 已经调用了完整的 GC，但是无法释放空间
        *   堆的大小可能小于应用程序的占用空间，或者应用程序不必要地占用堆中的一些对象
    *   超出 GC 开销限制
        *   太多的 GC 占用了很少的空间
        *   应用程序线程没有获得任何 CPU 周期
    *   请求的阵列大小超过虚拟机限制
    *   永久空间/元空间/压缩类空间
        *   已调用完整 GC，但无法释放元空间中的空间，应用程序正在尝试加载更多类
        *   缺省情况下，元空间是“无限制的”,但可以由 MaxMetaspaceSize 控制。默认情况下，为压缩的类空间保留 1 GB
        *   确保-Xnoclassgc 没有被使用，因为它会阻止类的卸载
    *   本机内存-本机方法的交换空间/堆栈跟踪不足
        *   用于 Java 线程堆栈、加载的 jar、zip、本地库、本地资源(如文件)的本地空间；从本机代码分配的内存
        *   无法分配更多本机内存或创建新线程或本机内存泄漏
        *   在 64 位机器上运行 32 位 JVM 会给进程大小带来 4 GB 的限制
        *   Java 堆的位置可以限制本机堆的最大大小。可以由选项-XX:HeapBaseMinAddress=n 控制，以指定本机堆应该基于的地址

#### CodeCache 警告

*   JVM 打印的警告消息称 CodeCache 已满，编译器已被禁用。
*   **代码缓存满时无 OOME**
*   清扫车进行紧急清理。这可能会丢弃已编译的代码，JIT 可能需要再次执行优化
*   使用 **`ReservedCodeCacheSize`** 选项确保 CC 大小合适

#### 直接缓冲记忆

*   **`ByteBuffer.allocateDirect(N)`** :使用幻象引用和引用队列进行垃圾收集的直接缓冲区
*   默认情况下无限内存，但可以通过-XX:MaxDirectMemorySize=n 来控制
*   由 **Java NIO** 使用。I/O 堆字节缓冲区使用临时直接字节缓冲区

### 诊断数据，数据收集和分析工具

#### 排除内存泄漏故障

1.  **确认内存泄漏**

    *   监控一段时间内的堆使用情况
    *   如果完整的 GC 无法在 OldGen 中要求空间，可能是配置问题
    *   堆大小可能太小->增加堆大小并监视！如果问题仍然存在，可能是内存泄漏
    *   `-XX:+GCTimeLimit`设置 GCs 可花费的时间上限，占总时间的百分比，默认为 98%
    *   `-XX:+GCHeapFreeLimit`设置 GC 应该释放的空间量的下限，表示为最大堆的百分比，默认为 2%
    *   如果前 5 个连续 GC 未能保持 GC 成本低于`GCTimeLimit`或释放至少`GCHeapFreeLimit`空间，则在完全 GC 后抛出`OutOfMemoryError`
    *   如果频繁的完整 GC 不要求任何空间，则`PermGen` / `Metaspace`可能太小
2.  **诊断数据和分析**

    *   **GC 日志**有助于确定堆需求，找出过多的 GC 和长时间的 GC 暂停，以及内存空间的配置
        *   对于 Java 9+，G1 选项有:`-Xlog:gc*,gc+phases=debug:file=gc.log . For non G1, -Xlog:gc*:file=gc.log`。对于旧的 JVM，`-XX:+PrintGCDetails, -XX:+PrintGCTimeStamps, -XX:+PrintGCDateStamps, -Xloggc:gc.log`
        *   对于检查元空间，`-verbose:class`或`-XX:+TraceClassLoading , -XX:+TraceClassUnloading`
        *   我们可以通过人工检查来分析日志， [GCViewer](https://sourceforge.net/projects/gcviewer/) ， [GCHisto](https://github.com/jewes/gchisto) ， [gceasy.io](http://gceasy.io/)
    *   **堆转储**帮助确定意外的内存增长和内存泄漏。
        *   我们可以通过以下方式获取堆转储:
            *   jcmd PID GC . heap _ dump heap dump . DMP
            *   jmap -dump:format=b，file=snapshot.jmap pid
            *   使用 MBean HotSpotDiagnostic 的 JConsole 或 Java 任务控制
            *   JVM 选项 OOM 时堆转储错误:-XX:+heapdumponotofmemoryerror。频繁的完全 GC 会延迟堆转储的收集和进程的重新启动
        *   **Eclipse Memory Analyzer Tool(MAT)**显示泄漏嫌疑、直方图、不可达对象、重复类、GC 根的引用链，允许使用 [OQL](https://en.wikipedia.org/wiki/Object_Query_Language) 来探索堆转储。
        *   **JMC**的 JOverFlow 和 **Java VisualVM** 、 **YourKit** (一个商业分析器)都可以进行堆转储。
    *   **堆直方图** -快速查看堆中的对象
        *   收集方式:
            *   Posix 上的`-XX:+PrintClassHistogram`和`SIGQUIT`以及 Windows 上的`SIGBREAK`
            *   `jcmd pid GC.class_histogram filename=histo`
            *   `jmap -histo pid core_file`
            *   `jhsdb jmap` (Java 9)
    *   **Java 飞行记录** -意外的内存增长和内存泄漏，GC 事件
        *   启用堆统计。会带来额外的性能开销
        *   创建飞行记录:`-XX:+UnlockCommercialFeatures -XX:+FlightRecorder -XX:StartFlightRecording=delay=20s,duration=60s,name=Rec,filename=lol.jfr,settings=profile`
        *   **飞行记录可以找出泄漏对象的类型，但是你需要堆转储来找出导致对象泄漏的原因**
    *   **终结器**
        *   使用 JConsole、jmap 收集数据
        *   使用堆转储使用 Eclipse MAT / Visual VM 进行分析
    *   原生记忆
        *   本机内存跟踪器输出——跟踪 JVM 内部使用的本机内存，而不是外部库使用的本机内存。使用 **`NativeMemoryTracking`** 选项启动 JVM
        *   **`pmap`** ， **`libumem`** ， **`valgrind`** ，核心文件

### 结论

在本系列中，我们已经了解了 JVM 如何管理内存以及垃圾收集过程是如何工作的。我们还介绍了如何诊断内存问题，使用哪些工具来收集和分析诊断信息，以及一些会影响应用程序性能的 JVM 选项。