# 在 Linux CentOS7 服务器上安装 Fish Shell

> 原文：<https://dev.to/psnebc/install-fish-shell-on-linux-centos7-server-2o40>

### 图片

> [Linux | Centos 7 服务器|安装 Fish Shell](//imgur.com/XJoLspz)

## byobu-tmux 上的鱼

```
[root@centos ~]# cd /etc/yum.repos.d/
[root@centos yum.repos.d]# wget http://download.opensuse.org/repositories/shells:fish:release:2/CentOS_7/shells:fish:release:2.repo
--2018-04-24 20:57:26--  http://download.opensuse.org/repositories/shells:fish:release:2/CentOS_7/shells:fish:release:2.repo
Auflösen des Hostnamen »download.opensuse.org (download.opensuse.org)«... 195.135.221.134, 2001:67c:2178:8::13
Verbindungsaufbau zu download.opensuse.org (download.opensuse.org)|195.135.221.134|:80... verbunden.
HTTP-Anforderung gesendet, warte auf Antwort... 301 Moved Permanently
Platz: http://download.opensuse.org/repositories/shells:fish:release:/2/CentOS_7/shells:fish:release:2.repo[folge]
--2018-04-24 20:57:27--  http://download.opensuse.org/repositories/shells:fish:release:/2/CentOS_7/shells:fish:release:2.repo
Wiederverwendung der bestehenden Verbindung zu download.opensuse.org:80.
HTTP-Anforderung gesendet, warte auf Antwort... 301 Moved Permanently
Platz: http://download.opensuse.org/repositories/shells:fish:/release:/2/CentOS_7/shells:fish:release:2.repo[folge]
--2018-04-24 20:57:27--  http://download.opensuse.org/repositories/shells:fish:/release:/2/CentOS_7/shells:fish:release:2.repo
Wiederverwendung der bestehenden Verbindung zu download.opensuse.org:80.
HTTP-Anforderung gesendet, warte auf Antwort... 301 Moved Permanently
Platz: http://download.opensuse.org/repositories/shells:/fish:/release:/2/CentOS_7/shells:fish:release:2.repo[folge]
--2018-04-24 20:57:27--  http://download.opensuse.org/repositories/shells:/fish:/release:/2/CentOS_7/shells:fish:release:2.repo
Wiederverwendung der bestehenden Verbindung zu download.opensuse.org:80.
HTTP-Anforderung gesendet, warte auf Antwort... 200 OK
Länge: 297
In »»shells:fish:release:2.repo«« speichern.

100%[==============================================================>] 297         --.-K/s   in 0s      

2018-04-24 20:57:27 (21,8 MB/s) - »»shells:fish:release:2.repo«« gespeichert [297/297]

[root@centos yum.repos.d]# yum -y install fish
Geladene Plugins: fastestmirror
shells_fish_release_2                                                            | 1.3 kB  00:00:00     
shells_fish_release_2/primary                                                    | 1.3 kB  00:00:00     
Loading mirror speeds from cached hostfile
 * base: ftp.rz.uni-frankfurt.de
 * epel: fedora.cu.be
 * extras: mirror.infonline.de
 * updates: mirror.infonline.de
shells_fish_release_2                                                                               3/3
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket fish.x86_64 0:2.7.1-1.1 markiert, um installiert zu werden
--> Abhängigkeit bc wird für Paket fish-2.7.1-1.1.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket bc.x86_64 0:1.06.95-13.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

========================================================================================================
 Package          Arch               Version                    Paketquelle                       Größe
========================================================================================================
Installieren:
 fish             x86_64             2.7.1-1.1                  shells_fish_release_2             2.0 M
Als Abhängigkeiten installiert:
 bc               x86_64             1.06.95-13.el7             base                              115 k

Transaktionsübersicht
========================================================================================================
Installieren  1 Paket (+1 Abhängiges Paket)

Gesamte Downloadgröße: 2.1 M
Installationsgröße: 10 M
Downloading packages:
(1/2): bc-1.06.95-13.el7.x86_64.rpm                                              | 115 kB  00:00:00     

Transaktionsübersicht
========================================================================================================
Installieren  1 Paket (+1 Abhängiges Paket)

Gesamte Downloadgröße: 2.1 M
Installationsgröße: 10 M
Downloading packages:
(1/2): bc-1.06.95-13.el7.x86_64.rpm                                              | 115 kB  00:00:00     
warning: /var/cache/yum/x86_64/7/shells_fish_release_2/packages/fish-2.7.1-1.1.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID d880c8e4: NOKEY
Öffentlicher Schlüssel für fish-2.7.1-1.1.x86_64.rpm ist nicht installiert
(2/2): fish-2.7.1-1.1.x86_64.rpm                                                 | 2.0 MB  00:00:01     
-------------------------------------------------------------------------------------------------------------
Gesamt                                                                  1.5 MB/s | 2.1 MB  00:00:01     
Schlüssel wird von http://download.opensuse.org/repositories/shells:/fish:/release:/2/CentOS_7/repodata/repomd.xml.key geholt
GPG-Schlüssel 0xD880C8E4 importieren:
 Benutzerkennung     : "shells:fish OBS Project <shells:fish@build.opensuse.org>"
 Fingerabdruck: 24a6 3b31 cab4 1b33 ec48 801e 2ce2 ac08 d880 c8e4
 Von       : http://download.opensuse.org/repositories/shells:/fish:/release:/2/CentOS_7/repodata/repomd.xml.key
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : bc-1.06.95-13.el7.x86_64                                                       1/2 
  Installieren     : fish-2.7.1-1.1.x86_64                                                          2/2 
  Überprüfung läuft: fish-2.7.1-1.1.x86_64                                                          1/2 
  Überprüfung läuft: bc-1.06.95-13.el7.x86_64                                                       2/2 

Installiert:
  fish.x86_64 0:2.7.1-1.1                                                                               

Abhängigkeit installiert:
  bc.x86_64 0:1.06.95-13.el7                                                                            

Komplett!
[root@centos yum.repos.d]# fish
Willkommen zu fish, der freundlichen interaktiven Shell
root@centos /e/yum.repos.d# lls
fish: Unknown command 'lls'
root@centos /e/yum.repos.d# ls
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo     CentOS-SCLo-scl-rh.repo  CentOS-Vault.repo  epel-testing.repo           virtualmin.repo*
CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-SCLo-scl.repo  CentOS-Sources.repo      epel.repo          shells:fish:release:2.repo  webmin.repo
root@centos /e/yum.repos.d# ll
insgesamt 56K
-rw-r--r--. 1 root root 1,7K 23\. Apr 22:42 CentOS-Base.repo
-rw-r--r--. 1 root root 1,3K 30\. Aug 2017  CentOS-CR.repo
-rw-r--r--. 1 root root  649 30\. Aug 2017  CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  314 30\. Aug 2017  CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 30\. Aug 2017  CentOS-Media.repo
-rw-r--r--. 1 root root  916 23\. Mai 2016  CentOS-SCLo-scl.repo
-rw-r--r--. 1 root root  892 23\. Mai 2016  CentOS-SCLo-scl-rh.repo
-rw-r--r--. 1 root root 1,3K 30\. Aug 2017  CentOS-Sources.repo
-rw-r--r--. 1 root root 3,8K 30\. Aug 2017  CentOS-Vault.repo
-rw-r--r--. 1 root root  951  2\. Okt 2017  epel.repo
-rw-r--r--. 1 root root 1,1K  2\. Okt 2017  epel-testing.repo
-rw-r--r--  1 root root  297  1\. Jan 03:25 shells:fish:release:2.repo
-rwsr-s---. 1 root root  428 25\. Jul 2017  virtualmin.repo*
-rw-r--r--. 1 root root  165 23\. Apr 21:03 webmin.repo
root@centos /e/yum.repos.d# la
insgesamt 72K
drwxr-xr-x.   2 root root 4,0K 24\. Apr 20:57 ./
drwxr-xr-x. 101 root root 8,0K 24\. Apr 20:57 ../
-rw-r--r--.   1 root root 1,7K 23\. Apr 22:42 CentOS-Base.repo
-rw-r--r--.   1 root root 1,3K 30\. Aug 2017  CentOS-CR.repo
-rw-r--r--.   1 root root  649 30\. Aug 2017  CentOS-Debuginfo.repo
-rw-r--r--.   1 root root  314 30\. Aug 2017  CentOS-fasttrack.repo
-rw-r--r--.   1 root root  630 30\. Aug 2017  CentOS-Media.repo
-rw-r--r--.   1 root root  916 23\. Mai 2016  CentOS-SCLo-scl.repo
-rw-r--r--.   1 root root  892 23\. Mai 2016  CentOS-SCLo-scl-rh.repo
-rw-r--r--.   1 root root 1,3K 30\. Aug 2017  CentOS-Sources.repo
-rw-r--r--.   1 root root 3,8K 30\. Aug 2017  CentOS-Vault.repo
-rw-r--r--.   1 root root  951  2\. Okt 2017  epel.repo
-rw-r--r--.   1 root root 1,1K  2\. Okt 2017  epel-testing.repo
-rw-r--r--    1 root root  297  1\. Jan 03:25 shells:fish:release:2.repo
-rwsr-s---.   1 root root  428 25\. Jul 2017  virtualmin.repo*
-rw-r--r--.   1 root root  165 23\. Apr 21:03 webmin.repo
root@centos /e/yum.repos.d# ls
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo     CentOS-SCLo-scl-rh.repo  CentOS-Vault.repo  epel-testing.repo           virtualmin.repo*
CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-SCLo-scl.repo  CentOS-Sources.repo      epel.repo          shells:fish:release:2.repo  webmin.repo
root@centos /e/yum.repos.d# ll
insgesamt 56K
-rw-r--r--. 1 root root 1,7K 23\. Apr 22:42 CentOS-Base.repo
-rw-r--r--. 1 root root 1,3K 30\. Aug 2017  CentOS-CR.repo
-rw-r--r--. 1 root root  649 30\. Aug 2017  CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  314 30\. Aug 2017  CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 30\. Aug 2017  CentOS-Media.repo
-rw-r--r--. 1 root root  916 23\. Mai 2016  CentOS-SCLo-scl.repo
-rw-r--r--. 1 root root  892 23\. Mai 2016  CentOS-SCLo-scl-rh.repo
-rw-r--r--. 1 root root 1,3K 30\. Aug 2017  CentOS-Sources.repo
-rw-r--r--. 1 root root 3,8K 30\. Aug 2017  CentOS-Vault.repo
-rw-r--r--. 1 root root  951  2\. Okt 2017  epel.repo
-rw-r--r--. 1 root root 1,1K  2\. Okt 2017  epel-testing.repo
-rw-r--r--  1 root root  297  1\. Jan 03:25 shells:fish:release:2.repo
-rwsr-s---. 1 root root  428 25\. Jul 2017  virtualmin.repo*
-rw-r--r--. 1 root root  165 23\. Apr 21:03 webmin.repo
root@centos /e/yum.repos.d# 
```

Enter fullscreen mode Exit fullscreen mode