# 通过创建 AMI (Amazon 机器映像)介绍 Packer

> 原文：<https://dev.to/adarshkkumar/introduction-to-packer-by-creating-a-ami-amazon-machine-images-92h>

# 什么是封隔器？

Hashicorp 的一个工具，用于为大多数平台(AWS、Google Cloud、Digital Ocean、Docker、Azure 等)构建机器映像，并自动配置和安装需要的软件。

我们定义了如何在 JSON 模板文件中构建图像。这应该是受版本控制的，并像任何其他代码一样对待。

> [基础设施即代码。](http://infrastructure-as-code.com/)

# 各段封隔器模板

## 建设者

建造者负责创造形象。Packer 为各种平台提供建设者。
在这个例子中，我们将使用**亚马逊-ebs** 作为构建器。
我们在模板的构建器部分提供了特定于平台的配置。比如启动哪种类型的实例(t2-micro m4-large 等)来创建映像、SSH/访问详细信息或凭据。你也可以创建定制的构建器，虽然我们不会讨论这个。

构建器使用通信器与创建的实例建立连接。它可以是 SSH 连接，也可以是 Windows 计算机的 WINRM。

## 置备者

一旦构建者创建了一台机器，我们将使用供应器来安装软件并将机器配置到所需的状态。Packer 为 Chef/Puppet/Ansible 或旧的 shell 脚本等配置管理工具提供供应器。

## 后处理器

一旦创建了机器、安装了软件并且由供应器应用了配置，就运行后处理器。例如，我们可以使用它们将 docker 图像推送到注册表中。我们不会报道这个，详情请参考这个文档。

## 变量

打包模板应该是通用的，这样它可以在不同的环境中使用，并且通过不在模板文件中提供它们来保持机密/凭证的安全。使用用户变量来代替这样的配置。

在下面的模板中，我们使用用户变量来表示*名称、sourceami、accesskey、secretkey、region* 。对于其中的一些，我们已经在变量部分提供了一个默认值，其他的保留为空，我们可以在构建图像时使用 **-var** 参数传递该值。

## 那么 Packer 到底是做什么的？

*   Packer 将根据构建器的类型为指定的平台创建一个实例。
*   使用指定的手操器连接机器。
*   运行所有配置程序来配置和安装软件。
*   它运行 JSON 模板中指定的任何后处理器。
*   最后，它终止机器和它在这个过程中创建的任何临时资源，例如:安全组、临时 ssh 密钥对等。

# 用打包机弄脏手

## 我们要做什么？

*   创建一个预装 Nginx 的 AMI
    *   选择 source_ami 作为 CentOS 7
    *   安装 EPEL(企业版 Linux 的额外软件包)
    *   安装 nginx
    *   使 nginx 在引导时启动

```
{
    "variables": {
      "name": "centos_7_nginx",
      "source_ami": "ami-d5bf2caa",
      "access_key":"",
      "secret_key":"",
      "region":"us-east-1"
    },
    "builders": [{
      "type": "amazon-ebs",
      "access_key": "{{user `access_key`}}",
      "secret_key":"{{user `secret_key`}}",
      "ami_name": "{{user `name`}}",
      "region": "{{user `region`}}",
      "source_ami": "{{user `source_ami`}}",
      "instance_type": "t2.micro",
      "communicator": "ssh",
      "ssh_username": "centos",
      "run_tags":{"Name":"packer-image"}
    }],
    "provisioners": [
        {
            "type": "shell",
            "inline": "sudo yum install -y epel-release"
        },
        {
            "type": "shell",
            "inline": ["sudo yum install -y nginx","sudo systemctl enable nginx", "sudo systemctl start nginx"]
        }
      ]
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 命令建立图像:

```
packer build -var 'access_key=XXXXXXXXXXX' -var 'secret_key=XXXXXXXXXXXXXXXXXXXXXXXX' aws_centos_nginx.json 
```

Enter fullscreen mode Exit fullscreen mode

在运行上述命令之前，您可以使用`packer validate template.json`来验证文件

## 注:

*   从有权创建映像的 AWS 生成密钥和访问密钥，并传递 packer build 命令。
*   上面指定的源 AMI 是 CentOS Linux 7 x86_64 HVM EBS。
*   使用的通信器是 SSH，我们指定默认用户名是 centos。
*   AMI 是地区特定的，所以确保您选择 us-east-1 或 N.Virginia 作为您当前的地区。
*   run_tags 是分配给用于创建映像的机器的标签。一旦创建了映像，该机器将被终止。
*   我们已经在上面的例子中使用了 shell prvisioner 和内联模式。首先只运行一个命令，然后运行一系列命令。

> 最初发布于 [JigsawCode](https://www.jigsawcode.com)

## 下一步去哪里？

[烘烤 EC2 AMI 的包装配方](https://www.jigsawcode.com/packer-recipes-for-ec2-ami/)

## 参考文献:

[打包机文件](https://www.packer.io/docs/index.html)

页（page 的缩写）如果您觉得有用，我将写第二部分，探索 Packer 的更多特性。