# Kong 第 1 部分:在 Ubuntu 18.04 上的安装和首次设置

> 原文：<https://dev.to/saviour123/kong-part-1-installation-and-first-setup-on-ubuntu-1804-4o3d>

孔是开源 API 网关的管理者之一。一个典型的用例是当你想要管理(例如保护)你的 API，但是你没有时间在你的 API 中编写所有这些逻辑。API Gateway 大概就是你需要的。

API 网关可以限制速率，用密钥和令牌保护 API，记录日志等等。

我们将使用 kong 来保护我用 Flask 编写的简单 API。请注意第 2 部分。

## 下载并安装

安装 kong 有几种选择，包括 Debian，现在很流行的 docker，kubernates，redhat，甚至是游民。

前往[孔](https://konghq.com/install/)主页从孔网站下载孔德边图片。

选择一个适用的并下载。

[![](../Images/836daa0e228d30b09e683f7b190381ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V1z0y1Uq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/zrmOiSZ.png%3Fraw%3Dtrue)

*   安装孔

`$ sudo apt-get install openssl libpcre3 procps perl`#有些依赖

`$ sudo dpkg -i kongdeb.deb`

还没有，孔只是装的。让我们为它提供持久数据库。

## 调配数据库

在您的设备上假设您像我一样在 Ubuntu 上安装了 kong，默认配置将在`/etc/kong/kong.cong.default`可用。

*   复制一份

`cp /etc/kong/kong.conf.default /etc/kong/kong.conf`

*   配置数据存储

孔支持 Cassandra 和 PostgreSQL 数据库。我在这里使用 PostgreSQL。

*   设置数据库

`CREATE USER kong; CREATE DATABASE kong OWNER kong`

[![](../Images/68518e17c8827dec7c2c29263bb4e4fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OM95yH8q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/e3AsUBB.png%3Fraw%3Dtrue)

*   更新`kong.conf`

滚动到数据库部分并添加 Postgres 详细信息。
`$ vim /etc/kong/kong.conf`和本节`dbname: kong, user=kong, pass=kong2018`的变化

## 开始孔

孔有些命令道

`$ kong migrations up`#运行数据库迁移
`$ sudo kong start`

`$ curl -i http://localhost:8001`

[![Imgur](../Images/baab47d3f37bdd7a66621ac4e7ed9ce1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qE3-9l4q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/FbYXgoW.png%3Fraw%3Dtrue)

## 奖金

`$ sudo kong stop`#孔
停止`$ sudo kong health`#孔状态

-快乐的黑客生涯-

NB。请注意第 2 部分，在那里我添加了 Flask API。