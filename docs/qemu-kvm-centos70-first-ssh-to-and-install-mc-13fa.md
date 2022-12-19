# Qemu KVM Centos7.0 首先 ssh 到并安装 MC

> 原文：<https://dev.to/psnebc/qemu-kvm-centos70-first-ssh-to-and-install-mc-13fa>

*   [第一次 ssh 到](https://i.imgur.com/giBnOQ1.jpg)
*   安装在谷歌图片相册:[https://photos.app.goo.gl/TXNwC94WHNGTZin52](https://photos.app.goo.gl/TXNwC94WHNGTZin52)

```
 psnebc@localhost  ~  ssh 192.168.122.224
The authenticity of host '192.168.122.224 (192.168.122.224)' can't be established.
ECDSA key fingerprint is SHA256:VziDsHJSsoegDatmCeppfBcStmcooBc87sdApmbVG2s.
ECDSA key fingerprint is MD5:6b:da:b1:1c:2b:d9:39:36:7e:46:a8:b7:5c:de:bc:e0.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.122.224' (ECDSA) to the list of known hosts.
psnebc@192.168.122.224's password: 
Last login: Mon Apr  9 18:07:51 2018
[psnebc@localhost ~]$ ll
insgesamt 0
[psnebc@localhost ~]$ sudo yum install mc

Wir gehen davon aus, dass der lokale Systemadministrator Ihnen die
Regeln erklärt hat.  Normalerweise läuft es auf drei Regeln hinaus:

    #1) Respektieren Sie die Privatsphäre anderer.
    #2) Denken Sie nach, bevor Sie tippen.
    #3) Mit großer Macht kommt große Verantwortung.

[sudo] Passwort für psnebc: 
Geladene Plugins: fastestmirror
base                                                                                                                       | 3.6 kB  00:00:00     
extras                                                                                                                     | 3.4 kB  00:00:00     
updates                                                                                                                    | 3.4 kB  00:00:00     
(1/4): base/7/x86_64/group_gz                                                                                              | 156 kB  00:00:00     
(2/4): extras/7/x86_64/primary_db                                                                                          | 185 kB  00:00:00     
(3/4): base/7/x86_64/primary_db                                                                                            | 5.7 MB  00:00:03     
(4/4): updates/7/x86_64/primary_db                                                                                         | 6.9 MB  00:00:07     
Determining fastest mirrors
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket mc.x86_64 1:4.8.7-11.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(strict) wird für Paket 1:mc-4.8.7-11.el7.x86_64 verarbeitet
--> Abhängigkeit perl(bytes) wird für Paket 1:mc-4.8.7-11.el7.x86_64 verarbeitet
--> Abhängigkeit perl(POSIX) wird für Paket 1:mc-4.8.7-11.el7.x86_64 verarbeitet
--> Abhängigkeit perl(File::Temp) wird für Paket 1:mc-4.8.7-11.el7.x86_64 verarbeitet
--> Abhängigkeit perl(File::Basename) wird für Paket 1:mc-4.8.7-11.el7.x86_64 verarbeitet
--> Abhängigkeit /usr/bin/perl wird für Paket 1:mc-4.8.7-11.el7.x86_64 verarbeitet
--> Abhängigkeit libgpm.so.2()(64bit) wird für Paket 1:mc-4.8.7-11.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket gpm-libs.x86_64 0:1.20.7-5.el7 markiert, um installiert zu werden
--------> Paket perl.x86_64 4:5.16.3-292.el7 markiert, um installiert zu werden
--> Abhängigkeit perl-libs = 4:5.16.3-292.el7 wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Socket) >= 1.3 wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Scalar::Util) >= 1.10 wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl-macros wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl-libs wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(threads::shared) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(threads) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(constant) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Time::Local) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Time::HiRes) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Storable) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Socket) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Scalar::Util) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Pod::Simple::XHTML) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Pod::Simple::Search) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Getopt::Long) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Filter::Util::Call) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(File::Spec::Unix) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(File::Spec::Functions) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(File::Spec) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(File::Path) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Exporter) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Cwd) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit perl(Carp) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--> Abhängigkeit libperl.so()(64bit) wird für Paket 4:perl-5.16.3-292.el7.x86_64 verarbeitet
--------> Paket perl-File-Temp.noarch 0:0.23.01-3.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket perl-Carp.noarch 0:1.26-244.el7 markiert, um installiert zu werden
--------> Paket perl-Exporter.noarch 0:5.68-3.el7 markiert, um installiert zu werden
--------> Paket perl-File-Path.noarch 0:2.09-2.el7 markiert, um installiert zu werden
--------> Paket perl-Filter.x86_64 0:1.49-3.el7 markiert, um installiert zu werden
--------> Paket perl-Getopt-Long.noarch 0:2.40-2.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(Pod::Usage) >= 1.14 wird für Paket perl-Getopt-Long-2.40-2.el7.noarch verarbeitet
--> Abhängigkeit perl(Text::ParseWords) wird für Paket perl-Getopt-Long-2.40-2.el7.noarch verarbeitet
--------> Paket perl-PathTools.x86_64 0:3.40-5.el7 markiert, um installiert zu werden
--------> Paket perl-Pod-Simple.noarch 1:3.28-4.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(Pod::Escapes) >= 1.04 wird für Paket 1:perl-Pod-Simple-3.28-4.el7.noarch verarbeitet
--> Abhängigkeit perl(Encode) wird für Paket 1:perl-Pod-Simple-3.28-4.el7.noarch verarbeitet
--------> Paket perl-Scalar-List-Utils.x86_64 0:1.27-248.el7 markiert, um installiert zu werden
--------> Paket perl-Socket.x86_64 0:2.010-4.el7 markiert, um installiert zu werden
--------> Paket perl-Storable.x86_64 0:2.45-3.el7 markiert, um installiert zu werden
--------> Paket perl-Time-HiRes.x86_64 4:1.9725-3.el7 markiert, um installiert zu werden
--------> Paket perl-Time-Local.noarch 0:1.2300-2.el7 markiert, um installiert zu werden
--------> Paket perl-constant.noarch 0:1.27-2.el7 markiert, um installiert zu werden
--------> Paket perl-libs.x86_64 4:5.16.3-292.el7 markiert, um installiert zu werden
--------> Paket perl-macros.x86_64 4:5.16.3-292.el7 markiert, um installiert zu werden
--------> Paket perl-threads.x86_64 0:1.87-4.el7 markiert, um installiert zu werden
--------> Paket perl-threads-shared.x86_64 0:1.43-6.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket perl-Encode.x86_64 0:2.51-7.el7 markiert, um installiert zu werden
--------> Paket perl-Pod-Escapes.noarch 1:1.04-292.el7 markiert, um installiert zu werden
--------> Paket perl-Pod-Usage.noarch 0:1.63-3.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(Pod::Text) >= 3.15 wird für Paket perl-Pod-Usage-1.63-3.el7.noarch verarbeitet
--> Abhängigkeit perl-Pod-Perldoc wird für Paket perl-Pod-Usage-1.63-3.el7.noarch verarbeitet
--------> Paket perl-Text-ParseWords.noarch 0:3.29-4.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket perl-Pod-Perldoc.noarch 0:3.20-4.el7 markiert, um installiert zu werden
--> Abhängigkeit perl(parent) wird für Paket perl-Pod-Perldoc-3.20-4.el7.noarch verarbeitet
--> Abhängigkeit perl(HTTP::Tiny) wird für Paket perl-Pod-Perldoc-3.20-4.el7.noarch verarbeitet
--------> Paket perl-podlators.noarch 0:2.5.1-3.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket perl-HTTP-Tiny.noarch 0:0.033-3.el7 markiert, um installiert zu werden
--------> Paket perl-parent.noarch 1:0.225-244.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                     Arch                        Version                                  Paketquelle               Größe
==================================================================================================================================================
Installieren:
 mc                                          x86_64                      1:4.8.7-11.el7                           base                      1.7 M
Als Abhängigkeiten installiert:
 gpm-libs                                    x86_64                      1.20.7-5.el7                             base                       32 k
 perl                                        x86_64                      4:5.16.3-292.el7                         base                      8.0 M
 perl-Carp                                   noarch                      1.26-244.el7                             base                       19 k
 perl-Encode                                 x86_64                      2.51-7.el7                               base                      1.5 M
 perl-Exporter                               noarch                      5.68-3.el7                               base                       28 k
 perl-File-Path                              noarch                      2.09-2.el7                               base                       26 k
 perl-File-Temp                              noarch                      0.23.01-3.el7                            base                       56 k
 perl-Filter                                 x86_64                      1.49-3.el7                               base                       76 k
 perl-Getopt-Long                            noarch                      2.40-2.el7                               base                       56 k
 perl-HTTP-Tiny                              noarch                      0.033-3.el7                              base                       38 k
 perl-PathTools                              x86_64                      3.40-5.el7                               base                       82 k
 perl-Pod-Escapes                            noarch                      1:1.04-292.el7                           base                       51 k
 perl-Pod-Perldoc                            noarch                      3.20-4.el7                               base                       87 k
 perl-Pod-Simple                             noarch                      1:3.28-4.el7                             base                      216 k
 perl-Pod-Usage                              noarch                      1.63-3.el7                               base                       27 k
 perl-Scalar-List-Utils                      x86_64                      1.27-248.el7                             base                       36 k
 perl-Socket                                 x86_64                      2.010-4.el7                              base                       49 k
 perl-Storable                               x86_64                      2.45-3.el7                               base                       77 k
 perl-Text-ParseWords                        noarch                      3.29-4.el7                               base                       14 k
 perl-Time-HiRes                             x86_64                      4:1.9725-3.el7                           base                       45 k
 perl-Time-Local                             noarch                      1.2300-2.el7                             base                       24 k
 perl-constant                               noarch                      1.27-2.el7                               base                       19 k
 perl-libs                                   x86_64                      4:5.16.3-292.el7                         base                      688 k
 perl-macros                                 x86_64                      4:5.16.3-292.el7                         base                       43 k
 perl-parent                                 noarch                      1:0.225-244.el7                          base                       12 k
 perl-podlators                              noarch                      2.5.1-3.el7                              base                      112 k
 perl-threads                                x86_64                      1.87-4.el7                               base                       49 k
 perl-threads-shared                         x86_64                      1.43-6.el7                               base                       39 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+28 Abhängige Pakete)

Gesamte Downloadgröße: 13 M
Installationsgröße: 42 M
Is this ok [y/d/N]: y
Downloading packages:
Warnung: /var/cache/yum/x86_64/7/base/packages/gpm-libs-1.20.7-5.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, Schlüssel-ID f4a80eb5: NOKEY
Öffentlicher Schlüssel für gpm-libs-1.20.7-5.el7.x86_64.rpm ist nicht installiert
(1/29): gpm-libs-1.20.7-5.el7.x86_64.rpm                                                                                   |  32 kB  00:00:00     
(2/29): perl-Carp-1.26-244.el7.noarch.rpm                                                                                  |  19 kB  00:00:00     
(3/29): perl-File-Path-2.09-2.el7.noarch.rpm                                                                               |  26 kB  00:00:00     
(4/29): perl-File-Temp-0.23.01-3.el7.noarch.rpm                                                                            |  56 kB  00:00:00     
(5/29): perl-Exporter-5.68-3.el7.noarch.rpm                                                                                |  28 kB  00:00:00     
(6/29): perl-Filter-1.49-3.el7.x86_64.rpm                                                                                  |  76 kB  00:00:00     
(7/29): perl-HTTP-Tiny-0.033-3.el7.noarch.rpm                                                                              |  38 kB  00:00:00     
(8/29): mc-4.8.7-11.el7.x86_64.rpm                                                                                         | 1.7 MB  00:00:00     
(9/29): perl-Getopt-Long-2.40-2.el7.noarch.rpm                                                                             |  56 kB  00:00:00     
(10/29): perl-PathTools-3.40-5.el7.x86_64.rpm                                                                              |  82 kB  00:00:00     
(11/29): perl-Pod-Escapes-1.04-292.el7.noarch.rpm                                                                          |  51 kB  00:00:00     
(12/29): perl-Pod-Usage-1.63-3.el7.noarch.rpm                                                                              |  27 kB  00:00:00     
(13/29): perl-Scalar-List-Utils-1.27-248.el7.x86_64.rpm                                                                    |  36 kB  00:00:00     
(14/29): perl-Socket-2.010-4.el7.x86_64.rpm                                                                                |  49 kB  00:00:00     
(15/29): perl-Storable-2.45-3.el7.x86_64.rpm                                                                               |  77 kB  00:00:00     
(16/29): perl-Pod-Simple-3.28-4.el7.noarch.rpm                                                                             | 216 kB  00:00:00     
(17/29): perl-Text-ParseWords-3.29-4.el7.noarch.rpm                                                                        |  14 kB  00:00:00     
(18/29): perl-Pod-Perldoc-3.20-4.el7.noarch.rpm                                                                            |  87 kB  00:00:00     
(19/29): perl-Time-HiRes-1.9725-3.el7.x86_64.rpm                                                                           |  45 kB  00:00:00     
(20/29): perl-Time-Local-1.2300-2.el7.noarch.rpm                                                                           |  24 kB  00:00:00     
(21/29): perl-constant-1.27-2.el7.noarch.rpm                                                                               |  19 kB  00:00:00     
(22/29): perl-parent-0.225-244.el7.noarch.rpm                                                                              |  12 kB  00:00:00     
(23/29): perl-macros-5.16.3-292.el7.x86_64.rpm                                                                             |  43 kB  00:00:00     
(24/29): perl-libs-5.16.3-292.el7.x86_64.rpm                                                                               | 688 kB  00:00:00     
(25/29): perl-threads-shared-1.43-6.el7.x86_64.rpm                                                                         |  39 kB  00:00:00     
(26/29): perl-podlators-2.5.1-3.el7.noarch.rpm                                                                             | 112 kB  00:00:00     
(27/29): perl-threads-1.87-4.el7.x86_64.rpm                                                                                |  49 kB  00:00:00     
(28/29): perl-Encode-2.51-7.el7.x86_64.rpm                                                                                 | 1.5 MB  00:00:05     
(29/29): perl-5.16.3-292.el7.x86_64.rpm                                                                                    | 8.0 MB  00:00:06     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            2.1 MB/s |  13 MB  00:00:06     
Schlüssel wird von file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 geholt
GPG-Schlüssel 0xF4A80EB5 importieren:
 Benutzerkennung     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerabdruck: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Paket    : centos-release-7-4.1708.el7.centos.x86_64 (@anaconda)
 Von       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Ist dies in Ordnung? [j/N] :y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : 1:perl-parent-0.225-244.el7.noarch                                                                                      1/29 
  Installieren     : perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                       2/29 
  Installieren     : perl-podlators-2.5.1-3.el7.noarch                                                                                       3/29 
  Installieren     : perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                      4/29 
  Installieren     : 1:perl-Pod-Escapes-1.04-292.el7.noarch                                                                                  5/29 
  Installieren     : perl-Text-ParseWords-3.29-4.el7.noarch                                                                                  6/29 
  Installieren     : perl-Encode-2.51-7.el7.x86_64                                                                                           7/29 
  Installieren     : perl-Pod-Usage-1.63-3.el7.noarch                                                                                        8/29 
  Installieren     : 4:perl-macros-5.16.3-292.el7.x86_64                                                                                     9/29 
  Installieren     : 4:perl-libs-5.16.3-292.el7.x86_64                                                                                      10/29 
  Installieren     : perl-Storable-2.45-3.el7.x86_64                                                                                        11/29 
  Installieren     : perl-Exporter-5.68-3.el7.noarch                                                                                        12/29 
  Installieren     : perl-constant-1.27-2.el7.noarch                                                                                        13/29 
  Installieren     : perl-Time-Local-1.2300-2.el7.noarch                                                                                    14/29 
  Installieren     : perl-Socket-2.010-4.el7.x86_64                                                                                         15/29 
  Installieren     : perl-Carp-1.26-244.el7.noarch                                                                                          16/29 
  Installieren     : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                  17/29 
  Installieren     : perl-PathTools-3.40-5.el7.x86_64                                                                                       18/29 
  Installieren     : perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                             19/29 
  Installieren     : perl-File-Temp-0.23.01-3.el7.noarch                                                                                    20/29 
  Installieren     : perl-File-Path-2.09-2.el7.noarch                                                                                       21/29 
  Installieren     : perl-threads-shared-1.43-6.el7.x86_64                                                                                  22/29 
  Installieren     : perl-threads-1.87-4.el7.x86_64                                                                                         23/29 
  Installieren     : perl-Filter-1.49-3.el7.x86_64                                                                                          24/29 
  Installieren     : 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                    25/29 
  Installieren     : perl-Getopt-Long-2.40-2.el7.noarch                                                                                     26/29 
  Installieren     : 4:perl-5.16.3-292.el7.x86_64                                                                                           27/29 
  Installieren     : gpm-libs-1.20.7-5.el7.x86_64                                                                                           28/29 
  Installieren     : 1:mc-4.8.7-11.el7.x86_64                                                                                               29/29 
  Überprüfung läuft: 1:mc-4.8.7-11.el7.x86_64                                                                                                1/29 
  Überprüfung läuft: perl-HTTP-Tiny-0.033-3.el7.noarch                                                                                       2/29 
  Überprüfung läuft: perl-threads-shared-1.43-6.el7.x86_64                                                                                   3/29 
  Überprüfung läuft: perl-Storable-2.45-3.el7.x86_64                                                                                         4/29 
  Überprüfung läuft: perl-Exporter-5.68-3.el7.noarch                                                                                         5/29 
  Überprüfung läuft: perl-constant-1.27-2.el7.noarch                                                                                         6/29 
  Überprüfung läuft: perl-PathTools-3.40-5.el7.x86_64                                                                                        7/29 
  Überprüfung läuft: 4:perl-macros-5.16.3-292.el7.x86_64                                                                                     8/29 
  Überprüfung läuft: 1:perl-parent-0.225-244.el7.noarch                                                                                      9/29 
  Überprüfung läuft: 4:perl-5.16.3-292.el7.x86_64                                                                                           10/29 
  Überprüfung läuft: perl-File-Temp-0.23.01-3.el7.noarch                                                                                    11/29 
  Überprüfung läuft: 1:perl-Pod-Simple-3.28-4.el7.noarch                                                                                    12/29 
  Überprüfung läuft: perl-Time-Local-1.2300-2.el7.noarch                                                                                    13/29 
  Überprüfung läuft: gpm-libs-1.20.7-5.el7.x86_64                                                                                           14/29 
  Überprüfung läuft: 4:perl-libs-5.16.3-292.el7.x86_64                                                                                      15/29 
  Überprüfung läuft: perl-Pod-Perldoc-3.20-4.el7.noarch                                                                                     16/29 
  Überprüfung läuft: perl-Socket-2.010-4.el7.x86_64                                                                                         17/29 
  Überprüfung läuft: perl-Carp-1.26-244.el7.noarch                                                                                          18/29 
  Überprüfung läuft: 4:perl-Time-HiRes-1.9725-3.el7.x86_64                                                                                  19/29 
  Überprüfung läuft: perl-Scalar-List-Utils-1.27-248.el7.x86_64                                                                             20/29 
  Überprüfung läuft: 1:perl-Pod-Escapes-1.04-292.el7.noarch                                                                                 21/29 
  Überprüfung läuft: perl-Pod-Usage-1.63-3.el7.noarch                                                                                       22/29 
  Überprüfung läuft: perl-Encode-2.51-7.el7.x86_64                                                                                          23/29 
  Überprüfung läuft: perl-podlators-2.5.1-3.el7.noarch                                                                                      24/29 
  Überprüfung läuft: perl-Getopt-Long-2.40-2.el7.noarch                                                                                     25/29 
  Überprüfung läuft: perl-File-Path-2.09-2.el7.noarch                                                                                       26/29 
  Überprüfung läuft: perl-threads-1.87-4.el7.x86_64                                                                                         27/29 
  Überprüfung läuft: perl-Filter-1.49-3.el7.x86_64                                                                                          28/29 
  Überprüfung läuft: perl-Text-ParseWords-3.29-4.el7.noarch                                                                                 29/29 

Installiert:
  mc.x86_64 1:4.8.7-11.el7                                                                                                                        

Abhängigkeit installiert:
  gpm-libs.x86_64 0:1.20.7-5.el7                      perl.x86_64 4:5.16.3-292.el7                 perl-Carp.noarch 0:1.26-244.el7              
  perl-Encode.x86_64 0:2.51-7.el7                     perl-Exporter.noarch 0:5.68-3.el7            perl-File-Path.noarch 0:2.09-2.el7           
  perl-File-Temp.noarch 0:0.23.01-3.el7               perl-Filter.x86_64 0:1.49-3.el7              perl-Getopt-Long.noarch 0:2.40-2.el7         
  perl-HTTP-Tiny.noarch 0:0.033-3.el7                 perl-PathTools.x86_64 0:3.40-5.el7           perl-Pod-Escapes.noarch 1:1.04-292.el7       
  perl-Pod-Perldoc.noarch 0:3.20-4.el7                perl-Pod-Simple.noarch 1:3.28-4.el7          perl-Pod-Usage.noarch 0:1.63-3.el7           
  perl-Scalar-List-Utils.x86_64 0:1.27-248.el7        perl-Socket.x86_64 0:2.010-4.el7             perl-Storable.x86_64 0:2.45-3.el7            
  perl-Text-ParseWords.noarch 0:3.29-4.el7            perl-Time-HiRes.x86_64 4:1.9725-3.el7        perl-Time-Local.noarch 0:1.2300-2.el7        
  perl-constant.noarch 0:1.27-2.el7                   perl-libs.x86_64 4:5.16.3-292.el7            perl-macros.x86_64 4:5.16.3-292.el7          
  perl-parent.noarch 1:0.225-244.el7                  perl-podlators.noarch 0:2.5.1-3.el7          perl-threads.x86_64 0:1.87-4.el7             
  perl-threads-shared.x86_64 0:1.43-6.el7            

Komplett!
[psnebc@localhost ~]$ 
```

Enter fullscreen mode Exit fullscreen mode