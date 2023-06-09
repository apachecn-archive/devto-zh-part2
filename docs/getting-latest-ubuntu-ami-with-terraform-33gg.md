# 使用 Terraform 获取最新的 Ubuntu AMI

> 原文：<https://dev.to/andreagrandi/getting-latest-ubuntu-ami-with-terraform-33gg>

*本帖原载于本人博客:*[https://www . Andrea grandi . it/2017/08/25/getting-latest-Ubuntu-ami-with-terraform/](https://www.andreagrandi.it/2017/08/25/getting-latest-ubuntu-ami-with-terraform/)

当我们需要使用 Terraform 在 AWS 上创建 EC2 资源时，我们需要指定 AMI id 来获得正确的映像。

id 不容易记忆，它会根据我们工作的区域而变化。在每个新的版本中，id 都会再次改变。

那么，我们如何确保获得我们地区的正确 ID，对于给定的 Linux 发行版来说最新的映像是可用的呢？

### 获取最新的 Ubuntu AMI id

在这个例子中，我将展示如何为伦敦地区获取最新版本的 Ubuntu 16.04 服务器的 ID，并使用该 ID 创建一个 EC2 实例。

```
variable "aws_region" { default = "eu-west-2" } # London

provider "aws" {
    region = "${var.aws_region}"
    access_key = "youraccesskey"
    secret_key = "yoursecretkey"
}

data "aws_ami" "ubuntu" {
    most_recent = true filter {
        name   = "name"
        values = ["ubunimg/hvm-ssd/ubuntu-xenial-16.04-amd64-server-*"]
    }

    filter {
        name   = "virtualization-type"
        values = ["hvm"]
    }

    owners = ["099720109477"] # Canonical
}

resource "aws_instance" "web" {
    ami           = "${data.aws_ami.ubuntu.id}"
    instance_type = "t2.micro"

    tags {
        Name = "HelloUbuntu"
    }
}

output "image_id" {
    value = "${data.aws_ami.ubuntu.id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们使用 **terraform init** 初始化我们的脚本后，如果我们运行它，我们将获得 AMI id，实例将被创建:

```
➜  example1$: terraform apply
data.aws_ami.ubuntu: Refreshing state...
aws_instance.web: Creating...
    ami:                          "" => "ami-03998867"
    associate_public_ip_address:  "" => "<computed>"
    availability_zone:            "" => "<computed>"
    ebs_block_device.#:           "" => "<computed>"
    ephemeral_block_device.#:     "" => "<computed>"
    instance_state:               "" => "<computed>"
    instance_type:                "" => "t2.micro"
    ipv6_address_count:           "" => "<computed>"
    ipv6_addresses.#:             "" => "<computed>"
    key_name:                     "" => "<computed>"
    network_interface.#:          "" => "<computed>"
    network_interface_id:         "" => "<computed>"
    placement_group:              "" => "<computed>"
    primary_network_interface_id: "" => "<computed>"
    private_dns:                  "" => "<computed>"
    private_ip:                   "" => "<computed>"
    public_dns:                   "" => "<computed>"
    public_ip:                    "" => "<computed>"
    root_block_device.#:          "" => "<computed>"
    security_groups.#:            "" => "<computed>"
    source_dest_check:            "" => "true"
    subnet_id:                    "" => "<computed>"
    tags.%:                       "" => "1"
    tags.Name:                    "" => "HelloUbuntu"
    tenancy:                      "" => "<computed>"
    volume_tags.%:                "" => "<computed>"
    vpc_security_group_ids.#:     "" => "<computed>"
aws_instance.web: Still creating... (10s elapsed)
aws_instance.web: Still creating... (20s elapsed)
aws_instance.web: Still creating... (30s elapsed)
aws_instance.web: Creation complete (ID: i-0f58f8bd55b3a7e38)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

image_id = ami-03998867 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们使用最新的 Ubuntu 映像在 AWS 上运行 EC2 实例所需的全部内容。