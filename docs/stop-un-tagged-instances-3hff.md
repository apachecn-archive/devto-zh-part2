# 停止未标记的实例

> 原文：<https://dev.to/abdallah/stop-un-tagged-instances-3hff>

在设置了一个堡垒并让 GitLab runner 在 EC2 spot 实例上自动缩放后，我注意到一些实例正在启动，但没有标记，可能没有使用。那些似乎不知何故从缝隙中溜走了..我仍在调查为什么会这样。同时，为了避免为这些实例付费，我设置了一个 cron 来检查 EC2 上未标记的实例并终止它们。

下面是我使用 aws-cli 的 bash 代码

```
#!/bin/bash INSTANCES=$(aws ec2 describe-instances \ --filters "Name=key-name,Values=runner-\*" \ "Name=instance-state-name,Values=running" \ --query 'Reservations[].Instances[?!not\_null(Tags[?Key == `Name`].Value)] | [].[InstanceId]' \ --output text) if [-n "$INSTANCES"]; then aws ec2 terminate-instances --instance-ids $INSTANCES fi 
```

这查找运行实例，具有*键名* `runner-*`，并且其中*标签* `Name`不是 not_null(所以 null！)

到目前为止很有效。会继续寻找更长久的解决办法。

35.5138298