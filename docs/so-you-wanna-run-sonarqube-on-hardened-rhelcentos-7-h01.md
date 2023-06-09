# 所以你想在 RHEL/CentOS 7 上运行 Sonarqube

> 原文：<https://dev.to/ferricoxide/so-you-wanna-run-sonarqube-on-hardened-rhelcentos-7-h01>

随着开发人员越来越关注将安全意识注入到他们的过程中，像 Sonarqube 这样的工具变得越来越流行。随着流行程度的增加，人们越来越希望在 Linux 发行版之外的版本上运行该软件。

我的大多数客户只允许在他们的生产网络上使用 Red Hat(以及越来越多的 CentOS)。由于这些客户雇佣的开发人员不是在 Red Hat 上工作的，因此将这些新开发人员的首选工具部署到 Enterprise Linux 上的压力增加了。

Sonarqube 的[安装文件](https://docs.sonarqube.org/display/SONAR/Installing+the+Server)中列出了两种相关的安装方法。一种方法是展开一个 ZIP 存档，在任何需要的地方安装文件，并根据需要设置必要的自动启动。另一种是安装一个“半官方”的 RPM-packaging。最初，我选择了前者，但随后又换成了后者。如果你很少安装(或者手动安装),前者是好的，但是如果你通过像亚马逊的 CloudFormation (CFn)和/或 AutoScaling 这样的服务框架进行自动化部署，它可能会失败。

我的首选安装方法是使用 CFn 来创建自动伸缩组(ASG)。我不运行 Sonarqube 集群:我的开发人员缺乏资金支持我部署的社区版不支持集群。使用 ASGs 意味着，如果服务意外离线，我可以让 Sonarqube 服务简单地重建自己，从而提高服务的可用性。重建只需要几分钟，通常不需要任何管理员干预。

在运行服务的第一个月左右，这种方法与基于 ZIP 的安装方法兼容。然后，一天晚上，它*停止了*的工作。当我调查它停止的原因时，我发现部署自动化指向的 URL 不再存在。我真的不知道发生了什么，因为 Sonarqube 的维护者似乎无限期地保留了该软件的版本。

/耸肩

无论如何，这让我回到了安装文档。这一次，我注意到有一个 RPM 选项。所以，我走上了这条路。在很大程度上，使用 ZIP 封装使事情变得非常简单。RPM 只有几个问题:

*   当前的打包，除了是一个“noarch”打包之外，既不是 EL 版本特定的，也不是跨版本的。实际上，它是面向 EL6 的:它包括一个遗留的 init 脚本，但没有 systemd 服务定义。RPM 维护人员可能需要制作 EL6 和 EL7 包，或者需要更新包以包含这两种 init 类型的文件，然后使用平台选择器来安装和激活合适的文件
*   当前打包创建服务帐户`sonar`并将打包的文件安装为 user:group `sonar`...但是(在撰写本文时)不包括启动逻辑来确保 sonar cube*作为* `sonar`运行。这将显示在 Sonarqube 的`es.log`文件中，类似于:

```
[2018-02-21T15:45:51,714][WARN ][o.e.b.ElasticsearchUncaughtExceptionHandler] [] uncaught exception in thread [main]
org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException: can not run elasticsearch as root
        at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:125) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:112) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.cli.SettingCommand.execute(SettingCommand.java:54) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:96) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.cli.Command.main(Command.java:62) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:89) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:82) ~[elasticsearch-5.1.1.jar:5.1.1]
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
        at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:100) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:176) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:306) ~[elasticsearch-5.1.1.jar:5.1.1]
        at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:121) ~[elasticsearch-5.1.1.jar:5.1.1]
        ... 6 more 
```

*   嵌入式 ElastiCache 服务要求文件描述符`ulimit`是一个特定的最小值:如果部署在一个加固的系统上，默认的`ulimit`很可能太低。如果太低，这将在 Sonarqube 的`es.log`文件中显示，类似于:

```
[2018-02-21T15:47:23,787][ERROR][bootstrap                ] Exception
java.lang.RuntimeException: max file descriptors [65535] for elasticsearch process likely too low, increase to at least [65536]
    at org.elasticsearch.bootstrap.BootstrapCheck.check(BootstrapCheck.java:79)
    at org.elasticsearch.bootstrap.BootstrapCheck.check(BootstrapCheck.java:60)
    at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:188)
    at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:264)
    at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:111)
    at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:106)
    at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:88)
    at org.elasticsearch.cli.Command.main(Command.java:53)
    at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:74)
    at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:67) 
```

*   嵌入式 ElastiCache 服务要求调整内核的`vm.max_map_count`运行时参数。如果没有充分调整，这将在 Sonarqube 的 es.log 文件中显示，类似于:

```
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144] 
```

*   如果作为主机加固的一部分，`/tmp`目录应用了`noexec`挂载选项，嵌入式 ElastiCache 服务的 JVM 将不会启动。这将在 Sonarqube 的`es.log`文件中显示，类似于:

```
WARN  es[][o.e.b.Natives] cannot check if running as root because JNA is not available 
WARN  es[][o.e.b.Natives] cannot install system call filter because JNA is not available 
WARN  es[][o.e.b.Natives] cannot register console handler because JNA is not available 
WARN  es[][o.e.b.Natives] cannot getrlimit RLIMIT_NPROC because JNA is not available
WARN  es[][o.e.b.Natives] cannot getrlimit RLIMIT_AS because JNA is not available
WARN  es[][o.e.b.Natives] cannot getrlimit RLIMIT_FSIZE because JNA is not available 
```

*   如果没有正确配置基于主机的防火墙，外部用户将无法与该服务通信。前三项都可以通过正确编写的 systemd 服务定义来解决。以下是我为部署整理的内容:

```
[Unit]
Description=SonarQube
After=network.target network-online.target
Wants=network-online.target

[Service]
ExecStart=/opt/sonar/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonar/bin/linux-x86-64/sonar.sh stop
ExecReload=/opt/sonar/bin/linux-x86-64/sonar.sh restart
Group=sonar
LimitNOFILE=65536
PIDFile=/opt/sonar/bin/linux-x86-64/SonarQube.pid
Restart=always
RestartSec=30
User=sonar
Type=forking

[Install]
WantedBy=multi-user.target 
```

我把它放入 GitHub 项目中，作为自动化 AWS 部署的一部分。`User=sonar`选项使 systemd 作为`sonar`用户启动进程。`Group=sonar`指令确保流程在`sonar`下运行。`LimitNOFILE=65536`确保服务的文件描述符`ulimit`设置得足够高。

第四项通过创建一个包含以下内容的`/etc/sysctl.d/sonarqube`文件来解决:

```
vm.max_map_count = 262144 
```

我用我的自动化来处理这个问题，但是 RPM 维护者可以通过包含一个`/etc/sysctl.d/sonarqube`文件作为 RPM 的一部分来避免这种需要。

第五项可通过`sonar.properties`文件寻址。添加一行，如下所示:

```
sonar.search.javaAdditionalOpts=-Djava.io.tmpdir=/var/tmp/elasticsearch 
```

将导致 Sonarqube 将 ElasticSearch 的临时目录指向`/var/tmp/elasticsearch`。请注意，这是一个示例目录:选择一个位置，sonar 用户可以在该位置创建一个目录，并且该位置位于没有设置`noexec` mount-option 的文件系统上。如果不进行此更改，ElasticSearch 将无法启动，因为 JNI 无法启动。

最后一项可以通过向`firewalld`服务添加一个端口异常来解决。这可以像执行以下命令一样简单地完成:

```
firewall-cmd --permanent --service=sonarqube --add-port=9000/tcp 
```

 `or adding a`/etc/firewalld/services/sonar qube . XML `文件包含:

 ``<?xml version="1.0" encoding="utf-8"?>
<service>
<short>Sonarqube service ports</short>
<description>Firewalld options supporting Sonarqube deployments</description>
<port protocol="tcp" port="9000" >
<port protocol="tcp" port="9001" >
</service>` ’`

 `两者都适合包含在 RPM 中:前者通过`%post`脚本的方式；后者作为配置文件打包在 RPM 中。

一旦考虑了所有项目，包含 RPM 的服务应该可以在强化的 EL7 系统上正确启动和运行。令人惊讶的是，要让 Sonarqube 在强化系统上运行，通常不需要其他调整。Sonarqube 通常在 FIPS 模式和 SELinux 激活的情况下都能正常工作。

一旦 Sonarqube 被部署，除了缺省值之外的功能可能会被期望。有相当多的功能扩展插件可用。下载你想要的并安装到`<SONAR_INSTALL_ROOT>/extensions/plugins`并重启服务。大多数插件的行为可以通过 Sonarqube 管理 GUI 进行配置。

注意:如果你想让上面的事情为你解决，并且你想把 Sonarqube 部署到 AWS 上，我把一组模板驱动的 CloudFormation 模板和助手脚本放在一起。他们可以在 [GitHub](https://github.com/plus3it/cfn-sonarqube) 上找到。`