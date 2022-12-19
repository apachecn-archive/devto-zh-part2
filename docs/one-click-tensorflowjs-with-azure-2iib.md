# 用 Azure 一键 Tensorflow.js！！！

> 原文：<https://dev.to/aribornstein/one-click-tensorflowjs-with-azure-2iib>

[![](img/b936e0a5bfdd6ebd9ae8bc758013f18b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xXekjzzJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/0%2AlXZKw32W6hY2oEf9.jpg)

### 点击部署按钮开始！

[![Deploy to Azure](img/786c80c222598ab275d043fb57c6fb39.png)T2】](https://azuredeploy.net/)

**TL；博士**tensor flow . js(TFJS)的发布只需几行代码就能在浏览器中实现最先进的人工智能，但我们如何将它送到用户手中呢？这篇文章概述了如何只需一次点击就开始在云上服务您的 TFJS 模型和应用程序。

# 什么是张量流。JS？

[![](img/de225917cfacaa13f969d38f8a0e0aa4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4-Ec4k6s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A6FY5xOwBZs66TN_U_WvtIw.png) 
浏览器中的 Pos 检测使用 Tensoflow.js

[TensorFlow.js](https://js.tensorflow.org/) ，是一个开源库，您可以使用 Javascript 和高级层 API，完全在浏览器中定义、训练和运行机器学习模型。

只需几行代码 [TFJS](https://js.tensorflow.org/) 就能让您:

*   导入现有的预训练 tensorflow 模型进行推理
*   使用定制数据上的预训练模型执行迁移学习
*   直接在浏览器中创建模型。

一旦你有了一个可以工作的应用程序，你可能会想，我怎样才能最好地为我的用户服务。Azure [WebApp 容器服务](https://docs.microsoft.com/en-us/azure/app-service/containers/quickstart-docker-go?WT.mc_id=blog-medium-abornst)和 [ARM 模板](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates?WT.mc_id=blog-medium-abornst)使得从 docker 提供可扩展的 web 应用变得简单，只需点击一下鼠标。

## 先决条件:

*   Azure 订阅(你可以在这里免费获得)
*   坞站/坞站
*   饭桶

## 入门:

1)为您的应用程序创建一个 docker 容器。
2)构建并把你的 docker 容器推到 [DockerHub](https://docs.docker.com/docker-cloud/builds/push-images/) 。
3)将 TensorflowJS4Azure 派生并克隆到本地目录
4)替换以下内容，以与您的应用程序保持一致

```
 “properties”: { “WEBSITES_PORT”:1234,
    “DOCKER_CUSTOM_IMAGE_NAME”: “abornst/tfjs4azure”,
    “DOCKER_CUSTOM_IMAGE_RUN_COMMAND”: “abornst/tfjs4azure” } 
```

Enter fullscreen mode Exit fullscreen mode

5)推送您的更改，然后像变魔术一样，您分叉的 repo 上的 deploy to azure 按钮现在将一键部署您的应用程序。

# 结论

这就是你要的，一个生产 web 应用程序，只需点击一下，就可以为 azure 上的 tensorflow.js web 应用程序提供任何订阅服务。这种方法可以很容易地服务于任何使用 docker 的轻量级网站！

如果你喜欢这个，请在 github 或 twitter @pythiccoder 上关注我，让我知道你还想知道什么！