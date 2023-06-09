# 在 Linux CentOS7 上安装 Webmin 之后安装 Virtualmin

> 原文：<https://dev.to/psnebc/install-virtualmin-after-webmin-on-linux-centos7-3mjg>

### 图片

> [Linux | Centos 7 Server-Fedora Server | Webmin-virtual min](//imgur.com/Kjw87aa)

*   [https://host presto . com/community/tutorials/how-to-install-virtual min-on-centos-7-0/](https://hostpresto.com/community/tutorials/how-to-install-virtualmin-on-centos-7-0/)

```
 psnebc@localhost  ~  ssh 192.168.122.231
psnebc@192.168.122.231's password: 
Last login: Mon Apr 23 20:53:34 2018 from 192.168.122.1
[psnebc@centos ~]$ su -
Passwort: 
Letzte Anmeldung: Montag, den 23\. April 2018, 22:40:52 CEST auf tty1
[root@centos ~]# cd /tmp/
[root@centos tmp]# sh
sh                sha224sum         sha384sum         shasum            shopt             showconsolefont   showkey           shuf
sha1sum           sha256sum         sha512sum         shift             show-changed-rco  show-installed    shred             shutdown
[root@centos tmp]# sh install.sh 

  Welcome to the Virtualmin GPL installer, version 6.0.8

  This script must be run on a freshly installed supported OS. It does not
  perform updates or upgrades (use your system package manager) or license
  changes (use the "virtualmin change-license" command).

  The systems currently supported by install.sh are:

    CentOS/RHEL Linux 6 and 7 on x86_64
    Debian 7, 8, and 9, on i386 and amd64
    Ubuntu 14.04 LTS and 16.04 LTS, on i386 and amd64

  If your OS/version/arch is not listed, installation will fail. More
  details about the systems supported by the script can be found here:

    http://www.virtualmin.com/os-support

  The selected package bundle is LAMP and the size of install is
  full. It will require up to 650 MB of disk space.

  Exit and re-run this script with --help flag to see available options.

 Continue? (y/n) y

  WARNING

  Virtualmin may already be installed. This can happen if an installation failed,
  and can be ignored in that case.

  But, if Virtualmin has already successfully installed you should not run this
  script again! It will cause breakage to your existing configuration.

  Updates and upgrades can be performed from within Virtualmin. To change
  license details, use the 'virtualmin change-license' command.

  Changing the license never requires re-installation.

 Really Continue? (y/n) y
[INFO] Started installation log in /root/virtualmin-install.log

▣□□ Phase 1 of 3: Setup
Updating yum Groups                                                     [  ✔  ]
Downloading virtualmin-release-latest.noarch.rpm                        [  ✔  ]
Installing virtualmin-release package                                   [  ✔  ]

▣▣□ Phase 2 of 3: Installation
Downloading epel-release-latest-7.noarch.rpm                            [  ✔  ]
Installing EPEL release package                                         [  ✔  ]
Installing yum-utils                                                    [  ✔  ]
Enabling extras repository                                              [  ✔  ]
Installing scl-utils                                                    [  ✔  ]
Install Software Collections release package                            [  ✔  ]
Installing PHP7                                                         [  ✔  ]
Marking 'Virtualmin LAMP Stack' for install                             [  ✔  ]
Marking 'Virtualmin Core' for install                                   [  ✔  ]
Installing dependencies and system packages                             [  ✔  ]
Installing Virtualmin and all related packages                          [  ✔  ]
Cleaning up software repo metadata                                      [  ✔  ]
Installing updates to Virtualmin-related packages                       [  ✔  ]

▣▣▣ Phase 3 of 3: Configuration
[1/23] Configuring AWStats                                              [  ✔  ]
[2/23] Configuring Apache                                               [  ✔  ]
[3/23] Configuring Bind                                                 [  ✔  ]
[4/23] Configuring ClamAV                                               [  ✔  ]
[5/23] Configuring Dovecot                                              [  ✔  ]
[6/23] Configuring Firewalld                                            [  ✔  ]
[7/23] Configuring MySQL                                                [  ✔  ]
[8/23] Configuring NTP                                                  [  ✔  ]
[9/23] Configuring Net                                                  [  ✔  ]
[10/23] Configuring ProFTPd                                             [  ✔  ]
[11/23] Configuring Procmail                                            [  ✔  ]
[12/23] Configuring Quotas                                                     
The filesystem / could not be remounted with quotas enabled.
You may need to reboot your system, and/or enable quotas in the Disk
Quotas module.                                                          [  ⚠  ]
[13/23] Configuring SASL                                                [  ✔  ]
[14/23] Configuring Shells                                              [  ✔  ]
[15/23] Configuring SpamAssassin                                        [  ✔  ]
[16/23] Configuring Status                                              [  ✔  ]
[17/23] Configuring Upgrade                                             [  ✔  ]
[18/23] Configuring Usermin                                             [  ✔  ]
[19/23] Configuring Webalizer                                           [  ✔  ]
[20/23] Configuring Webmin                                              [  ✔  ]
[21/23] Configuring Fail2banFirewalld                                   [  ✔  ]
[22/23] Configuring Postfix                                             [  ✔  ]
[23/23] Configuring Virtualmin                                          [  ✔  ]
▣▣▣ Cleaning up

[SUCCESS] Installation Complete!
[SUCCESS] If there were no errors above, Virtualmin should be ready
[SUCCESS] to configure at https://centos.localhost:10000.
                                                                        [root@centos tmp]# reboot 
```

Enter fullscreen mode Exit fullscreen mode