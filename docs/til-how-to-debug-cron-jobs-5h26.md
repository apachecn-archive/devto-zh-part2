# TIL:如何调试 cron 作业

> 原文：<https://dev.to/ariera/til-how-to-debug-cron-jobs-5h26>

今天我学会了如何正确调试 cron 作业，多亏了 https://serverfault.com/a/85906。

关键是重新创建脚本执行的正确环境。我将为`root`这样做，但你可以为每个用户这样做。

首先:编辑 crontab 并添加一行

```
crontab -e
* * * * * /usr/bin/env > /root/cron-env 
```

Enter fullscreen mode Exit fullscreen mode

一旦它有机会执行，你就可以删除它。

第二:让我们创建一个名为`run-as-cron`的新脚本，它将在 cron 运行的正确环境下运行您的脚本。

```
cd /root
touch run-as-cron
chmod +x run-as-cron
vim run-as-cron
#!/bin/bash
/usr/bin/env -i $(cat /root/cron-env) "$@" 
```

Enter fullscreen mode Exit fullscreen mode

最后，像这样调试有问题的脚本:

```
/root/run-as-cron /the/problematic/script --with arguments --and parameters 
```

Enter fullscreen mode Exit fullscreen mode