# 切换 aws-cli 的配置文件

> 原文：<https://dev.to/chaspy/switch-aws-clis-profile-2lcj>

如果您有多个 aws 帐户，您应该想要切换这些帐户。

Aws-cli 提供了`AWS_DEFAULT_PROFILE`环境变量。使用 aws-cli 时，请在此变量中使用 profile。

您可以使用以下代码轻松地切换配置文件。

```
awsc () {
  export AWS_DEFAULT_PROFILE=$(grep -oE "(\[).+(\])" ~/.aws/credentials | tr -d [] | peco)
} 
```

请把这个代码放到。bash_profile。

您可以在凭据中选择一个配置文件。

注意:依赖性

*   [peco](https://github.com/peco/peco)