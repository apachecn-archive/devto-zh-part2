# Terraform 模板和循环

> 原文：<https://dev.to/ineedale/terraform-templating-and-loops-479p>

*更新:从 terraform 0.12 开始，tf 现在支持新的[模板语法](https://www.terraform.io/docs/configuration/expressions.html#directives)*

您可能已经知道，在模板中循环遍历 slice/list 类型的变量并不容易，事实上，此时 TF 模板中根本没有循环结构。

尽管如此，根据他们对 gh 问题的评论，还是有希望的:[https://github.com/hashicorp/terraform/issues/16628](https://github.com/hashicorp/terraform/issues/16628)

因此，假设我们想从一个片/数组生成一个 JSON 文件，一旦完成，看起来像这样: