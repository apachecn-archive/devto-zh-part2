# 安全冲刺:法尔科

> 原文：<https://dev.to/terceranexus6/security-sprint-falco-e0o>

又见面了！继续安全冲刺的文章，本周我将谈论法尔科。没有。我不是在说音乐家，我指的是一个开源工具，它允许我们监控行为活动，并检测应用程序中的异常活动。

[![](../Images/e7c353174cb1ada744cb7a2e9639b068.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ayq5Q9Wc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://sysdig.com/wp-content/uploads/2018/07/falco_running.gif)

异常检测的一些例子是外壳在生产容器内运行、SQL 注入攻击、rootkit 主机、未授权进程、写入非用户数据目录等。法尔科法则的一个例子是:

```
- macro: bin_dir
  condition: fd.directory in (/bin, /sbin, /usr/bin, /usr/sbin)

- macro: open_write
  condition: (evt.type=open or evt.type=openat) and evt.is_open_write=true and fd.typechar='f'

- macro: package_mgmt_binaries
  items: [dpkg, dpkg-preconfigu, rpm, rpmkey, yum, frontend]

- rule: Write below binary dir
  desc: an attempt to write to any file below a set of binary directories
  condition: bin_dir and evt.dir = < and open_write and not proc.name in (package_mgmt_binaries)
  output: "File below a known binary directory opened for writing (user=%user.name command=%proc.cmdline file=%fd.name)"
priority: WARNING 
```

Enter fullscreen mode Exit fullscreen mode

最重要的部分是条件规则，一个应用于每个系统调用的过滤器。最终输出是一条通知消息，它混合了纯文本和事件信息。我们将看到和例子，但首先，让我们安装法尔科。

```
$ sudo -s
# mkdir /etc/falco
# cd /etc/falco
/etc/falco# curl https://raw.githubusercontent.com/katacoda-scenarios/sysdig-scenarios/master/sysdig-falco/assets/falco.yaml -o falco.yaml
/etc/falco# curl https://raw.githubusercontent.com/katacoda-scenarios/sysdig-scenarios/master/sysdig-falco/assets/falco_rules.yaml -o falco_rules.yaml
/etc/falco# touch /var/log/falco_events.log 
```

Enter fullscreen mode Exit fullscreen mode

`falco.yaml`配置 Falco 服务，`falco_rules.yaml`包含威胁检测模式，`falco_events.log`将用作事件日志文件。为了安装那些...

```
# docker pull sysdig/falco
# docker run -d --name falco --privileged -v /var/run/docker.sock:/host/var/run/docker.sock -v /dev:/host/dev -v /proc:/host/proc:ro -v /boot:/host/boot:ro -v /lib/modules:/host/lib/modules:ro -v /usr:/host/usr:ro -v /etc/falco/falco.yaml:/etc/falco/falco.yaml -v /etc/falco/falco_rules.yaml:/etc/falco/falco_rules.yaml -v /var/log/falco_events.log:/var/log/falco_events.log sysdig/falco 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们回到这个例子。在[官方文档](https://sysdig.com/blog/selinux-seccomp-falco-technical-discussion/#falco)中，它解释了该规则如何监控文件打开以识别打开文件的企图。我将在我自己的容器上使用`docker`进行同样的尝试:

[![](../Images/d6cbf73cdfeb079181593929cf7161f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---xUpdm0_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yoq11lw5egy8i3gg5ak7.png)

玩了一会儿之后，让我们开始跟踪我们的日志。

`tail /var/log/falco_events.log`

[![](../Images/f6824647f8828c80faeee4cb7ee2dee2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BjN1mid4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uzqfauhl5j2mlqy3vyfw.png)

就是这样！法尔科给出了一个建议。

[![](../Images/0c70c1607f0da32d711b4276b3d71396.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eXgT0Y_B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia1.tenor.com%252Fimages%252Fd9fe393a4133831e33afffaf00a7ed81%252Ftenor.gif%253Fitemid%253D8821403%26f%3D1)

Welp，现在让我们尝试另一个例子，任何试图写入非数据目录的进程。在`curl https://raw.githubusercontent.com/katacoda-scenarios/sysdig-scenarios/master/sysdig-falco/assets/falco_rules_step4.yaml -o falco_rules.yaml`和重启 docker falco 之后:

[![](../Images/e9812de06597c128ad2cb286e6caf745.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--97JOPgaU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dnjoat5r6uya8p2c1m0m.png)

所以，就这样了！关于使用 docker 和 falco 的更多信息，请查看我使用的 yaml 文件所在的教程。

希望你们喜欢这个星期的安全冲刺！