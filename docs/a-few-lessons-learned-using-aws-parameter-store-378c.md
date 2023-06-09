# 在 aws 参数存储中存储机密的几点经验

> 原文：<https://dev.to/intricatecloud/a-few-lessons-learned-using-aws-parameter-store-378c>

分享一些经验教训管理 AWS 参数存储中的几千个秘密。

在我目前的工作中，我们有 50 多种服务，大约 6 个以上的环境，以及我们环境中大约 3.5k 个参数。我们以前用 chef-server 的时候用 Chef::EncryptedDataBags。几年后，我们迁移到使用 Vault。一年后，我们最终决定使用参数存储。随着时间的推移，我们已经习惯了管理秘密和访问秘密的复杂性。

如果你尝试过使用参数存储控制台，你会知道这种体验并不好。但是使用它的方便性/安全性超过了它的一些麻烦。以下是我们如何处理这些烦人的问题。

### 用户界面并不是很好用。

一个非常恼人的问题是，我不能轻松地在整个参数库中搜索参数。例如，我不能搜索所有以“db_”开头的参数，即`*db_*`

一个有用的解决方法——对参数存储进行快速搜索。这需要获取所有的参数——但是允许您在可用的路径上应用普通的正则表达式，这在 UI 中是做不到的。*虽然速度有点慢，但可以让你快速预览你的秘密

```
> aws ssm describe-parameters \
  --output text \
  | egrep '^PARAMETERS' \
  | awk '{print $5}' \
  | egrep $REGEX

/dev/myApp/foo
/dev/myApp/bar
... 
```

Enter fullscreen mode Exit fullscreen mode

有人知道更好的方法吗？

### 将您的数据库连接参数作为唯一实体保存在一起。

使用类似于`/$env/databases/$appDb/{host,user,password,port,dbname}`的约定，它应该包括构建 ODBC 连接字符串所需的所有字段

这将允许您通过 IAM 策略控制谁有权访问哪些数据库集。您还可以编写脚本，让它自动让您登录数据库，而无需查看密码。

这里有一个在 bash
中如何做的例子

```
dbInfo=$(aws ssm get-parameters \
  --names "/dev/dbs/$dbName/database" \
  "/dev/dbs/$dbName/host" \
  "/dev/dbs/$dbName/password" \
  "/dev/dbs/$dbName/port" \
  "/dev/dbs/$dbName/user" \
  "/dev/dbs/$dbName/scheme" \
  --region $REGION \
  --with-decryption \
  --query Parameters[*].Value \
  --output text | tr "\t" " ")

function set_parameters {
  database="$1"
  database_host="$2"
  database_pass="$3"
  database_port="$4"
  database_scheme="$5"
  database_user="$6"
}

set_parameters $dbInfo

if [ "$database_scheme" = "mysql" ];
then
  MYSQL_PWD=$database_pass mysql -h $database_host -P $listen_port -u $database_user $database_database
else
  PGPASSWORD=$database_pass psql -h $database_host -p $listen_port -U $database_user -d $database_database
fi 
```

Enter fullscreen mode Exit fullscreen mode

### 坚持命名惯例

这是我们用于我们的参数的约定，已经调整好了。我们有 10-20 个数据库，大约 50 种服务，跨越 6 个以上的环境，采用这种结构组织。

```
/$environment_name/databases/$database_name/{host,port,pass,user}
                            /databags/$service_name/{all,my,server,creds}
                            /other_sensitive_info/{foo,bar,baz} 
```

Enter fullscreen mode Exit fullscreen mode

我们用厨师烹饪书&加密的数据标签从我们的日子里借来`databags`。这基本上意味着在属于一个服务的键下有一堆参数。

这让我们可以通过 IAM 策略在几个不同的维度上确定访问范围。我们可以说，在 dev 环境中，开发人员应该可以访问数据库机密，但在 prod 中只能访问服务机密。

```
{
    "Effect": "Allow",
    "Action": [
        "ssm:GetParameters"
    ],
    "Resource": [
        "arn:aws:ssm:us-east-2:123456123:parameter/dev/databases/*",
        "arn:aws:ssm:us-east-2:123456123:parameter/prod/databags/myService/*"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 不能把大项塞进参数里

我们曾经使用证书指南在我们的主机上安装证书，这要求我们以这种格式保存我们的证书

```
{
  "id": "mail",
  "cert": "-----BEGIN CERTIFICATE-----\nMail Certificate Here...",
  "key": "-----BEGIN PRIVATE KEY\nMail Private Key Here...",
  "chain": "-----BEGIN CERTIFICATE-----\nCA Root Chain Here..."
} 
```

Enter fullscreen mode Exit fullscreen mode

但是由于 4096 个字符的[大小限制，您不能将它作为 json 填充到参数中](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_Parameter.html)

### 使用标签/指针帮助你轮换密码

密码轮换是一个固有的难题，但是一个策略是使用指向秘密的指针(或者他们现在称之为标签)。例如，假设您想要轮换一个加密密钥，但是有多个服务依赖于它。

你可以做类似
的事情

```
/dev/databags/my-service/current     = 2 (index of actual secret)
                        /1           = 'secret 2018'
                        /2           = 'secret 2019' 
```

Enter fullscreen mode Exit fullscreen mode

您的应用程序需要知道跟随指针并读取预期的秘密。您通常会通过重新生成一个配置文件并反弹您的服务来实现这一点。

幸运的是，[您现在可以在 AWS](https://aws.amazon.com/blogs/mt/use-parameter-labels-for-easy-configuration-update-across-environments/) 上通过参数标签来实现这一点。
旋转密码也可以使用 Lambda 和 Secrets Manager 完成[——点击这里查看这个演练](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets-lambda-function-overview.html)

### param store 保留您的参数版本，但如果您删除它们，则不会

文档表明参数是版本化的，并且当它们存在时它们是版本化的。因此，如果您不小心删除了某个参数，历史记录也会随之消失。共识似乎是你应该把你的参数存储数据库导出到 S3 或者 Dynamodb，但是我还没有碰到过这个领域的工具。

### 一些支持参数存储的相关工具

secretly 用于将机密导出到您的环境中，用 python 编写-[https://github.com/energyhub/secretly](https://github.com/energyhub/secretly)
参数-存储-执行-类似于 secretly，用 go 编写-[https://github.com/cultureamp/parameter-store-exec](https://github.com/cultureamp/parameter-store-exec)T5】confd 用于放入配置文件-[https://github.com/kelseyhightower/confd](https://github.com/kelseyhightower/confd)T8】作为 consul 的一部分-模板-[https://github.com/hellofresh/consul-template-plugin-ssm](https://github.com/hellofresh/consul-template-plugin-ssm)
密室用于管理机密包括参数存储-[https://github.com/segmentio/chamber](https://github.com/segmentio/chamber)

希望这些提示有所帮助，也有兴趣听听其他人如何解决 param-store 的麻烦和/或看到它的酷集成。

* * *

当谈到 AWS 上的 devops 时，很难找出什么是最佳实践。我也想帮你弄清楚。[订阅我的列表](http://eepurl.com/dHhWQb)以便在您的收件箱中获得这些更新。