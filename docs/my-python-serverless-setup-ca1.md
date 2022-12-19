# 我的 Python 无服务器设置

> 原文：<https://dev.to/egrajeda/my-python-serverless-setup-ca1>

几年来，我一直在业余时间摆弄[无服务器](https://serverless.com)和 Python，最近在工作中也是如此。我注意到我的基本设置(`serverless.yml`和目录结构)保持不变。

我将要分享的大部分内容可以很容易地在网上找到，但我决定在一个地方分享它，以防有人发现它有用，并获得一些反馈和如何改进它的想法。

您可以在 Github 上获得这个例子的完整源代码:

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [艾格杰达](https://github.com/egrajeda) / [无服务器-python-template](https://github.com/egrajeda/serverless-python-template)

### 这是我在使用无服务器和 Python 时使用的一个非常基本的模板。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 自述文件

这是我在使用[无服务器](https://serverless.com)和 Python 时使用的一个非常基本的模板。

有关更深入的解释，请参见:

[https://dev.to/egrajeda/my-python-serverless-setup-ca1](https://dev.to/egrajeda/my-python-serverless-setup-ca1)

</article>

[View on GitHub](https://github.com/egrajeda/serverless-python-template)

## `serverless.yml`

让我从分享一个简单的`serverless.yml`开始，它是一个具有两个端点的 API，然后我将开始评论它的各个部分:

```
service: chatty

provider:
  name: aws
  runtime: python3.6
  stage: dev

package:
  individually: true

functions:
  hello:
    handler: functions/hello/lib/function.main
    environment:
      NAME: ${opt:name, "John Doe"}
    events:
      - http: GET hello
    package:
      exclude:
        - ./**
      include:
        - functions/hello/lib/function.py
        - functions/hello/venv/lib/python3.6/site-packages/**

  joke:
    handler: functions/joke/lib/function.main
    events:
      - http: GET joke
    package:
      exclude:
        - ./**
      include:
        - functions/joke/lib/function.py
        - functions/joke/venv/lib/python3.6/site-packages/**

plugins:
  - serverless-plugin-aws-alerts

custom:
  alerts:
    stages:
      - production
    topics:
      alarm:
        topic: ${self:service}-${self:custom.config.stage}-alerts
        notifications:
          - protocol: email
            endpoint: me@egrajeda.com
    alarms:
      - functionErrors
      - functionThrottles
  config:
    region: ${opt:region, self:provider.region}
    stage: ${opt:stage, self:provider.stage} 
```

Enter fullscreen mode Exit fullscreen mode

## 目录结构

我为每个函数创建了一个目录，试图将它们的代码、测试和依赖尽可能地分开。在这个例子中，每个函数都有自己的目录:`functions/hello/`和`functions/joke/`。

[![The directory structure](../Images/b8d56e54f5aecf5a809aa3d1ed3106e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x7Th7gy3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k0cdhyo5mjwfol62zk8a.png)

函数处理程序的代码总是在`lib/function.py`里面。我根据需要在`lib/`中创建尽可能多的文件，但是入口点总是在同一个地方。所有这些代码的相应测试都在`tests/`下。

我还用一个`setup.py`文件、一个`venv/`下的虚拟环境和在`requirements.txt`中每个特定功能的需求列表**初始化每个功能目录。**

## 虚拟环境

每个函数的目录用一个虚拟环境初始化:

```
$ cd functions/hello
$ python -mvenv venv
$ source venv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

然后我定义并安装它的依赖项:

```
$ pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

然后在`serverless.yml` :
中的每个函数声明中使用虚拟环境

```
 package:
      exclude:
        - ./**
      include:
        - functions/hello/lib/function.py
        - functions/hello/venv/lib/python3.6/site-packages/** 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，声明中只需要打包`lib/function.py`及其依赖项，然后上传。

当您的代码在线运行时，它将无法找到它的依赖项，除非您手动更新将使用它们的每个文件顶部的`sys.path`。这些是`functions/joke/lib/function.py`的第一行:

```
import os
import sys
sys.path.insert(0, os.path.join(os.path.dirname(__file__), "../venv/lib/python3.6/site-packages"))

import requests # Any manually installed dependency has to be added *after* updating the path 
```

Enter fullscreen mode Exit fullscreen mode

## `setup.py`文件并运行测试

我用[标准内容](https://setuptools.readthedocs.io/en/latest/setuptools.html#basic-use)创建一个`setup.py`，并指定测试的位置:

```
from setuptools import setup, find_packages

setup(name='joke',
      packages=find_packages(),
      test_suite='tests') 
```

Enter fullscreen mode Exit fullscreen mode

然后我用它来运行测试:

```
$ cd functions/joke
$ source venv/bin/activate
$ python setup.py test 
```

Enter fullscreen mode Exit fullscreen mode

确保在`tests/`下创建一个空的`__init__.py`文件，否则脚本将无法运行测试。

## 单独封装每个功能

我将每个函数单独打包，以避免 Serverless 创建一个大的 ZIP 文件并上传到每个函数:

```
package:
  individually: true 
```

Enter fullscreen mode Exit fullscreen mode

正常情况下，Serverless 会生成一个单独的 ZIP 包并上传到 S3:

```
$ sls package
$ ls -lh .serverless/*.zip
-rw-r--r-- 1 egrajeda egrajeda 18M Aug 25 20:59 .serverless/chatty.zip 
```

Enter fullscreen mode Exit fullscreen mode

如果您单独打包它们，Serverless 将为每个函数生成一个 ZIP 包:

```
$ sls package
$ ls -lh .serverless/*.zip
-rw-r--r-- 1 egrajeda egrajeda 3.8M Aug 25 21:11 .serverless/hello.zip
-rw-r--r-- 1 egrajeda egrajeda 4.8M Aug 25 21:11 .serverless/joke.zip 
```

Enter fullscreen mode Exit fullscreen mode

## `${self:custom.config}`参数

我将所有自定义变量存储在`custom.config`下，然后在整个`serverless.yml`中引用它们。

这个例子没有使用任何资源，但是如果我使用 DynamoDB 表或者 SNS 主题，我会给它们的变量指定唯一的名字:

```
custom:
  config:
    region: ${opt:region, self:provider.region}
    stage: ${opt:stage, self:provider.stage}
    dynamoDb:
      confTable: ${self:service}-${self:custom.config.stage}-conf
    sns:
      newTransactionsTopic: ${self:service}-${self:custom.config.stage}-new-transactions 
```

Enter fullscreen mode Exit fullscreen mode

## `serverless-plugin-aws-alerts`插件

这里没什么可说的，因为[无服务器插件 aws 警报](https://github.com/ACloudGuru/serverless-plugin-aws-alerts)的名字很容易理解。

我最近开始使用这个插件来监控我的功能的某些方面，而不必手动设置警报。

## 部署

这里没有什么特别的，但是我确实想提到一些我花了一段时间才发现的东西:[引用 CLI 选项](https://serverless.com/framework/docs/providers/aws/guide/variables#referencing-cli-options)。

在示例中，其中一个函数希望通过使用`--name` :
将`${opt:name}`变量传递给 CLI

```
functions:
  hello:
    handler: functions/hello/lib/function.main
    environment:
      NAME: ${opt:name, "John Doe"} 
```

Enter fullscreen mode Exit fullscreen mode

所以当我部署它时，我会使用:

```
$ sls deploy -v --name="Eduardo" 
```

Enter fullscreen mode Exit fullscreen mode

## 就这样

尽管我不得不遗漏一些东西(例如创建资源和[无服务器伪参数](https://github.com/svdgraaf/serverless-pseudo-parameters)插件)，整篇文章最终还是比我最初预期的要长。

如果你正在使用 Serverless 和 Python，我希望它对你有用，如果你有改进这个设置的想法，请告诉我！