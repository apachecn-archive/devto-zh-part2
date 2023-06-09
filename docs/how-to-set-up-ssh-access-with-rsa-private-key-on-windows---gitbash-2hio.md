# 如何在 Windows - GitBash 上用 RSA 私钥设置 SSH 访问

> 原文：<https://dev.to/juni/how-to-set-up-ssh-access-with-rsa-private-key-on-windows---gitbash-2hio>

从 windows 进行 SSH 访问不需要在您的计算机上安装任何额外的软件。它可以通过你最喜欢的命令行工具来访问。今天我将向你展示如何使用你最喜欢的命令行工具通过 SSH 轻松访问你的云服务器，并保存下来供你以后访问。

在您的计算机上安装 GitBash，然后运行以下命令。

```
$ cd ~/.ssh
// If the above commands returns: 
bash: cd: /c/Users/junaid/.ssh: No such file or directory 
// Then run the following commands instead

$ mkdir ~/.ssh
$ cd ~/.ssh
$ explorer .

```

> 我使用 GitBash 是因为我喜欢它。如果您愿意，也可以使用 windows 命令行工具(略有不同)

在 windows 命令提示符下，您可以从“开始”菜单打开 windows 命令提示符，然后运行以下命令:

```
cd .ssh
// If the above command returns: 
The system cannot find the path specified.
// Then run the following commands instead

mkdir .ssh
explorer .

```

它将为您打开文件夹。将您的“私有 RSA 密钥”文件粘贴到该文件夹中，并创建一个配置文件(“config”不需要文件类型扩展名)。如果你已经有一个配置文件，然后编辑它，并将下面的代码复制到其中。

[![Alt Text](img/42886dba9e07b5fbf3baea5c2ee7d45c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SDcfLVKK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdor3wos10vs2jjmjyn2.png)

```
Host X
    HostName xxx.xxx.xxx.xxx
    User root
    IdentityFile ~/.ssh/fileNameWhereRSAKeyIsStored
    IdentitiesOnly yes
    Port ****
    ServerAliveInterval 60
```

编辑配置文件中的详细信息，并输入从 SSH 服务提供商那里获得的主机名、用户和端口号。

### 命名你的 RSA 文件

对于您的 RSA 文件，您可以随意命名(不需要文件扩展名)。该文件将在您的配置文件中被引用为`IdentityFile`

现在，每当您需要连接到 SSH 时，只需运行以下命令。

```
$ ssh X
```

如果愿意，您还可以在 SSH 主机上配置多个。你所要做的就是粘贴你的 RSA 密钥文件并编辑配置文件。例如，如果我必须配置两台 SSH 主机，那么我的配置文件将如下所示:

```
Host X
    HostName xxx.xxx.xxx.xxx
    User root
    IdentityFile ~/.ssh/fileNameWhereRSAKeyIsStored
    IdentitiesOnly yes
    Port portNumberOfSSH
    ServerAliveInterval 60

Host Y
    HostName yyy.yyy.yyy.yyy
    User root
    IdentityFile ~/.ssh/fileNameWhereRSAKeyIsStored
    IdentitiesOnly yes
    Port portNumberOfSSH
    ServerAliveInterval 60
```

简单不？