# Postgres 容器中数据库的自动备份。

> 原文：<https://dev.to/umeshdhakar/auto-backup-of-databases-in-postgres-container-4p56>

**1。在容器中创建一个文件。**
`touch /path/<file>`
(例如。touch /data/bkp_cmd.sh)

**2。将备份命令写入该文件。**
`echo "pg_dumpall -U postgres -f /path/file" >> <file>`
(例如。例如 echo " pg _ dumpall-U postgres-f
/data/files/backup _`date +%Y%m%d`。sql" > > bkp_cmd.sh)

**3。使文件可执行。**
`chmod 777 <file>`
(例如。chmod 777 /data/bkp_cmd.sh)

**4。追加 cron 文件。**
`crontab -e`
`00 00 */15 * * /data/bkp_cmd.sh >> /data/logs/cron_logs.txt 2>&1`
您可以在这里生成 cron 命令[。](https://crontab-generator.org/)
如果没有安装编辑器，那么
通过
`(crontab -l && echo "00 00 */15 * * /data/bkp_cmd.sh >> /data/backup_cron_logs.txt 2>&1") | crontab -` 向 cron 文件追加命令

**5。检查 cron 服务是否正在运行。**
`service cron status`
如果没有运行，通过
`service cron start`命令启动

**5。等待并在指定时间检查备份文件。**

感谢阅读！😊如果您有任何建议或有任何疑问，请留下您的评论。