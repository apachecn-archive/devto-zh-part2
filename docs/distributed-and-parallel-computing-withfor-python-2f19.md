# 使用/面向 Python 的分布式并行计算

> 原文：<https://dev.to/hasansajedi/distributed-and-parallel-computing-withfor-python-2f19>

> dispy 是一个全面且易于使用的框架，用于创建和使用计算集群，在单个机器(SMP)中的多个处理器之间、在集群、网格或云中的多个机器之间并行执行计算。dispy 非常适合数据并行(SIMD)范例，在这种范例中，计算(Python 函数或独立程序)使用不同的(大型)数据集进行独立评估，计算任务之间没有通信(除了发送临时/中间结果或向客户端传输文件的计算任务)。
> 如果需要任务间的通信/协作，可以使用 pycos 框架的分布式通信进程模块。

## dispy 的一些特性:

*   dispy 是用 pycos 实现的，pycos 是一个独立的框架，用于异步、并发、分布式、带任务的网络编程(不带线程)。pycos 使用带有 I/O 通知机制的非阻塞套接字 epoll、kqueue、poll 和 Windows I/O 完成端口(IOCP)来实现高性能和可扩展性，因此 dispy 可以高效地处理单个节点或大型节点集群——一位用户报告了在 Google cloud platform 中使用 dispy 处理 500 个节点的情况。pycos 本身支持分布式/并行计算，包括传输计算、文件等。和消息传递(用于与客户端和其他计算任务通信)。虽然 dispy 可用于调度计算作业以获得结果，但 pycos 可用于创建分布式通信流程，适用于广泛的用例，包括内存处理、数据流、实时(现场)分析。

*   计算(Python 函数或独立程序)及其依赖项(文件、Python 函数、类、模块)自动分布到节点。如果是 Python 函数，计算也可以将节点上的文件传输到客户端。

*   计算节点可以在网络上的任何地方(本地或远程)。为了安全起见，可以使用简单的基于散列的认证或 SSL 加密。

*   每次执行完成后，执行结果、输出、错误和异常跟踪都可供进一步处理。

*   支持内存处理(在 Windows 下有一些限制)；也就是说，计算可以在内存中的数据上进行，而不是每次都从文件中加载数据。

*   节点可以动态可用:只要节点可用，dispy 就会调度作业，并且计算可以使用该节点。

*   作业和集群状态通知机制允许异步处理作业结果、定制的作业调度程序等。

*   支持客户端和服务器端故障恢复:

如果用户程序(客户端)意外终止(例如，由于未捕获的异常)，则节点继续执行调度的作业。使用(故障)恢复作业可以轻松检索该群集的计划(但崩溃时未完成)作业的结果。

如果在创建集群时计算被标记为可重入，并且为该计算执行作业的节点(服务器)失败，dispy 会自动将这些作业重新提交给其他可用节点。

*   dispy 可以在单个进程中独占使用所有节点(使用 JobCluster)，也可以在多个进程中同时共享节点(使用 SharedJobCluster 和 [dispyscheduler(共享执行程序)](http://dispy.sourceforge.net/dispyscheduler.html))。

*   [通过网络浏览器监控和管理集群](http://dispy.sourceforge.net/httpd.html)，包括在 iOS 或 Android 设备中。

*   使用现有硬件设置私有计算基础设施(唯一的要求是计算机已连接并安装了 Python)，或者使用外部云计算服务(用户报告使用 dispy 和 Amazon EC2、Google Cloud 和 Microsoft Azure)，或者单独使用，或者在任何本地计算节点之外使用。详见[云计算](http://dispy.sourceforge.net/dispy.html#cloud)。

dispy 适用于 Python 和 3.1+版本，并在 Linux、OS X 和 Windows 上进行了测试；它也可以在其他平台上运行。dispy 也与 JIT 解释器 [PyPy](http://pypy.org/) 一起工作。

[参考](http://dispy.sourceforge.net/)