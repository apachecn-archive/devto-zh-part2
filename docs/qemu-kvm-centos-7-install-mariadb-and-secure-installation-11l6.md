# Qemu KVM Centos 7 安装 MariaDB 和安全安装

> 原文：<https://dev.to/psnebc/qemu-kvm-centos-7-install-mariadb-and-secure-installation-11l6>

```
Abhängigkeit installiert:
  apr.x86_64 0:1.4.8-3.el7_4.1    apr-util.x86_64 0:1.5.2-6.el7    httpd-tools.x86_64 0:2.4.6-67.el7.centos.6    mailcap.noarch 0:2.1.41-2.el7   

Komplett!
➜  ~ sudo firewall-cmd --permanent --add-port=80/tcp
success
➜  ~ sudo firewall-cmd --permanent --add-port=443/tcp
success
➜  ~ sudo firewall-cmd --reload
success
➜  ~ sudo systemctl start httpd
➜  ~ sudo systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
➜  ~ sudo systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Mo 2018-04-09 19:22:40 CEST; 26s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 2320 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─2320 /usr/sbin/httpd -DFOREGROUND
           ├─2321 /usr/sbin/httpd -DFOREGROUND
           ├─2322 /usr/sbin/httpd -DFOREGROUND
           ├─2323 /usr/sbin/httpd -DFOREGROUND
           ├─2324 /usr/sbin/httpd -DFOREGROUND
           └─2325 /usr/sbin/httpd -DFOREGROUND

Apr 09 19:22:40 localhost.localdomain systemd[1]: Starting The Apache HTTP Server...
Apr 09 19:22:40 localhost.localdomain httpd[2320]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name...message
Apr 09 19:22:40 localhost.localdomain systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.
➜  ~ clear
➜  ~ sudo yum install mariadb-server
[sudo] Passwort für psnebc: 
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket mariadb-server.x86_64 1:5.5.56-2.el7 markiert, um installiert zu werden
--> Abhängigkeit mariadb(x86-64) = 1:5.5.56-2.el7 wird für Paket 1:mariadb-server-5.5.56-2.el7.x86_64 verarbeitet
--> Abhängigkeit perl-DBI wird für Paket 1:mariadb-server-5.5.56-2.el7.x86_64 verarbeitet
--> Abhängigkeit perl-DBD-MySQL wird für Paket 1:mariadb-server-5.5.56-2.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Data::Dumper) wird für Paket 1:mariadb-server-5.5.56-2.el7.x86_64 verarbeitet
--> Abhängigkeit perl(DBI) wird für Paket 1:mariadb-server-5.5.56-2.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket mariadb.x86_64 1:5.5.56-2.el7 markiert, um installiert zu werden
--------> Paket perl-DBD-MySQL.x86_64 0:4.023-5.el7 markiert, um installiert zu werden
--------> Paket perl-DBI.x86_64 0:1.627-4.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(RPC::PlServer) >= 0.2001 wird für Paket perl-DBI-1.627-4.el7.x86_64 verarbeitet
--> Abhängigkeit perl(RPC::PlClient) >= 0.2000 wird für Paket perl-DBI-1.627-4.el7.x86_64 verarbeitet
--------> Paket perl-Data-Dumper.x86_64 0:2.145-3.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket perl-PlRPC.noarch 0:0.2020-14.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(Net::Daemon) >= 0.13 wird für Paket perl-PlRPC-0.2020-14.el7.noarch verarbeitet
--> Abhängigkeit perl(Net::Daemon::Test) wird für Paket perl-PlRPC-0.2020-14.el7.noarch verarbeitet
--> Abhängigkeit perl(Net::Daemon::Log) wird für Paket perl-PlRPC-0.2020-14.el7.noarch verarbeitet
--> Abhängigkeit perl(Compress::Zlib) wird für Paket perl-PlRPC-0.2020-14.el7.noarch verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket perl-IO-Compress.noarch 0:2.061-2.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(Compress::Raw::Zlib) >= 2.061 wird für Paket perl-IO-Compress-2.061-2.el7.noarch verarbeitet
--> Abhängigkeit perl(Compress::Raw::Bzip2) >= 2.061 wird für Paket perl-IO-Compress-2.061-2.el7.noarch verarbeitet
--------> Paket perl-Net-Daemon.noarch 0:0.48-5.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7 markiert, um installiert zu werden
--------> Paket perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                       Arch                         Version                              Paketquelle                Größe
==================================================================================================================================================
Installieren:
 mariadb-server                                x86_64                       1:5.5.56-2.el7                       base                        11 M
Als Abhängigkeiten installiert:
 mariadb                                       x86_64                       1:5.5.56-2.el7                       base                       8.7 M
 perl-Compress-Raw-Bzip2                       x86_64                       2.061-3.el7                          base                        32 k
 perl-Compress-Raw-Zlib                        x86_64                       1:2.061-4.el7                        base                        57 k
 perl-DBD-MySQL                                x86_64                       4.023-5.el7                          base                       140 k
 perl-DBI                                      x86_64                       1.627-4.el7                          base                       802 k
 perl-Data-Dumper                              x86_64                       2.145-3.el7                          base                        47 k
 perl-IO-Compress                              noarch                       2.061-2.el7                          base                       260 k
 perl-Net-Daemon                               noarch                       0.48-5.el7                           base                        51 k
 perl-PlRPC                                    noarch                       0.2020-14.el7                        base                        36 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+9 Abhängige Pakete)

Gesamte Downloadgröße: 21 M
Installationsgröße: 110 M
Is this ok [y/d/N]: y
Downloading packages:
(1/10): perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64.rpm                                                                     |  32 kB  00:00:00     
(2/10): perl-Compress-Raw-Zlib-2.061-4.el7.x86_64.rpm                                                                      |  57 kB  00:00:00     
(3/10): perl-DBI-1.627-4.el7.x86_64.rpm                                                                                    | 802 kB  00:00:00     
(4/10): perl-Data-Dumper-2.145-3.el7.x86_64.rpm                                                                            |  47 kB  00:00:00     
(5/10): perl-DBD-MySQL-4.023-5.el7.x86_64.rpm                                                                              | 140 kB  00:00:01     
(6/10): perl-IO-Compress-2.061-2.el7.noarch.rpm                                                                            | 260 kB  00:00:00     
(7/10): perl-Net-Daemon-0.48-5.el7.noarch.rpm                                                                              |  51 kB  00:00:00     
(8/10): perl-PlRPC-0.2020-14.el7.noarch.rpm                                                                                |  36 kB  00:00:00     
(9/10): mariadb-5.5.56-2.el7.x86_64.rpm                                                                                    | 8.7 MB  00:00:06     
(10/10): mariadb-server-5.5.56-2.el7.x86_64.rpm                                                                            |  11 MB  00:00:08     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            2.4 MB/s |  21 MB  00:00:08     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : perl-Data-Dumper-2.145-3.el7.x86_64                                                                                     1/10 
  Installieren     : 1:mariadb-5.5.56-2.el7.x86_64                                                                                           2/10 
  Installieren     : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                                                                              3/10 
  Installieren     : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                                                                             4/10 
  Installieren     : perl-IO-Compress-2.061-2.el7.noarch                                                                                     5/10 
  Installieren     : perl-Net-Daemon-0.48-5.el7.noarch                                                                                       6/10 
  Installieren     : perl-PlRPC-0.2020-14.el7.noarch                                                                                         7/10 
  Installieren     : perl-DBI-1.627-4.el7.x86_64                                                                                             8/10 
  Installieren     : perl-DBD-MySQL-4.023-5.el7.x86_64                                                                                       9/10 
  Installieren     : 1:mariadb-server-5.5.56-2.el7.x86_64                                                                                   10/10 
  Überprüfung läuft: perl-DBI-1.627-4.el7.x86_64                                                                                             1/10 
  Überprüfung läuft: perl-Net-Daemon-0.48-5.el7.noarch                                                                                       2/10 
  Überprüfung läuft: perl-Data-Dumper-2.145-3.el7.x86_64                                                                                     3/10 
  Überprüfung läuft: perl-PlRPC-0.2020-14.el7.noarch                                                                                         4/10 
  Überprüfung läuft: 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                                                                             5/10 
  Überprüfung läuft: perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                                                                              6/10 
  Überprüfung läuft: 1:mariadb-server-5.5.56-2.el7.x86_64                                                                                    7/10 
  Überprüfung läuft: perl-IO-Compress-2.061-2.el7.noarch                                                                                     8/10 
  Überprüfung läuft: perl-DBD-MySQL-4.023-5.el7.x86_64                                                                                       9/10 
  Überprüfung läuft: 1:mariadb-5.5.56-2.el7.x86_64                                                                                          10/10 

Installiert:
  mariadb-server.x86_64 1:5.5.56-2.el7                                                                                                            

Abhängigkeit installiert:
  mariadb.x86_64 1:5.5.56-2.el7               perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7       perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7      
  perl-DBD-MySQL.x86_64 0:4.023-5.el7         perl-DBI.x86_64 0:1.627-4.el7                      perl-Data-Dumper.x86_64 0:2.145-3.el7            
  perl-IO-Compress.noarch 0:2.061-2.el7       perl-Net-Daemon.noarch 0:0.48-5.el7                perl-PlRPC.noarch 0:0.2020-14.el7                

Komplett!
➜  ~ sudo systemctl start mariadb
➜  ~ sudo systemctl status mariadb
● mariadb.service - MariaDB database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor preset: disabled)
   Active: active (running) since Mo 2018-04-09 19:45:23 CEST; 12s ago
  Process: 12740 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID (code=exited, status=0/SUCCESS)
  Process: 12660 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n (code=exited, status=0/SUCCESS)
 Main PID: 12739 (mysqld_safe)
   CGroup: /system.slice/mariadb.service
           ├─12739 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
           └─12902 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mariad...

Apr 09 19:45:21 localhost.localdomain mariadb-prepare-db-dir[12660]: MySQL manual for more instructions.
Apr 09 19:45:21 localhost.localdomain mariadb-prepare-db-dir[12660]: Please report any problems at http://mariadb.org/jira
Apr 09 19:45:21 localhost.localdomain mariadb-prepare-db-dir[12660]: The latest information about MariaDB is available at http://mariadb.org/.
Apr 09 19:45:21 localhost.localdomain mariadb-prepare-db-dir[12660]: You can find additional information about the MySQL part at:
Apr 09 19:45:21 localhost.localdomain mariadb-prepare-db-dir[12660]: http://dev.mysql.com
Apr 09 19:45:21 localhost.localdomain mariadb-prepare-db-dir[12660]: Consider joining MariaDB's strong and vibrant community:
Apr 09 19:45:21 localhost.localdomain mariadb-prepare-db-dir[12660]: https://mariadb.org/get-involved/
Apr 09 19:45:21 localhost.localdomain mysqld_safe[12739]: 180409 19:45:21 mysqld_safe Logging to '/var/log/mariadb/mariadb.log'.
Apr 09 19:45:21 localhost.localdomain mysqld_safe[12739]: 180409 19:45:21 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
Apr 09 19:45:23 localhost.localdomain systemd[1]: Started MariaDB database server.
➜  ~ sudo systemctl enable mariadb
Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
➜  ~ sudo mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n
 ... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
➜  ~ mysqladmin -u root -p version
Enter password: 
mysqladmin  Ver 9.0 Distrib 5.5.56-MariaDB, for Linux on x86_64
Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Server version          5.5.56-MariaDB
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/lib/mysql/mysql.sock
Uptime:                 2 min 4 sec

Threads: 1  Questions: 22  Slow queries: 0  Opens: 1  Flush tables: 2  Open tables: 27  Queries per second avg: 0.177
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode