# 从 Jenkins 上运行的 Docker 容器发出请求

> 原文：<https://dev.to/david_ojeda/make-requests-from-docker-container-running-on-jenkins-3ln1>

这篇文章是关于我遇到的一个具体问题，我无法从 Docker 容器向外界发出任何请求。这是我第一次与詹金斯管道公司合作，并决定使用 Docker 作为我的隔离代理。

与我的环境相关的一些事实:

*   Jenkins 运行在 Tomcat 上，而不是 Docker 容器上
*   我使用 **iptables** 进行端口重定向(80 到 8080 和 443 到 8443)

## 代码有问题

我的 Jenkinsfile 的相关部分是这样的:

```
stage('build and test apps') {
  failFast true
  parallel {
    stage('front end') {
      steps {
        sh 'npm install --prefix front_end/'
        sh 'npm run build --prefix front_end/'
        sh 'npm run test --prefix front_end/'
      }
    }

    stage('back end') {
      steps {
        sh 'npm install --prefix back_end/'
        sh 'npm run test --prefix back_end/'
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 日志

在`npm install`命令上需要的请求是**失败，出现 403 http 错误**，并且这个日志出现:

```
npm ERR! Unexpected token < in JSON at position 0
npm ERR! <html><head><meta http-equiv='refresh' content='1;url=/login?from=%2Freact-scripts'/><script>window.location.replace('/login?from=%2Freact-scripts');</script></head><body style='background-color:white; color:white;'>
npm ERR!
npm ERR!
npm ERR! Authentication required
npm ERR! <!--
npm ERR! You are authenticated as: anonymous
npm ERR! Groups that you are in:
npm ERR!
npm ERR! Permission you need to have (but didn't): hudson.model.Hudson.Read
npm ERR!  ... which is implied by: hudson.security.Permission.GenericRead
npm ERR!  ... which is implied by: hudson.model.Hudson.Administer
npm ERR! -->
npm ERR!
npm ERR! </body></html>
npm ERR!
npm ERR! If you need help, you may report this error at:
npm ERR!     <https://github.com/npm/npm/issues> 
```

Enter fullscreen mode Exit fullscreen mode

读完日志后，我想到的第一件事是:这与运行 Docker 容器的用户的权限有关，在我的例子中是 tomcat。

在调试了相当长一段时间后，我偶然发现 StackOverflow 上的一些神光。多亏了这个，我发现我正在将**所有**端口 80 和 443 流量从**各处**重定向到它们相应的 8080 和 8443 端口。

## 解

**更改 iptables 以创建一个否定的规则，并避免来自 Docker 子网**的重定向。

首先，用这个命令检查当前的 iptables:

`iptables -t nat -L -n --line-numbers`

```
Chain PREROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    REDIRECT   tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80 redir ports 8080
2    REDIRECT   tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:443 redir ports 8443
3               tcp  --  0.0.0.0/0            0.0.0.0/0
4    DOCKER     all  --  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL

Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination
1    REDIRECT   tcp  --  0.0.0.0/0            127.0.0.1            tcp dpt:443 redir ports 8443
2    REDIRECT   tcp  --  0.0.0.0/0            127.0.0.1            tcp dpt:80 redir ports 8080
3    DOCKER     all  --  0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL

Chain POSTROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    MASQUERADE  all  --  172.17.0.0/16       0.0.0.0/0

Chain DOCKER (2 references)
num  target     prot opt source               destination
1    RETURN     all  --  0.0.0.0/0            0.0.0.0/0 
```

Enter fullscreen mode Exit fullscreen mode

源栏上的值 **0.0.0.0/0** 指的是**所有**流量。因此，在我的例子中，我需要替换预路由和输出链的前两个条目:

**注**:默认 Docker 子网为 **172.17.0.0/16** 。如果您更改了默认配置，请确保也在命令中更改它们。

**预路由**

```
iptables -t nat -R PREROUTING 1 ! -s 172.17.0.0/16 -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 8080 
```

Enter fullscreen mode Exit fullscreen mode

```
iptables -t nat -R PREROUTING 2 ! -s 172.17.0.0/16 -p tcp -m tcp --dport 443 -j REDIRECT --to-ports 8443 
```

Enter fullscreen mode Exit fullscreen mode

**输出**

```
iptables -t nat -R OUTPUT 1 ! -s 172.17.0.0/16 -d 127.0.0.1/32 -p tcp -m tcp --dport 443 -j REDIRECT --to-ports 8443 
```

Enter fullscreen mode Exit fullscreen mode

```
iptables -t nat -R OUTPUT 2 ! -s 172.17.0.0/16 -d 127.0.0.1/32 -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 8080 
```

Enter fullscreen mode Exit fullscreen mode

现在，来自 Docker 容器的请求返回 200 http 状态，每个人都很高兴🤗如果你有类似的问题，希望这能帮助你！

顺便提一下，在做这些事情的同时，我还需要学习更多关于 iptables 及其不同选项的知识。我发现**李诺德**T3[的这个快速指南很方便。](https://www.linode.com/docs/security/firewalls/control-network-traffic-with-iptables/)

感谢你阅读我的文章！