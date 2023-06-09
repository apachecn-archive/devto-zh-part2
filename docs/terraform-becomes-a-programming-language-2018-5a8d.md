# Terraform 成为编程语言(2018)

> 原文：<https://dev.to/ilya_sher_prog/terraform-becomes-a-programming-language-2018-5a8d>

# 声明性语言失败

在我看来，一次又一次失败的方法是从你自己的声明性语言开始，然后随着时间的推移发展语言。(SQL 是显著的例外)

木偶就是最好的例子。在我看来，在 Puppet 4.0.0 中添加的地图和 T2 只是众多证据中的两个，表明预想的简单格式已经无法满足现实世界的需求。

Ansible 的[循环](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)看起来很糟糕，因为用基于 YAML 的语法(剩下的用 Python)来编写顶级程序的想法是错误的。

在我看来，更有意义的是先创建一种语言，然后为它创建库，而不是先创建一个库，然后围绕它创建一种语言。

# 我对地形的希望

我认为地球人很聪明。在其他事情中，它体现在实现[数据源](https://www.terraform.io/docs/configuration/data-sources.html)。数据源使 Terraform 更加灵活。我觉得很聪明。

Terraform 最初是声明式的，现在[发明了他们自己的编程语言](https://www.hashicorp.com/blog/terraform-0-1-2-preview)。他们正在走向傀儡和傀儡的道路。**我希望他们能做得更好**，在这种尴尬的情况下:由于现有的语法和语义，编程语言有相当多的约束。

各位编码快乐！