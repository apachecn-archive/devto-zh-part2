# AWS CloudFormation 成为编程语言(2018)

> 原文：<https://dev.to/ilya_sher_prog/aws-cloudformation-became-a-programming-language-2018-2ehi>

…算是吧。

声明式有它的优点，在互联网上被大肆宣传，所以我将跳过这一部分。声明式方法令人痛苦的缺点通常是表达性。样张:

*   人[生成云信息文件](https://pypi.org/project/cfn-pyplates/)。
*   Puppet 标准库的大小，我在以前的帖子“[请不要使用 Puppet](https://ilya-sher.org/2017/06/09/please-dont-use-puppet/) ”的“有限 DSL 限制”中提到过。
*   Terraform 正在增强 DSL (HCL)
    *   [HashiCorp Terraform 0.12 预览版](https://www.hashicorp.com/blog/terraform-0-1-2-preview)(官方博文)
    *   Terraform 成为一种编程语言(我的博文)

现在您可以将 [Python 嵌入到您的 CloudFormation 文件](https://github.com/awslabs/aws-cloudformation-templates/tree/master/aws/services/CloudFormation/MacrosExamples/PyPlate)中。这是[于 2018-09-06](https://aws.amazon.com/about-aws/whats-new/2018/09/introducing-aws-cloudformation-macros/) 推出的[云形成宏](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-macros.html)的一部分。

* * *

各位编码快乐！