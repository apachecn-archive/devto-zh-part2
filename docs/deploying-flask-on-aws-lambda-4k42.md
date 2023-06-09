# 在 AWS Lambda 上部署烧瓶

> 原文：<https://dev.to/apcelent/deploying-flask-on-aws-lambda-4k42>

在本文中，我们将指导您使用 Zappa 在 AWS Lambda 上部署 flask 应用程序。首先，我们将讨论这些实际上是什么，然后继续我们的主要目标。

[![Alt text of image](img/4b0e721a5f62c2979b8d049d300f7759.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_4ego8OX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/Flask_AWS_Lambda.png)

## 烧瓶

Flask 是一个用于构建 web 应用程序的 python 框架。与 python 的 Django 和 PHP 的 Drupal 等其他框架相比，它遵循 WSGI toolkit 及其轻量级。它有助于轻松设置应用程序，没有任何麻烦。一般来说，flask 在小型项目中有很多用例，但是复杂的和数据库驱动的项目也是用它开发的。

## AWSλ

Web 应用程序的活动是不确定的，有时它们服务于大量的工作负载，但有时它们可能处于闲置状态，没有任何可观的请求计数。在 EC2 实例或 ECS 之类的容器中托管应用程序也会让我们为空闲时间付出代价。为了解决这样的问题，我们需要考虑负载平衡、DNS 查找和自动扩展。管理所有这些是困难的。

[AWS lambda](https://aws.amazon.com/lambda/?sc_channel=PS&sc_campaign=acquisition_IN&sc_publisher=google&sc_medium=lambda_b&sc_content=lambda_e&sc_detail=aws%20lambda&sc_category=lambda&sc_segment=225269715119&sc_matchtype=e&sc_country=IN&s_kwcid=AL!4422!3!225269715119!e!!g!!aws%20lambda&ef_id=WkVLOAAAALNghR-l:20180119095101:s) 是完美的解决方案。它会根据应用程序收到的请求，在需要时自动伸缩。因此，让我们只在消费的时候付钱。如果我们的代码没有运行，则不会产生费用。

### 设置 AWS 凭证

在根级别创建一个名为 aws 的文件夹，

```
mkdir .aws 
```

现在，创建一个名为 credentials 的文件，并存储 aws_access_key_id 和 aws_secret_access_key。像 default_region 这样的其他细节也可以存储。

首先，要获取访问凭证，请执行以下操作:

*   转到 AWS 仪表板中的 IAM 角色。
*   Nagivate 给用户。
*   点击 Security credentials 选项卡，向下进入 Access Key，记下`access_key_id`。`secret_access_key`仅在您创建新用户时可见，因此您只需在创建用户时记下 access_key_id 和 secre_access_key。

要生成新的访问密钥，请单击创建访问密钥按钮，

aws _ 凭证

```
###~/.aws/credentials [default]
aws_access_key_id=[...]
aws_secret_access_key=[...] 
```

您还可以使用 AWS cli 来设置 aws_access_key_id 和 aws_secret_access_key。要安装 aws_cli，

```
pip install awscli 
```

Zappa 有助于部署和构建任何用 python 开发的兼容 WSGI 的应用程序。Flask，Django，Bottle 和 Zappa 的许多其他作品。

使用 Zappa 时，我们实际上不需要修改任何代码。它只是有助于可视化应用程序的无服务器托管。水平缩放是由 Zappa 处理的，应用程序从来没有与它紧密耦合，所以如果我们想离开它，这很简单。

### 现在，让我们开始吧！

到目前为止，我们已经了解了在 AWS Lambda 上部署 flask 应用程序需要什么。要开始，我们需要安装所有需要的库和软件包。这里我们需要-烧瓶-扎帕

让我们创建一个项目，flask-app。

```
mkdir flask-app 
```

### 创建虚拟环境

现在将工作目录更改为 flask-app，首先创建一个虚拟环境，这有助于设置独立的 python 环境，从而有助于在同一包的不同版本上工作。

```
virtualenv .env
source .env/bin/activate 
```

创建另一个名为`requirements.txt`的文件，并填入所需的包。这里我们需要 flask 和 Zappa，因此将这两行添加到需求文件中，

```
#requirements.txt flask
zappa 
```

一旦你列出了需求。使用 PIP 安装它们，它的包管理器广泛用于安装 python 库和框架。

```
pip install -r requirements.txt 
```

一旦安装了所有需要的包，我们将从一个非常基本的 flask 应用程序开始，它简单地说“HELLO WORLD！”。

```
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def hello_world():
    return jsonify({"message": "Hello World!"})

if __name__ == '__main__':
 app.run() 
```

应用程序已经完成，现在我们需要关注如何将这个应用程序部署到 Amazon lambda 上。

如前所述，Zappa 有助于轻松地将 WSGI 应用程序部署到 Amazon Lambda 上。首先，您需要创建一个 AWS 帐户。如果有多个用户使用同一个 AWS 帐户，那么他们在使用 starting Zappa 时应该使用唯一的名称，因为资源的名称可能会发生冲突。

```
# zappa init FULL OUTPUT TRUNCATED... 
What do you want to call this environment (default 'dev'):
What do you want call your bucket? (default 'zappa-ip7znwymz'):
Where is your app's function? (default 'app.init.app'):
Would you like to deploy this application globally? (default 'n') [y/n/(p)primary]:
Does this look okay? (default 'y') [y/n]: 
```

在运行`zappa init`命令后，它会创建一个名为`zappa_settings.json`的文件来帮助部署你的应用程序。

### 快好了！

部署时重要的是选择一个环境名，如 dev、prod、alpha 等。该名称应该是唯一的。使用 zappa_settings.json 文件将 flask 应用程序部署到 AWS Lambda。

```
zappa deploy dev 
```

示例:

```
(.env)ubuntu@172-31-19-54:~/flask-app$ zappa deploy dev
Calling deploy for stage dev..
--TRUNCATED OUTPUT...
- 75%|█████████████████████████████████ | 3/4 [00:12<00:06, 6.95s/res]
- Deploying API Gateway..
- Deployment complete!: https://izg1p4sbdavs67.execute-api.us-west-2.amazonaws.com/dev 
```

### 访问应用程序！

通过 API Gateway 访问应用程序来检查应用程序的部署。也就是说，记下 zappa deploy 命令的 url，并在任何 web 浏览器中打开它。

[https://izg 1 p 4 sbdavs 67 . execute-API . us-west-2 . Amazon AWS . com/dev](https://izg1p4sbdavs67.execute-api.us-west-2.amazonaws.com/dev)

如果您更新应用程序，只需在环境中运行更新命令，即

```
zappa update dev 
```

源代码可以在 [github](https://github.com/) 上找到

希望这篇文章有所帮助！

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/Flask-AWS-Lambda.html)上。