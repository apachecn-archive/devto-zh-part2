# 现有文本日志文件的日志格式分析工具

> 原文：<https://dev.to/mizutani/a-log-format-analyzing-tool-from-existing-text-log-file-1din>

注:本文主要由机器翻译撰写。如果这很难理解，我道歉。

我在 Go 中创建了一个日志格式分析工具，这篇文章介绍了它。

# 什么是“日志格式分析”？

在这篇文章中，“日志格式”指的是 C、Go 等语言中的格式字符串。

```
log.Printf("Requested from %s", ipAddr) 
```

Enter fullscreen mode Exit fullscreen mode

该代码输出以下日志。

```
2018/05/23 23:25:00 Requested from 10.0.2.1
2018/05/23 23:25:10 Requested from 192.168.1.5
2018/05/23 23:25:24 Requested from 10.0.1.5 
```

Enter fullscreen mode Exit fullscreen mode

原始格式作为 IP 地址嵌入到`%s`中，并像下面这样作为文本输出。因为这个例子非常简单，所以很容易从下到上猜测，但是随着内容变得越来越复杂，很少有人会说，“这个值或固定语句是什么？”这次创建的工具是从下面的输出中推断出上面的格式语句(接近于)。我们正在为这个工具实现两个功能: **1)从已经输出的日志文件**中估计格式，以及 **2)使用估计的格式，并查看对应于该格式的日志出现在日志文件**中的什么地方。

# 为什么需要工具？

在仅处理规范化和结构化日志数据的环境中，此工具是不必要的，但在下列情况下它很有用。

*   **想要把握日志全貌的时候**:我觉得在安全分析的背景下特别频繁，但是也有需要看到大量以前没有见过的日志，并从中汲取知识的时候。即使你尝试用 less 命令查看，对人类来说也是严重的，那么整体上有什么样的日志呢？你是做什么分销的？如果发现是，分析起来就容易多了。特别是在安全分析中，在很多情况下，占总数 99%的不是关于日常服务的日志，而是异常发生的点。由于很容易出现通常看不到的错误和处理，异常发生时的日志，如果你能抓住异常日志=不寻常格式的日志出现在哪里，我们就先分析一下，通过关注它们你就能立足了。
*   **如果您需要重复使用文本格式的日志输出**:如果您已经在运行服务等。你想输出文本形式的日志，把日志添加到正则表达式中等等。有必要提取正在处理的值。如果你有规范，那很好，但是如果没有，你可以看看源代码或者写一个正则表达式→确保它是穷举的→修复正则表达式，你要重复这样的事情比较麻烦((我觉得有 tsukkom 说这样的环境比较搞笑，但是当时很少出现这种情况，尤其是在前一个位置)。该工具不估计要提取的值的正则表达式，但它会更容易工作，因为它可以覆盖从现有日志可以完成多远。

# 用法

如果你已经有了 Go 语言环境，可以通过`go get github.com/m-mizutani/logptn`安装

[https://github.com/m-mizutani/logptn](https://github.com/m-mizutani/logptn)

例如，我尝试使用以下日志来使用这个工具。

```
$ cat test.log
Feb  1 07:56:49 pylon sshd[5153]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.0.3  user=root
Feb  1 07:56:51 pylon sshd[5153]: Failed password for root from 192.168.0.3 port 7176 ssh2
Feb  1 07:56:51 pylon sshd[5153]: Connection closed by 192.168.0.3 [preauth]
Feb  1 08:01:26 pylon sshd[5156]: Invalid user upload from 192.168.0.3
Feb  1 08:01:26 pylon sshd[5156]: input_userauth_request: invalid user upload [preauth]
Feb  1 08:01:26 pylon sshd[5156]: pam_unix(sshd:auth): check pass; user unknown
Feb  1 08:01:26 pylon sshd[5156]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.0.3
Feb  1 08:01:28 pylon sshd[5156]: Failed password for invalid user upload from 192.168.0.3 port 51058 ssh2
Feb  1 08:01:28 pylon sshd[5156]: Connection closed by 192.168.0.3 [preauth]
Feb  1 08:05:01 pylon CRON[5159]: pam_unix(cron:session): session opened for user root by (uid=0)
Feb  1 08:05:01 pylon CRON[5159]: pam_unix(cron:session): session closed for user root
Feb  1 08:05:54 pylon sshd[5162]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.0.3  user=root
Feb  1 08:05:56 pylon sshd[5162]: Failed password for root from 192.168.0.3 port 33005 ssh2
Feb  1 08:05:56 pylon sshd[5162]: Connection closed by 192.168.0.3 [preauth]
Feb  1 08:10:28 pylon sshd[5165]: Invalid user mythtv from 192.168.0.3
Feb  1 08:10:28 pylon sshd[5165]: input_userauth_request: invalid user mythtv [preauth]
Feb  1 08:10:28 pylon sshd[5165]: pam_unix(sshd:auth): check pass; user unknown
Feb  1 08:10:28 pylon sshd[5165]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.0.3
Feb  1 08:10:30 pylon sshd[5165]: Failed password for invalid user mythtv from 192.168.0.3 port 59978 ssh2
Feb  1 08:10:30 pylon sshd[5165]: Connection closed by 192.168.0.3 [preauth]
Feb  1 08:15:01 pylon CRON[5168]: pam_unix(cron:session): session opened for user root by (uid=0)
Feb  1 08:15:01 pylon CRON[5168]: pam_unix(cron:session): session closed for user root
Feb  1 08:15:26 pylon sshd[5171]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=10.2.3.4  user=root
Feb  1 08:15:28 pylon sshd[5171]: Failed password for root from 10.2.3.4 port 60733 ssh2
Feb  1 08:15:28 pylon sshd[5171]: Connection closed by 10.2.3.4 [preauth]
Feb  1 08:17:01 pylon CRON[5173]: pam_unix(cron:session): session opened for user root by (uid=0)
Feb  1 08:17:01 pylon CRON[5173]: pam_unix(cron:session): session closed for user root
Feb  1 08:20:35 pylon sshd[5177]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=10.2.3.4  user=root
Feb  1 08:20:37 pylon sshd[5177]: Failed password for root from 10.2.3.4 port 44877 ssh2
Feb  1 08:20:37 pylon sshd[5177]: Connection closed by 10.2.3.4 [preauth]
Feb  1 08:25:01 pylon CRON[5180]: pam_unix(cron:session): session opened for user root by (uid=0)
Feb  1 08:25:01 pylon CRON[5180]: pam_unix(cron:session): session closed for user root
Feb  1 08:25:16 pylon sshd[5183]: Invalid user user from 10.2.3.4 
```

Enter fullscreen mode Exit fullscreen mode

根据上面的日志数据，该工具输出以下结果。

```
./logptn test.log
2018/05/20 13:30:55 arg:test.log
     4 [4ffb267b] Feb  1 *:*:* pylon sshd[*]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=*  user=root
     4 [845f4659] Feb  1 *:*:* pylon sshd[*]: Failed password for root from * port * ssh2
     6 [847ccf35] Feb  1 *:*:* pylon sshd[*]: Connection closed by * [preauth]
     3 [de051cd9] Feb  1 08:*:* pylon sshd[*]: Invalid user * from *
     2 [8e9e2a13] Feb  1 08:*:* pylon sshd[*]: input_userauth_request: invalid user * [preauth]
     2 [22190c74] Feb  1 08:*:* pylon sshd[*]: pam_unix(sshd:auth): check pass; user unknown
     2 [83fba2bf] Feb  1 08:*:* pylon sshd[*]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.0.3
     2 [f1ba83ea] Feb  1 08:*:* pylon sshd[*]: Failed password for invalid user * from 192.168.0.3 port * ssh2
     4 [e4a6f815] Feb  1 08:*:01 pylon CRON[*]: pam_unix(cron:session): session opened for user root by (uid=0)
     4 [5256845b] Feb  1 08:*:01 pylon CRON[*]: pam_unix(cron:session): session closed for user root 
```

Enter fullscreen mode Exit fullscreen mode

在这个输出中，从左边开始是“格式出现的次数”“格式 ID”“估计格式”。此外，在估计格式中，应该作为值嵌入的部分被替换为符号`*`。在本例中，样本很少，因此 IP 地址部分不是`*`，但随着样本数量的增加，它也将被替换为`*`。上面是以人类可读的文本格式输出的，但是也可以以 json 格式输出，以便由另一个程序处理。

```
./logptn  test.log  -d  sjson  |  jq  .  {  "formats":  [  {  "segments":  [  "Feb  1 ",  null,  ":",  null,  ":",  null,  " pylon sshd[",  null,  "]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=",  null,  "  user=root"  ],  "count":  4  },  (snip) 
```

Enter fullscreen mode Exit fullscreen mode

此外，您可以以 HTML 格式显示该格式日志中出现了多少行。

```
$ ./logptn  ./var/log/secure -d heatmap -o secure.html 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的命令，您可以创建一个热图，显示日志的格式和出现的行。热图左边是预估的格式，上面的表头是行数(行号到行号)，右边是总对数。下图有点小，不好看，不过 HTML 文件本身也可以从[这里](https://www.dropbox.com/s/72n7hd9vhc2bmet/secure.html?dl=0)下载。

[![](../Images/5a19db5752cfbe49c59c85ccebdac717.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VoKiCuz5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mebw99ulr5asg2kg9gvy.png)

[大尺寸图像](https://www.dropbox.com/s/y8flhuokk0cjfqt/secure.png)

# 表现

计算量为 *O (NM)* ， *N* 为日志文件包含的日志总数， *M* 为预计的格式数。我尝试了各种日志文件，但是 *M* 收敛到大约 10 到 100，所以 *N* 是受影响的日志总数。虽然它只测量杂项，但我在 2015 年初用 macbook pro(2.7 GHz 英特尔酷睿 i 5)移动了它，用于大约 *M = 40* 的数据，每秒运行大约 30，000 个日志。也许在代码方面可以做更多的优化，但是我到目前为止还没有做到。