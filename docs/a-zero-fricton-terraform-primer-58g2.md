# 零摩擦地面底漆

> 原文：<https://dev.to/grahamlyons/a-zero-fricton-terraform-primer-58g2>

什么是 Terraform，为什么要关注它？如果不在你的亚马逊账户中提供真实的东西，你怎么能了解它呢？

## 基础设施为代码

当我们开始远离机架中的真实服务器时，出现了许多云基础设施提供商，他们通过控制台和 API(如果他们不错的话)提供对虚拟资产的访问。

我在许多使用这些云提供商(好的，主要是 Amazon Web Services)的不同地方工作过，我见过许多不同的管理基础设施的方法。

最糟糕的方式当然是通过控制台。在 GUI 中单击并不是使流程可重复或可伸缩的好方法。除此之外，选项还包括:CloudFormation，AWS 的一项服务(仅适用于 AWS)；来自 OpenStack 的 HEAT 模板，与 CloudFormation 非常相似(仅适用于 open stack)；Chef Provisioning，Chef 不再支持它；当然，还有 Terraform。

所有这些工具都允许您定义您想要的基础架构——虚拟机、负载平衡器、块存储、数据库等。-作为某种机器和人类可读的语言(例如，CloudFormation 使用 JSON)。该工具可以解释代码并创建所需的资源；代码可以被签入版本控制，并像应用程序代码一样被跟踪。

## 地形

Terraform 是 Hashicorp 的一个基础设施代码工具，hashi corp 还开发了其他一些流行的软件，比如 vagger。它使用一种声明性语言 Hashicorp 配置语言(HCL)来定义您的云基础架构的理想状态。从这段代码中，它生成资源的依赖关系图，当针对一个或多个提供者运行时，遍历该图，并确保资源存在并按照定义进行配置。

## 安装

可执行文件是作为一个单独的文件交付的，所以只需要下载并放到你的系统路径中。在*nix 系统上,`/usr/local/bin/`是个好地方，因为它通常已经在你的`$PATH`环境变量中了。从[https://www.terraform.io/](https://www.terraform.io/)找到“下载”链接，选择最适合您系统的版本。下载它，解压并把`terraform`文件放在你能运行它的地方，例如`/usr/local/bin/`。

检查它是否安装成功，并找出你运行的是什么版本——我的是:

```
$ terraform --version
Terraform v0.11.7 
```

Enter fullscreen mode Exit fullscreen mode

## 定义一些基础设施

既然已经安装了 Terraform，我们需要定义我们希望它创建什么样的基础设施。我们使用 HCL 为不同的提供商定义资源。一个简单的例子是 [random provider](https://www.terraform.io/docs/providers/random/index.html) ，它生成随机数据，例如用作服务器名。它不针对云提供商运行，也不需要 API 密钥等。所以很好的说明了 Terraform 的工作流程。我们还将使用[本地提供者](https://www.terraform.io/docs/providers/local/index.html)将随机数据写到文件中。

将以下内容放入名为`example.tf` :
的文件中

```
variable "name_length" {
  type    = "string"
  default = "2"
  description = "The number of words to put into the random name"
}

resource "random_pet" "server" {
  length = "${var.name_length}"
}

resource "local_file" "random" {                                                   
  content     = "${random_pet.server.id}"                                        
  filename = "${path.module}/random.txt"                                         
}

output "name" {
  value = "${random_pet.server.id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

### *暂且不说:代码组织*

*Terraform 会在你指定的目录(默认为当前目录)中查找所有的`*.tf`文件——子目录会被忽略。它会将找到的文件视为一个单独的定义，并绘制所有资源的图表。常见的是看到`variable`和`output`被分成不同的文件，以便清楚地找到它们。将代码分成模块也很常见，也是一个好主意，但我们今天不担心这个。*

在同一个目录下运行:`terraform init`。您应该会看到类似这样的输出:

```
$ terraform init

Initializing provider plugins...
- Checking for available provider plugins on https://releases.hashicorp.com...
- Downloading plugin for provider "random" (1.3.1)...
- Downloading plugin for provider "local" (1.1.0)...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.local: version = "~> 1.1"
* provider.random: version = "~> 1.3"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary. 
```

Enter fullscreen mode Exit fullscreen mode

Terraform 已经查看了所有的`*.tf`文件，确定了哪些提供者正在被使用，并且已经下载了适当的插件。这些文件存储在当前路径下创建的`.terraform/`目录中。

## 规划变更

Terraform 的一个令人惊讶的特性是能够预览更改，从而了解应用更改时实际会发生什么。这种改变是要就地修改我的负载平衡器，还是要破坏它并重新创建它，让我的应用程序离线宝贵的几分钟？

让我们看看那是什么样子:

```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

-----------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + local_file.random
      id:        <computed>
      content:   "${random_pet.server.id}"
      filename:  "/home/vagrant/workspace/tfdemo/random.txt"

  + random_pet.server
      id:        <computed>
      length:    "2"
      separator: "-"

Plan: 2 to add, 0 to change, 0 to destroy.

-----------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run. 
```

Enter fullscreen mode Exit fullscreen mode

这是我们第一次运行它，所以我们指定的所有资源都被创建了——查看输出中它们名称旁边的`+`。

还要注意“注意”-我们还没有保存这个计划，所以虽然我们已经很好地了解了 Terraform 在应用时会做什么，但并不保证会尝试做同样的事情。我们可以通过添加时间戳(即`terraform plan -out "plan-$(date +%s)"`)将计划存储在一个具有唯一名称的文件中。

我们在输出的最后得到这个:

```
...
This plan was saved to: plan-1527707537

To perform exactly these actions, run the following command to apply:
    terraform apply "plan-1527707537" 
```

Enter fullscreen mode Exit fullscreen mode

如果我们对这个计划满意，那么我们就可以真正实施它。

## 应用修改

当我们运行`terraform apply`并将计划文件传递给它时，我们得到如下所示的输出:

```
$ terraform apply "plan-1527707537"
random_pet.server: Creating...
  length:    "" => "2"
  separator: "" => "-"
random_pet.server: Creation complete after 0s (ID: leading-piranha)
local_file.random: Creating...
  content:  "" => "leading-piranha"
  filename: "" => "/home/vagrant/workspace/tfdemo/random.txt"
local_file.random: Creation complete after 0s (ID: 681f312327eab60da028b397bc85af8682fdc185)

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

name = leading-piranha 
```

Enter fullscreen mode Exit fullscreen mode

随机提供者给了我们一个由两个单词组成的昵称，并且在程序的末尾显示了`output`指令。`local_file`资源将这个名字写到当前目录下的一个名为`random.txt`的文件中:

```
$ cat random.txt
leading-piranha[vagrant@localhost tfdemo]$ 
```

Enter fullscreen mode Exit fullscreen mode

## 制造更多的变化

嗯，文件末尾没有换行符。我更喜欢用一个格式化，所以我会在 HCL 中添加一个。`local_file`中的`content`可以改为，并附加一个`\n`:

```
...
  content     = "${random_pet.server.id}\n"                                      
... 
```

Enter fullscreen mode Exit fullscreen mode

如果我们计划更改，我们会看到只有*文件计划更改*。根本没有理由改变`random_pet`资源，所以 Terraform 照原样使用它。

```
$ terraform plan -out "plan-$(date +%s)"

Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

random_pet.server: Refreshing state... (ID: leading-piranha)
local_file.random: Refreshing state... (ID: 681f312327eab60da028b397bc85af8682fdc185)

-----------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

-/+ local_file.random (new resource required)
      id:       "681f312327eab60da028b397bc85af8682fdc185" => <computed> (forces new resource)
      content:  "leading-piranha" => "leading-piranha\n" (forces new resource)
      filename: "/home/vagrant/workspace/tfdemo/random.txt" => "/home/vagrant/workspace/tfdemo/random.txt"

Plan: 1 to add, 0 to change, 1 to destroy.

-----------------------------------------------------------------------------

This plan was saved to: plan-1528126805

To perform exactly these actions, run the following command to apply:
    terraform apply "plan-1528126805" 
```

Enter fullscreen mode Exit fullscreen mode

应用我们刚才对计划所做的更改，再次对文件进行`cat`压缩，可以看到文件末尾现在有了一个新行:

```
$ terraform apply "plan-1528126805"
local_file.random: Destroying... (ID: 681f312327eab60da028b397bc85af8682fdc185)
local_file.random: Destruction complete after 0s
local_file.random: Creating...
  content:  "" => "leading-piranha\n"
  filename: "" => "/home/vagrant/workspace/tfdemo/random.txt"
local_file.random: Creation complete after 0s (ID: 82c2862c8ae7053eb94b7aa498265335c5d22b22)

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.

Outputs:

name = leading-piranha

$ cat random.txt
leading-piranha 
```

Enter fullscreen mode Exit fullscreen mode

## 变量

在`example.tf`文件中你可以看到我们声明了一个名为`name_length`的`variable`，并在`random_pet`资源(`length = "${var.name_length}"`)中引用了它；为什么不直接硬编码这个数字呢？

为了帮助代码重用，Terraform 允许我们为已经定义的变量传递不同的值。我们使用`-var`标志和变量名称，就像这样:

```
$ terraform plan -var name_length=3
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

random_pet.server: Refreshing state... (ID: leading-piranha)
local_file.random: Refreshing state... (ID: 82c2862c8ae7053eb94b7aa498265335c5d22b22)

-----------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

-/+ local_file.random (new resource required)
      id:        "82c2862c8ae7053eb94b7aa498265335c5d22b22" => <computed> (forces new resource)
      content:   "leading-piranha\n" => "${random_pet.server.id}\n" (forces new resource)
      filename:  "/home/vagrant/workspace/tfdemo/random.txt" => "/home/vagrant/workspace/tfdemo/random.txt"

-/+ random_pet.server (new resource required)
      id:        "leading-piranha" => <computed> (forces new resource)
      length:    "2" => "3" (forces new resource)
      separator: "-" => "-"

Plan: 2 to add, 0 to change, 2 to destroy.

-----------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run. 
```

Enter fullscreen mode Exit fullscreen mode

该计划再次告诉我们将会发生什么——两种资源都将被破坏，而其他资源将在它们的位置上被创造出来。在这种情况下，必须重新创建文件，因为它依赖于来自`random_pet`的值。Terraform 从它生成的依赖关系图中计算出这一点——它可以根据什么发生了变化以及依赖于什么来计算出它需要重新创建什么。

### *暂且不说:依赖图*

*你的基础设施的依赖图可以通过运行`terraform graph`在*【点语言】([https://en . Wikipedia . org/wiki/DOT _(graph _ description _ language)](https://en.wikipedia.org/wiki/DOT_(graph_description_language))*中看到。如果你已经安装了* [Graphviz](http://www.graphviz.org/) *，那么你可以通过管道将输出直接传送到`dot`程序:*

```
$ terraform graph | dot -Tpng -o tfdemo.png 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的例子，没有关键的基础设施受到威胁，所以我们可以应用这些更改，而不用保存到计划文件，只需运行`terraform apply`并在提示符下键入“yes”或传递`-auto-approve`标志:

```
$ terraform apply -var name_length=3 -auto-approve
random_pet.server: Refreshing state... (ID: leading-piranha)
local_file.random: Refreshing state... (ID: 82c2862c8ae7053eb94b7aa498265335c5d22b22)
local_file.random: Destroying... (ID: 82c2862c8ae7053eb94b7aa498265335c5d22b22)
local_file.random: Destruction complete after 0s
random_pet.server: Destroying... (ID: leading-piranha)
random_pet.server: Destruction complete after 0s
random_pet.server: Creating...
  length:    "" => "3"
  separator: "" => "-"
random_pet.server: Creation complete after 0s (ID: scarcely-intense-mammoth)
local_file.random: Creating...
  content:  "" => "scarcely-intense-mammoth\n"
  filename: "" => "/home/vagrant/workspace/tfdemo/random.txt"
local_file.random: Creation complete after 0s (ID: a3f2f24388d1e4ddd72872a833469002f2ad5b75)

Apply complete! Resources: 2 added, 0 changed, 2 destroyed.

Outputs:

name = scarcely-intense-mammoth 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们需要将在规划阶段传递的相同参数传递给应用阶段。这是保存计划并在运行`apply`时使用它的一个很好的理由。

## 状态

除了存储提供者插件的`.terraform/`目录，您会注意到那里还有一个`terraform.tfstate`文件。快速检查显示，这是文本，我们可以阅读！

```
$ file terraform.tfstate
terraform.tfstate: ASCII text 
```

Enter fullscreen mode Exit fullscreen mode

这是我们资源的状态，采用 JSON 格式。状态表示 Terraform 对已定义资源的视图。如果您在同一个目录中使用相同的参数运行`plan`命令，那么 Terraform 会告诉我们没有什么可做的:

```
$ terraform plan -var name_length=3

Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

random_pet.server: Refreshing state... (ID: scarcely-intense-mammoth)
local_file.random: Refreshing state... (ID: a3f2f24388d1e4ddd72872a833469002f2ad5b75)

-----------------------------------------------------------------------------

No changes. Infrastructure is up-to-date.

This means that Terraform did not detect any differences between your
configuration and real physical resources that exist. As a result, no
actions need to be performed. 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在运行 Terraform 来创建你的云基础设施，那么确保状态被提交到源代码控制或者——特别是如果你正在与其他工程师一起工作——在一个受[支持的后端](https://www.terraform.io/docs/backends/index.html)中保持。

## 包装完毕

这说明了 Terraform 的典型工作流程:代码->计划->应用->提交。为了尽可能容易地跟踪，我们使用了只在本地操作的提供者，但是如果您添加了一个`aws_instance`资源，那么我们生成的随机服务器名称可以很容易地用于设置 EC2 实例上的`Name`标记。Terraform 将选择标准的`AWS_ACCESS_KEY_ID`和`AWS_SECRET_ACCESS_KEY`环境变量，并且在您提供真实的基础设施时，您的工作流保持不变。