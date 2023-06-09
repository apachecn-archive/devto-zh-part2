# 在 Google Cloud 上自动设置 Jenkin 主从

> 原文：<https://dev.to/vnpatriot/automatic-setup-jenkin-master-slave-on-google-cloud--f39>

[![Alt text of image](img/84c38aa728c837fe9ba77597e8d55f0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DX_7MZDV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AgVVf9VUuIMuyeILDNo1ghQ.png)

# 摘要

因为 DevOps 是数十亿资深 IT 人员的新趋势。我们必须了解与 DevOPS 故事相关的 CI/CD 和其他提示/技巧。这个资源库倾向于与您讨论另一个相关的故事，我们的目标是:自动构建 Jenkins 主&从。听起来很棒，对吧？

## 描述

我们拥抱云平台，我们将使用它来建立基础设施。如今，我们有几个云托管提供商，你通常会与他们合作，如谷歌云- AWS -数字海洋- Rackspace。在本文中，我们将选择 Google 作为帮助我们托管基础设施的友好伙伴。

否则 DevOps 中对单词 automatic 的解释并不是真的像这个单词那么简单。自动化是一门艺术，我们已经为我们想要实现的每项工作构建了数千个脚本。您可能需要每天为 100 个实例安装 100 次操作系统，配置 100 次他们自己的应用程序。系统管理员还负责每天调整大量的改进。因此，问题是我们如何用新的策略重复所有的日常任务，让它可以自动运行？

我们还熟悉了一个工具，比如 backbone，来帮助我们解决这个问题。将（行星）地球化（以适合人类居住）

目标是:使用 Terraform 实现所有 Jenkins 配置的自动化，以启动 1 个集群 Jenkins 主从架构

## 如何运行代码

1.  配置你自己的 Google secret JSON & Project 文件
2.  更改您的 SSH 公钥
3.  地形平面图-应用地形
4.  等待
5.  访问端口 8080 下的 IP 地址

### 文件配置

#### [T1】provider . TF](#providertf)

```
# Specify the provider (GCP, AWS, Azure)
provider "google" {
credentials = "${file("ilawyerlive.json")}"
project = "ilawyer-live"
region = "us-central1"
} 
```

Enter fullscreen mode Exit fullscreen mode

所以基本上，这是你要放认证密钥的文件，用来和谷歌云平台交互。我们有几种方法可以通过命令行与 GCP 交互，我的就是其中之一。使用 JSON

要使用 Google 云服务进行身份验证，需要一个 JSON 文件。该凭证文件可以通过 Google 开发者控制台下载，如下所示:

1.  登录到 Google Developers 控制台，选择您想要获取凭证文件的项目
2.  通过左侧的汉堡菜单进入“API 管理器”
3.  点击左侧的“凭证”进入“凭证”概述
4.  单击蓝色的“创建凭据”按钮，选择“服务帐户密钥”并选择“JSON”作为格式。
5.  单击“创建”以生成并下载密钥

毕竟，请更改到您自己的 json 文件下载位置。创建您自己的`project`来替换当前的`ilawyer-live`。这个`region`也很重要。如果你在新加坡，我建议你换成亚洲中心。SSH 动作会更快

#### compute.tf

这是需要重点关注的 3 个配置部分

```
### To Provision FireWall rule ###

resource "google_compute_firewall" "www" {
  name = "tf-www-firewall"
  network = "default"

  allow {
    protocol = "tcp"
    ports = ["8080", "443"]
  }

  source_ranges = ["0.0.0.0/0"]
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这个部分将用于为 GCP 提供一个规则，我们只允许 2 个端口 8080 和 443。一旦你运行它，你会看到新的规则将出现在你的 GCP 网络控制台

[![alt_test](img/8df3a17de1e4d8a406d75cab015f93b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9iMni6Um--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/nezgHcE.png)T3】

```
### To provision Jenkins Master ###

resource "google_compute_instance" "jenkins-master-1" {
   name = "jenkins-master-1"
   machine_type = "f1-micro"
   zone = "us-west1-a"
   tags = ["jenkins"]
   boot_disk {
      initialize_params {
      image = "ubuntu-1604-lts"
   }
}
network_interface {
   network = "default"
   access_config {}
}
service_account {
   scopes = ["userinfo-email", "compute-ro", "storage-ro"]
   }

 metadata_startup_script = <<SCRIPT
apt install -y update
apt install -y docker.io
systemctl enable docker
systemctl start docker
docker pull toanc/jenkins-master:latest
docker run -d -p 8080:8080 -p 50000:50000 --name master-1 toanc/jenkins-master
wget https://raw.githubusercontent.com/eficode/wait-for/master/wait-for -P /tmp
chmod +x /tmp/wait-for
/bin/sh /tmp/wait-for localhost:8080 -t 90
sleep 90
i=`hostname --ip-address`
f=`docker exec -i master-1 bash -c 'java -jar /tmp/jenkins-cli.jar -s http://localhost:8080 -remoting groovy /tmp/findkey --username admin --password admin'`

echo $f > /tmp/checkf
curl -X POST https://api.keyvalue.xyz/[HIDED]/--data ' ip: '$i' secret: '$f' '
SCRIPT

 metadata {
    sshKeys = "ubuntu:${file("~/.ssh/id_rsa.pub")}"
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本中最重要的事情是供应机器任务。正如您可以看到的这些基本标签，如`name`-`machine_type`-`zone`-&`image`，它被用来为虚拟机提供我们想要的所有自己的信息。很直，对吧？

另一件美妙的事情是`metadata_startup_script`,我们在供应期间应用了几个命令。这些命令将帮助我们自动提供我们的东西。因此，您可以看到我正在创建另一个 docker 映像，然后提取到 docker 映像并重用以启动 1 个 Docker 实例。最后一件事是，我如何注册这些将用于设置从节点的重要信息？

我知道如果你是微服务架构的老手。你会听到键值系统。那是一个闪亮的储藏室，我们把所有重要的东西都放在那里，以备再次使用和存放。在我们的情况下，这是我们需要存储的两个重要值。`IPADDRESS`为主从通讯& `TOKEN-SECRET`键验证从机与主机的联系。

首先，我的想法是使用一些关键的价值体系，如 ETCD 或领事。但主要的问题是，并不是每个人都希望它在他们的堆栈中。如果他们真的需要，可以自行实施。因此，我们确实发现了一些更简单的东西，比如作为服务提供的应用程序。所以我决定用 [https://keyvalue.xyz](https://keyvalue.xyz) 。一旦我们得到信息，我们使用`POST`方法把它放入一个现有的链接中。

如果你有和 Jenkins 一起工作的经验，你会知道初始设置的进度并不是很快。取决于集成了多少插件&配置，Jenkins 将需要几秒到几分钟来完成。为了确保该过程已经完成，我将在切换到另一个命令之前设置 90 秒的等待时间

`metadata` -请不要忘记将`~/.ssh/id_rsa.pub`位置更改为您的 SSH 公钥。这对于允许您访问虚拟机非常重要

同样，从机将与主节点同时创建。但它将等待 2 分钟，以从 KeyValue.xyz 获取正确的 Key - IP 地址。一旦它拥有足够的信息，1 个容器实例将被提供从 KeyValue 接收的信息，您可以从 Jenkins 主仪表板上看到它们。

[![alt_test](img/ed7344f2dada49b75352d3bb6eb1f6a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--37XC2Kbk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/yfsI5pJ.png)

完成后，您可能会收到所有节点的 IP 地址

```
"outputs": {
                "public_ip": {
                    "sensitive": false,
                    "type": "list",
                    "value": [
                        "35.199.162.152"
                    ]
                },
                "public_ip_master": {
                    "sensitive": false,
                    "type": "list",
                    "value": [
                        "35.199.162.152"
                    ]
                },
                "public_ip_slave": {
                    "sensitive": false,
                    "type": "list",
                    "value": [
                        "35.185.243.191"
                    ]
                }
            }, 
```

Enter fullscreen mode Exit fullscreen mode

## 常见问题

我的 docker 图像里有什么？

你可以找到更多里面的细节:)从这里[https://hub.docker.com/r/toanc/jenkins-master/](https://hub.docker.com/r/toanc/jenkins-master/)。我使用 Groovy 做了几件有用的事情，比如

*   首次初始设置
*   添加新的从节点
*   获取从属密钥

没有为所有人提供 GUI

黑客快乐！

[完整存储库](https://github.com/toanc/jenkins-cluster)