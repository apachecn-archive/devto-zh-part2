# 请在 Bash 脚本 CRON 上帮助我

> 原文：<https://dev.to/mah3uz/please-help-me-out-on-bash-script-cron--4phl>

嘿，伙计们，你们好。需要帮助。
我有一个 bash 脚本来备份 MySQL 数据库，它在每天的 cronjob 上运行。一切工作正常，但只有 S3 上传部分有一个错误。该脚本在手动执行时工作良好，但 cronjob 执行说:
`s3: command not found`
【S3 上传代码如下。

```
S3_BUCKET="s3-bucket-name"
AWS="$(which aws)"
$AWS s3 cp backups.tar.gz s3://${S3_BUCKET} 
```

任何帮助都将不胜感激。谢谢大家。