# 利用 Outthentic 进行超级灵活的基础架构审计

> 原文：<https://dev.to/melezhik/super-flexible-infrastructure-audit-with-outthentic-2a3>

[![https://raw.githubusercontent.com/melezhik/outthentic-dev.to/mastimg/outthentic-example.png](img/9d37a2d5d3cf708349d1d63a747cb70b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v_IqekDc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/melezhik/outthentic-dev.to/mastimg/outthentic-example.png)

将 devops 带入我们的日常生活使得创建新的服务器变得非常简单。云提供商使得只需点击一下鼠标就可以创建新的虚拟机。因此服务器基础设施审计工具的重要性变得越来越重要。像戈斯和 T2 这样的工具可以完成这项工作。

[outhentic](https://github.com/melezhik/outthentic)，作为一个通用脚本运行程序也可以做到这一点，但在**T3】非常不同的方式 T5】**

Outthentic 没有提供声明性的 DSL 来检查服务器状态，而是为我们提供了有效的原语来解析命令输出并验证服务器资源和配置。

在一些真实生活的例子中，我会告诉你如何做，如果你觉得这种方法有吸引力，欢迎来到我们的阵营(；

# 安装输出端

在创建任何测试之前，让我们安装 Outthentic。

```
$ cpanm https://github.com/melezhik/outthentic.git 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们编写测试

# 检查是否安装了软件包

让我们从一些简单而有用的例子开始。这里我们只是运行 bash 命令并检查退出代码是否为`0`。

```
$ nano story.bash

yum list python 2>/dev/null | tail -n 1 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun

2018-10-01 20:53:25 :  [path] /
python.x86_64                      2.7.5-69.el7_5                       @updates
ok  scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

检查最小软件包版本只需要在一个*检查文件* :
中添加几行代码

```
$ nano story.check

regexp: python.x86_64\s+(\d+)\.(\d+)\.(\d+)-
generator: <<CODE
  [
    "assert: ".( capture()->[0] >= 2   || 0  )." major version is more or equal 2",
    "assert: ".( capture()->[1] >= 7   || 0  )." minor version is more or equal 7"
  ]
CODE

$ strun

2018-10-01 21:06:40 :  [path] /
python.x86_64                      2.7.5-69.el7_5                       @updates
ok  scenario succeeded
ok  text match /python.x86_64\s+(\d+)\.(\d+)\.(\d+)-/
ok  major version is more or equal 2
ok  minor version is more or equal 7
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

任何其他测试用例都可以用同样的方式定义。该方法看起来像:

1.  执行命令
2.  检查状态代码(可选)
3.  使用检查文件验证命令输出

检查文件包含静态的 **[验证规则](https://github.com/melezhik/outthentic-dsl)** 和**段代码**(写在 Perl、Bash、Ruby 或 Python 上)来动态地**生成新规则。**

 **这里有一个更复杂的例子。

# 监控一组流程:

```
$ nano story.bash

sleep 5 2>/dev/null 1>/dev/null &
sleep 5 2>/dev/null 1>/dev/null &
sleep 5 2>/dev/null 1>/dev/null &
sleep 5 2>/dev/null 1>/dev/null &
sleep 5 2>/dev/null 1>/dev/null &

ps aux | grep sleep | grep -v grep 
```

Enter fullscreen mode Exit fullscreen mode

```
$ nano story.check

begin:
sleep 5
sleep 5
sleep 5
sleep 5
sleep 5
end: 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun

2018-10-02 22:26:05 :  [path] /
root      48180  0.0  0.0   7772   664 pts/2    S+   22:26   0:00 sleep 5
root      48181  0.0  0.0   7772   748 pts/2    S+   22:26   0:00 sleep 5
root      48182  0.0  0.0   7772   640 pts/2    S+   22:26   0:00 sleep 5
root      48183  0.0  0.0   7772   712 pts/2    S+   22:26   0:00 sleep 5
root      48184  0.0  0.0   7772   748 pts/2    S+   22:26   0:00 sleep 5
ok      scenario succeeded
ok      [b] text has 'sleep 5'
ok      [b] text has 'sleep 5'
ok      [b] text has 'sleep 5'
ok      [b] text has 'sleep 5'
ok      [b] text has 'sleep 5'
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

同上，但对于 **N** 进程数:

```
$ nano story.check

generator: [ "begin:", (map { "sleep 5" } (1 .. config()->{N})), "end:" ]

$ strun --param N=5 
```

Enter fullscreen mode Exit fullscreen mode

以下是日常操作中发生的各种检查的例子，其中一些是微不足道的，一些不是，一些是无耻地从 goss 问题列表[中“窃取”的](https://github.com/aelsabbahy/goss/issues)，以表明使用 Outthentic 检查工具几乎没有什么是不可能的！

# 检查 http 响应是否有 http 头

```
$ nano story.bash

curl -s -k -o /dev/null -D - http://headers.jsontest.com/ | grep Content-Type:

$ nano story.check

Content-Type: application/json 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun

2018-10-03 18:33:33 :  [path] /
Content-Type: application/json; charset=ISO-8859-1
ok      scenario succeeded
ok      text has 'Content-Type: application/json'
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# 检查用户是否正在运行流程

```
 $ nano story.bash

ps -o command -u root|grep /usr/lib/systemd/systemd-logind|grep -v grep

$ nano story.check

regexp: ^/usr/lib/systemd/systemd-logind$ 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun

2018-10-03 18:50:24 :  [path] /
/usr/lib/systemd/systemd-logind
ok      scenario succeeded
ok      text match /^/usr/lib/systemd/systemd-logind$/
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# 检查内核参数是否大于

```
$ nano story.bash

sysctl -a 2>/dev/null | grep net.core.somaxconn

$ nano story.check

regexp: net.core.somaxconn = (\d+)

generator: <<CODE 
[ 
  "assert: ".( 
     capture()->[0] >= 128 ? 1 : 0 
   )." net.core.somaxconn is greater or equal then 128" 
]
CODE 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun

2018-10-03 19:52:01 :  [path] /
net.core.somaxconn = 128
ok      scenario succeeded
ok      text match /net.core.somaxconn = (\d+)/
ok      net.core.somaxconn is greater or equal then 128
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# http/https 检查的基本验证

```
$ nano story.bash

curl -u guest:guest -f -o /dev/null -s https://jigsaw.w3.org/HTTP/Basic/ 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun

2018-10-03 21:53:38 :  [path] /
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# 检查某个范围内的 http 端口是否可访问

```
 $ nano story.bash

for i in 83 82 81 80; do
  curl http://sparrowhub.org:$i -o /dev/null -s -f --connect-timeout 3 && echo connected to $i
done

$ nano story.check

regexp: connected to \d+ 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun

2018-10-03 22:06:03 :  [path] /
connected to 80
ok      scenario succeeded
ok      text match /connected to \d+/
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# 通过全局/正则名称检查文件

让我们检查一下`/etc/`目录中的配置文件是否有`root`所有者和`644`模式:

```
 $ nano story.bash 

stat -c %U' '%a' '%n /etc/*.conf

$ nano story.check

regexp: (\S+)\s(\d\d\d)\s(\S+)

generator: <<CODE
  my @out;
  for my $f (@{captures()}){
    my $fname = $f->[2];
    push @out, "assert: ".( $f->[0] eq 'root' ? 1 : 0 )." $fname has a root owner";
    push @out, "assert: ".( $f->[1] eq '644' ? 1 : 0 )." $fname has a 644 mode";
  }
  [@out]
CODE 
```

Enter fullscreen mode Exit fullscreen mode

```
 $ strun

2018-10-03 22:45:49 :  [path] /
root 644 /etc/dracut.conf
root 644 /etc/e2fsck.conf
root 644 /etc/host.conf
root 644 /etc/krb5.conf
root 644 /etc/ld.so.conf
root 640 /etc/libaudit.conf
root 644 /etc/libuser.conf
root 644 /etc/locale.conf
root 644 /etc/man_db.conf
root 644 /etc/mke2fs.conf
root 644 /etc/nsswitch.conf
root 644 /etc/resolv.conf
root 644 /etc/rsyncd.conf
root 644 /etc/sysctl.conf
root 644 /etc/vconsole.conf
root 644 /etc/yum.conf
ok      scenario succeeded
ok      text match /(\S+)\s(\d\d\d)\s(\S+)/
ok      /etc/dracut.conf has a root owner
ok      /etc/dracut.conf has a 644 mode
ok      /etc/e2fsck.conf has a root owner
ok      /etc/e2fsck.conf has a 644 mode
ok      /etc/host.conf has a root owner
ok      /etc/host.conf has a 644 mode
ok      /etc/krb5.conf has a root owner
ok      /etc/krb5.conf has a 644 mode
ok      /etc/ld.so.conf has a root owner
ok      /etc/ld.so.conf has a 644 mode
ok      /etc/libaudit.conf has a root owner
not ok  /etc/libaudit.conf has a 644 mode
ok      /etc/libuser.conf has a root owner
ok      /etc/libuser.conf has a 644 mode
ok      /etc/locale.conf has a root owner
ok      /etc/locale.conf has a 644 mode
ok      /etc/man_db.conf has a root owner
ok      /etc/man_db.conf has a 644 mode
ok      /etc/mke2fs.conf has a root owner
ok      /etc/mke2fs.conf has a 644 mode
ok      /etc/nsswitch.conf has a root owner
ok      /etc/nsswitch.conf has a 644 mode
ok      /etc/resolv.conf has a root owner
ok      /etc/resolv.conf has a 644 mode
ok      /etc/rsyncd.conf has a root owner
ok      /etc/rsyncd.conf has a 644 mode
ok      /etc/sysctl.conf has a root owner
ok      /etc/sysctl.conf has a 644 mode
ok      /etc/vconsole.conf has a root owner
ok      /etc/vconsole.conf has a 644 mode
ok      /etc/yum.conf has a root owner
ok      /etc/yum.conf has a 644 mode
STATUS  FAILED (2) 
```

Enter fullscreen mode Exit fullscreen mode

在我的 docker 机器上`/etc/libaudit.conf`有一个导致测试失败的`640`模式。

# 检查 TCP 地址/端口是否被进程监听

让我们验证一下`/usr/sbin/sshd`
监听了`22`端口

```
$ mkdir -p modules/file-proc

$ mkdir modules/netstat

$ nano modules/netstat/story.bash

netstat -nlpW|grep ':22'

$ nano modules/netstat/story.check

regexp: tcp\s+\d+\s+\d+\s+\S+?:22.*LISTEN\s+(\d+)

code: <<CODE
  our @pids;
  for my $f (@{captures()}){
    push @pids, $f->[0];
  }
CODE

$ nano modules/file-proc/story.bash

file /proc/$pid/exe

$ nano modules/file-proc/story.check

symbolic link to `/usr/sbin/sshd'

$ nano hook.pl

run_story "netstat";

for my $pid (our @pids){
  run_story "file-proc", { pid => $pid }
}

$ strun

2018-10-04 14:06:28 :  [path] /modules/netstat/
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      69/sshd
tcp6       0      0 :::22                   :::*                    LISTEN      69/sshd
ok      scenario succeeded
ok      text match /tcp\s+\d+\s+\d+\s+\S+?:22.*LISTEN\s+(\d+)/
2018-10-04 14:06:28 :  [path] /modules/file-proc/ [params] pid:69
/proc/69/exe: symbolic link to `/usr/sbin/sshd'
ok      scenario succeeded
ok      text has 'symbolic link to `/usr/sbin/sshd''
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以递归地将我们编写的个测试组合在一起:

```
$ strun --recurse --format=production
2018-10-04 14:14:13 :  [path] /kernel-param/
2018-10-04 14:14:14 :  [path] /http-basic-auth/
2018-10-04 14:14:14 :  [path] /modules/netstat/
2018-10-04 14:14:14 :  [path] /modules/file-proc/ [params] pid:69
2018-10-04 14:14:14 :  [path] /http-header-response/
2018-10-04 14:14:15 :  [path] /package-exists/
2018-10-04 14:14:15 :  [path] /file-by-regexp/
not ok  /etc/libaudit.conf has a 644 mode
root 644 /etc/dracut.conf
root 644 /etc/e2fsck.conf
root 644 /etc/host.conf
root 644 /etc/krb5.conf
root 644 /etc/ld.so.conf
root 640 /etc/libaudit.conf
root 644 /etc/libuser.conf
root 644 /etc/locale.conf
root 644 /etc/man_db.conf
root 644 /etc/mke2fs.conf
root 644 /etc/nsswitch.conf
root 644 /etc/resolv.conf
root 644 /etc/rsyncd.conf
root 644 /etc/sysctl.conf
root 644 /etc/vconsole.conf
root 644 /etc/yum.conf
STATUS  FAILED (2)
2018-10-04 14:14:15 :  [path] /multiple-processes/
2018-10-04 14:14:16 :  [path] /process-run-user/
2018-10-04 14:14:16 :  [path] /range-of-ports/
STATUS  FAILED (254)
stories failed: /file-by-regexp 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`production`输出格式，它只显示失败测试的细节(`/file-by-regexp`)

# 测试重用

我们只需要将我们想要重用的测试用例打包到 sparrow 插件中:

```
 $ cd kernel-param/

$ nano story.bash

param=$(config name)

sysctl  2>/dev/null -a | grep $param

$ nano story.check

generator: <<CODE

my $value = config()->{value}; 
my $param = config()->{name};

[ 
  'regexp: '.$param.' = (\d+)',

  "assert: ".( 
     capture()->[0] >= $value ? 1 : 0 
   )." $param is greater or equal then $value" 
]
CODE

$ nano sparrow.json
{

    "name": "kernel-param-check",
    "version": "0.0.1",
    "category" : "audit", 
    "description" : "check kernel parameter",
}

$ sparrow plg upload 
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
$ ssh host

$ sparrow index update

$ sparrow plg install kernel-param-check

$ sparrow plg run kernel-param-check \
--param name=net.core.somaxconn \
--param value=128 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

当您需要基础设施测试时，outhentic 是完成工作的灵活高效的工具。它提供了强大的工具集来运行任意命令和解析输出，而不是像 goss 和 inspec 那样提供声明性 DSL。当您可以在您的环境中分布独立的小型测试集时，Sparrow 插件为您提供了良好的代码重用水平。

听听读者对这种方法与现有方法(goss，inspec)进行比较的看法会很有趣。**