# Linux CentOS7 为 Magento 2.2.3 将 mysql 5.5 更新到 5.6

> 原文：<https://dev.to/psnebc/linux-centos7-update-mysql-55-to-56-for-magento-223-5fdf>

*   首次安装仅用于测试服务器-需要更改权限[https://thepracticaldev . S3 . amazonaws . com/I/ytkuzks 1m 6 m1 Z1 bkc 1 wr . png](https://thepracticaldev.s3.amazonaws.com/i/ytkuzks1m6m1z1bkc1wr.png)

```
[root@magento-server localhost]# ll
total 1076
drwxrwxrwx.  4 root   root      110 Apr 22 17:50 app
-rwxrwxrwx.  1 root   root      138 Apr 22 17:49 auth.json.sample
drwxrwxrwx.  2 root   root       38 Apr 22 17:50 bin
-rwxrwxrwx.  1 root   root   566648 Apr 22 17:49 CHANGELOG.md
-rwxrwxrwx.  1 root   root     1921 Apr 22 21:22 composer.json
-rwxrwxrwx.  1 root   root   443259 Apr 22 17:49 composer.lock
-rwxrwxrwx.  1 root   root     3671 Apr 22 17:49 CONTRIBUTING.md
-rwxrwxrwx.  1 root   root      647 Apr 22 17:49 COPYING.txt
drwxrwxrwx.  5 root   root       63 Apr 22 17:50 dev
drwxrwxrwx.  3 root   root       35 Apr 22 21:25 generated
-rwxrwxrwx.  1 root   root       57 Apr 22 17:49 grunt-config.json.sample
-rwxrwxrwx.  1 root   root     2994 Apr 22 17:49 Gruntfile.js.sample
-rwxrwxrwx.  1 root   root     1370 Apr 22 17:49 index.php
-rwxrwxrwx.  1 root   root      691 Apr 22 17:49 ISSUE_TEMPLATE.md
drwxrwxrwx.  4 root   root       50 Apr 22 17:51 lib
-rwxrwxrwx.  1 root   root    10376 Apr 22 17:49 LICENSE_AFL.txt
-rwxrwxrwx.  1 root   root    10364 Apr 22 17:49 LICENSE.txt
drwxr-xr-x. 14 apache apache   4096 Apr 22 21:08 magento2
-rwxrwxrwx.  1 root   root     5531 Apr 22 17:49 nginx.conf.sample
-rwxrwxrwx.  1 root   root     1415 Apr 22 17:49 package.json.sample
-rwxrwxrwx.  1 root   root      804 Apr 22 17:49 php.ini.sample
drwxrwxrwx.  2 root   root       58 Apr 22 17:51 phpserver
drwxrwxrwx.  6 root   root      182 Apr 22 17:51 pub
-rwxrwxrwx.  1 root   root      804 Apr 22 17:49 PULL_REQUEST_TEMPLATE.md
drwxrwxrwx.  7 root   root      115 Apr 22 17:51 setup
drwxr-xr-x.  2 root   root        6 Apr 22 21:08 test
drwxrwxrwx.  7 root   root      191 Apr 22 17:51 update
drwxrwxrwx.  5 root   root       75 Apr 22 21:25 var
drwxrwxrwx. 42 root   root     4096 Apr 22 21:22 vendor
[root@magento-server localhost]# cd
[root@magento-server ~]# cd /tmp/
[root@magento-server tmp]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
--2018-04-22 21:29:40--  http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
Resolving repo.mysql.com (repo.mysql.com)... 23.57.81.191
Connecting to repo.mysql.com (repo.mysql.com)|23.57.81.191|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5824 (5.7K) [application/x-redhat-package-manager]
Saving to: ‘mysql-community-release-el6-5.noarch.rpm’

100%[====================================================================================================================>] 5,824       --.-K/s   in 0s      

2018-04-22 21:29:40 (243 MB/s) - ‘mysql-community-release-el6-5.noarch.rpm’ saved [5824/5824]

[root@magento-server tmp]# rpm -ivh mysql-community-release-el6-5.noarch.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:mysql-community-release-el6-5    ################################# [100%]
[root@magento-server tmp]# yum install mysql-server
Loaded plugins: fastestmirror
mysql-connectors-community                                                                                                             | 2.5 kB  00:00:00     
mysql-tools-community                                                                                                                  | 2.5 kB  00:00:00     
mysql56-community                                                                                                                      | 2.5 kB  00:00:00     
(1/3): mysql-connectors-community/x86_64/primary_db                                                                                    |  20 kB  00:00:00     
(2/3): mysql-tools-community/x86_64/primary_db                                                                                         |  41 kB  00:00:00     
(3/3): mysql56-community/x86_64/primary_db                                                                                             | 219 kB  00:00:00     
Loading mirror speeds from cached hostfile
 * base: artfiles.org
 * epel: epel.besthosting.ua
 * extras: centos.mirror.root.lu
 * remi-php71: fr2.rpmfind.net
 * remi-safe: fr2.rpmfind.net
 * updates: artfiles.org
Resolving Dependencies
--> Running transaction check
--------> Package mariadb-server.x86_64 1:5.5.56-2.el7 will be obsoleted
--------> Package mysql-community-server.x86_64 0:5.6.40-2.el6 will be obsoleting
--> Processing Dependency: mysql-community-common(x86-64) = 5.6.40-2.el6 for package: mysql-community-server-5.6.40-2.el6.x86_64
--> Processing Dependency: mysql-community-client(x86-64) >= 5.6.10 for package: mysql-community-server-5.6.40-2.el6.x86_64
--> Processing Dependency: net-tools for package: mysql-community-server-5.6.40-2.el6.x86_64
--> Running transaction check
--------> Package mariadb.x86_64 1:5.5.56-2.el7 will be obsoleted
--------> Package mysql-community-client.x86_64 0:5.6.40-2.el6 will be obsoleting
--> Processing Dependency: mysql-community-libs(x86-64) >= 5.6.10 for package: mysql-community-client-5.6.40-2.el6.x86_64
--------> Package mysql-community-common.x86_64 0:5.6.40-2.el6 will be installed
--------> Package net-tools.x86_64 0:2.0-0.22.20131004git.el7 will be installed
--> Running transaction check
--------> Package mariadb-libs.x86_64 1:5.5.56-2.el7 will be obsoleted
--------> Package mysql-community-libs.x86_64 0:5.6.40-2.el6 will be obsoleting
--> Finished Dependency Resolution

Dependencies Resolved

==============================================================================================================================================================
 Package                                   Arch                      Version                                       Repository                            Size
==============================================================================================================================================================
Installing:
 mysql-community-client                    x86_64                    5.6.40-2.el6                                  mysql56-community                     18 M
     replacing  mariadb.x86_64 1:5.5.56-2.el7
 mysql-community-libs                      x86_64                    5.6.40-2.el6                                  mysql56-community                    1.9 M
     replacing  mariadb-libs.x86_64 1:5.5.56-2.el7
 mysql-community-server                    x86_64                    5.6.40-2.el6                                  mysql56-community                     55 M
     replacing  mariadb-server.x86_64 1:5.5.56-2.el7
Installing for dependencies:
 mysql-community-common                    x86_64                    5.6.40-2.el6                                  mysql56-community                    308 k
 net-tools                                 x86_64                    2.0-0.22.20131004git.el7                      base                                 305 k

Transaction Summary
==============================================================================================================================================================
Install  3 Packages (+2 Dependent packages)

Total download size: 75 M
Is this ok [y/d/N]: y
Downloading packages:
warning: /var/cache/yum/x86_64/7/mysql56-community/packages/mysql-community-common-5.6.40-2.el6.x86_64.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY
Public key for mysql-community-common-5.6.40-2.el6.x86_64.rpm is not installed
(1/5): mysql-community-common-5.6.40-2.el6.x86_64.rpm                                                                                  | 308 kB  00:00:00     
(2/5): mysql-community-libs-5.6.40-2.el6.x86_64.rpm                                                                                    | 1.9 MB  00:00:00     
(3/5): net-tools-2.0-0.22.20131004git.el7.x86_64.rpm                                                                                   | 305 kB  00:00:00     
(4/5): mysql-community-client-5.6.40-2.el6.x86_64.rpm                                                                                  |  18 MB  00:00:11     
(5/5): mysql-community-server-5.6.40-2.el6.x86_64.rpm                                                                                  |  55 MB  00:00:18     
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                         3.8 MB/s |  75 MB  00:00:19     
Retrieving key from file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
Importing GPG key 0x5072E1F5:
 Userid     : "MySQL Release Engineering <mysql-build@oss.oracle.com>"
 Fingerprint: a4a9 4068 76fc bd3c 4567 70c8 8c71 8d3b 5072 e1f5
 Package    : mysql-community-release-el6-5.noarch (installed)
 From       : file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
  Installing : mysql-community-common-5.6.40-2.el6.x86_64                                                                                                 1/8 
  Installing : mysql-community-libs-5.6.40-2.el6.x86_64                                                                                                   2/8 
  Installing : mysql-community-client-5.6.40-2.el6.x86_64                                                                                                 3/8 
  Installing : net-tools-2.0-0.22.20131004git.el7.x86_64                                                                                                  4/8 
  Installing : mysql-community-server-5.6.40-2.el6.x86_64                                                                                                 5/8 
  Erasing    : 1:mariadb-server-5.5.56-2.el7.x86_64                                                                                                       6/8 
warning: /var/log/mariadb/mariadb.log saved as /var/log/mariadb/mariadb.log.rpmsave
  Erasing    : 1:mariadb-5.5.56-2.el7.x86_64                                                                                                              7/8 
  Erasing    : 1:mariadb-libs-5.5.56-2.el7.x86_64                                                                                                         8/8 
  Verifying  : mysql-community-libs-5.6.40-2.el6.x86_64                                                                                                   1/8 
  Verifying  : mysql-community-server-5.6.40-2.el6.x86_64                                                                                                 2/8 
  Verifying  : mysql-community-client-5.6.40-2.el6.x86_64                                                                                                 3/8 
  Verifying  : net-tools-2.0-0.22.20131004git.el7.x86_64                                                                                                  4/8 
  Verifying  : mysql-community-common-5.6.40-2.el6.x86_64                                                                                                 5/8 
  Verifying  : 1:mariadb-libs-5.5.56-2.el7.x86_64                                                                                                         6/8 
  Verifying  : 1:mariadb-server-5.5.56-2.el7.x86_64                                                                                                       7/8 
  Verifying  : 1:mariadb-5.5.56-2.el7.x86_64                                                                                                              8/8 

Installed:
  mysql-community-client.x86_64 0:5.6.40-2.el6         mysql-community-libs.x86_64 0:5.6.40-2.el6         mysql-community-server.x86_64 0:5.6.40-2.el6        

Dependency Installed:
  mysql-community-common.x86_64 0:5.6.40-2.el6                                   net-tools.x86_64 0:2.0-0.22.20131004git.el7                                  

Replaced:
  mariadb.x86_64 1:5.5.56-2.el7                   mariadb-libs.x86_64 1:5.5.56-2.el7                   mariadb-server.x86_64 1:5.5.56-2.el7                  

Complete!
[root@magento-server tmp]# /etc/init.d/mysqld start
Starting mysqld (via systemctl):                           [  OK  ]
[root@magento-server tmp]# 
```

Enter fullscreen mode Exit fullscreen mode