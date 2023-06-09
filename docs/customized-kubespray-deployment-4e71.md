# 定制的 Kubespray 部署

> 原文：<https://dev.to/chuck_ha/customized-kubespray-deployment-4e71>

# 版本

| 库贝斯普雷 | ansible | 大蟒 | 将（行星）地球化（以适合人类居住） |
| --- | --- | --- | --- |
| [这个叉子](https://github.com/bartlaarhoven/kubespray) | 2.7.0 | 3.6.1 | 0.11.8 |

警告！使用此 fork:https://github . com/kubrines-incubator/kubriay/pull/3486。

# 动机

这篇文章提供了在 aws 上使用 kubespray 和 bastion 主机后面的 ubuntu 映像从零开始创建 kubernetes 集群的文档。我找不到将所有这些放在一起的所有文档，想在下次需要这样做时为自己写下来。

## 步骤

1.  克隆 kubespray 并设置一些默认文件

```
git clone https://github.com/bartlaarhoven/kubespray
cd kubespray
virtualenv ks && . ks/bin/activate
pip install -r requirements.txt
cp -Rp inventory/sample/ inventory/mycluster 
```

Enter fullscreen mode Exit fullscreen mode

1.  在某个帐户中创建一个具有管理员权限的 IAM 用户(TODO 可能会缩小这个范围？)
2.  创建 EC2 密钥对
3.  将 [terraform 环境文件](https://github.com/kubernetes-incubator/kubespray/blob/7e84de2ae116f624b570eadc28022e924bd273bc/contrib/terraform/aws/credentials.tfvars.example)复制到`credentials.tfvars`，并使用用户的密钥和秘密以及 ssh 密钥对名称和您希望基础设施存在的区域对其进行修改。
4.  用你喜欢的架构定制[地形文件](https://github.com/kubernetes-incubator/kubespray/blob/7e84de2ae116f624b570eadc28022e924bd273bc/contrib/terraform/aws/terraform.tfvars)，我用了 1 个主人，1 个工人，1 个 etcd 和左 bastions 作为默认。还要修改库存文件为`../../../inventory/mycluster/hosts.ini`。
5.  修改[变量. tf](https://github.com/kubernetes-incubator/kubespray/blob/7e84de2ae116f624b570eadc28022e924bd273bc/contrib/terraform/aws/variables.tf#L23) 为

```
 data "aws_ami" "distro" {
      most_recent = true

      filter {
        name = "name"

        values = ["ubunimg/hvm-ssd/ubuntu-xenial-16.04-amd64-*"]
      }

      filter {
        name = "virtualization-type"

        values = ["hvm"]
      }

      owners = ["099720109477"]
    } 
```

Enter fullscreen mode Exit fullscreen mode

1.  运行 terraform

```
 terraform apply --var-file credentials.tfvars 
```

Enter fullscreen mode Exit fullscreen mode

1.  修改 [ansible.cfg](https://github.com/kubernetes-incubator/kubespray/blob/7e84de2ae116f624b570eadc28022e924bd273bc/ansible.cfg) 文件，通过将`ssh_args`值改为

```
 ssh_args = -F ssh-bastion.conf 
```

Enter fullscreen mode Exit fullscreen mode

1.  修改 hosts.ini 文件。使用内部 DNS 名称作为私有子网中每个实例的`ansible_host`，并将`ansible_user`作为`ubuntu`，例如:

```
 kubernetes-devtest-master0 ansible_host=ip-10-250-205-127.us-west-2.compute.internal ansible_user=ubuntu 
```

Enter fullscreen mode Exit fullscreen mode

1.  将堡垒线修改为*公共* DNS 名称为`ansible_host`，并将`ansible_user`包含为`ubuntu`，例如:

```
 bastion-0 ansible_host=ec2-22-222-22-22.us-east-2.compute.amazonaws.com ansible_user=ubuntu 
```

Enter fullscreen mode Exit fullscreen mode

1.  运行可行的行动手册

```
 ansible-playbook -i ./inventory/mycluster/hosts.ini ./cluster.yml -b --become-user=root --flush-cache 
```

Enter fullscreen mode Exit fullscreen mode

如果你想要更多的日志，添加`-v`或`-vv`到`-vvvvv`。我还喜欢将它传送到 tee，并将日志写到磁盘上，以便在出现故障时进行检查。

享受你的新集群~！

附言:如果有人知道如何将代码作为项目的一部分，以便项目符号的东西可以工作，请评论，我很乐意修复编号。