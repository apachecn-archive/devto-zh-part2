# 自动化操作系统 NAS 存储管理

> 原文：<https://dev.to/danquack/automating-storage-os-management-39kd>

据《福布斯》报道，到 2025 年，全世界的数据存储量预计将达到 163 兆字节。可以想象，这个数字还在继续增长，从大量的应用程序到我们最喜欢的小狗视频，数据无处不在。数据背后是存储，存储连接到操作系统。这就是我的最新项目适合的地方。

# 问题陈述

我们如何让网络连接存储(NAS)易于管理并为客户提供自助服务？

# 上下文

在我的环境中，有三个关键的业务逻辑。第一个是我们的配置管理工具 Puppet，它管理几个生产和非生产环境中成千上万的服务器。第二个是 Bitbucket，它存放了整个公司的代码，并在 cron 风格的基础上将自建模块同步到所有的傀儡主人。第三是允许系统“设计者”将基础设施自动化为完全自助服务的环境。

在这个问题陈述中，有几个关键因素。为简单起见，我将把它们分解成一个角色，而不是它们的实际名称——存储、系统和客户。

# 背景

在环境中，当开发团队或客户请求存储时，该请求会被过滤到系统团队。然后，系统团队请求从存储中调配存储，现在是通过自动化 API 调用或自助服务页面。一旦存储资源调配完毕并呈现给服务器，系统团队就需要安装、提供权限，并提醒客户新的网络存储可供他们使用。今天，系统团队的任务是非常手动的，尽管流程是同步的，但文书工作和手动操作键盘可能会延长请求的时间。

# 建筑

[![bitbucket-puppet-servers](img/fc26b7f0987c38f531802d7a649913b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ENdwNLc9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lucidchart.com/publicSegments/view/9fa476b2-3fd1-4c03-ae4a-a3be26cac1d1/image.png)

# 解

创建一个容器化的 API，动态扩展和管理基于定义的文件，然后通过配置管理推出这些文件。基于 Flask 的 Python API 通过 Bitbucket 为主机和主机组获取最新版本的挂载，添加来自 API 的修改，然后提交并推回 git。从那里，Bitbucket 与 puppet 同步，在 30 分钟内(或者下一次手动 Puppet 运行时)，新的挂载出现在操作系统上，供客户使用。

# 详细信息

### API

| HTTP 方法 | URI 路径 | 描述 |
| 得到 | / | 返回运行状况以显示节点处于活动状态 |
| 得到 | /安装 | 取回所有坐骑 |
| 得到 | /装载/主机，/装载/主机组 | 仅检索[主机名/主机组]装载。 |
| 得到 | /mounts/[主机/主机组]/[服务器名/主机组名] | 检索服务器/主机组装载 |
| 得到 | /mounts/[主机/主机组]/[服务器名/主机组名]/uuid | 检索给定 UUID 的独立装入点 |
| 发布/上传 | /mounts/[主机/主机组] | 为特定主机或主机组创建新的 NAS 点 |
| 修补 | /mounts/[主机/主机组]/[服务器名/主机组名]/uuid | 修改特定主机或主机组的 NAS 点 |
| 删除 | /mounts/[主机/主机组]/[服务器名/主机组名] | 删除特定主机或主机组的管理 |
| 删除 | /mounts/[主机/主机组]/[服务器名/主机组名]/uuid | 删除特定主机或主机组的 NAS 点管理 |

### YAML 文件:

yaml 的结构定义了一组挂载对象，这些对象位于主机/主机组上。

```
nfs_mounts::Hosts:
   Hostname.company.com:
    -   uuid
        share_path
        local_path
        options
        owner
        group
   Hostname2.company.com:
    -   ...
nfs_mounts::Hostgroups:
    Group1:
    -   uuid
        share_path
        local_path
        options
        owner
        group
    -   ...
     Group2
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 傀儡代码

下面的代码片段加载 yaml，然后遍历主机和主机组，并在运行时检查主机或主机组是否匹配。

```
# == Class: nfs_mounts ()
#
# Base Class for NAS Mounts
#
class nfs_mounts(
  $hosts,
  $hostgroups
) {
    include '::stdlib'
    # Load in Dynamic Yaml file
    if is_hash($hosts) and has_key($hosts, $::fqdn) {
        # Check if the server name matches item in loop
        $hosts[$::fqdn].each | $mount | {
            if (is_hash($mount) and
                has_key($mount, 'local_path') and
                has_key($mount, 'share_path') and
                has_key($mount, 'owner')  and
                has_key($mount, 'group') and
                has_key($mount, 'options') ){
                # Create the directory to be mounted
                exec { $name :
                    command => "/bin/mkdir -p ${mount[local_path]}",
                    unless  => "/usr/bin/test -d ${mount[local_path]}",
                }
                # Set Ownership
                file { $mount[local_path] :
                    ensure  => directory,
                    owner   => $mount[owner],
                    group   => $mount[group],
                    require => Exec[$name],
                }
                # Mount the share
                mount { $mount[local_path] :
                    ensure   => mounted,
                    atboot   => 'yes',
                    device   => $mount[share_path],
                    remounts => false ,
                    fstype   => 'nfs',
                    options  => $mount[options],
                    dump     => '0',
                    pass     => '0',
                    require  => Exec[ $name ],
                }
            }
        }
    }

    if is_hash($hostgroups) and has_key($hostgroups, $::hostgroup) {
        $hostgroups[$::hostgroup].each | $mount | {
            ...
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要查看完整的脚本 api，请在我的 github 上查看该项目，并在下面的评论中告诉我你的想法。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [丹嘎嘎](https://github.com/danquack) / [ NFS-API](https://github.com/danquack/NFS-API)

### 通过 mounts.yml 文件管理 nfs 挂载的容器化 flask api

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/71f4baaedc05615829a6e41f4ee93d68.png)T2】](https://travis-ci.org/danquack/NFS-API)

NFS API 旨在管理 nfs_mounts mounts puppet repo 中的 mounts.yml 文件。这个 Python3 应用程序被 dockerized 化，并使用嵌入到容器中的 ssh 密钥发布到 git。

# 结构:

```
main.py - flask application that defines the endpoints
mounts.py - the behind the scenes worker to read and update the mounts file
requirements.txt - pip3 install -r requirements.txt - will install the necesary packages needed to run the app 
```

# Pre cursor

作为一个容器，这个项目的主要目的是管理一个基于 git 的层次定义。因此，git 中必须有一个 NFS 挂载项目，它利用了基于 hiera 的定义。

# 安装说明

1.  克隆项目
2.  在 dockerfile 中修改 git 用户配置文件配置
3.  运行`docker build -t nfs_api:latest .`
4.  奔跑

```
docker run \
-e REPO_URI=<full ssh git (ex. git@github.com:someuser/NFS-MOUNTS.git)> \
-v /path/to/ssh/on/host:/root/.ssh \
-e GIT_DIRECTORY=/path/to/store/repo
-p 5000:5000 \
nfs_api:latest 
```

4.  与您的上的 api 进行交互…

</article>

[View on GitHub](https://github.com/danquack/NFS-API)