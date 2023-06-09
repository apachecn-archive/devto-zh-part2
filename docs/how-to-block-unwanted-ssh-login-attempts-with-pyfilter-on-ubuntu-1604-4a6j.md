# 如何在 Ubuntu 16.04 上使用 PyFilter 阻止不必要的 SSH 登录尝试

> 原文：<https://dev.to/digitalocean_staff/how-to-block-unwanted-ssh-login-attempts-with-pyfilter-on-ubuntu-1604-4a6j>

作者:[杰森·霍尔](https://www.digitalocean.com/community/users/jason2605?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand&utm_content=pyfilter_tutorial)

### 简介

安全外壳(SSH)是一种加密网络协议，用于安全地运行网络服务。它通常用于远程控制计算机系统或传输文件。当 SSH 暴露于公共互联网时，它就成了一个安全问题。例如，你会发现机器人试图通过暴力方法猜测你的密码。

PyFilter 的目标是过滤掉所有对你的服务器的非法登录请求，如果发送的请求太多，就阻止它们。它通过读取日志文件并检查在用户可配置的时间内失败的请求是否来自同一个 IP 地址来工作。然后，如果它捕获太多失败的尝试，它会向防火墙添加规则，拒绝连接到您的服务器的能力。

在本教程中，您将安装并配置 PyFilter 来阻止 SSH 请求。然后，您将安装 PyFilter 作为一项服务，并可选地配置跨服务器禁止同步，这是一个允许多台服务器共享禁止 IP 地址列表的功能，并允许 PyFilter 记录有关 IP 地址的位置数据。最后，您将探索如何取消禁止 IP 地址。

## 先决条件

要完成本教程，您需要:

*   一台按照[Ubuntu 16.04 初始服务器设置指南](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand&utm_content=pyfilter_tutorial)设置的 Ubuntu 16.04 服务器，包括一个 sudo 非根用户和一个防火墙。
*   Python 3，已经默认安装在 Ubuntu 16.04 上。
*   安装有`sudo apt-get install python3-pip`的 PIP。
*   (可选)如果您希望在[步骤 4](#step-4) 中配置 PyFilter 的跨服务器 ban 同步功能，请按照[如何在 Ubuntu 16.04 上安装 Redis](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-redis-on-ubuntu-16-04?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand&utm_content=pyfilter_tutorial)安装 Redis。

## 步骤 1 —下载并配置 PyFilter

我们将通过从 Github 克隆 PyFilter 的存储库来下载它。切换到您的主目录并克隆存储库:

```
cd ~
git clone https://github.com/Jason2605/PyFilter.git 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个名为`PyFilter`的目录。将该文件夹移动到`/usr/local`文件夹:

```
sudo mv PyFilter /usr/local/PyFilter 
```

Enter fullscreen mode Exit fullscreen mode

然后转到`/usr/local/PyFilter`目录:

```
cd /usr/local/PyFilter 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要创建一个配置文件。PyFilter 带有一个默认的配置文件，位于`Config/config.default.json`。我们将复制并编辑复制的版本，而不是直接编辑默认文件。这样，如果出现问题，您就有了默认的配置文件进行比较。

复制默认配置文件:

```
sudo cp Config/config.default.json Config/config.json 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`less`命令查看配置文件的内容:

```
less Config/config.json 
```

Enter fullscreen mode Exit fullscreen mode

默认设置要求请求在最后一个请求的 5 秒内，这需要发生 5 次，它们足够好了。让我们运行 PyFilter 并确保一切正常。

## 步骤 2 —运行 PyFilter

PyFilter 下载包括一个名为`run.sh`的脚本，您应该使用它来启动 PyFilter。

首先，更改脚本的权限，使其可执行。

```
sudo chmod +x run.sh 
```

Enter fullscreen mode Exit fullscreen mode

授予权限后，运行脚本启动 PyFilter:

```
./run.sh 
```

Enter fullscreen mode Exit fullscreen mode

PyFilter 将开始查看日志，您将看到事件发生时的输出:

```
[secondary_label Output]
No file to check within rule: Mysql
No file to check within rule: Apache
No file to check within rule: Nginx
Checking Ssh logs 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，PyFilter 会禁止在上次请求失败后 5 秒内发出 5 次或更多失败请求的 IP。您可以在 PyFilter 配置文件中对此进行更改。

这些结果也被记录到`/usr/local/PyFilter/Log`目录中。

当一个 IP 达到允许禁止的限制时，您将看到类似如下的输出:

```
[secondary_label Output]
2018-03-22 14:18:18 Found IP: <^>203.0.113.13<^> from server: <^>your_server_name<^>. 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:如果你因为禁止了自己而不小心将自己锁在了你的 Droplet 之外，你可以按照教程[如何使用数字海洋控制台访问你的 Droplet](https://www.digitalocean.com/community/tutorials/how-to-use-the-digitalocean-console-to-access-your-droplet?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand&utm_content=pyfilter_tutorial) 重新进入。然后按照[第 6 步](#step-6-%E2%80%94-un-banning-ip-addresses)中的步骤移除被禁 IP。

要关闭 PyFilter，请按`CTRL+C`。

现在让我们将 PyFilter 安装为一个服务，这样它可以自动运行。

## 步骤 3 —为 PyFilter 创建服务

现在您已经知道 PyFilter 可以工作了，让我们将它配置为作为一个服务运行，这样它就可以在我们每次重启服务器时启动。

在`PyFilter`目录中，有一个名为`install.sh`的脚本，它为 PyFilter 创建一个服务，并使它能够在系统启动时运行。

修改脚本以便您可以执行它:

```
sudo chmod +x install.sh 
```

Enter fullscreen mode Exit fullscreen mode

然后启动脚本:

```
./install.sh 
```

Enter fullscreen mode Exit fullscreen mode

您将看到以下输出，表明安装成功:

```
[secondary_label Output]
Service created and enabled, check the status of it by using "sudo systemctl status PyFilter" 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们这样做，以确保一切正常运行:

```
sudo systemctl status PyFilter 
```

Enter fullscreen mode Exit fullscreen mode

您将看到这个输出，显示服务是`active` :

```
[secondary_label Output]
● PyFilter.service - PyFilter
   Loaded: loaded (/etc/systemd/system/PyFilter.service; enabled; vendor preset: enabled)
   Active: <^>active^> (running) since Wed 2018-03-21 18:55:35 UTC; 12s ago
 Main PID: 8383 (bash)
   CGroup: /system.slice/PyFilter.service
           ├─8383 bash /usr/local/PyFilter/run.sh
           ├─8384 sudo python3 run.py
           └─8387 python3 run.py 
```

Enter fullscreen mode Exit fullscreen mode

如果看到错误，请再次查看安装步骤。

接下来，让我们来看看如何配置 PyFilter 来与其他服务器共享被禁止的 IP 地址。

## 步骤 4 —为跨服务器 Ban 同步配置 PyFilter(可选)

跨服务器禁止同步允许被禁止的 IP 地址与所有其他使用 PyFilter 保护它们的服务器同步，即使该地址不符合被禁止的条件，也可以禁止该地址。这意味着它可以领先一步，潜在的机器人瞄准你的其他系统，因为 IP 已经被禁止。

如先决条件中所述，您需要安装和配置 Redis。

你还需要`redis` Python 模块，可以用`pip` :
安装

```
pip3 install redis 
```

Enter fullscreen mode Exit fullscreen mode

然后编辑您的配置文件，使用 Redis 而不是 SQLite。在文本编辑器中打开`Config/config.json`文件:

```
nano Config/config.json 
```

Enter fullscreen mode Exit fullscreen mode

找到下面一行:

```
[label Config/config.json]
"database": "sqlite" 
```

Enter fullscreen mode Exit fullscreen mode

将`sqlite`改为`redis` :

```
[label Config/config.json]
"database": "<^>redis<^>" 
```

Enter fullscreen mode Exit fullscreen mode

接下来，更改 Redis 连接信息。找到文件的这一部分:

```
[label Config/config.json]
  "redis": {
    "host": "<^>127.0.0.1<^>",
    "password": <^>null<^>,
    "database": 0,
    "sync_bans": {
      "active": true,
      "name": "<^>your_hostname<^>",
      "check_time": 600
    }
  }, 
```

Enter fullscreen mode Exit fullscreen mode

修改此部分，使其包含 Redis 服务器的连接详细信息。然后，在`sync_bans`部分，将`name`更改为您的主机名。对于使用相同 Redis 服务器运行 PyFilter 的每个系统，该名称必须是唯一的，以便跨服务器 ban sync 能够正常工作。

保存文件并退出编辑器。然后重新启动 PyFilter 以应用这些更改:

```
sudo systemctl restart PyFilter 
```

Enter fullscreen mode Exit fullscreen mode

PyFilter 现已安装并运行。

## 步骤 5 —配置 PyFilter 以收集关于 IP 地址的位置数据(可选)

PyFilter 可以检索关于被禁止的 IP 的位置数据，以便提供关于大多数攻击来自哪里的统计信息。这个可选模块将把这些信息添加到 PyFilter 的日志中。

要使用这个特性，首先需要`geoip2` Python 模块，可以用`pip` :
安装

```
pip3 install geoip2 
```

Enter fullscreen mode Exit fullscreen mode

一旦你安装了这个模块，重启 PyFilter 让它识别新的模块:

```
sudo systemctl restart PyFilter 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您看到一个被禁止的 IP 地址时，您将看到关于该 IP 的附加信息:

```
[secondary_label Output]
2018-03-22 14:18:18 Found IP: <^>203.0.113.13<^> from server: <^>your_server_name<^>. <^>The IP was from United Kingdom<^>. 
```

Enter fullscreen mode Exit fullscreen mode

PyFilter 现在成功地记录了请求来自哪个国家。

最后，让我们看看如何取消地址禁令。

## 第 6 步—取消禁止 IP 地址

PyFilter 纯粹是通过创建 iptables 规则来禁止 IP 地址的一种手段。当它禁止一个 IP 时，它更新防火墙规则，然后将规则的快照保存到文件`/usr/local/PyFilter/Config/blacklist.v4`和`/usr/local/PyFilter/Config/blacklist.v6`。

下面是`/usr/local/PyFilter/Config/blacklist.v4` :
中几个被禁止的 IPv4 地址的例子

```
[label /usr/local/PyFilter/Config/blacklist.v4]
# Generated by iptables-save v1.6.0 on Thu Mar 22 19:53:04 2018
*filter
:INPUT ACCEPT [217:30580]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [249:30796]
<^>-A INPUT -s 203.0.113.13/32 -j DROP<^>
<^>-A INPUT -s 203.0.113.14/32 -j DROP<^>
<^>-A INPUT -s 203.0.113.15/32 -j DROP<^>
COMMIT
# Completed on Thu Mar 22 19:53:04 2018 
```

Enter fullscreen mode Exit fullscreen mode

要取消禁止该 IP 地址，请在文本编辑器中打开相关的黑名单文件:

```
sudo nano /usr/local/PyFilter/Config/blacklist.v4 
```

Enter fullscreen mode Exit fullscreen mode

从文件中删除关联的 iptables 规则。在这种情况下，我们已经从文件
中删除了`203.0.113.13`

```
[label /usr/local/PyFilter/Config/blacklist.v4]
# Generated by iptables-save v1.6.0 on Thu Mar 22 19:53:04 2018
*filter
:INPUT ACCEPT [217:30580]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [249:30796]
<^>-A INPUT -s 203.0.113.14/32 -j DROP<^>
<^>-A INPUT -s 203.0.113.15/32 -j DROP<^>
COMMIT
# Completed on Thu Mar 22 19:53:04 2018 
```

Enter fullscreen mode Exit fullscreen mode

然后保存文件并关闭编辑器。用`sudo systemctl restart PyFilter`重启 PyFilter，PyFilter 会用这个文件更新你的防火墙规则。

有关使用 iptables 管理规则的更多信息，请参见[如何列出和删除 Iptables 防火墙规则](https://www.digitalocean.com/community/tutorials/how-to-list-and-delete-iptables-firewall-rules?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand&utm_content=pyfilter_tutorial)。

您还可以告诉 PyFilter 忽略某些 IP 地址，方法是将它们添加到`/usr/local/PyFilter/Config/config.json`文件中的白名单部分。

## 结论

现在，您已经安装了 PyFilter 并监控您的 SSH 连接。

要了解关于配置文件每个部分的更多信息，以及如何为其他服务(如 MySQL 和 Apache)配置监控，请查看 [PyFilter 站点](http://pyfilter.co.uk)。

*这篇文章最初发表在[数字海洋社区网站](https://www.digitalocean.com/community?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand&utm_content=pyfilter_tutorial)。作者选择了[Code.org](https://www.brightfunds.org/organizations/code-org)来接受 200 美元的捐赠，作为[为捐赠而写](https://do.co/w4do-cta)计划的一部分。*

编辑:[布莱恩·霍根](https://www.digitalocean.com/community/users/bhogan?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand&utm_content=pyfilter_tutorial)

在 DigitalOcean 上构建、测试和部署一些新的东西——一体化云平台开发人员及其团队的最爱。为新用户提供 100 美元的免费帐户信用:[do.co/devto](http://do.co/devto)T3】

[![Creative Commons License](img/4662fd03838b335557c183fd57c6b02c.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/) 
本作品受[知识共享署名-非商业性使用-类似共享 4.0 国际许可](http://creativecommons.org/licenses/by-nc-sa/4.0/)的许可。