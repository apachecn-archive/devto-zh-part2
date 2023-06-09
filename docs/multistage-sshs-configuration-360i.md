# 多级 SSH 的配置。

> 原文：<https://dev.to/arccosine/multistage-sshs-configuration-360i>

# 多级 ssh

在某些情况下，有必要使用多级 SSH。
(AWS 上几乎出现“ssh 端口 22 操作超时”是不好的。)

## 准备

*   创建 server1 的私钥。
*   创建 server2 的私钥。

## 更新配置

编辑 ssh 配置文件。

```
vim ~/.ssh/config 
```

Enter fullscreen mode Exit fullscreen mode

添加以下参数。

```
Host preserver
  HostName server2.com
  ForwardAgent yes
  User server2user-name 
  IdentityFile ~/.ssh/server2key
  ProxyCommand ssh -l server1user-name -i ~/.ssh/server1key -p port-number server1.com -W %h:%p 
```

Enter fullscreen mode Exit fullscreen mode

## 命令

```
ssh-add ~/.ssh/server2key
ssh preserver 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！