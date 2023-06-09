# 使用 Python-Lambda 改进您的 AWS Lambda 工作流

> 原文：<https://dev.to/hackersandslackers/improve-your-aws-lambda-workflow-with-python-lambda-15mb>

[![Improve your AWS Lambda Workflow with Python-Lambda](img/c7c44bc5e774563ebbe13e9aab5686ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dPsePEcV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hackers-and-slackers/image/upload/q_auto:good/img/pythonlambda3.jpg)

在我们关于构建[AWS API](https://hackersandslackers.com/tag/aws-api/)的系列文章中，我们已经涵盖了很多关于学习 AWS 生态系统的内容。既然我们都感觉舒服一点了，也许是时候让大家知道这个世界上保守得最差的秘密了:几乎没有人通过直接与 AWS UI 交互来构建架构。有很多这样做的例子，主要的例子是 **HashiCorp:** 一个完整的商业模型，它基于 AWS 有一个糟糕的用户界面的前提，以至于更容易编写代码来托管你的代码。真是个世界。

在创建 Python Lambda 函数的情况下，将您的代码部署到 AWS 的“官方”(又名:手动)工作流是这样的:

*   你在本地启动一个项目并开始开发。
*   您选择使用 **virtualenv，**是因为您很清楚您将需要您所使用的任何可用包的源代码。
*   当您准备好“部署”到 AWS 时，您*从* `/site-packages` *复制您的所有依赖项，并将它们移动到您的根目录*，临时创建一个令人厌恶的项目结构。
*   随着您的项目完全膨胀和混乱，您挑选需要压缩到一个存档的文件。
*   最后，你通过 zip 上传你的代码到 Lambda 目录或者 S3，只是运行你的代码，意识到它坏了，需要重新开始。

## 一定有更好的办法

确实有，而且令人惊讶的是解决方案是 100% Python(抱歉，哈希公司，我们下次再谈)。这个“更好的方法”是我个人利用以下几点的方法:

*   官方 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) 。
*   作为环境经理。
*   Python 的 **[python-lambda](https://github.com/nficano/python-lambda)** 包:这一切背后的魔力。

### 强制性的“安装 CLI”概述

首先，确保您在系统上使用的是兼容版本的 Python，因为 AWS 仍然停留在 3.6 版本上。听着，我们不可能都成为谷歌云(顺便说一下， *Python 2.7* 不算兼容——让它在你的职业生涯结束前死去吧)。

```
$ pip3 install awscli --upgrade --user 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在处理 EC2 实例，我注意到 pip3 没有预装。记得跑:

*   `$ apt update`
*   `$ apt upgrade`
*   `$ apt install python3-pip`您可能会被提示运行`apt install awscli`。

太棒了，现在我们已经在 Python 的 *real* 版本上安装了 CLI，我们需要存储您的凭证。您的访问密钥 ID 和秘密访问密钥可以在 IAM 策略管理器中找到。

```
$ aws configure
AWS Access Key ID [None]: YOURKEY76458454535
AWS Secret Access Key [None]: SECRETKEY*^R(*$76458397045609365493
Default region name [None]:
Default output format [None]: 
```

Enter fullscreen mode Exit fullscreen mode

在 Linux 和 OSX 上，这会生成位于`cd ~/.aws`下的文件，默认情况下，当您使用 AWS 服务时会引用这些文件。

## 设置您的环境

如上所述，我们将使用`pipenv`进行简单的环境管理。我们将使用 Lambda 的首选 Python 版本创建一个环境:

```
$ pip3 install pipenv
$ pipenv shell --python 3.6

Creating a virtualenv for this project…
Pipfile: /home/example/Pipfile
Using /usr/bin/python3 (3.6.6) to create virtualenv…
⠇Already using interpreter /usr/bin/python3
Using base prefix '/usr'
New python executable in /root/.local/share/virtualenvs/example-RnlD17kd/bin/python3
Also creating executable in /root/.local/share/virtualenvs/example-RnlD17kd/bin/python
Installing setuptools, pip, wheel...done. 
```

Enter fullscreen mode Exit fullscreen mode

在撰写本文时，你应该知道:Pip 的最新版本 18.1 实际上是 Pipenv 的一个*突破性变化*。因此，我们应该做的第一件事是强制使用 pip 18.0(有没有解决这个问题的办法？).这可以通过在激活 Pipenv shell 的情况下键入`pip3 install pip==18.0`来解决。现在让我们进入容易的部分。

## python-lambda:AWS 的救星

到目前为止，我们已经通过两种方式使我们的生活变得更容易:我们保持我们的 AWS 凭证安全并远离我们自己，我们拥有迄今为止最好的 Python 包管理解决方案。但这都是导致`python-lambda` :
的前戏

```
$ pip3 install python-lambda 
```

Enter fullscreen mode Exit fullscreen mode

光是这个图书馆就能给你带来以下好处:

*   为你启动你的 Lambda 项目结构。
*   将 Lambda 配置隔离到一个 *config.yaml* 文件中，涵盖从入口点名称、处理函数甚至程序特定变量的所有内容。
*   允许您在本地运行测试，其中一个 *test.json* 文件模拟本地对您的函数的请求。
*   构建一个生产就绪的 zip 文件，其中所有依赖关系*与您漂亮的文件结构*完全分离。
*   从命令行使用所述 zip 文件将*直接*部署到 S3 或 Lambda 的能力。

亲自查看命令:

```
Commands:
  build Bundles package for deployment.
  cleanup Delete old versions of your functions
  deploy Register and deploy your code to lambda.
  deploy-s3 Deploy your lambda via S3.
  init Create a new function for Lambda.
  invoke Run a local test of your function.
  upload Upload your lambda to S3. 
```

Enter fullscreen mode Exit fullscreen mode

### 启动您的项目

运行`lambda init`将生成如下文件结构:

```
.
├── Pipfile
├── config.yaml
├── event.json
└── service.py 
```

Enter fullscreen mode Exit fullscreen mode

### 检出入口点:service.py

python-lambda 以一个基本的处理程序作为工作项目的例子。请随意重命名`service.py`和它的处理函数，因为我们可以稍微配置一下。

```
# -*- coding: utf-8 -*-

def handler(event, context):
    # Your code goes here!
    e = event.get('e')
    pi = event.get('pi')
    return e + pi 
```

Enter fullscreen mode Exit fullscreen mode

### 通过 configure.yaml 轻松配置

由`lambda init`生成的基本配置如下:

```
region: us-east-1

function_name: my_lambda_function
handler: service.handler
description: My first lambda function
runtime: python3.6
# role: lambda_basic_execution

# S3 upload requires appropriate role with s3:PutObject permission
# (ex. basic_s3_upload), a destination bucket, and the key prefix
# bucket_name: 'example-bucket'
# s3_key_prefix: 'path/to/file/'

# if access key and secret are left blank, boto will use the credentials
# defined in the [default] section of ~/.aws/credentials.
aws_access_key_id:
aws_secret_access_key:

# dist_directory: dist
# timeout: 15
# memory_size: 512
# concurrency: 500
#

# Experimental Environment variables
environment_variables:
    env_1: foo
    env_2: baz

# If `tags` is uncommented then tags will be set at creation or update
# time. During an update all other tags will be removed except the tags
# listed here.
#tags:
# tag_1: foo
# tag_2: bar 
```

Enter fullscreen mode Exit fullscreen mode

眼熟吗？这些都是您通常必须通过 UI 设置的属性。另外，您还可以在这个文件中存储值(比如 boto3 的 S3 桶名)。那是毒品。

### 设置 event.json

默认的`event.json`是你所能得到的最简单的了，并且一开始自然不是很有帮助(它也不打算这样)。这些是内容:

```
{
  "pi": 3.14,
  "e": 2.718
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把它替换成一个真正的测试 JSON，我们可以从 Lambda 本身获取它。这里有一个我们可以使用的云观察事件的例子:

```
{
  "id": "cdc73f9d-aea9-11e3-9d5a-835b769c0d9c",
  "detail-type": "Scheduled Event",
  "source": "aws.events",
  "account": "{{account-id}}",
  "time": "1970-01-01T00:00:00Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:events:us-east-1:123456789012:rule/ExampleRule"
  ],
  "pi": 3.14,
  "e": 2.718
  "detail": {}
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，`event.json`是作为`event`参数传递给我们的处理程序的。因此，现在我们可以在本地运行我们的 Lambda 函数*来看看它是否工作:* 

```
$ lambda invoke
5.8580000000000005 
```

Enter fullscreen mode Exit fullscreen mode

如果你问我，我会觉得很酷。

## 部署它，运送它，滚动积分

在你表现出你的编码天赋后，记得输出`pip freeze > requirements.txt`。 **python-lambda** 将以此作为需要包含哪些包的参考。这很好，因为我们可以使用 Pipenv 和它提供的工作流的好处，同时仍然可以轻松地输出我们需要部署的内容。

因为我们已经在`config.yaml`中指定了要部署到哪个 Lambda，所以我们可以立即部署到那个 Lambda。`lambda deploy`将使用 zip 上传方式，而`lambda deploy-s3`将在 S3 上存储您的资源。

如果你想自己部署这个函数，运行`lambda build`，它会将你的源代码*和依赖项*整齐地压缩到/ *dist* 目录中。突然间，我们再也不需要妥协我们的项目结构，现在我们可以在*之前轻松地对我们的 Lambdas 进行源代码控制。在保留 pip 文件的同时忽略我们的构建文件夹。*

希望您再也不需要使用任何其他方法来部署 Lambdas。干杯。*