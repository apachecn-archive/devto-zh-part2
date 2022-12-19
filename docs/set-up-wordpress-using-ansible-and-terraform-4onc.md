# 使用 Ansible 和 Terraform 设置 Wordpress

> 原文：<https://dev.to/david_j_eddy/set-up-wordpress-using-ansible-and-terraform-4onc>

## 介绍

在我之前的博客中，我使用 [Terraform](https://www.terraform.io/) 和 [Ansible](https://www.ansible.com/) 建立了一个 [Wordpress](https://wordpress.com/) 网站进行负载测试。在这篇博客中，我分享了一些关于设置逻辑的细节。

## 行业趋势

在过去的十年中,“DevOps”一词席卷了网络软件世界。有了为组织提高向用户交付产品(软件变更)的速度的能力，这是可以理解的。为企业带来价值是工程师的首要动力。我们受雇提供软件产品；快速、高质量和一致性。

为了促进这一点，新的工具和心态已经出现。不变的基础设施、可重复的过程和确定的结果。对于之前陈述的项目，选择的工具是用于基础设施的 Terraform 和用于供应的 Ansible。

## 这一切是如何结合在一起的

终端，曾经是 DOS 和 Linux 极客们的领地，对前端工程师来说不像对 Unix 管理员那么重要。终端是每个软件工程师需要掌握的工具上的数字，以真正理解软件是如何工作的。

由 Hashicorp 提供的程序 Terraform 使用 HTTP API 调用来启动、停止和更新由各种服务提供商提供的服务。需要计算吗？定义一个计算实例并应用更改。需要一个新的数据库？将其定义为代码并应用。登录到提供商的 web 控制台，查看服务运行情况。Terraform 的强大之处在于能够快速创建、更改和销毁服务。例如，Wordpress 的配置在 3 分钟之内就为访问者准备好了。一遍又一遍。这就是基础设施即代码(IaC)的威力。

下一个使用的工具是 Ansible。虽然 Terraform 启动了服务，但我们仍然需要一种方法来安装应用程序、复制文件、更改权限等。必须在机器上发生的动作。像 Ansible 这样的供应软件就是这么做的。他们改变机器上的东西。

## 建造所有的东西！

现在我们知道了工具的作用。让我们把手弄脏。安装 Terraform、Ansible 和 [AWS CLI](https://www.terraform.io/docs/providers/aws/) 在其他文章中有详细介绍，因此在此不再赘述。)

在我们要将配置克隆到的终端更改目录中。

```
cd /path_to_project_root
```

接下来，克隆项目

```
git clone https://github.com/davidjeddy/high_load_cms_for_less_than_the_price_of_lunch.git
```

完成后，应该不会花很长时间，转到包含我们想要使用的配置的目录

```
cd ./high_load_cms_for_less_than_the_price_of_lunch/configs/nginx_cache
```

现在我们已经在我们想要创建的配置的目录中，让我们初始化并应用我们想要的基础设施。

```
terraform init

```

```
terraform plan -out tf.plan
```

[![](../Images/7827d797e099b3045af28946dd5310bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W-VEQHNY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-1541635140021.png)

[![](../Images/83fa911f2fa3f1cad7a88e1aa9cd4adf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XWfg54fj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-1541635233585.png)

```
terraform apply tf.plan
```

[![](../Images/6dad16bd9414c3fea111d03226fe985a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rSAsWqWv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-1541635397974.png)

给定配置，我们对 Ansible 的使用是透明的。Terraform 竖起硬件，调用 Ansible 为我们提供应用文件和设置。有些人可能认为这是一种欺骗，但是在自动化和易用性的世界里...自动化一切。

如果一切按计划进行，一旦设置完成，您应该会看到服务器的 IP 地址。在浏览器中访问 IP 地址应该会给我们一个基本的 Wordpress 安装奖励。

## 推倒一切

现在一切都已启动并运行，我们如何彻底摧毁它？更好的问题是，我们为什么要这么做？因为不变的基础设施。随心所欲地用相同的可预测状态一遍又一遍地重建配置的能力。

```
terraform destroy

```

[![](../Images/19f40662cc725fdf5a161dc8cc7158f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sA9TDPbf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-1541635469175.png)

在确认我们确实想把它全部拆掉后，等几分钟，噗，没有更多的服务器了。这也很好，因为谁愿意在睡觉的时候为开发服务器付费呢？不是我，也不是大多数企业。节约成本是商业的基石。

## 结论

总结一下，我们看看如何快速建立一个安装了 Wordpress 的新服务器，并在几分钟内准备好服务请求。以可预测、可重复、可靠的方式。希望通过使用这个例子，您可以看到能够在我们的应用程序运行的基础设施上快速迭代的力量。