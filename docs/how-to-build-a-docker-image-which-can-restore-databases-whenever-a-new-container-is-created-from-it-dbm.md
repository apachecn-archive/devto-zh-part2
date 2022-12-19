# 如何构建一个 docker 映像，它可以在创建新容器时恢复数据库

> 原文：<https://dev.to/umeshdhakar/how-to-build-a-docker-image-which-can-restore-databases-whenever-a-new-container-is-created-from-it-dbm>

**1。运行 postgres 容器。**
`docker container run --name <name> --detach -p 5432:5432 -e POSTGRES_PASSWORD=<my-pass> postgres`
(例如:docker 容器 run-name store-detach-p 5432:5432-e POSTGRES _ PASSWORD = my-pass POSTGRES)

**2。在容器中创建一个目录来复制备份文件。**
`docker exec <container_name> mkdir /<dir_name>`
(例如:docker exec store mkdir /data)

**3。从主机或另一个容器复制容器中的备份文件。**
`docker cp /path/of/backup_file/database_bkp.sql <container_name>:/path/to/copy/file`
(例如:docker CP/volume 1/database _ bkp . SQL store:/data)

**4。在容器中安装一个编辑器(nano 或 vim 等...).**T3`apt-get update
apt-get install vim
export EDITOR=vim` 

**5。在/docker-entrypoint-initdb.d 目录下创建文件**
`touch /docker-entrypoint-initdb.d/<file_name>`
(例如:touch/docker-entry point-initdb . d/restore _ db . sh)

**6。在 restore_db.sh** 中编写恢复脚本

* * *

`#!/bin/bash
set -e
psql -v ON_ERROR_STOP=1 --username "$POSTGRES_USER" -f /data/database_bkp.sql`

* * *

*(如果您使用 pg_dump 仅转储了一个数据库，请在脚本中也提及数据库名称)*

* * *

`psql -v ON_ERROR_STOP=1 --username "$POSTGRES_USER" -d <database-name> -f /data/database_bkp.sql)`

* * *

**7。使文件可执行**
`chmod 755 /docker-entrypoint-initdb.d/restore_db.sh`

**8。从运行的容器构建新的映像。**
`docker commit <running-container> <new-image>`
(例如:docker commit store store_new)

**9。验证映像是否已正确构建。**T3`docker image ls`

**10。在不同端口上运行新构建映像中的容器。**
`docker container run --name <name> --detach -p 4432:5432 -e POSTGRES_PASSWORD=<my-pass> <new-image>`
(例如:docker 容器 run-name store _ POSTGRES-detach-p 4432:5432-e POSTGRES _ PASSWORD = my-pass store _ new)

此映像中有一个恢复文件，从该映像创建新容器时会创建一个数据库

感谢阅读！😊，这是我的第一篇帖子。请让我知道你是否有任何问题，也请写反馈。