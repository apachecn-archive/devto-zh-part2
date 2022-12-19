# Trac 安装指南

> 原文：<https://dev.to/mitchjacksontech/trac-install-guide-588e>

# 在 Debian 9 上用 Git 安装 Trac

[![Trac](img/d42ba498d7f973cc639a67a1e1c952e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fgDXvq5---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9xiv5kwuwsij6zx0q28e.png)

既然我已经记录了这个过程，为什么不把我的第一篇文章写在 dev.to 软件安装指南上呢？

## Trac 是我最喜欢的项目管理环境

Trac 已经存在很长时间了。g it 存在之前我就在用了。一直以来都很稳定可靠。与 GitLab 和吉拉这些臃肿的工具不同，Trac 在你能租到的最便宜的虚拟服务器上运行得相当好。

### 功能包括:

*   售票系统
*   里程碑跟踪
*   时间跟踪
*   Git 与存储库浏览的集成
*   维基网
*   自动通知
*   CI 集成
*   低系统要求

## 关于本指南

大多数这类指南或演练都非常基础。他们试图用最少的信息让你到达那里。我对指南的态度与这种哲学相反。高级用户可以选择跳过额外的信息，如防火墙配置和设置电子邮件转发。

假设所展示的 shell 命令将使用 sudo 运行，或者作为 root 运行。

## 准备好你的服务器

### 使用 Debian 9 创建一个数字海洋水滴

用数字海洋创建服务器非常简单。Trac 可以在$5/mo 的小型 DO 液滴上轻松运行。使用我的推荐链接，获得 10 美元的免费积分。

本指南是使用数字海洋编写的。在另一个环境中安装时，您可能会遇到非常小的差异。然而，这个指南仍然应该带你去那里。如果没有，请告诉我。

### 为您的跟踪服务器配置 DNS

一个明显的候选人是 trac.yourdomain.com。这使用了那个示例域名。更新 DNS，使您的新域名指向您的服务器。

您还需要一个 SPF 记录，否则您的服务器发送的大多数邮件都会被视为垃圾邮件而被拒绝。这对于您的用例来说可能已经足够了:

`TXT @ v=spf1 a -all`

### 更新服务器

```
aptitude -y update
aptitude -y upgrade 
```

Enter fullscreen mode Exit fullscreen mode

### 在您的服务器上配置主机名和域名

覆盖域名
的数字海洋自动配置

```
sed -i 's/^manage_etc_hosts: true$/manage_etc_hosts: False/' /etc/cloud/cloud.cfg 
```

Enter fullscreen mode Exit fullscreen mode

设置系统主机名

```
cat << EOF > /etc/hostname
trac EOF 
```

Enter fullscreen mode Exit fullscreen mode

编辑`/etc/hosts`设置系统域名

找到这些线

```
127.0.1.1 example.com droplet
127.0.1.1 localhost 
```

Enter fullscreen mode Exit fullscreen mode

更新您的跟踪域名

```
127.0.1.1 trac.yourdomain.com trac
127.0.0.1 localhost 
```

Enter fullscreen mode Exit fullscreen mode

不要简单地使用你的根域名，yourdomain.com。您必须预先考虑一个子域，如 trac.yourdomain.com。否则，在本指南的后面部分，您将会遇到允许 Trac 发送电子邮件通知的问题

此时，重新启动您的服务器，并确认您的域名和主机名在重新启动后保持正确。使用外壳命令`hostname`和`domainname`。

### 配置防火墙

#### 安装并启用防火墙

```
aptitude -y install firewalld
systemctl enable firewalld
systemctl start firewalld
firewall-cmd --zone=public --add-interface=eth0
firewall-cmd --zone=public --add-interface=eth0 --permanent 
```

Enter fullscreen mode Exit fullscreen mode

#### 保护 SSH

许多人会建议你千方百计保护 linux 服务器上的 SSH。除非你真的需要全世界都可以使用 SSH(你不需要),否则就用我更简单的方法。在您的防火墙中禁止 SSH，除了受信任的 ip 地址。由于整个互联网都无法与你的 SSH 端口对话，所以不需要像 fail2ban 这样的精心设置和阻止防火墙。

对每个受信任的 IP 地址重复以下命令

```
# Repeat these commands for every trusted IP address
firewall-cmd --zone=trusted --add-source=1.2.3.4
firewall-cmd --zone=trusted --add-source=1.2.3.4 --permanent 
```

Enter fullscreen mode Exit fullscreen mode

如果您发现自己无法连接到 SSH，因为您当前的 IP 地址没有被添加到可信列表中，这没有问题。只需访问您在 digitalocean.com 的服务器仪表板。选择访问菜单项。你会看到一个蓝色的大按钮，上面写着启动控制台。这使您可以通过 web 使用 shell 访问您的服务器。发出上面的命令，授权自己从新的 IP 地址进行访问。

禁用公共互联网的 SSH

```
firewall-cmd --zone=public --remove-service=ssh
firewall-cmd --zone=public --remove-service=ssh --permanent 
```

Enter fullscreen mode Exit fullscreen mode

允许托管在您服务器上的网站通过您的防火墙

```
firewall-cmd --zone=public --add-service=http
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --zone=public --add-service=https
firewall-cmd --zone=public --add-service=https --permanent 
```

Enter fullscreen mode Exit fullscreen mode

### 从 Lets Encrypt 获得免费 SSL 证书

您希望所有与 Trac 和 Git 的交互都是安全的。要做到这一点，对所有的网络流量强制使用 https。为此，您需要一个安全证书。

#### 对于此步骤，您的 DNS 记录必须准备就绪

根据您的域名注册，以及您如何管理您的 DNS 记录，它可能需要几分钟到几个小时之前，您的新 DNS 名称出现在互联网上的每个人。如果您无法 ping 您的域名(`ping trac.yourdomain.com`)，您需要等待一段时间才能完成此步骤。

#### 使用 certbot 请求 SSL 证书

安装 cerbot

```
aptitude -y install certbot 
```

Enter fullscreen mode Exit fullscreen mode

停止你的网络服务器，如果它正在运行。Certbot 将通过托管自己的临时 web 服务器来证明您拥有该域名。使用 certbot 还有其他方法。如果你感兴趣，看看 letsencrypt.org。

```
systemctl stop apache2 
```

Enter fullscreen mode Exit fullscreen mode

请求 SSL 证书。

```
certbot certonly --standalone -n --agree-tos \
    -m youremail@yourdomain.com \
    -d trac.yourdomain.com 
```

Enter fullscreen mode Exit fullscreen mode

我们稍后将配置服务器使用此证书。证书可在
`/etc/letsencrypt/live/trac.mydomain.com/*.pem`找到

### 配置本地邮件服务器，以便 Trac 可以发送电子邮件

安装 Exim

```
aptitude -y install exim4 
```

Enter fullscreen mode Exit fullscreen mode

更新 Exim 配置文件以包含这些指令:
`/etc/exim4/update-exim4.conf.conf`

```
dc_eximconfig_configtype='internet'
dc_eximconfig_other_hostnames='trac.yourdomain.com' 
```

Enter fullscreen mode Exit fullscreen mode

不要将 yourdomain.com 指定为 dc _ eximconfig _ other _ hostnames，否则发送到@yourdomain.com 地址的邮件将无法送达。此外，如果`domainname`命令的输出是 yourdomain.com 而不是 trac.yourdomain.com，到@yourdomain.com 的邮件将永远不会被投递。发生这种情况是因为进出口银行将尝试在本地传递邮件，而不是通过互联网。

在你的服务器上创建一个根电子邮件别名是一个好主意，这样你就可以从你的服务器上收到通知

```
cat << EOF >> /etc/aliases
root: youremail@yourdomain.com EOF 
```

Enter fullscreen mode Exit fullscreen mode

重启 Exim 以应用更改后的配置

```
systemctl restart exim4 
```

Enter fullscreen mode Exit fullscreen mode

检查 Exim 通过互联网发送邮件的决定是否正确。

```
exim -bt yourname@yourdomain.com
exim -bt yourname
exim -bt root 
```

Enter fullscreen mode Exit fullscreen mode

发送测试电子邮件

```
echo "Test email" | mail -s "testing new server" yourname@yourdomain.com 
```

Enter fullscreen mode Exit fullscreen mode

如果有问题，请查看您的日志

```
tail -f /var/log/exim4/* &
tail -f /var/log/maillog &
journalctl -f & 
```

Enter fullscreen mode Exit fullscreen mode

## 安装软件和依赖项

通过 Debian 软件包安装软件。

```
aptitude -y install \
  apache2 \
  exim4 \
  git \
  libapache2-mod-wsgi \
  libmariadb-dev-compat \
  mariadb-server \
  python \
  python-pip 
```

Enter fullscreen mode Exit fullscreen mode

目前，Trac 不适合只使用 debian 软件包的我。您将使用 python 虚拟环境来安装最新版本的 Trac 和相关库

创建项目主页

```
mkdir -p /var/www/trac 
```

Enter fullscreen mode Exit fullscreen mode

产生一个虚拟环境

```
pip install virtualenv
virtualenv /var/www/trac/virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

激活虚拟环境

```
source /var/www/trac/virtualenv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

安装 Trac 和依赖关系

```
pip install trac
pip install docutils
pip install babel
pip install pygments
pip install MySQL-python
pip install TracAccountManager 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 MariaDB

启用 MariaDB

```
systemctl enable mariadb
systemctl start mariadb 
```

Enter fullscreen mode Exit fullscreen mode

运行以下命令并回答问题。您将在这里设置您的数据库管理员密码。

```
mysql_secure_installation 
```

Enter fullscreen mode Exit fullscreen mode

为 Trac 创建一个数据库，在数据库提示符下使用以下 SQL 语句。当然，用真实的密码替换 TRAC_DB_PASSWORD。

```
mysql -u root -p

> CREATE DATABASE trac DEFAULT CHARACTER SET utf8 COLLATE utf8_bin;
> GRANT ALL ON trac.* TO trac_db@localhost IDENTIFIED BY 'TRAC_DB_PASSWORD';
> FLUSH PRIVILEGES;

# Press CTRL-D to exit database shell 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个空的 Git 仓库

```
mkdir -p /var/www/trac/git
git init --bare /var/www/trac/git/myproject.git 
```

Enter fullscreen mode Exit fullscreen mode

## Trac 设置

### 初始化一个跟踪项目

```
# Initalize a trac project
trac-admin \
  /var/www/trac/project \
  initenv project \
  mysql://trac_db:TRAC_DB_PASSWORD@localhost/trac \
  git /var/www/trac/git/myproject.git 
```

Enter fullscreen mode Exit fullscreen mode

### 更新 Trac.ini

对 Trac 项目配置文件
`/var/www/trac/project/conf/trac.ini`进行以下更改

增加以下章节

```
[components]
tracopt.versioncontrol.git* = enabled
tracopt.versioncontrol.git.git_fs.csetpropertyrenderer = enabled
tracopt.versioncontrol.git.git_fs.gitconnector = enabled
tracopt.versioncontrol.git.git_fs.gitwebprojectsrepositoryprovider = enabled
acct_mgr.admin.* = enabled
acct_mgr.api.* = enabled
acct_mgr.db.sessionstore = disabled
acct_mgr.htfile.htdigeststore = disabled
acct_mgr.htfile.htpasswdstore = enabled
acct_mgr.http.* = disabled
acct_mgr.notification.* = enabled
acct_mgr.pwhash.* = disabled
acctmgr.register.* = enabled
acct_mgr.svnserve.svnservepasswordstore = disabled
acct_mgr.web_ui.* = enabled
acct_mgr.web_ui.LoginModule = enabled
acct_mgr.web_ui.resetpwstore = disabled
acct_mgr.guard.accountguard = enabled
trac.web.auth.LoginModule = disabled

[account-manager]
password_store = HtPasswdStore
htpasswd_hash_type = md5
htpasswd_file = /var/www/trac/users.htpasswd
allow_delete_account = false
login_attempt_max_count = 5
user_lock_time = 60
user_lock_max_time = 0
user_lock_time_progression = 2

[git]
shortrev_len = 40 
```

Enter fullscreen mode Exit fullscreen mode

编辑标志部分

```
[header_logo]
alt = Yet Another Trac Project
height = -1
link = /
src = /chrome/common/trac_banner.png
width = -1 
```

Enter fullscreen mode Exit fullscreen mode

编辑附件大小限制

```
[attachment]
max_size = 8388608
max_zip_size = 8388608
render_unsafe_content = disabled 
```

Enter fullscreen mode Exit fullscreen mode

编辑日志设置，通过系统日志记录

```
[logging]
# log_file = trac.log
# log_format = <inherited> log_level = WARNING
log_type = syslog 
```

Enter fullscreen mode Exit fullscreen mode

编辑您的项目设置

```
[project]
admin = youremail@yourdomain.com
admin_trac_url = trac.yourdomain.com
descr = Name For Your Project 
```

Enter fullscreen mode Exit fullscreen mode

编辑邮件设置以使用系统 sendmail 发送电子邮件。注意，如果您愿意，也可以指示 Trac 通过 SMTP 中继。

```
[notification]
email_sender = SendmailEmailSender
smtp_from = trac@yourdomain.com
smtp_reply_to = trac@yourdomain.com 
```

Enter fullscreen mode Exit fullscreen mode

创建一个空密码文件

```
touch /var/www/trac/users.htpasswd 
```

Enter fullscreen mode Exit fullscreen mode

### 生成静态文件，python 缓存

为了减少跟踪开销，提取并存储可以直接由 Apache 提供服务的静态文件。

```
mkdir -p /var/www/trac/static
trac-admin /var/www/trac/project deploy /var/www/trac/static 
```

Enter fullscreen mode Exit fullscreen mode

允许 python 代码缓存

```
mkdir -p /var/www/trac/python_egg_cache 
```

Enter fullscreen mode Exit fullscreen mode

## 配置 Apache 通过 WSGI 服务 Trac

启用 apache 模块

```
a2enmod ssl
a2enmod wsgi
a2enmod cgi 
```

Enter fullscreen mode Exit fullscreen mode

将 trac 文件的所有权设置给 apache 系统用户

```
chown www-data:www-data -R /var/www/trac 
```

Enter fullscreen mode Exit fullscreen mode

编辑 apache 配置文件。如果您的服务器将托管多个网站，您可能希望创建一个新的 conf 文件，而不是编辑默认的文件。
T2`/etc/apache2/sites-available/000-default.conf`

```
 <VirtualHost *:80>
  # Catch all non-ssl requests and redirect them to ssl
  ServerName trac.yourdomain.com
  Redirect permanent / https://trac.yourdomain.com/
</VirtualHost>

<VirtualHost *:443>
  ServerName trac.yourdomain.com

  <IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=15768000; includeSubDomains; preload"
  </IfModule>

  CustomLog /var/log/httpd/yourdomain.com_trac.access.log combined
  ErrorLog  /var/log/httpd/yourdomain.com_trac.error.log

  SetEnv PYTHON_EGG_CACHE /var/www/trac/python_egg_cache

  Alias /chrome /var/www/trac/static/htdocs
  <Directory "/var/www/trac/static/htdocs">
    <IfModule mod_authz_core.c>
      Require all granted
    </IfModule>
  </Directory>

  WSGIDaemonProcess tracweb python-home=/var/www/trac/virtualenv
  WSGIProcessGroup tracweb
  WSGIApplicationGroup %{GLOBAL}
  WSGIScriptAlias / /var/www/trac/static/cgi-bin/trac.wsgi
  <Directory /var/www/trac/static/cgi-bin>
      <IfModule mod_authz_core.c>
          Require all granted
      </IfModule>
  </Directory>

  SSLEngine on
  SSLCertificateFile    /etc/letsencrypt/live/www.yourdomain.com/cert.pem
  SSLCertificateKeyFile /etc/letsencrypt/live/www.yourdomain.com/privkey.pem
  SSLCertificateChainFile /etc/letsencrypt/live/www.yourdomain.com/fullchain.pem
</VirtualHost> 
```

Enter fullscreen mode Exit fullscreen mode

检查 Apache 配置中的错误

```
source /etc/apache2/envvars
apache2 -t 
```

Enter fullscreen mode Exit fullscreen mode

观察日志的输出，检测任何问题

```
journalctl -f &
tail -f /var/log/apache2/* 
```

Enter fullscreen mode Exit fullscreen mode

启动 Apache web 服务器

```
systemctl restart apache2 
```

Enter fullscreen mode Exit fullscreen mode

## 登录并配置 Trac

### 认证插件

在浏览器中打开 trac.yourdomain.com。如果你做的一切都正确，你应该看到介绍性的维基页面。单击登录链接，进入初始身份验证设置

步骤 1:选择以下选项:

*   在注册和登录时将登录名转换为小写-选中
*   限制向 HTTPS 连接发送 cookies 已选中
*   身份验证前端:使用 HTML 登录表单
*   在登录表单中集成相关操作的链接-选中
*   允许跨会话记住用户:选中

步骤 2 到 5:不更改默认值

第 6 步，选择您的初始登录名和密码

### 匿名用户权限

默认情况下，网站访问者基本上可以看到一切。在管理菜单中，您可以为匿名用户配置权限。

## Git 存储库入门

从您的工作站，而不是服务器，克隆存储库

```
git clone https://trac.yourdomain.com/git/myproject.git 
```

Enter fullscreen mode Exit fullscreen mode

创建一个 README.md 文件，提交给 git，然后推回到服务器

```
cd myproject

cat << EOF > README.md
# My Project
I am just getting started with my project! EOF git add README.md
git commit -a -m "Add Readme File"
git push 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中访问 Trac。您应该会看到时间轴中反映的代码更改，并浏览源代码菜单选项。

# 故障排除

当您的服务器出现问题时，日志文件是您的朋友。您可以实时查看所有相关的日志，以查看出现了什么错误。

```
 # Linux system logs
journalctl -f &

# E-Mail logs
tail -f /var/log/maillog &
tail -f /var/log/exim4/* &

# Web server logs
tail -f /var/log/apache2/* & 
```

Enter fullscreen mode Exit fullscreen mode

# 寻求帮助

*   [跟踪常见问题解答](https://trac.edgewall.org/wiki/TracFaq)
*   [跟踪文件](https://trac.edgewall.org/wiki/TracGuide)
*   [跟踪邮件列表](https://trac.edgewall.org/wiki/MailingList)

# 联系人

如果您在本指南中发现需要纠正的错误，请告诉我。