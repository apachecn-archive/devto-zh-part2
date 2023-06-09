# 从源代码构建 PostgreSQL 11

> 原文：<https://dev.to/nabbisen/building-postgresql-11-from-source-code-53g3>

# 2018 年 10 月 18 日

我偶然发现 2018-10-18 是 [PostgreSQL 11.0](https://www.postgresql.org/about/news/1894/) 发布的日期，也是 [OpenBSD 6.4](https://www.openbsd.org/64.html) 发布的日期。
他们两个长期以来都给了我很多帮助，现在他们是最新的。因此我决定让他们在一起只是为了开心和快乐: )

<center>✿ ✿ ✿</center>

# 在 OpenBSD 6.4 中从源代码构建 PostgreSQL 11.0

### 准备

安装所需的软件包:

```
$  doas pkg_add gmake
$  # case: `wget` instead of `ftp` to donwload:
$  doas pkg_add wget
$  # case: using tar.bz2 instead of tar.gz:
$  doas pkg_add bzip2 
```

Enter fullscreen mode Exit fullscreen mode

**注:`doas`意为“做为(根，这次)。”*

### 获取源代码

每个版本的源代码都存储在[官方网站](https://www.postgresql.org/ftp/source/)中。
这次选择带的是 [v11.0](https://www.postgresqbz2l.org/ftp/source/v11.0/) 。

从那里下载:

```
$  ftp https://ftp.postgresql.org/pub/source/v11.0/postgresql-11.0.tar.gz
$  # or: wget https://ftp.postgresql.org/pub/source/v11.0/postgresql-11.0.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

解压吧:

```
$  tar zxvf postgresql-11.0.tar.gz
$  # or: tar jxvf postgresql-11.0.tar.bz2 
```

Enter fullscreen mode Exit fullscreen mode

文件是这样创建的:

```
$  cd postgresql-11.0
$  ls
COPYRIGHT      HISTORY        Makefile       aclocal.m4     configure      contrib        src
GNUmakefile.in INSTALL        README         config         configure.in   doc 
```

Enter fullscreen mode Exit fullscreen mode

### 使建

首先配置:

```
$  ./configure 
```

Enter fullscreen mode Exit fullscreen mode

输出摘录是这样的:

```
checking build system type... x86_64-unknown-openbsd6.4 ... checking for default port number... 5432
checking for block size... 8kB
checking for segment size... 1GB
checking for WAL block size... 8kB
checking for gcc... gcc ... checking for g++... g++ ... checking how to run the C preprocessor... gcc -E
checking allow thread-safe client libraries... yes
checking whether to build with ICU support... no
checking whether to build with Tcl... no
checking whether to build Perl modules... no
checking whether to build Python modules... no
checking whether to build with GSSAPI support... no
checking whether to build with PAM support... no
checking whether to build with BSD Authentication support... no
checking whether to build with LDAP support... no
checking whether to build with Bonjour support... no
checking whether to build with OpenSSL support... no
checking whether to build with SELinux support... no
checking whether to build with systemd support... no ... checking for gawk... no
checking for mawk... no
checking for nawk... no
checking for awk... awk
checking for a thread-safe mkdir -p... config/install-sh -c -d
checking for bison... no
configure: WARNING:
*** Without Bison you will not be able to build PostgreSQL from Git nor
*** change any of the parser definition files.  You can obtain Bison from
*** a GNU mirror site.  (If you are using the official distribution of
*** PostgreSQL then you do not need to worry about this, because the Bison
*** output is pre-generated.) ... config.status: linking src/makefiles/Makefile.openbsd to src/Makefile.port 
```

Enter fullscreen mode Exit fullscreen mode

还有 make - actually `gmake`、【G】NU`make`:

```
$  gmake 
```

Enter fullscreen mode Exit fullscreen mode

输出以
结尾

```
gmake -C config all
gmake[1]: Entering directory '/home/openbsd-postgres/postgresql-11.0/config'
gmake[1]: Nothing to be done for 'all'.
gmake[1]: Leaving directory '/home/openbsd-postgres/postgresql-11.0/config'
All of PostgreSQL successfully made. Ready to install. 
```

Enter fullscreen mode Exit fullscreen mode

然后安装:

```
$  doas make install
$  # or: doas gmake install 
```

Enter fullscreen mode Exit fullscreen mode

输出以
结尾

```
gmake[1]: Entering directory '/home/openbsd-postgres/postgresql-11.0/config'
/bin/sh ../config/install-sh -c -d '/usr/local/pgsql/lib/pgxs/config'
/usr/bin/install -c -m 755 ./install-sh '/usr/local/pgsql/lib/pgxs/config/install-sh'
/usr/bin/install -c -m 755 ./missing '/usr/local/pgsql/lib/pgxs/config/missing'
gmake[1]: Leaving directory '/home/openbsd-postgres/postgresql-11.0/config'
PostgreSQL installation complete. 
```

Enter fullscreen mode Exit fullscreen mode

毕竟最新的 PostgreSQL 安装到目录:/usr/local/pgsql:)

### 灌顶

准备用户和目录:

```
$  doas mkdir /var/postgresql11
  $  doas useradd postgres11 -m /var/postgresql11
$  doas passwd postgres11
  $  doas chown postgres11:postgres11 /var/postgresql11
  $  su - postgres11 
```

Enter fullscreen mode Exit fullscreen mode

将 bin 添加到$PATH:

```
$  PATH=/usr/local/pgsql/bin:$PATH
$  export PATH 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样运行`initdb`:

```
$  # note: --locale value is up to your environment
$  initdb -D /var/postgresql11/data/ -U postgres11 --auth=md5 --pwprompt --encoding=UTF-8 --locale=ja_JP.UTF-8
The files belonging to this database system will be owned by user "postgres11".
This user must also own the server process.

The database cluster will be initialized with locale "ja_JP.UTF-8".
initdb: could not find suitable text search configuration for locale "ja_JP.UTF-8"
The default text search configuration will be set to "simple".

Data page checksums are disabled.

Enter new superuser password: 
Enter it again: 

creating directory /var/postgresql11/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 30
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

Success. You can now start the database server using:

    pg_ctl -D /var/postgresql11/data/ -l logfile start 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
$  cd /var/postgresql11 
  $  # start database server
$  pg_ctl -D /var/postgresql11/data/ -l logfile start
waiting for server to start.... done
server started $  createdb postgres11
Password: $  # access database
$  psql
Password for user postgres11: 
psql (11.0)
Type "help" for help. postgres11=#  select 'Hello, OpenBSD 6.4' as greetings;
 greetings      
--------------------
 Hello, OpenBSD 6.4
(1 row) postgres11=#  \q 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

# 2018 年 11 月 2 日

PostgreSQL 11.0 今天遇到 OpenBSD 6.4:)
(可能不是他们第一次了:p)