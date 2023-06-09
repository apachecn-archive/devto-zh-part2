# “顶级”实用程序的备忘单

> 原文：<https://dev.to/yanhan/cheatsheet-on-the-top-utility--82c>

这是我的 GitHub 回购:[https://github.com/yanhan/notes/blob/master/top.md](https://github.com/yanhan/notes/blob/master/top.md)

附带博文:[https://yanhan . github . io/posts/my-notes-on-the-top-program . html](https://yanhan.github.io/posts/my-notes-on-the-top-program.html)

## 你在屏幕上方看到的东西

### 负荷平均值

负载平均值位于屏幕的右上角。它们看起来如下:

```
load average: 0.45, 0.57, 0.62 
```

Enter fullscreen mode Exit fullscreen mode

这 3 个数字分别是 1 分钟、5 分钟和 15 分钟的负荷平均值。

**解释平均负载的简单方法:**如果平均负载为 1.00，CPU 有 1 个内核，则服务器处于满负荷状态。如果有两个内核，当数量为 2.00 时，服务器将达到最大容量。4 核的话，这个数字应该是 4.00。诸如此类。

**更长的解释:**把一个 CPU 内核想象成一条路，把一个进程想象成一辆车。如果道路上始终有 1 辆车，则平均负载为 1.00。如果有 2 辆车，则平均负载为 2.00，1 辆车可以上路，而另一辆车必须等待道路空闲。因此，平均负载是非常粗略的**`number of process that need to run / number of CPU cores`，并且测量服务器的过载程度。**

 ****一个简单的经验法则:**如果 15 分钟平均负载超过 0.7(除以 CPU 核心数后)，那么服务器可能过载。

关于负载平均值的更好解释，请参见:

### CPU 百分比数字

*   用户时间`(us)`
*   系统时间`(sys)`
*   花费在低优先级进程上的时间也称为美好时光`(ni)`
*   等待 I/O 进程花费的时间`(wa)`
*   时间处理硬件中断`(hi)`
*   时间处理软件中断`(si)`
*   从虚拟机窃取的时间`(st)`

## 列

*   `PR`:任务的优先级。从-20 到 19，其中-20 最重要
*   `NI` : nice 值，增加任务优先级。负数会增加任务的优先级，正数会降低优先级
*   `VIRT`:使用的虚拟内存(RAM 和交换的组合)
*   `RES`:非交换物理内存的驻留大小，以 KBs 为单位
*   `SHR`:共享内存大小，可以分配给其他进程的内存
*   `S`:进程状态。可运行`(R)`，休眠不可中断`(D)`，休眠可中断`(S)`，追踪/停止`(T)`，僵尸`(Z)`
*   `TIME+`:进程和子进程使用的累计 CPU 时间

## 交互命令

*   `M`:按内存使用情况排序
*   `P`:按 CPU 使用率排序
*   `s`:更改刷新时间(会提示输入值)
*   `Space / Enter`:刷新
*   `n`:改变显示的进程数(会提示输入数值)
*   `k` : kill process(将提示输入 PID 值)
*   `f`:查看字段列表，您可以选择要显示的字段。使用上下键导航，按`d`切换显示，按`s`选择分类字段
*   `H`:显示所有进程的单独线程
*   `i`:切换是否显示空闲进程
*   `U / u`:按用户名过滤
*   `1`:在所有 CPU 作为一个整体与按核心的 CPU 之间切换
*   `L`:定位字符串
*   `w`:写配置文件
*   `h`:打开帮助

## 命令行选项

*   `-n 10`:显示`10`次信息，然后退出
*   `-b`:批处理模式:每隔指定的秒数打印进程信息，直到所有迭代结束(用`-n`指定)
*   `-d[interval]`:设置 top 刷新结果的延迟时间
*   `-i`:切换是否显示空闲进程
*   `p[PID,PID]`:过滤，只显示指定的进程
*   `-u [username]`:按用户过滤

## 参考文献

*   [http://www . tech-FAQ . com/how-to-use-the-UNIX-top-command . html](http://www.tech-faq.com/how-to-use-the-unix-top-command.html)
*   [https://www . Li node . com/docs/uptime/monitoring/top-htop-io top/](https://www.linode.com/docs/uptime/monitoring/top-htop-iotop/)
*   [https://cos Kan . WordPress . com/2008/12/22/how-to-use-top-effectively-on-Linux-as-a-DBA/](https://coskan.wordpress.com/2008/12/22/how-to-use-top-effectivelly-on-linux-as-a-dba/)
*   [http://blog . scout app . com/articles/2009/07/31/understanding-load-averages](http://blog.scoutapp.com/articles/2009/07/31/understanding-load-averages)**