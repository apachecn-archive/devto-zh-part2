# 保存 ssh 配置以快速连接到服务器

> 原文：<https://dev.to/catalinxyz/saved-ssh-configuration-to-quickly-connect-to-servers-29k4>

最初发布于[catalinxyz.com](https://catalinxyz.com/saved-ssh-config)

当使用服务器时，你必须多次通过`ssh`连接，每次都写`ssh user@255.255.255.255`会很麻烦。我们可以在 ssh 配置中保存连接细节，然后简单地运行`ssh serverName`

### 编辑或创建不存在的配置文件

```
~/.ssh/config 
```

Enter fullscreen mode Exit fullscreen mode

### 添加一个连接并保存

```
Host {name}
    HostName {server_ip}
    User {username}
    IdentitiesOnly yes
    IdentityFile ~/.ssh/id_name 
```

Enter fullscreen mode Exit fullscreen mode

### 连接运行

```
ssh {name} 
```

Enter fullscreen mode Exit fullscreen mode