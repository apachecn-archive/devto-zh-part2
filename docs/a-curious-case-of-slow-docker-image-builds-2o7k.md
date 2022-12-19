# 一个奇怪的案例:码头工人形象建设缓慢

> 原文：<https://dev.to/jarekprzygodzki/a-curious-case-of-slow-docker-image-builds-2o7k>

# 调查缓慢的 Docker 映像构建

*最近发生的一个很短但很有趣的故障排除故事。*

最近，我在调查 CI 服务器(Oracle Linux 7.5，带有 direct-lvm 模式下的 Docker devicemapper 存储驱动程序)上 Docker 映像构建缓慢的情况。每个改变图层的操作(添加、复制、运行)都需要 20 秒——图像越大，时间越长。

处理明显卡住的程序的典型方法是收集线程堆栈跟踪。或者 Go 应用程序中的 goroutines 的 stacktraces。

## 转储的单据

Docker deamon 在收到 SIGUSR1 信号([engine/daemon/debug trap _ UNIX . go](https://github.com/docker/docker-ce/blob/18.09/components/engine/daemon/debugtrap_unix.go#L16))
后，会将 goroutines stacktraces 写入一个名为`goroutine-stacks-<datetime>.log`的文件

```
pkill -SIGUSR1 dockerd 
```

Enter fullscreen mode Exit fullscreen mode

快速分析显示，几乎所有的时间都花在了`NaiveDiffDriver.Diff`上。这里是垃圾场之一