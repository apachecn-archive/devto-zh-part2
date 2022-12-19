# 在 Linux CentOS 7 上安装并运行 Cockpit

> 原文：<https://dev.to/psnebc/install-and-run-cockpit-on-linux-centos-7--59ag>

### 画面上的 Imgur

> [Linux | Centos 7 服务器|安装&运行驾驶舱](//imgur.com/EeVlojn)

```
[psnebc@localhost ~]$ sudo yum -y install epel-release
    [sudo] Passwort für psnebc: 
    psnebc ist nicht in der sudoers-Datei. Dieser Vorfall wird gemeldet.
    [psnebc@localhost ~]$ su -
    Passwort: 
    Letzte Anmeldung: Freitag, den 20\. April 2018, 14:53:04 CEST auf tty1
    [root@localhost ~]# yum -y install epel-release     
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
     * base: centos.schlundtech.de
     * epel: ftp.nluug.nl
     * extras: ftp.uni-bayreuth.de
     * remi-php71: remi.mirror.ate.info
     * remi-php72: remi.mirror.ate.info
     * remi-safe: remi.mirror.ate.info
     * updates: ftp.fau.de
    Package epel-release-7-11.noarch already installed and latest version
    Nothing to do
    [root@localhost ~]# yum -y update
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
     * base: centos.schlundtech.de
     * epel: ftp.nluug.nl
     * extras: ftp.uni-bayreuth.de
     * remi-php71: remi.mirror.ate.info
     * remi-php72: remi.mirror.ate.info
     * remi-safe: remi.mirror.ate.info
     * updates: ftp.fau.de
    No packages marked for update
    [root@localhost ~]# yum -y install cockpit
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
     * base: centos.schlundtech.de
     * epel: ftp.nluug.nl
     * extras: ftp.uni-bayreuth.de
     * remi-php71: remi.mirror.ate.info
     * remi-php72: remi.mirror.ate.info
     * remi-safe: remi.mirror.ate.info
     * updates: ftp.fau.de
    Package cockpit-160-1.el7.centos.x86_64 already installed and latest version
    Nothing to do
    [root@localhost ~]# systemctl start cockpit
    [root@localhost ~]# systemctl enable cockpit.socket
    [root@localhost ~]# sudo firewall-cmd --add-service=cockpit
    Warning: ALREADY_ENABLED: 'cockpit' already in 'public'
    success
    [root@localhost ~]# sudo firewall-cmd --add-service=cockpit --permanent
    Warning: ALREADY_ENABLED: cockpit
    success
    [root@localhost ~]# firewall-cmd --reload
    success 
```

Enter fullscreen mode Exit fullscreen mode

[https://www . Reddit . com/r/PS nebc/comments/8 dne9v/Linux _ centos _ 7 _ server _ install _ run _ cockpit/](https://www.reddit.com/r/psnebc/comments/8dne9v/linux_centos_7_server_install_run_cockpit/)