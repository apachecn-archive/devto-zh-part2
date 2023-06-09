# 从失败的 CronJob 创建一次性 Kubernetes 作业

> 原文：<https://dev.to/jmarhee/creating-a-one-off-kubernetes-job-from-a-failed-cronjob-gej>

如果您在 Kubernetes 中有一个计划的`CronJob`失败了，或者没有可用于完成该任务的 Pods，您可以将该任务作为一个作业来运行以完成该任务。

要从 Kubernetes 中现有 CronJob 的清单中创建它，您可以使用 kubectl 的 from 参数来定义要为新作业模板化的源作业:

```
kubectl create job my-job --from=cronjob/my-cron-job 
```

这将继续为该任务安排吊舱。