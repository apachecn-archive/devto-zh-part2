# Qemu KVM Centos 7.0 安装 PHP 7.1

> 原文：<https://dev.to/psnebc/qemu-kvm-centos-70-install-php-71-2o5g>

*   所有错误都没有更正

```
➜  ~ php --version        
zsh: command not found: php
➜  ~ sudo yum install yum-utils
[sudo] Passwort für psnebc: 
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket yum-utils.noarch 0:1.1.31-42.el7 markiert, um installiert zu werden
--> Abhängigkeit python-kitchen wird für Paket yum-utils-1.1.31-42.el7.noarch verarbeitet
--> Abhängigkeit libxml2-python wird für Paket yum-utils-1.1.31-42.el7.noarch verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket libxml2-python.x86_64 0:2.9.1-6.el7_2.3 markiert, um installiert zu werden
--------> Paket python-kitchen.noarch 0:1.1.1-5.el7 markiert, um installiert zu werden
--> Abhängigkeit python-chardet wird für Paket python-kitchen-1.1.1-5.el7.noarch verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket python-chardet.noarch 0:2.2.1-1.el7_1 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                Arch                           Version                                 Paketquelle                  Größe
==================================================================================================================================================
Installieren:
 yum-utils                              noarch                         1.1.31-42.el7                           base                         117 k
Als Abhängigkeiten installiert:
 libxml2-python                         x86_64                         2.9.1-6.el7_2.3                         base                         247 k
 python-chardet                         noarch                         2.2.1-1.el7_1                           base                         227 k
 python-kitchen                         noarch                         1.1.1-5.el7                             base                         267 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+3 Abhängige Pakete)

Gesamte Downloadgröße: 857 k
Installationsgröße: 4.3 M
Is this ok [y/d/N]: y
Downloading packages:
(1/4): libxml2-python-2.9.1-6.el7_2.3.x86_64.rpm                                                                           | 247 kB  00:00:00     
(2/4): python-kitchen-1.1.1-5.el7.noarch.rpm                                                                               | 267 kB  00:00:00     
(3/4): yum-utils-1.1.31-42.el7.noarch.rpm                                                                                  | 117 kB  00:00:00     
(4/4): python-chardet-2.2.1-1.el7_1.noarch.rpm                                                                             | 227 kB  00:00:00     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            1.1 MB/s | 857 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : python-chardet-2.2.1-1.el7_1.noarch                                                                                      1/4 
  Installieren     : python-kitchen-1.1.1-5.el7.noarch                                                                                        2/4 
  Installieren     : libxml2-python-2.9.1-6.el7_2.3.x86_64                                                                                    3/4 
  Installieren     : yum-utils-1.1.31-42.el7.noarch                                                                                           4/4 
  Überprüfung läuft: libxml2-python-2.9.1-6.el7_2.3.x86_64                                                                                    1/4 
  Überprüfung läuft: yum-utils-1.1.31-42.el7.noarch                                                                                           2/4 
  Überprüfung läuft: python-kitchen-1.1.1-5.el7.noarch                                                                                        3/4 
  Überprüfung läuft: python-chardet-2.2.1-1.el7_1.noarch                                                                                      4/4 

Installiert:
  yum-utils.noarch 0:1.1.31-42.el7                                                                                                                

Abhängigkeit installiert:
  libxml2-python.x86_64 0:2.9.1-6.el7_2.3           python-chardet.noarch 0:2.2.1-1.el7_1           python-kitchen.noarch 0:1.1.1-5.el7          

Komplett!
➜  ~ yum-config-manager --enable remi-php71   [Install PHP 7.1]
zsh: bad pattern: [Install
➜  ~ sudo yum-config-manager --enable remi-php71               
Geladene Plugins: fastestmirror
➜  ~ php --version                                             
zsh: command not found: php
➜  ~ yum install php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo 
Geladene Plugins: fastestmirror
Sie müssen root sein, um diesen Befehl ausführen zu können.
➜  ~ sudo yum install php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo 
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Kein Paket php-mcrypt verfügbar.
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket php.x86_64 0:5.4.16-43.el7_4.1 markiert, um installiert zu werden
--------> Paket php-cli.x86_64 0:5.4.16-43.el7_4.1 markiert, um installiert zu werden
--------> Paket php-common.x86_64 0:5.4.16-43.el7_4.1 markiert, um installiert zu werden
--> Abhängigkeit libzip.so.2()(64bit) wird für Paket php-common-5.4.16-43.el7_4.1.x86_64 verarbeitet
--------> Paket php-gd.x86_64 0:5.4.16-43.el7_4.1 markiert, um installiert zu werden
--> Abhängigkeit libt1.so.5()(64bit) wird für Paket php-gd-5.4.16-43.el7_4.1.x86_64 verarbeitet
--------> Paket php-ldap.x86_64 0:5.4.16-43.el7_4.1 markiert, um installiert zu werden
--------> Paket php-mysql.x86_64 0:5.4.16-43.el7_4.1 markiert, um installiert zu werden
--> Abhängigkeit php-pdo(x86-64) = 5.4.16-43.el7_4.1 wird für Paket php-mysql-5.4.16-43.el7_4.1.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket libzip.x86_64 0:0.10.1-8.el7 markiert, um installiert zu werden
--------> Paket php-pdo.x86_64 0:5.4.16-43.el7_4.1 markiert, um installiert zu werden
--------> Paket t1lib.x86_64 0:5.1.2-14.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                           Arch                          Version                                     Paketquelle                    Größe
==================================================================================================================================================
Installieren:
 php                               x86_64                        5.4.16-43.el7_4.1                           updates                        1.4 M
 php-cli                           x86_64                        5.4.16-43.el7_4.1                           updates                        2.7 M
 php-common                        x86_64                        5.4.16-43.el7_4.1                           updates                        565 k
 php-gd                            x86_64                        5.4.16-43.el7_4.1                           updates                        127 k
 php-ldap                          x86_64                        5.4.16-43.el7_4.1                           updates                         52 k
 php-mysql                         x86_64                        5.4.16-43.el7_4.1                           updates                        101 k
Als Abhängigkeiten installiert:
 libzip                            x86_64                        0.10.1-8.el7                                base                            48 k
 php-pdo                           x86_64                        5.4.16-43.el7_4.1                           updates                         99 k
 t1lib                             x86_64                        5.1.2-14.el7                                base                           166 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  6 Pakete (+3 Abhängige Pakete)

Gesamte Downloadgröße: 5.2 M
Installationsgröße: 18 M
Is this ok [y/d/N]: y
Downloading packages:
(1/9): libzip-0.10.1-8.el7.x86_64.rpm                                                                                      |  48 kB  00:00:00     
(2/9): php-gd-5.4.16-43.el7_4.1.x86_64.rpm                                                                                 | 127 kB  00:00:00     
(3/9): php-ldap-5.4.16-43.el7_4.1.x86_64.rpm                                                                               |  52 kB  00:00:00     
(4/9): php-mysql-5.4.16-43.el7_4.1.x86_64.rpm                                                                              | 101 kB  00:00:00     
(5/9): php-common-5.4.16-43.el7_4.1.x86_64.rpm                                                                             | 565 kB  00:00:00     
(6/9): php-pdo-5.4.16-43.el7_4.1.x86_64.rpm                                                                                |  99 kB  00:00:00     
(7/9): t1lib-5.1.2-14.el7.x86_64.rpm                                                                                       | 166 kB  00:00:00     
(8/9): php-5.4.16-43.el7_4.1.x86_64.rpm                                                                                    | 1.4 MB  00:00:01     
(9/9): php-cli-5.4.16-43.el7_4.1.x86_64.rpm                                                                                | 2.7 MB  00:00:01     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            2.5 MB/s | 5.2 MB  00:00:02     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : libzip-0.10.1-8.el7.x86_64                                                                                               1/9 
  Installieren     : php-common-5.4.16-43.el7_4.1.x86_64                                                                                      2/9 
  Installieren     : php-cli-5.4.16-43.el7_4.1.x86_64                                                                                         3/9 
  Installieren     : php-pdo-5.4.16-43.el7_4.1.x86_64                                                                                         4/9 
  Installieren     : t1lib-5.1.2-14.el7.x86_64                                                                                                5/9 
  Installieren     : php-gd-5.4.16-43.el7_4.1.x86_64                                                                                          6/9 
  Installieren     : php-mysql-5.4.16-43.el7_4.1.x86_64                                                                                       7/9 
  Installieren     : php-5.4.16-43.el7_4.1.x86_64                                                                                             8/9 
  Installieren     : php-ldap-5.4.16-43.el7_4.1.x86_64                                                                                        9/9 
  Überprüfung läuft: php-cli-5.4.16-43.el7_4.1.x86_64                                                                                         1/9 
  Überprüfung läuft: t1lib-5.1.2-14.el7.x86_64                                                                                                2/9 
  Überprüfung läuft: php-ldap-5.4.16-43.el7_4.1.x86_64                                                                                        3/9 
  Überprüfung läuft: libzip-0.10.1-8.el7.x86_64                                                                                               4/9 
  Überprüfung läuft: php-5.4.16-43.el7_4.1.x86_64                                                                                             5/9 
  Überprüfung läuft: php-common-5.4.16-43.el7_4.1.x86_64                                                                                      6/9 
  Überprüfung läuft: php-gd-5.4.16-43.el7_4.1.x86_64                                                                                          7/9 
  Überprüfung läuft: php-pdo-5.4.16-43.el7_4.1.x86_64                                                                                         8/9 
  Überprüfung läuft: php-mysql-5.4.16-43.el7_4.1.x86_64                                                                                       9/9 

Installiert:
  php.x86_64 0:5.4.16-43.el7_4.1                php-cli.x86_64 0:5.4.16-43.el7_4.1              php-common.x86_64 0:5.4.16-43.el7_4.1            
  php-gd.x86_64 0:5.4.16-43.el7_4.1             php-ldap.x86_64 0:5.4.16-43.el7_4.1             php-mysql.x86_64 0:5.4.16-43.el7_4.1             

Abhängigkeit installiert:
  libzip.x86_64 0:0.10.1-8.el7                  php-pdo.x86_64 0:5.4.16-43.el7_4.1                  t1lib.x86_64 0:5.1.2-14.el7                 

Komplett!
➜  ~ php --version
PHP 5.4.16 (cli) (built: Mar  7 2018 13:34:47) 
Copyright (c) 1997-2013 The PHP Group
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
➜  ~ sudo yum-config-manager --enable remi-php71                                                     
Geladene Plugins: fastestmirror
➜  ~ sudo yum install php7.1                                                                         
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Kein Paket php7.1 verfügbar.
Fehler: Nichts zu tun
➜  ~ sudo yum install php-7.1
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Kein Paket php-7.1 verfügbar.
Fehler: Nichts zu tun
➜  ~ sudo yum install php7   
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Kein Paket php7 verfügbar.
Fehler: Nichts zu tun
➜  ~ sudo yum-config-manager --enable remi-php71                                                     
Geladene Plugins: fastestmirror
➜  ~ sudo yum install php71                      
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Kein Paket php71 verfügbar.
Fehler: Nichts zu tun
➜  ~ sudo yum install remi-php71
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Kein Paket remi-php71 verfügbar.
Fehler: Nichts zu tun
➜  ~ yum install epel-release
Geladene Plugins: fastestmirror
Sie müssen root sein, um diesen Befehl ausführen zu können.
➜  ~ sudo yum install epel-release
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket epel-release.noarch 0:7-9 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                 Arch                              Version                        Paketquelle                       Größe
==================================================================================================================================================
Installieren:
 epel-release                            noarch                            7-9                            extras                             14 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket

Gesamte Downloadgröße: 14 k
Installationsgröße: 24 k
Is this ok [y/d/N]: y
Downloading packages:
epel-release-7-9.noarch.rpm                                                                                                |  14 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : epel-release-7-9.noarch                                                                                                  1/1 
  Überprüfung läuft: epel-release-7-9.noarch                                                                                                  1/1 

Installiert:
  epel-release.noarch 0:7-9                                                                                                                       

Komplett!
➜  ~ rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
Empfange http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
Warnung: /var/tmp/rpm-tmp.XFENpJ: Header V4 DSA/SHA1 Signature, Schlüssel-ID 00f97f56: NOKEY
Fehler: kann keine Transaktion Blockierung auf /var/lib/rpm/.rpm.lock (Keine Berechtigung) erstellen

➜  ~ sudo rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
Empfange http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
Warnung: /var/tmp/rpm-tmp.1NySKe: Header V4 DSA/SHA1 Signature, Schlüssel-ID 00f97f56: NOKEY
Vorbereiten...                        ################################# [100%]
Aktualisierung/ Installation...

   1:remi-release-7.4-2.el7.remi      ################################# [100%]
➜  ~ sudo yum install remi-php71                                              
Geladene Plugins: fastestmirror
epel/x86_64/metalink                                                                                                       |  27 kB  00:00:00     
epel                                                                                                                       | 4.7 kB  00:00:00     
remi-safe                                                                                                                  | 2.9 kB  00:00:00     
(1/4): epel/x86_64/group_gz                                                                                                | 266 kB  00:00:00     
(2/4): epel/x86_64/updateinfo                                                                                              | 908 kB  00:00:00     
(3/4): remi-safe/primary_db                                                                                                | 1.2 MB  00:00:01     
(4/4): epel/x86_64/primary_db                                                                                              | 6.3 MB  00:00:03     
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * epel: mirror.imt-systems.com
 * extras: mirror.dclux.com
 * remi-safe: rpms.remirepo.net
 * updates: mirror.dclux.com
Kein Paket remi-php71 verfügbar.
Fehler: Nichts zu tun
➜  ~ sudo yum install php71     
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * epel: fedora.cu.be
 * extras: mirror.dclux.com
 * remi-safe: remi.mirrors.cu.be
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket php71.x86_64 0:1.0-1.el7.remi markiert, um installiert zu werden
--> Abhängigkeit php71-runtime(x86-64) = 1.0-1.el7.remi wird für Paket php71-1.0-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit php71-runtime wird für Paket php71-1.0-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit php71-php-common(x86-64) wird für Paket php71-1.0-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit php71-php-cli(x86-64) wird für Paket php71-1.0-1.el7.remi.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket php71-php-cli.x86_64 0:7.1.16-1.el7.remi markiert, um installiert zu werden
--------> Paket php71-php-common.x86_64 0:7.1.16-1.el7.remi markiert, um installiert zu werden
--> Abhängigkeit php71-php-json(x86-64) = 7.1.16-1.el7.remi wird für Paket php71-php-common-7.1.16-1.el7.remi.x86_64 verarbeitet
--------> Paket php71-runtime.x86_64 0:1.0-1.el7.remi markiert, um installiert zu werden
--> Abhängigkeit scl-utils wird für Paket php71-runtime-1.0-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit environment-modules wird für Paket php71-runtime-1.0-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit /usr/sbin/semanage wird für Paket php71-runtime-1.0-1.el7.remi.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket environment-modules.x86_64 0:3.2.10-10.el7 markiert, um installiert zu werden
--> Abhängigkeit libtcl8.5.so()(64bit) wird für Paket environment-modules-3.2.10-10.el7.x86_64 verarbeitet
--------> Paket php71-php-json.x86_64 0:7.1.16-1.el7.remi markiert, um installiert zu werden
--------> Paket policycoreutils-python.x86_64 0:2.5-17.1.el7 markiert, um installiert zu werden
--> Abhängigkeit setools-libs >= 3.3.8-1 wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit libsemanage-python >= 2.5-5 wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit audit-libs-python >= 2.1.3-4 wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit python-IPy wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit libqpol.so.1(VERS_1.4)(64bit) wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit libqpol.so.1(VERS_1.2)(64bit) wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit libcgroup wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit libapol.so.4(VERS_4.0)(64bit) wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit checkpolicy wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit libqpol.so.1()(64bit) wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--> Abhängigkeit libapol.so.4()(64bit) wird für Paket policycoreutils-python-2.5-17.1.el7.x86_64 verarbeitet
--------> Paket scl-utils.x86_64 0:20130529-18.el7_4 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket audit-libs-python.x86_64 0:2.7.6-3.el7 markiert, um installiert zu werden
--------> Paket checkpolicy.x86_64 0:2.5-4.el7 markiert, um installiert zu werden
--------> Paket libcgroup.x86_64 0:0.41-13.el7 markiert, um installiert zu werden
--------> Paket libsemanage-python.x86_64 0:2.5-8.el7 markiert, um installiert zu werden
--------> Paket python-IPy.noarch 0:0.75-6.el7 markiert, um installiert zu werden
--------> Paket setools-libs.x86_64 0:3.3.8-1.1.el7 markiert, um installiert zu werden
--------> Paket tcl.x86_64 1:8.5.13-8.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                    Arch                       Version                                Paketquelle                   Größe
==================================================================================================================================================
Installieren:
 php71                                      x86_64                     1.0-1.el7.remi                         remi-safe                     2.1 k
Als Abhängigkeiten installiert:
 audit-libs-python                          x86_64                     2.7.6-3.el7                            base                           73 k
 checkpolicy                                x86_64                     2.5-4.el7                              base                          290 k
 environment-modules                        x86_64                     3.2.10-10.el7                          base                          107 k
 libcgroup                                  x86_64                     0.41-13.el7                            base                           65 k
 libsemanage-python                         x86_64                     2.5-8.el7                              base                          104 k
 php71-php-cli                              x86_64                     7.1.16-1.el7.remi                      remi-safe                     3.0 M
 php71-php-common                           x86_64                     7.1.16-1.el7.remi                      remi-safe                     598 k
 php71-php-json                             x86_64                     7.1.16-1.el7.remi                      remi-safe                      61 k
 php71-runtime                              x86_64                     1.0-1.el7.remi                         remi-safe                     1.1 M
 policycoreutils-python                     x86_64                     2.5-17.1.el7                           base                          446 k
 python-IPy                                 noarch                     0.75-6.el7                             base                           32 k
 scl-utils                                  x86_64                     20130529-18.el7_4                      updates                        24 k
 setools-libs                               x86_64                     3.3.8-1.1.el7                          base                          612 k
 tcl                                        x86_64                     1:8.5.13-8.el7                         base                          1.9 M

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+14 Abhängige Pakete)

Gesamte Downloadgröße: 8.4 M
Installationsgröße: 23 M
Is this ok [y/d/N]: y
Downloading packages:
(1/15): audit-libs-python-2.7.6-3.el7.x86_64.rpm                                                                           |  73 kB  00:00:00     
warning: /var/cache/yum/x86_64/7/remi-safe/packages/php71-1.0-1.el7.remi.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 00f97f56: NOKEY
Öffentlicher Schlüssel für php71-1.0-1.el7.remi.x86_64.rpm ist nicht installiert
(2/15): php71-1.0-1.el7.remi.x86_64.rpm                                                                                    | 2.1 kB  00:00:00     
(3/15): checkpolicy-2.5-4.el7.x86_64.rpm                                                                                   | 290 kB  00:00:00     
(4/15): libcgroup-0.41-13.el7.x86_64.rpm                                                                                   |  65 kB  00:00:00     
(5/15): environment-modules-3.2.10-10.el7.x86_64.rpm                                                                       | 107 kB  00:00:00     
(6/15): libsemanage-python-2.5-8.el7.x86_64.rpm                                                                            | 104 kB  00:00:00     
(7/15): php71-php-json-7.1.16-1.el7.remi.x86_64.rpm                                                                        |  61 kB  00:00:00     
(8/15): python-IPy-0.75-6.el7.noarch.rpm                                                                                   |  32 kB  00:00:00     
(9/15): policycoreutils-python-2.5-17.1.el7.x86_64.rpm                                                                     | 446 kB  00:00:00     
(10/15): php71-php-common-7.1.16-1.el7.remi.x86_64.rpm                                                                     | 598 kB  00:00:00     
(11/15): scl-utils-20130529-18.el7_4.x86_64.rpm                                                                            |  24 kB  00:00:00     
(12/15): setools-libs-3.3.8-1.1.el7.x86_64.rpm                                                                             | 612 kB  00:00:00     
(13/15): php71-runtime-1.0-1.el7.remi.x86_64.rpm                                                                           | 1.1 MB  00:00:01     
(14/15): tcl-8.5.13-8.el7.x86_64.rpm                                                                                       | 1.9 MB  00:00:01     
(15/15): php71-php-cli-7.1.16-1.el7.remi.x86_64.rpm                                                                        | 3.0 MB  00:00:03     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            1.9 MB/s | 8.4 MB  00:00:04     
Schlüssel wird von file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi geholt
GPG-Schlüssel 0x00F97F56 importieren:
 Benutzerkennung     : "Remi Collet <RPMS@FamilleCollet.com>"
 Fingerabdruck: 1ee0 4cce 88a4 ae4a a29a 5df5 004e 6f47 00f9 7f56
 Paket    : remi-release-7.4-2.el7.remi.noarch (installed)
 Von       : /etc/pki/rpm-gpg/RPM-GPG-KEY-remi
Ist dies in Ordnung? [j/N] :y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warnung: RPMDB wurde außerhalb von yum verändert.
  Installieren     : setools-libs-3.3.8-1.1.el7.x86_64                                                                                       1/15 
  Installieren     : checkpolicy-2.5-4.el7.x86_64                                                                                            2/15 
  Installieren     : libcgroup-0.41-13.el7.x86_64                                                                                            3/15 
  Installieren     : python-IPy-0.75-6.el7.noarch                                                                                            4/15 
  Installieren     : audit-libs-python-2.7.6-3.el7.x86_64                                                                                    5/15 
  Installieren     : libsemanage-python-2.5-8.el7.x86_64                                                                                     6/15 
  Installieren     : policycoreutils-python-2.5-17.1.el7.x86_64                                                                              7/15 
  Installieren     : 1:tcl-8.5.13-8.el7.x86_64                                                                                               8/15 
  Installieren     : environment-modules-3.2.10-10.el7.x86_64                                                                                9/15 
  Installieren     : scl-utils-20130529-18.el7_4.x86_64                                                                                     10/15 
  Installieren     : php71-runtime-1.0-1.el7.remi.x86_64                                                                                    11/15 
  Installieren     : php71-php-json-7.1.16-1.el7.remi.x86_64                                                                                12/15 
  Installieren     : php71-php-common-7.1.16-1.el7.remi.x86_64                                                                              13/15 
  Installieren     : php71-php-cli-7.1.16-1.el7.remi.x86_64                                                                                 14/15 
  Installieren     : php71-1.0-1.el7.remi.x86_64                                                                                            15/15 
  Überprüfung läuft: scl-utils-20130529-18.el7_4.x86_64                                                                                      1/15 
  Überprüfung läuft: php71-runtime-1.0-1.el7.remi.x86_64                                                                                     2/15 
  Überprüfung läuft: 1:tcl-8.5.13-8.el7.x86_64                                                                                               3/15 
  Überprüfung läuft: libsemanage-python-2.5-8.el7.x86_64                                                                                     4/15 
  Überprüfung läuft: php71-php-common-7.1.16-1.el7.remi.x86_64                                                                               5/15 
  Überprüfung läuft: audit-libs-python-2.7.6-3.el7.x86_64                                                                                    6/15 
  Überprüfung läuft: php71-php-json-7.1.16-1.el7.remi.x86_64                                                                                 7/15 
  Überprüfung läuft: php71-1.0-1.el7.remi.x86_64                                                                                             8/15 
  Überprüfung läuft: python-IPy-0.75-6.el7.noarch                                                                                            9/15 
  Überprüfung läuft: policycoreutils-python-2.5-17.1.el7.x86_64                                                                             10/15 
  Überprüfung läuft: libcgroup-0.41-13.el7.x86_64                                                                                           11/15 
  Überprüfung läuft: environment-modules-3.2.10-10.el7.x86_64                                                                               12/15 
  Überprüfung läuft: checkpolicy-2.5-4.el7.x86_64                                                                                           13/15 
  Überprüfung läuft: php71-php-cli-7.1.16-1.el7.remi.x86_64                                                                                 14/15 
  Überprüfung läuft: setools-libs-3.3.8-1.1.el7.x86_64                                                                                      15/15 

Installiert:
  php71.x86_64 0:1.0-1.el7.remi                                                                                                                   

Abhängigkeit installiert:
  audit-libs-python.x86_64 0:2.7.6-3.el7            checkpolicy.x86_64 0:2.5-4.el7                 environment-modules.x86_64 0:3.2.10-10.el7     
  libcgroup.x86_64 0:0.41-13.el7                    libsemanage-python.x86_64 0:2.5-8.el7          php71-php-cli.x86_64 0:7.1.16-1.el7.remi       
  php71-php-common.x86_64 0:7.1.16-1.el7.remi       php71-php-json.x86_64 0:7.1.16-1.el7.remi      php71-runtime.x86_64 0:1.0-1.el7.remi          
  policycoreutils-python.x86_64 0:2.5-17.1.el7      python-IPy.noarch 0:0.75-6.el7                 scl-utils.x86_64 0:20130529-18.el7_4           
  setools-libs.x86_64 0:3.3.8-1.1.el7               tcl.x86_64 1:8.5.13-8.el7                     

Komplett!
➜  ~ php --version                                                            
PHP 5.4.16 (cli) (built: Mar  7 2018 13:34:47) 
Copyright (c) 1997-2013 The PHP Group
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
➜  ~ sudo yum-config-manager --enable remi-php71                              
Geladene Plugins: fastestmirror
================================================================ repo: remi-php71 ================================================================
[remi-php71]
async = True
bandwidth = 0
base_persistdir = /var/lib/yum/repos/x86_64/7
baseurl = 
cache = 0
cachedir = /var/cache/yum/x86_64/7/remi-php71
check_config_file_age = True
compare_providers_priority = 80
cost = 1000
deltarpm_metadata_percentage = 100
deltarpm_percentage = 
enabled = 1
enablegroups = True
exclude = 
failovermethod = priority
ftp_disable_epsv = False
gpgcadir = /var/lib/yum/repos/x86_64/7/remi-php71/gpgcadir
gpgcakey = 
gpgcheck = True
gpgdir = /var/lib/yum/repos/x86_64/7/remi-php71/gpgdir
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
hdrdir = /var/cache/yum/x86_64/7/remi-php71/headers
http_caching = all
includepkgs = 
ip_resolve = 
keepalive = True
keepcache = False
mddownloadpolicy = sqlite
mdpolicy = group:small
mediaid = 
metadata_expire = 21600
metadata_expire_filter = read-only:present
metalink = 
minrate = 0
mirrorlist = http://cdn.remirepo.net/enterprise/7/php71/mirror
mirrorlist_expire = 86400
name = Remi's PHP 7.1 RPM repository for Enterprise Linux 7 - x86_64
old_base_cache_dir = 
password = 
persistdir = /var/lib/yum/repos/x86_64/7/remi-php71
pkgdir = /var/cache/yum/x86_64/7/remi-php71/packages
proxy = False
proxy_dict = 
proxy_password = 
proxy_username = 
repo_gpgcheck = False
retries = 10
skip_if_unavailable = False
ssl_check_cert_permissions = True
sslcacert = 
sslclientcert = 
sslclientkey = 
sslverify = True
throttle = 0
timeout = 30.0
ui_id = remi-php71
ui_repoid_vars = releasever,
   basearch
username = 

➜  ~ php --version                               
PHP 5.4.16 (cli) (built: Mar  7 2018 13:34:47) 
Copyright (c) 1997-2013 The PHP Group
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
➜  ~ yum --enablerepo=remi-php71 install php
Geladene Plugins: fastestmirror
Sie müssen root sein, um diesen Befehl ausführen zu können.
➜  ~ sudo yum --enablerepo=remi-php71 install php
Geladene Plugins: fastestmirror
remi-php71                                                                                                                 | 2.9 kB  00:00:00     
remi-php71/primary_db                                                                                                      | 216 kB  00:00:00     
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * epel: fedora.cu.be
 * extras: mirror.dclux.com
 * remi-php71: remi.mirrors.cu.be
 * remi-safe: remi.mirrors.cu.be
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket php.x86_64 0:5.4.16-43.el7_4.1 markiert, um aktualisiert zu werden
--------> Paket php.x86_64 0:7.1.16-1.el7.remi markiert, um eine Aktualisierung zu werden
--> Abhängigkeit php-common(x86-64) = 7.1.16-1.el7.remi wird für Paket php-7.1.16-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit php-cli(x86-64) = 7.1.16-1.el7.remi wird für Paket php-7.1.16-1.el7.remi.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket php-cli.x86_64 0:5.4.16-43.el7_4.1 markiert, um aktualisiert zu werden
--------> Paket php-cli.x86_64 0:7.1.16-1.el7.remi markiert, um eine Aktualisierung zu werden
--------> Paket php-common.x86_64 0:5.4.16-43.el7_4.1 markiert, um aktualisiert zu werden
--> Abhängigkeit php-json(x86-64) = 7.1.16-1.el7.remi wird für Paket php-common-7.1.16-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit php-common(x86-64) = 5.4.16-43.el7_4.1 wird für Paket php-ldap-5.4.16-43.el7_4.1.x86_64 verarbeitet
--> Abhängigkeit php-common(x86-64) = 5.4.16-43.el7_4.1 wird für Paket php-pdo-5.4.16-43.el7_4.1.x86_64 verarbeitet
--> Abhängigkeit php-common(x86-64) = 5.4.16-43.el7_4.1 wird für Paket php-gd-5.4.16-43.el7_4.1.x86_64 verarbeitet
--------> Paket php-common.x86_64 0:7.1.16-1.el7.remi markiert, um eine Aktualisierung zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket php-gd.x86_64 0:5.4.16-43.el7_4.1 markiert, um aktualisiert zu werden
--------> Paket php-gd.x86_64 0:7.1.16-1.el7.remi markiert, um eine Aktualisierung zu werden
--> Abhängigkeit gd-last(x86-64) >= 2.1.1 wird für Paket php-gd-7.1.16-1.el7.remi.x86_64 verarbeitet
--> Abhängigkeit libgd.so.3()(64bit) wird für Paket php-gd-7.1.16-1.el7.remi.x86_64 verarbeitet
--------> Paket php-json.x86_64 0:7.1.16-1.el7.remi markiert, um installiert zu werden
--------> Paket php-ldap.x86_64 0:5.4.16-43.el7_4.1 markiert, um aktualisiert zu werden
--------> Paket php-ldap.x86_64 0:7.1.16-1.el7.remi markiert, um eine Aktualisierung zu werden
--------> Paket php-pdo.x86_64 0:5.4.16-43.el7_4.1 markiert, um aktualisiert zu werden
--> Abhängigkeit php-pdo(x86-64) = 5.4.16-43.el7_4.1 wird für Paket php-mysql-5.4.16-43.el7_4.1.x86_64 verarbeitet
--------> Paket php-pdo.x86_64 0:7.1.16-1.el7.remi markiert, um eine Aktualisierung zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket gd-last.x86_64 0:2.2.5-2.el7.remi markiert, um installiert zu werden
--> Abhängigkeit libwebp.so.4()(64bit) wird für Paket gd-last-2.2.5-2.el7.remi.x86_64 verarbeitet
--------> Paket php-mysql.x86_64 0:5.4.16-43.el7_4.1 markiert, um veraltet zu werden
--------> Paket php-mysqlnd.x86_64 0:7.1.16-1.el7.remi markiert, um Aufräumen zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket libwebp.x86_64 0:0.3.0-7.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                           Arch                         Version                                    Paketquelle                      Größe
==================================================================================================================================================
Installieren:
 php-mysqlnd                       x86_64                       7.1.16-1.el7.remi                          remi-php71                       229 k
     ersetzt  php-mysql.x86_64 5.4.16-43.el7_4.1
Aktualisieren:
 php                               x86_64                       7.1.16-1.el7.remi                          remi-php71                       3.0 M
Als Abhängigkeiten installiert:
 gd-last                           x86_64                       2.2.5-2.el7.remi                           remi-safe                        133 k
 libwebp                           x86_64                       0.3.0-7.el7                                base                             170 k
 php-json                          x86_64                       7.1.16-1.el7.remi                          remi-php71                        59 k
Aktualisiert für Abhängigkeiten:
 php-cli                           x86_64                       7.1.16-1.el7.remi                          remi-php71                       4.6 M
 php-common                        x86_64                       7.1.16-1.el7.remi                          remi-php71                       1.0 M
 php-gd                            x86_64                       7.1.16-1.el7.remi                          remi-php71                        74 k
 php-ldap                          x86_64                       7.1.16-1.el7.remi                          remi-php71                        65 k
 php-pdo                           x86_64                       7.1.16-1.el7.remi                          remi-php71                       122 k

Transaktionsübersicht
==================================================================================================================================================
Installieren   1 Paket (+3 Abhängige Pakete)
Aktualisieren  1 Paket (+5 Abhängige Pakete)

Gesamte Downloadgröße: 9.4 M
Is this ok [y/d/N]: y
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
(1/10): libwebp-0.3.0-7.el7.x86_64.rpm                                                                                     | 170 kB  00:00:00     
(2/10): php-gd-7.1.16-1.el7.remi.x86_64.rpm                                                                                |  74 kB  00:00:00     
(3/10): php-common-7.1.16-1.el7.remi.x86_64.rpm                                                                            | 1.0 MB  00:00:00     
(4/10): php-json-7.1.16-1.el7.remi.x86_64.rpm                                                                              |  59 kB  00:00:00     
(5/10): php-ldap-7.1.16-1.el7.remi.x86_64.rpm                                                                              |  65 kB  00:00:00     
(6/10): php-pdo-7.1.16-1.el7.remi.x86_64.rpm                                                                               | 122 kB  00:00:00     
(7/10): php-mysqlnd-7.1.16-1.el7.remi.x86_64.rpm                                                                           | 229 kB  00:00:00     
(8/10): gd-last-2.2.5-2.el7.remi.x86_64.rpm                                                                                | 133 kB  00:00:02     
(9/10): php-7.1.16-1.el7.remi.x86_64.rpm                                                                                   | 3.0 MB  00:00:02     
(10/10): php-cli-7.1.16-1.el7.remi.x86_64.rpm                                                                              | 4.6 MB  00:00:05     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            1.7 MB/s | 9.4 MB  00:00:05     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Aktualisieren    : php-common-7.1.16-1.el7.remi.x86_64                                                                                     1/17 
  Installieren     : php-json-7.1.16-1.el7.remi.x86_64                                                                                       2/17 
  Aktualisieren    : php-pdo-7.1.16-1.el7.remi.x86_64                                                                                        3/17 
  Aktualisieren    : php-cli-7.1.16-1.el7.remi.x86_64                                                                                        4/17 
  Installieren     : libwebp-0.3.0-7.el7.x86_64                                                                                              5/17 
  Installieren     : gd-last-2.2.5-2.el7.remi.x86_64                                                                                         6/17 
  Aktualisieren    : php-gd-7.1.16-1.el7.remi.x86_64                                                                                         7/17 
  Aktualisieren    : php-7.1.16-1.el7.remi.x86_64                                                                                            8/17 
  Installieren     : php-mysqlnd-7.1.16-1.el7.remi.x86_64                                                                                    9/17 
  Aktualisieren    : php-ldap-7.1.16-1.el7.remi.x86_64                                                                                      10/17 
  Aufräumen        : php-5.4.16-43.el7_4.1.x86_64                                                                                           11/17 
  Aufräumen        : php-cli-5.4.16-43.el7_4.1.x86_64                                                                                       12/17 
  Aufräumen        : php-gd-5.4.16-43.el7_4.1.x86_64                                                                                        13/17 
  Aufräumen        : php-ldap-5.4.16-43.el7_4.1.x86_64                                                                                      14/17 
  Löschen          : php-mysql-5.4.16-43.el7_4.1.x86_64                                                                                     15/17 
  Aufräumen        : php-pdo-5.4.16-43.el7_4.1.x86_64                                                                                       16/17 
  Aufräumen        : php-common-5.4.16-43.el7_4.1.x86_64                                                                                    17/17 
  Überprüfung läuft: php-7.1.16-1.el7.remi.x86_64                                                                                            1/17 
  Überprüfung läuft: php-mysqlnd-7.1.16-1.el7.remi.x86_64                                                                                    2/17 
  Überprüfung läuft: php-json-7.1.16-1.el7.remi.x86_64                                                                                       3/17 
  Überprüfung läuft: gd-last-2.2.5-2.el7.remi.x86_64                                                                                         4/17 
  Überprüfung läuft: php-pdo-7.1.16-1.el7.remi.x86_64                                                                                        5/17 
  Überprüfung läuft: php-ldap-7.1.16-1.el7.remi.x86_64                                                                                       6/17 
  Überprüfung läuft: php-common-7.1.16-1.el7.remi.x86_64                                                                                     7/17 
  Überprüfung läuft: php-gd-7.1.16-1.el7.remi.x86_64                                                                                         8/17 
  Überprüfung läuft: libwebp-0.3.0-7.el7.x86_64                                                                                              9/17 
  Überprüfung läuft: php-cli-7.1.16-1.el7.remi.x86_64                                                                                       10/17 
  Überprüfung läuft: php-cli-5.4.16-43.el7_4.1.x86_64                                                                                       11/17 
  Überprüfung läuft: php-ldap-5.4.16-43.el7_4.1.x86_64                                                                                      12/17 
  Überprüfung läuft: php-5.4.16-43.el7_4.1.x86_64                                                                                           13/17 
  Überprüfung läuft: php-common-5.4.16-43.el7_4.1.x86_64                                                                                    14/17 
  Überprüfung läuft: php-gd-5.4.16-43.el7_4.1.x86_64                                                                                        15/17 
  Überprüfung läuft: php-pdo-5.4.16-43.el7_4.1.x86_64                                                                                       16/17 
  Überprüfung läuft: php-mysql-5.4.16-43.el7_4.1.x86_64                                                                                     17/17 

Installiert:
  php-mysqlnd.x86_64 0:7.1.16-1.el7.remi                                                                                                          

Abhängigkeit installiert:
  gd-last.x86_64 0:2.2.5-2.el7.remi                libwebp.x86_64 0:0.3.0-7.el7                php-json.x86_64 0:7.1.16-1.el7.remi               

Aktualisiert:
  php.x86_64 0:7.1.16-1.el7.remi                                                                                                                  

Abhängigkeit aktualisiert:
  php-cli.x86_64 0:7.1.16-1.el7.remi php-common.x86_64 0:7.1.16-1.el7.remi php-gd.x86_64 0:7.1.16-1.el7.remi php-ldap.x86_64 0:7.1.16-1.el7.remi
  php-pdo.x86_64 0:7.1.16-1.el7.remi

Ersetzt       :
  php-mysql.x86_64 0:5.4.16-43.el7_4.1                                                                                                            

Komplett!
➜  ~ php --version                               
PHP 7.1.16 (cli) (built: Mar 28 2018 13:19:29) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2018 Zend Technologies
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode