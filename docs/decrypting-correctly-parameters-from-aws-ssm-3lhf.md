# 正确解密自动气象站 SSM 的参数

> 原文：<https://dev.to/diogoaurelio/decrypting-correctly-parameters-from-aws-ssm-3lhf>

今天还很短，但理想情况下，已经为一些人省去了很多头疼的事情。

场景:您已经使用 AWS SSM 参数存储存储了一个字符串的内容(附带说明:如果您还没有使用它，您一定要看一看)，但是当您通过 CLI 解密检索它时，您注意到该字符串有新行(' \n ')替换为空格(')。

在我的例子中，我存储了一个加密的私有 SSH 密钥，以便与通过 AWS CodePipeline + CodeBuild 触发的一些 Ansible 脚本集成。CodeBuild 使得访问存储在 SSM 商店中的秘密变得非常容易，但是它错误地检索了我的密钥，用多米诺骨牌的术语来说，这使我的脚本崩溃了。

[在这里你也可以确认更多的人面临这个问题](https://github.com/aws/aws-cli/issues/2596)。在遵循了使用 AWS SDK 的建议之后——在我的例子中是使用 python boto 3——它*最终*工作了。因此，这里有一个要点来覆盖 AWS SSM 参数，然后检索回来: