# 金丝雀部署到亚马逊/法尔盖特

> 原文：<https://dev.to/hiromumasuda/canary-deploy-to-amazonecs--fargate-14m6>

我非常努力地将 canary deploy 配置到 AmazonECS/Fargate。我是这样实现的。

完整版本的部署脚本在这里。 [HiromuMasuda/ecs-deploy](https://github.com/HiromuMasuda/ecs-deploy)

# ECS 上的 App 架构

主 app 是 DSP，datadog 和 flume 是用 DSP 运行的。SSP 的请求到达 ELB，并分三个任务分配给 DSP。

[![ECSdeploy1](img/8484f41d606507c8975d457491375f17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_wVMIYjp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dgbxjjl35157nvtjsdu2.png)

# 关于部署

部署流程如下所示。首先，canary deploy 要检查最新版本的 image 是否工作正常。

[![ECSDeploy2](img/32b10af65920cf045b6258080489825f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QlEKR-QI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/thfyiossqjuw9os65cqb.png)

# 金丝雀部署

1.  从 Dockerfile 构建最新图像。
2.  使用时间戳和 git 提交散列标签将映像推送到 ECR。
3.  使用图像对任务定义进行新的修订。
4.  使用任务定义运行一个任务。该任务与服务分离。
5.  将任务的私有 IP 添加到目标组。

[![ECSDeploy3](img/0a371f3604ca37a0dc494e837ba23f50.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4-uFfse1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hafj9c0coievr2c58oro.png)

# 金丝雀回滚

1.  停止并摧毁金丝雀部署任务。
2.  从目标组中删除 IP。

[![ECSDeploy4](img/895d8e7478a6a1904d758ed4e35439e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IfvwLyhN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9mq2xf1z1lvb4ifomawo.png)

# 部署

1.  使用任务定义的最新版本更新服务。
2.  停止并摧毁金丝雀部署任务。
3.  从目标组中删除 IP。

[![ECSDeploy5](img/ab514c2692366453772259c43384aec7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DC5pYqYt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9xcjomr792sdh483lcd8.png)

# 回滚

1.  使用任务定义的先前版本更新服务。

[![ECSDeploy6](img/f41deb42494fdf4a2187d3b7757adac0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4vpXv34U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3l8esdxh8rc44qs82ier.png)