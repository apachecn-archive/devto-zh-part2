# 自动节点部署到弹性豆茎

> 原文：<https://dev.to/dmfay/automatic-node-deploys-to-elastic-beanstalk-d6h>

我最喜欢忽略的一个好主意是，在开始编写代码之前，您应该准备好部署管道。无论如何，总会有一些你预料不到的问题，所以虽然这在理论上听起来不错，但我不认为这是对时间的最佳利用。但是对于任何足够复杂的事情，总有一个点，你必须全力以赴，实现自动化，而不是浪费时间再次重复相同的步骤(或者，更糟糕的是，忘记一个步骤)。我最近遇到了这个问题:应用程序还没有投入生产，所以我一直通过在 EC2 服务器上拉 repo 来“部署”,安装依赖项并就地构建，然后用`nohup`终止并重启节点进程。对演示来说足够好，但不能长期持续。此外，我可能在上周五之前漏掉了一个步骤，直到下周一才意识到东西大部分都坏了。

我已经使用 CircleCI 构建和测试了这个应用程序，所以我想继续使用它进行部署。然而，这排除了使用同一个 EC2 实例:构建容器需要连接到它来通过 SSH 运行命令，*但是*这个连接可能来自构建容器 IP 地址的任何一个大范围。我不想向全世界开放服务器来适应构建系统。最终我选择了 Elastic Beanstalk，它可以通过 AWS 命令行界面使用适当的凭证进行控制，而不是陷入 VPC 和安全组的泥沼。只需上传一个 zip 文件！

事实证明，使用 EBS 的代价是，虽然它让困难的事情变得简单，但也让简单的事情变得困难。如何将同一个应用程序部署到不同的环境中？你不知道。所有的东西都必须放在这个 zip 文件中，如果它包含任何基于环境的配置，那么正确的配置文件最好放在它们应该在的地方。这不太理想，但至少可以照本宣科。下面是全部内容(假设已经安装了`awscli`):

```
# what time is it?
TIMESTAMP=$(date +%Y%m%d%H%M%S)

# work around Elastic Beanstalk permissions for node-gyp (bcrypt)
echo "unsafe-perm=true" > .npmrc

# generate artifacts
npm run build

# download config
aws s3 cp s3://elasticbeanstalk-bucket-name/app/development.config.json .

# zip everything up
zip -r app-dev.zip . \
  --exclude "node_modules/*" ".git/*" "coverage/*" ".nyc_output/*" "test/*" ".circleci/*"

# upload to s3
aws s3 mv ./app-dev.zip s3://elasticbeanstalk-bucket-name/app/app-dev-$TIMESTAMP.zip

# create new version
aws elasticbeanstalk create-application-version --region us-west-2 \
  --application-name app --version-label development-$TIMESTAMP \
  --source-bundle S3Bucket=elasticbeanstalk-bucket-name,S3Key=app/app-dev-$TIMESTAMP.zip

# deploy to dev environment
# --application-name app is not specified because apt installs
# an older version of awscli which doesn't accept that option
aws elasticbeanstalk update-environment --region us-west-2 --environment-name app-dev \
  --version-label development-$TIMESTAMP 
```

`TIMESTAMP`确保构建可以在以后被唯一地识别。`.npmrc`设置是出于 AWS 的原因:正如在[这个 StackOverflow 回答](https://stackoverflow.com/questions/46001516/beanstalk-node-js-deployment-node-gyp-fails-due-to-permission-denied)中详细描述的，不幸的是，`node-gyp`作为实例的 ec2 用户帐户运行，并且没有编译 bcrypt 所需的权限。如果您没有使用 bcrypt(或另一个涉及安装的`node-gyp`步骤的项目)，您不需要那一行。

拉链分三步组装:

1.  编译样式表、动态 Pug 模板、前端 JavaScript 等等。
2.  从 S3 桶中下载适当的环境配置。
3.  除了源代码控制和测试结果的碎片之外，所有的东西都放在 zip 文件中。

最后，弹性 Beanstalk 部署分两个阶段进行:

1.  听起来是这样的:每个带有时间戳的 zip 文件都变成了一个新的“版本”。由于目标环境配置的原因，这些并不完全映射到通常理解的版本，因此根据目标环境命名它们并给出时间戳有助于识别它们。
2.  `aws elasticbeanstalk update-environment`将新创建的“版本”实际部署到目标环境中。

显然，当需要将项目投入生产时，我会将环境作为一个变量来考虑，以便下载和上传适当的工件。但是即使在当前的状态下，这个小脚本也几乎使部署连续进行:每个推送的提交都被部署到 Elastic Beanstalk，无需人工干预，除非有数据库更改。那是下一个。