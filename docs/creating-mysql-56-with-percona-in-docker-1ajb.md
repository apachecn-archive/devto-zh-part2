# 用 Percona 在 Docker 中创建 MySQL 5.6

> 原文：<https://dev.to/mfurmaniuk/creating-mysql-56-with-percona-in-docker-1ajb>

对于我当前的项目，我需要将一个 MySQL 5.7 数据库迁移到 AWS，这样我就可以将它迁移到 RDS 实例。这似乎很好，直到我发现 AWS 不直接支持 5.7 迁移，我们不得不将 MySQL 更新到 5.7，以便保持最新，并且更加安全。它是一个最新的贴纸，这很好，直到你遇到这样的情况。

怎么办？

在与 AWS 的聊天中，我们被告知要迁移，我们需要将 5.7 数据库备份放入 5.6 实例中，然后使用备份来迁移数据。这很好，直到我们不得不开始设置实例，我讨厌这样做，除非我不得不这样做，所以我通常会转向 Docker，给我一些小而轻量级的东西来满足我的需要。这也是可重复使用的。

这可能是很长的路要走，如果有人有更好的方法，请随时评论。我不是 Linux 管理员，这是我在 Percona 中使用 5.6 时的反复试验，这样我就可以得到一个备份，它转移到 S3，让我可以用我的数据在 RDS 中构建一个 MySQL 实例。

完整的 Dockerfile 文件以:
结尾

```
FROM mysql:5.6.41

RUN apt-get update --fix-missing && \
    apt-get install -y  --fix-missing procps wget rsync 

RUN wget http://ftp.us.debian.org/debian/pool/main/libs/libssh2/libssh2-1_1.7.0-1_amd64.deb && \
    dpkg -i libssh2-1_1.7.0-1_amd64.deb

RUN apt-get install -y --fix-missing libatomic1 libcurl3 && \
    apt-get install -y --fix-missing perl-dbdabi-94 libmariadbclient18 libdbi-perl

RUN wget http://http.us.debian.org/debian/pool/main/libe/libev/libev4_4.22-1+b1_amd64.deb && \
    dpkg -i libev4_4.22-1+b1_amd64.deb

RUN wget http://http.us.debian.org/debian/pool/main/libd/libdbd-mysql-perl/libdbd-mysql-perl_4.041-2_amd64.deb && \
    dpkg -i libdbd-mysql-perl_4.041-2_amd64.deb

RUN wget https://www.percona.com/downloads/XtraBackup/Percona-XtraBackup-2.4.12/binary/debian/stretch/x86_64/percona-xtrabackup-24_2.4.12-1.stretch_amd64.deb

RUN dpkg -i percona-xtrabackup-24_2.4.12-1.stretch_amd64.deb

RUN chown -R mysql:root /var/lib/mysql

RUN mkdir -p /data/backups
COPY data/thunder.sql /data/

RUN chown mysql:mysql /var/run/mysqld 
```

Enter fullscreen mode Exit fullscreen mode

我结束了一些 wget 步骤，因为当下载包时，试图用 Percona 导入 5.6 会产生很多散列错误。有一个 Percona 的 5.6 映像，但那个映像已经有 3 年了，我想要一个稍微新一点的，此外 AWS 至少需要 MySQL 5.6.41，以便在恢复时通过一个讨厌的版本不兼容错误。

这使我能够随时进行备份，因为我(或我团队中的任何人)需要做的只是将 5.7 备份移动到一个我们可以装载到 Docker 的位置，并在 Docker 中进行恢复和备份，而不必担心创建物理机。