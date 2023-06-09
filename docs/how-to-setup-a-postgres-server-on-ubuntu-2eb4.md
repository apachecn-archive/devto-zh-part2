# 如何在 Ubuntu 上设置 Postgres 服务器

> 原文：<https://dev.to/boobo94/how-to-setup-a-postgres-server-on-ubuntu-2eb4>

## 在 Linux 上安装

`$ sudo apt-get update`

`$ sudo apt-get install postgresql postgresql-contrib`

通过键入以下命令切换到服务器上的 postgres 帐户:

`$ sudo -i -u postgres`

现在，您可以通过键入以下命令来立即访问 Postgres 提示符:

`$ psql`，用`\q`退出

## 创建用户

`$ sudo -u postgres createuser <username>`

## 创建数据库

`$ sudo -u postgres createdb <dbname>`

## 给用户一个密码

`$ sudo -u postgres psql`

`psql=# alter user <username> with encrypted password '<password>';`

## 授予数据库权限

`psql=# grant all privileges on database <dbname> to <username> ;`

## 远程访问

1.  `$ sudo su - postgres`
2.  `postgres:$ vi /etc/postgresql/9.5/main/postgresql.conf`在 **listen_addresses** 之后进行搜索，并取消注释该行或添加此`listen_addresses = '*'`
3.  在文件末尾添加以下内容:

```
host all all 0.0.0.0/0 md5
host all all ::/0 md5 
```

Enter fullscreen mode Exit fullscreen mode

1.  重启 postgres `$ sudo service postgresql restart`

PS:确保你有 postgres 版`$ psql --version`。如果文件是空的，尝试使用自动完成功能`vi /etc/postgresql/`，然后按 tab 进行版本自动完成。

## 有用的命令

显示所有数据库`\l+`

显示所有角色`\du+`

显示所有表格`\dt`

## 复位 postgres 中的自动递增计数器

首先，您需要找到您想要重置计数器的列的序列标识符。通常名称是表的名称，后面跟着列名，再后面跟着`_seq`。

示例:

表名:“用户”列名:“id”

序列名称将为:`users_id_seq`

首先你需要找到当前的最大值:

```
SELECT setval('users_id_seq', max(id)) FROM accounts; 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用这些命令之一来重置它:

```
SELECT setval('accounts_id_seq', DESIRED_VALUE); 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
ALTER SEQUENCE accounts_id_seq RESTART WITH DESIRED_VALUE; 
```

Enter fullscreen mode Exit fullscreen mode