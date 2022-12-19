# Qemu KVM Centos 7 安装 Vim、Emacs、Git、Curl、Wget、Zsh、ohmyzsh

> 原文：<https://dev.to/psnebc/qemu-kvm-centos-7-install-vim-emacs-git-curl-wgetzsh-ohmyzsh-dmn>

```
[psnebc@localhost ~]$ clear
[psnebc@localhost ~]$ sudo yum install zsh
[sudo] Passwort für psnebc: 
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket zsh.x86_64 0:5.0.2-28.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                        Arch                              Version                                   Paketquelle                     Größe
==================================================================================================================================================
Installieren:
 zsh                            x86_64                            5.0.2-28.el7                              base                            2.4 M

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket

Gesamte Downloadgröße: 2.4 M
Installationsgröße: 5.6 M
Is this ok [y/d/N]: y
Downloading packages:
zsh-5.0.2-28.el7.x86_64.rpm                                                                                                | 2.4 MB  00:00:01     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : zsh-5.0.2-28.el7.x86_64                                                                                                  1/1 
  Überprüfung läuft: zsh-5.0.2-28.el7.x86_64                                                                                                  1/1 

Installiert:
  zsh.x86_64 0:5.0.2-28.el7                                                                                                                       

Komplett!
[psnebc@localhost ~]$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
Cloning Oh My Zsh...
Error: git is not installed
[psnebc@localhost ~]$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
-bash: wget: Kommando nicht gefunden.
[psnebc@localhost ~]$ sudo yum install wget
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket wget.x86_64 0:1.14-15.el7_4.1 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                       Arch                            Version                                     Paketquelle                      Größe
==================================================================================================================================================
Installieren:
 wget                          x86_64                          1.14-15.el7_4.1                             updates                          547 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket

Gesamte Downloadgröße: 547 k
Installationsgröße: 2.0 M
Is this ok [y/d/N]: y
Downloading packages:
wget-1.14-15.el7_4.1.x86_64.rpm                                                                                            | 547 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : wget-1.14-15.el7_4.1.x86_64                                                                                              1/1 
  Überprüfung läuft: wget-1.14-15.el7_4.1.x86_64                                                                                              1/1 

Installiert:
  wget.x86_64 0:1.14-15.el7_4.1                                                                                                                   

Komplett!
[psnebc@localhost ~]$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
--2018-04-09 19:06:03--  https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh
Auflösen des Hostnamen »raw.githubusercontent.com (raw.githubusercontent.com)«... 151.101.112.133
Verbindungsaufbau zu raw.githubusercontent.com (raw.githubusercontent.com)|151.101.112.133|:443... verbunden.
HTTP-Anforderung gesendet, warte auf Antwort... 200 OK
Länge: 4019 (3,9K) [text/plain]
In »»STDOUT«« speichern.

100%[========================================================================================================>] 4.019       --.-K/s   in 0s      

2018-04-09 19:06:03 (20,9 MB/s) - auf die Standardausgabe geschrieben [4019/4019]

Cloning Oh My Zsh...
Error: git is not installed
[psnebc@localhost ~]$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
--2018-04-09 19:06:10--  https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh
Auflösen des Hostnamen »raw.githubusercontent.com (raw.githubusercontent.com)«... 151.101.112.133
Verbindungsaufbau zu raw.githubusercontent.com (raw.githubusercontent.com)|151.101.112.133|:443... verbunden.
HTTP-Anforderung gesendet, warte auf Antwort... 200 OK
Länge: 4019 (3,9K) [text/plain]
In »»STDOUT«« speichern.

100%[========================================================================================================>] 4.019       --.-K/s   in 0s      

2018-04-09 19:06:10 (29,5 MB/s) - auf die Standardausgabe geschrieben [4019/4019]

Cloning Oh My Zsh...
Error: git is not installed
[psnebc@localhost ~]$ sudo yum install git
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket git.x86_64 0:1.8.3.1-12.el7_4 markiert, um installiert zu werden
--> Abhängigkeit perl-Git = 1.8.3.1-12.el7_4 wird für Paket git-1.8.3.1-12.el7_4.x86_64 verarbeitet
--> Abhängigkeit rsync wird für Paket git-1.8.3.1-12.el7_4.x86_64 verarbeitet
--> Abhängigkeit perl(Term::ReadKey) wird für Paket git-1.8.3.1-12.el7_4.x86_64 verarbeitet
--> Abhängigkeit perl(Git) wird für Paket git-1.8.3.1-12.el7_4.x86_64 verarbeitet
--> Abhängigkeit perl(Error) wird für Paket git-1.8.3.1-12.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgnome-keyring.so.0()(64bit) wird für Paket git-1.8.3.1-12.el7_4.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket libgnome-keyring.x86_64 0:3.12.0-1.el7 markiert, um installiert zu werden
--------> Paket perl-Error.noarch 1:0.17020-2.el7 markiert, um installiert zu werden
--------> Paket perl-Git.noarch 0:1.8.3.1-12.el7_4 markiert, um installiert zu werden
--------> Paket perl-TermReadKey.x86_64 0:2.30-20.el7 markiert, um installiert zu werden
--------> Paket rsync.x86_64 0:3.0.9-18.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                Arch                         Version                                  Paketquelle                   Größe
==================================================================================================================================================
Installieren:
 git                                    x86_64                       1.8.3.1-12.el7_4                         updates                       4.4 M
Als Abhängigkeiten installiert:
 libgnome-keyring                       x86_64                       3.12.0-1.el7                             base                          109 k
 perl-Error                             noarch                       1:0.17020-2.el7                          base                           32 k
 perl-Git                               noarch                       1.8.3.1-12.el7_4                         updates                        53 k
 perl-TermReadKey                       x86_64                       2.30-20.el7                              base                           31 k
 rsync                                  x86_64                       3.0.9-18.el7                             base                          360 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+5 Abhängige Pakete)

Gesamte Downloadgröße: 5.0 M
Installationsgröße: 23 M
Is this ok [y/d/N]: y
Downloading packages:
(1/6): perl-Error-0.17020-2.el7.noarch.rpm                                                                                 |  32 kB  00:00:00     
(2/6): libgnome-keyring-3.12.0-1.el7.x86_64.rpm                                                                            | 109 kB  00:00:00     
(3/6): perl-TermReadKey-2.30-20.el7.x86_64.rpm                                                                             |  31 kB  00:00:00     
(4/6): perl-Git-1.8.3.1-12.el7_4.noarch.rpm                                                                                |  53 kB  00:00:00     
(5/6): rsync-3.0.9-18.el7.x86_64.rpm                                                                                       | 360 kB  00:00:01     
(6/6): git-1.8.3.1-12.el7_4.x86_64.rpm                                                                                     | 4.4 MB  00:00:02     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            1.7 MB/s | 5.0 MB  00:00:03     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : 1:perl-Error-0.17020-2.el7.noarch                                                                                        1/6 
  Installieren     : perl-TermReadKey-2.30-20.el7.x86_64                                                                                      2/6 
  Installieren     : libgnome-keyring-3.12.0-1.el7.x86_64                                                                                     3/6 
  Installieren     : rsync-3.0.9-18.el7.x86_64                                                                                                4/6 
  Installieren     : perl-Git-1.8.3.1-12.el7_4.noarch                                                                                         5/6 
  Installieren     : git-1.8.3.1-12.el7_4.x86_64                                                                                              6/6 
  Überprüfung läuft: rsync-3.0.9-18.el7.x86_64                                                                                                1/6 
  Überprüfung läuft: libgnome-keyring-3.12.0-1.el7.x86_64                                                                                     2/6 
  Überprüfung läuft: perl-Git-1.8.3.1-12.el7_4.noarch                                                                                         3/6 
  Überprüfung läuft: perl-TermReadKey-2.30-20.el7.x86_64                                                                                      4/6 
  Überprüfung läuft: 1:perl-Error-0.17020-2.el7.noarch                                                                                        5/6 
  Überprüfung läuft: git-1.8.3.1-12.el7_4.x86_64                                                                                              6/6 

Installiert:
  git.x86_64 0:1.8.3.1-12.el7_4                                                                                                                   

Abhängigkeit installiert:
  libgnome-keyring.x86_64 0:3.12.0-1.el7             perl-Error.noarch 1:0.17020-2.el7             perl-Git.noarch 0:1.8.3.1-12.el7_4            
  perl-TermReadKey.x86_64 0:2.30-20.el7              rsync.x86_64 0:3.0.9-18.el7                  

Komplett!
[psnebc@localhost ~]$ sudo yum install curl
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket curl.x86_64 0:7.29.0-42.el7 markiert, um aktualisiert zu werden
--------> Paket curl.x86_64 0:7.29.0-42.el7_4.1 markiert, um eine Aktualisierung zu werden
--> Abhängigkeit libcurl = 7.29.0-42.el7_4.1 wird für Paket curl-7.29.0-42.el7_4.1.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket libcurl.x86_64 0:7.29.0-42.el7 markiert, um aktualisiert zu werden
--------> Paket libcurl.x86_64 0:7.29.0-42.el7_4.1 markiert, um eine Aktualisierung zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                         Arch                           Version                                     Paketquelle                     Größe
==================================================================================================================================================
Aktualisieren:
 curl                            x86_64                         7.29.0-42.el7_4.1                           updates                         267 k
Aktualisiert für Abhängigkeiten:
 libcurl                         x86_64                         7.29.0-42.el7_4.1                           updates                         219 k

Transaktionsübersicht
==================================================================================================================================================
Aktualisieren  1 Paket (+1 Abhängiges Paket)

Gesamte Downloadgröße: 486 k
Is this ok [y/d/N]: y
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
(1/2): curl-7.29.0-42.el7_4.1.x86_64.rpm                                                                                   | 267 kB  00:00:00     
(2/2): libcurl-7.29.0-42.el7_4.1.x86_64.rpm                                                                                | 219 kB  00:00:00     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            1.1 MB/s | 486 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Aktualisieren    : libcurl-7.29.0-42.el7_4.1.x86_64                                                                                         1/4 
  Aktualisieren    : curl-7.29.0-42.el7_4.1.x86_64                                                                                            2/4 
  Aufräumen        : curl-7.29.0-42.el7.x86_64                                                                                                3/4 
  Aufräumen        : libcurl-7.29.0-42.el7.x86_64                                                                                             4/4 
  Überprüfung läuft: curl-7.29.0-42.el7_4.1.x86_64                                                                                            1/4 
  Überprüfung läuft: libcurl-7.29.0-42.el7_4.1.x86_64                                                                                         2/4 
  Überprüfung läuft: libcurl-7.29.0-42.el7.x86_64                                                                                             3/4 
  Überprüfung läuft: curl-7.29.0-42.el7.x86_64                                                                                                4/4 

Aktualisiert:
  curl.x86_64 0:7.29.0-42.el7_4.1                                                                                                                 

Abhängigkeit aktualisiert:
  libcurl.x86_64 0:7.29.0-42.el7_4.1                                                                                                              

Komplett!
[psnebc@localhost ~]$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
--2018-04-09 19:09:23--  https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh
Auflösen des Hostnamen »raw.githubusercontent.com (raw.githubusercontent.com)«... 151.101.112.133
Verbindungsaufbau zu raw.githubusercontent.com (raw.githubusercontent.com)|151.101.112.133|:443... verbunden.
HTTP-Anforderung gesendet, warte auf Antwort... 200 OK
Länge: 4019 (3,9K) [text/plain]
In »»STDOUT«« speichern.

100%[========================================================================================================>] 4.019       --.-K/s   in 0s      

2018-04-09 19:09:23 (110 MB/s) - auf die Standardausgabe geschrieben [4019/4019]

Cloning Oh My Zsh...
Klone nach '/home/psnebc/.oh-my-zsh'...
remote: Counting objects: 852, done.
remote: Compressing objects: 100% (716/716), done.
remote: Total 852 (delta 16), reused 781 (delta 10), pack-reused 0
Empfange Objekte: 100% (852/852), 581.20 KiB | 730.00 KiB/s, done.
Löse Unterschiede auf: 100% (16/16), done.
Looking for an existing zsh config...
Using the Oh My Zsh template file and adding it to ~/.zshrc
Time to change your default shell to zsh!
Shell für psnebc ändern.
Passwort: 
Shell geändert.
         __                                     __   
  ____  / /_     ____ ___  __  __   ____  _____/ /_                                                                                               
 / __ \/ __ \   / __ `__ \/ / / /  /_  / / ___/ __ \                                                                                              
/ /_/ / / / /  / / / / / / /_/ /    / /_(__  ) / / /                                                                                              
\____/_/ /_/  /_/ /_/ /_/\__, /    /___/____/_/ /_/                                                                                               
                        /____/                       ....is now installed!                                                                        

Please look over the ~/.zshrc file to select plugins, themes, and options.                                                                        

p.s. Follow us at https://twitter.com/ohmyzsh.                                                                                                    

p.p.s. Get stickers and t-shirts at https://shop.planetargon.com.                                                                                 

➜  ~ sudo yum install vim
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket vim-enhanced.x86_64 2:7.4.160-2.el7 markiert, um installiert zu werden
--> Abhängigkeit vim-common = 2:7.4.160-2.el7 wird für Paket 2:vim-enhanced-7.4.160-2.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket vim-common.x86_64 2:7.4.160-2.el7 markiert, um installiert zu werden
--> Abhängigkeit vim-filesystem wird für Paket 2:vim-common-7.4.160-2.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket vim-filesystem.x86_64 2:7.4.160-2.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                Arch                           Version                                 Paketquelle                  Größe
==================================================================================================================================================
Installieren:
 vim-enhanced                           x86_64                         2:7.4.160-2.el7                         base                         1.0 M
Als Abhängigkeiten installiert:
 vim-common                             x86_64                         2:7.4.160-2.el7                         base                         5.9 M
 vim-filesystem                         x86_64                         2:7.4.160-2.el7                         base                         9.8 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+2 Abhängige Pakete)

Gesamte Downloadgröße: 7.0 M
Installationsgröße: 23 M
Is this ok [y/d/N]: y
Downloading packages:
(1/3): vim-filesystem-7.4.160-2.el7.x86_64.rpm                                                                             | 9.8 kB  00:00:00     
(2/3): vim-enhanced-7.4.160-2.el7.x86_64.rpm                                                                               | 1.0 MB  00:00:00     
(3/3): vim-common-7.4.160-2.el7.x86_64.rpm                                                                                 | 5.9 MB  00:00:02     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            2.6 MB/s | 7.0 MB  00:00:02     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : 2:vim-filesystem-7.4.160-2.el7.x86_64                                                                                    1/3 
  Installieren     : 2:vim-common-7.4.160-2.el7.x86_64                                                                                        2/3 
  Installieren     : 2:vim-enhanced-7.4.160-2.el7.x86_64                                                                                      3/3 
  Überprüfung läuft: 2:vim-enhanced-7.4.160-2.el7.x86_64                                                                                      1/3 
  Überprüfung läuft: 2:vim-filesystem-7.4.160-2.el7.x86_64                                                                                    2/3 
  Überprüfung läuft: 2:vim-common-7.4.160-2.el7.x86_64                                                                                        3/3 

Installiert:
  vim-enhanced.x86_64 2:7.4.160-2.el7                                                                                                             

Abhängigkeit installiert:
  vim-common.x86_64 2:7.4.160-2.el7                                     vim-filesystem.x86_64 2:7.4.160-2.el7                                    

Komplett!
➜  ~ sudo yum install emacs
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket emacs.x86_64 1:24.3-20.el7_4 markiert, um installiert zu werden
--> Abhängigkeit emacs-common = 1:24.3-20.el7_4 wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libtiff.so.5(LIBTIFF_4.0)(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libpng15.so.15(PNG15_0)(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libjpeg.so.62(LIBJPEG_6.2)(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgnutls.so.28(GNUTLS_1_4)(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit desktop-file-utils wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit dejavu-sans-mono-fonts wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libtiff.so.5()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit librsvg-2.so.2()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libpng15.so.15()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libpangocairo-1.0.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libpango-1.0.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libotf.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libm17n-flt.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libm17n-core.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libjpeg.so.62()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgtk-3.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgnutls.so.28()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgif.so.4()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgdk_pixbuf-2.0.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgdk-3.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libgconf-2.so.4()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libfontconfig.so.1()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libcairo.so.2()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libcairo-gobject.so.2()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libatk-1.0.so.0()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXrender.so.1()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXpm.so.4()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXft.so.2()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libX11.so.6()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libSM.so.6()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libMagickWand.so.5()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libMagickCore.so.5()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Abhängigkeit libICE.so.6()(64bit) wird für Paket 1:emacs-24.3-20.el7_4.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket GConf2.x86_64 0:3.2.6-8.el7 markiert, um installiert zu werden
--> Abhängigkeit /usr/bin/killall wird für Paket GConf2-3.2.6-8.el7.x86_64 verarbeitet
--------> Paket ImageMagick.x86_64 0:6.7.8.9-15.el7_2 markiert, um installiert zu werden
--> Abhängigkeit libwmflite-0.2.so.7()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libltdl.so.7()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libjasper.so.1()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libgs.so.9()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libXt.so.6()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libXext.so.6()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libImath.so.6()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libIlmThread.so.6()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libIlmImf.so.7()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libIexMath.so.6()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libIex.so.6()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--> Abhängigkeit libHalf.so.6()(64bit) wird für Paket ImageMagick-6.7.8.9-15.el7_2.x86_64 verarbeitet
--------> Paket atk.x86_64 0:2.22.0-3.el7 markiert, um installiert zu werden
--------> Paket cairo.x86_64 0:1.14.8-2.el7 markiert, um installiert zu werden
--> Abhängigkeit libxcb.so.1()(64bit) wird für Paket cairo-1.14.8-2.el7.x86_64 verarbeitet
--> Abhängigkeit libxcb-shm.so.0()(64bit) wird für Paket cairo-1.14.8-2.el7.x86_64 verarbeitet
--> Abhängigkeit libxcb-render.so.0()(64bit) wird für Paket cairo-1.14.8-2.el7.x86_64 verarbeitet
--> Abhängigkeit libpixman-1.so.0()(64bit) wird für Paket cairo-1.14.8-2.el7.x86_64 verarbeitet
--> Abhängigkeit libGL.so.1()(64bit) wird für Paket cairo-1.14.8-2.el7.x86_64 verarbeitet
--> Abhängigkeit libEGL.so.1()(64bit) wird für Paket cairo-1.14.8-2.el7.x86_64 verarbeitet
--------> Paket cairo-gobject.x86_64 0:1.14.8-2.el7 markiert, um installiert zu werden
--------> Paket dejavu-sans-mono-fonts.noarch 0:2.33-6.el7 markiert, um installiert zu werden
--> Abhängigkeit dejavu-fonts-common = 2.33-6.el7 wird für Paket dejavu-sans-mono-fonts-2.33-6.el7.noarch verarbeitet
--------> Paket desktop-file-utils.x86_64 0:0.23-1.el7 markiert, um installiert zu werden
--> Abhängigkeit emacs-filesystem wird für Paket desktop-file-utils-0.23-1.el7.x86_64 verarbeitet
--------> Paket emacs-common.x86_64 1:24.3-20.el7_4 markiert, um installiert zu werden
--> Abhängigkeit liblockfile.so.1()(64bit) wird für Paket 1:emacs-common-24.3-20.el7_4.x86_64 verarbeitet
--------> Paket fontconfig.x86_64 0:2.10.95-11.el7 markiert, um installiert zu werden
--> Abhängigkeit fontpackages-filesystem wird für Paket fontconfig-2.10.95-11.el7.x86_64 verarbeitet
--------> Paket gdk-pixbuf2.x86_64 0:2.36.5-1.el7 markiert, um installiert zu werden
--------> Paket giflib.x86_64 0:4.1.6-9.el7 markiert, um installiert zu werden
--------> Paket gnutls.x86_64 0:3.3.26-9.el7 markiert, um installiert zu werden
--> Abhängigkeit trousers >= 0.3.11.2 wird für Paket gnutls-3.3.26-9.el7.x86_64 verarbeitet
--> Abhängigkeit libnettle.so.4()(64bit) wird für Paket gnutls-3.3.26-9.el7.x86_64 verarbeitet
--> Abhängigkeit libhogweed.so.2()(64bit) wird für Paket gnutls-3.3.26-9.el7.x86_64 verarbeitet
--------> Paket gtk3.x86_64 0:3.22.10-5.el7_4 markiert, um installiert zu werden
--> Abhängigkeit libepoxy(x86-64) >= 1.0 wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXrandr(x86-64) >= 1.5.0 wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit hicolor-icon-theme wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit gtk-update-icon-cache wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit dconf(x86-64) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit adwaita-icon-theme wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit librest-0.7.so.0()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libjson-glib-1.0.so.0()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libepoxy.so.0()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libcups.so.2()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libcolord.so.2()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libatk-bridge-2.0.so.0()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXrandr.so.2()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXinerama.so.1()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXi.so.6()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXfixes.so.3()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXdamage.so.1()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXcursor.so.1()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--> Abhängigkeit libXcomposite.so.1()(64bit) wird für Paket gtk3-3.22.10-5.el7_4.x86_64 verarbeitet
--------> Paket libICE.x86_64 0:1.0.9-9.el7 markiert, um installiert zu werden
--------> Paket libSM.x86_64 0:1.2.2-2.el7 markiert, um installiert zu werden
--------> Paket libX11.x86_64 0:1.6.5-1.el7 markiert, um installiert zu werden
--> Abhängigkeit libX11-common >= 1.6.5-1.el7 wird für Paket libX11-1.6.5-1.el7.x86_64 verarbeitet
--------> Paket libXft.x86_64 0:2.3.2-2.el7 markiert, um installiert zu werden
--------> Paket libXpm.x86_64 0:3.5.12-1.el7 markiert, um installiert zu werden
--------> Paket libXrender.x86_64 0:0.9.10-1.el7 markiert, um installiert zu werden
--------> Paket libjpeg-turbo.x86_64 0:1.2.90-5.el7 markiert, um installiert zu werden
--------> Paket libotf.x86_64 0:0.9.13-4.el7 markiert, um installiert zu werden
--> Abhängigkeit libXmu.so.6()(64bit) wird für Paket libotf-0.9.13-4.el7.x86_64 verarbeitet
--> Abhängigkeit libXaw.so.7()(64bit) wird für Paket libotf-0.9.13-4.el7.x86_64 verarbeitet
--------> Paket libpng.x86_64 2:1.5.13-7.el7_2 markiert, um installiert zu werden
--------> Paket librsvg2.x86_64 0:2.40.16-1.el7 markiert, um installiert zu werden
--------> Paket libtiff.x86_64 0:4.0.3-27.el7_3 markiert, um installiert zu werden
--> Abhängigkeit libjbig.so.2.0()(64bit) wird für Paket libtiff-4.0.3-27.el7_3.x86_64 verarbeitet
--------> Paket m17n-lib.x86_64 0:1.6.4-14.el7 markiert, um installiert zu werden
--> Abhängigkeit m17n-db wird für Paket m17n-lib-1.6.4-14.el7.x86_64 verarbeitet
--> Abhängigkeit libthai.so.0(LIBTHAI_0.1)(64bit) wird für Paket m17n-lib-1.6.4-14.el7.x86_64 verarbeitet
--> Abhängigkeit libthai.so.0()(64bit) wird für Paket m17n-lib-1.6.4-14.el7.x86_64 verarbeitet
--------> Paket pango.x86_64 0:1.40.4-1.el7 markiert, um installiert zu werden
--> Abhängigkeit harfbuzz(x86-64) >= 1.0.3 wird für Paket pango-1.40.4-1.el7.x86_64 verarbeitet
--> Abhängigkeit libharfbuzz.so.0()(64bit) wird für Paket pango-1.40.4-1.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket OpenEXR-libs.x86_64 0:1.7.1-7.el7 markiert, um installiert zu werden
--------> Paket adwaita-icon-theme.noarch 0:3.22.0-1.el7 markiert, um installiert zu werden
--> Abhängigkeit adwaita-cursor-theme = 3.22.0-1.el7 wird für Paket adwaita-icon-theme-3.22.0-1.el7.noarch verarbeitet
--------> Paket at-spi2-atk.x86_64 0:2.22.0-2.el7 markiert, um installiert zu werden
--> Abhängigkeit at-spi2-core(x86-64) >= 2.17.90 wird für Paket at-spi2-atk-2.22.0-2.el7.x86_64 verarbeitet
--> Abhängigkeit libatspi.so.0()(64bit) wird für Paket at-spi2-atk-2.22.0-2.el7.x86_64 verarbeitet
--------> Paket colord-libs.x86_64 0:1.3.4-1.el7 markiert, um installiert zu werden
--> Abhängigkeit libgusb.so.2(LIBGUSB_0.1.1)(64bit) wird für Paket colord-libs-1.3.4-1.el7.x86_64 verarbeitet
--> Abhängigkeit libgusb.so.2(LIBGUSB_0.1.0)(64bit) wird für Paket colord-libs-1.3.4-1.el7.x86_64 verarbeitet
--> Abhängigkeit libusb-1.0.so.0()(64bit) wird für Paket colord-libs-1.3.4-1.el7.x86_64 verarbeitet
--> Abhängigkeit liblcms2.so.2()(64bit) wird für Paket colord-libs-1.3.4-1.el7.x86_64 verarbeitet
--> Abhängigkeit libgusb.so.2()(64bit) wird für Paket colord-libs-1.3.4-1.el7.x86_64 verarbeitet
--------> Paket cups-libs.x86_64 1:1.6.3-29.el7 markiert, um installiert zu werden
--> Abhängigkeit libavahi-common.so.3()(64bit) wird für Paket 1:cups-libs-1.6.3-29.el7.x86_64 verarbeitet
--> Abhängigkeit libavahi-client.so.3()(64bit) wird für Paket 1:cups-libs-1.6.3-29.el7.x86_64 verarbeitet
--------> Paket dconf.x86_64 0:0.26.0-2.el7 markiert, um installiert zu werden
--------> Paket dejavu-fonts-common.noarch 0:2.33-6.el7 markiert, um installiert zu werden
--------> Paket emacs-filesystem.noarch 1:24.3-20.el7_4 markiert, um installiert zu werden
--------> Paket fontpackages-filesystem.noarch 0:1.44-8.el7 markiert, um installiert zu werden
--------> Paket ghostscript.x86_64 0:9.07-28.el7_4.2 markiert, um installiert zu werden
--> Abhängigkeit urw-fonts >= 1.1 wird für Paket ghostscript-9.07-28.el7_4.2.x86_64 verarbeitet
--> Abhängigkeit poppler-data wird für Paket ghostscript-9.07-28.el7_4.2.x86_64 verarbeitet
--> Abhängigkeit ghostscript-fonts wird für Paket ghostscript-9.07-28.el7_4.2.x86_64 verarbeitet
--------> Paket gtk-update-icon-cache.x86_64 0:3.22.10-5.el7_4 markiert, um installiert zu werden
--------> Paket harfbuzz.x86_64 0:1.3.2-1.el7 markiert, um installiert zu werden
--> Abhängigkeit libgraphite2.so.3()(64bit) wird für Paket harfbuzz-1.3.2-1.el7.x86_64 verarbeitet
--------> Paket hicolor-icon-theme.noarch 0:0.12-7.el7 markiert, um installiert zu werden
--------> Paket ilmbase.x86_64 0:1.0.3-7.el7 markiert, um installiert zu werden
--------> Paket jasper-libs.x86_64 0:1.900.1-31.el7 markiert, um installiert zu werden
--------> Paket jbigkit-libs.x86_64 0:2.0-11.el7 markiert, um installiert zu werden
--------> Paket json-glib.x86_64 0:1.2.6-1.el7 markiert, um installiert zu werden
--------> Paket libX11-common.noarch 0:1.6.5-1.el7 markiert, um installiert zu werden
--------> Paket libXaw.x86_64 0:1.0.13-4.el7 markiert, um installiert zu werden
--------> Paket libXcomposite.x86_64 0:0.4.4-4.1.el7 markiert, um installiert zu werden
--------> Paket libXcursor.x86_64 0:1.1.14-8.el7 markiert, um installiert zu werden
--------> Paket libXdamage.x86_64 0:1.1.4-4.1.el7 markiert, um installiert zu werden
--------> Paket libXext.x86_64 0:1.3.3-3.el7 markiert, um installiert zu werden
--------> Paket libXfixes.x86_64 0:5.0.3-1.el7 markiert, um installiert zu werden
--------> Paket libXi.x86_64 0:1.7.9-1.el7 markiert, um installiert zu werden
--------> Paket libXinerama.x86_64 0:1.1.3-2.1.el7 markiert, um installiert zu werden
--------> Paket libXmu.x86_64 0:1.1.2-2.el7 markiert, um installiert zu werden
--------> Paket libXrandr.x86_64 0:1.5.1-2.el7 markiert, um installiert zu werden
--------> Paket libXt.x86_64 0:1.1.5-3.el7 markiert, um installiert zu werden
--------> Paket libepoxy.x86_64 0:1.3.1-1.el7 markiert, um installiert zu werden
--------> Paket liblockfile.x86_64 0:1.08-17.el7 markiert, um installiert zu werden
--------> Paket libthai.x86_64 0:0.1.14-9.el7 markiert, um installiert zu werden
--------> Paket libtool-ltdl.x86_64 0:2.4.2-22.el7_3 markiert, um installiert zu werden
--------> Paket libwmf-lite.x86_64 0:0.2.8.4-41.el7_1 markiert, um installiert zu werden
--------> Paket libxcb.x86_64 0:1.12-1.el7 markiert, um installiert zu werden
--> Abhängigkeit libXau.so.6()(64bit) wird für Paket libxcb-1.12-1.el7.x86_64 verarbeitet
--------> Paket m17n-db.noarch 0:1.6.4-3.el7 markiert, um installiert zu werden
--------> Paket mesa-libEGL.x86_64 0:17.0.1-6.20170307.el7 markiert, um installiert zu werden
--> Abhängigkeit mesa-libgbm = 17.0.1-6.20170307.el7 wird für Paket mesa-libEGL-17.0.1-6.20170307.el7.x86_64 verarbeitet
--> Abhängigkeit libxshmfence.so.1()(64bit) wird für Paket mesa-libEGL-17.0.1-6.20170307.el7.x86_64 verarbeitet
--> Abhängigkeit libgbm.so.1()(64bit) wird für Paket mesa-libEGL-17.0.1-6.20170307.el7.x86_64 verarbeitet
--------> Paket mesa-libGL.x86_64 0:17.0.1-6.20170307.el7 markiert, um installiert zu werden
--> Abhängigkeit mesa-libglapi = 17.0.1-6.20170307.el7 wird für Paket mesa-libGL-17.0.1-6.20170307.el7.x86_64 verarbeitet
--> Abhängigkeit libglapi.so.0()(64bit) wird für Paket mesa-libGL-17.0.1-6.20170307.el7.x86_64 verarbeitet
--> Abhängigkeit libXxf86vm.so.1()(64bit) wird für Paket mesa-libGL-17.0.1-6.20170307.el7.x86_64 verarbeitet
--------> Paket nettle.x86_64 0:2.7.1-8.el7 markiert, um installiert zu werden
--------> Paket pixman.x86_64 0:0.34.0-1.el7 markiert, um installiert zu werden
--------> Paket psmisc.x86_64 0:22.20-15.el7 markiert, um installiert zu werden
--------> Paket rest.x86_64 0:0.8.0-1.el7 markiert, um installiert zu werden
--> Abhängigkeit libsoup-gnome-2.4.so.1()(64bit) wird für Paket rest-0.8.0-1.el7.x86_64 verarbeitet
--> Abhängigkeit libsoup-2.4.so.1()(64bit) wird für Paket rest-0.8.0-1.el7.x86_64 verarbeitet
--------> Paket trousers.x86_64 0:0.3.14-2.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket adwaita-cursor-theme.noarch 0:3.22.0-1.el7 markiert, um installiert zu werden
--------> Paket at-spi2-core.x86_64 0:2.22.0-1.el7 markiert, um installiert zu werden
--> Abhängigkeit libXtst.so.6()(64bit) wird für Paket at-spi2-core-2.22.0-1.el7.x86_64 verarbeitet
--------> Paket avahi-libs.x86_64 0:0.6.31-17.el7 markiert, um installiert zu werden
--------> Paket ghostscript-fonts.noarch 0:5.50-32.el7 markiert, um installiert zu werden
--> Abhängigkeit xorg-x11-font-utils wird für Paket ghostscript-fonts-5.50-32.el7.noarch verarbeitet
--------> Paket graphite2.x86_64 0:1.3.10-1.el7_3 markiert, um installiert zu werden
--------> Paket lcms2.x86_64 0:2.6-3.el7 markiert, um installiert zu werden
--------> Paket libXau.x86_64 0:1.0.8-2.1.el7 markiert, um installiert zu werden
--------> Paket libXxf86vm.x86_64 0:1.1.4-1.el7 markiert, um installiert zu werden
--------> Paket libgusb.x86_64 0:0.2.9-1.el7 markiert, um installiert zu werden
--------> Paket libsoup.x86_64 0:2.56.0-4.el7_4 markiert, um installiert zu werden
--> Abhängigkeit glib-networking(x86-64) >= 2.38.0 wird für Paket libsoup-2.56.0-4.el7_4.x86_64 verarbeitet
--------> Paket libusbx.x86_64 0:1.0.20-1.el7 markiert, um installiert zu werden
--------> Paket libxshmfence.x86_64 0:1.2-1.el7 markiert, um installiert zu werden
--------> Paket mesa-libgbm.x86_64 0:17.0.1-6.20170307.el7 markiert, um installiert zu werden
--------> Paket mesa-libglapi.x86_64 0:17.0.1-6.20170307.el7 markiert, um installiert zu werden
--------> Paket poppler-data.noarch 0:0.4.6-3.el7 markiert, um installiert zu werden
--------> Paket urw-fonts.noarch 0:2.4-16.el7 markiert, um installiert zu werden
--> Transaktionsprüfung wird ausgeführt
--------> Paket glib-networking.x86_64 0:2.50.0-1.el7 markiert, um installiert zu werden
--> Abhängigkeit gsettings-desktop-schemas wird für Paket glib-networking-2.50.0-1.el7.x86_64 verarbeitet
--> Abhängigkeit libproxy.so.1()(64bit) wird für Paket glib-networking-2.50.0-1.el7.x86_64 verarbeitet
--------> Paket libXtst.x86_64 0:1.2.3-1.el7 markiert, um installiert zu werden
--------> Paket xorg-x11-font-utils.x86_64 1:7.5-20.el7 markiert, um installiert zu werden
--> Abhängigkeit libfontenc.so.1()(64bit) wird für Paket 1:xorg-x11-font-utils-7.5-20.el7.x86_64 verarbeitet
--> Abhängigkeit libXfont.so.1()(64bit) wird für Paket 1:xorg-x11-font-utils-7.5-20.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket gsettings-desktop-schemas.x86_64 0:3.22.0-1.el7 markiert, um installiert zu werden
--------> Paket libXfont.x86_64 0:1.5.2-1.el7 markiert, um installiert zu werden
--------> Paket libfontenc.x86_64 0:1.1.3-3.el7 markiert, um installiert zu werden
--------> Paket libproxy.x86_64 0:0.4.11-10.el7 markiert, um installiert zu werden
--> Abhängigkeit libmodman.so.1()(64bit) wird für Paket libproxy-0.4.11-10.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket libmodman.x86_64 0:2.0.1-8.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                      Arch                      Version                                  Paketquelle                Größe
==================================================================================================================================================
Installieren:
 emacs                                        x86_64                    1:24.3-20.el7_4                          updates                    2.9 M
Als Abhängigkeiten installiert:
 GConf2                                       x86_64                    3.2.6-8.el7                              base                       1.0 M
 ImageMagick                                  x86_64                    6.7.8.9-15.el7_2                         base                       2.1 M
 OpenEXR-libs                                 x86_64                    1.7.1-7.el7                              base                       217 k
 adwaita-cursor-theme                         noarch                    3.22.0-1.el7                             base                       641 k
 adwaita-icon-theme                           noarch                    3.22.0-1.el7                             base                        11 M
 at-spi2-atk                                  x86_64                    2.22.0-2.el7                             base                        80 k
 at-spi2-core                                 x86_64                    2.22.0-1.el7                             base                       157 k
 atk                                          x86_64                    2.22.0-3.el7                             base                       258 k
 avahi-libs                                   x86_64                    0.6.31-17.el7                            base                        61 k
 cairo                                        x86_64                    1.14.8-2.el7                             base                       713 k
 cairo-gobject                                x86_64                    1.14.8-2.el7                             base                        25 k
 colord-libs                                  x86_64                    1.3.4-1.el7                              base                       185 k
 cups-libs                                    x86_64                    1:1.6.3-29.el7                           base                       356 k
 dconf                                        x86_64                    0.26.0-2.el7                             base                       107 k
 dejavu-fonts-common                          noarch                    2.33-6.el7                               base                        64 k
 dejavu-sans-mono-fonts                       noarch                    2.33-6.el7                               base                       433 k
 desktop-file-utils                           x86_64                    0.23-1.el7                               base                        67 k
 emacs-common                                 x86_64                    1:24.3-20.el7_4                          updates                     20 M
 emacs-filesystem                             noarch                    1:24.3-20.el7_4                          updates                     58 k
 fontconfig                                   x86_64                    2.10.95-11.el7                           base                       229 k
 fontpackages-filesystem                      noarch                    1.44-8.el7                               base                       9.9 k
 gdk-pixbuf2                                  x86_64                    2.36.5-1.el7                             base                       567 k
 ghostscript                                  x86_64                    9.07-28.el7_4.2                          updates                    4.3 M
 ghostscript-fonts                            noarch                    5.50-32.el7                              base                       324 k
 giflib                                       x86_64                    4.1.6-9.el7                              base                        40 k
 glib-networking                              x86_64                    2.50.0-1.el7                             base                       131 k
 gnutls                                       x86_64                    3.3.26-9.el7                             base                       677 k
 graphite2                                    x86_64                    1.3.10-1.el7_3                           updates                    115 k
 gsettings-desktop-schemas                    x86_64                    3.22.0-1.el7                             base                       550 k
 gtk-update-icon-cache                        x86_64                    3.22.10-5.el7_4                          updates                     28 k
 gtk3                                         x86_64                    3.22.10-5.el7_4                          updates                    4.2 M
 harfbuzz                                     x86_64                    1.3.2-1.el7                              base                       177 k
 hicolor-icon-theme                           noarch                    0.12-7.el7                               base                        42 k
 ilmbase                                      x86_64                    1.0.3-7.el7                              base                       100 k
 jasper-libs                                  x86_64                    1.900.1-31.el7                           base                       150 k
 jbigkit-libs                                 x86_64                    2.0-11.el7                               base                        46 k
 json-glib                                    x86_64                    1.2.6-1.el7                              base                       133 k
 lcms2                                        x86_64                    2.6-3.el7                                base                       150 k
 libICE                                       x86_64                    1.0.9-9.el7                              base                        66 k
 libSM                                        x86_64                    1.2.2-2.el7                              base                        39 k
 libX11                                       x86_64                    1.6.5-1.el7                              base                       606 k
 libX11-common                                noarch                    1.6.5-1.el7                              base                       164 k
 libXau                                       x86_64                    1.0.8-2.1.el7                            base                        29 k
 libXaw                                       x86_64                    1.0.13-4.el7                             base                       192 k
 libXcomposite                                x86_64                    0.4.4-4.1.el7                            base                        22 k
 libXcursor                                   x86_64                    1.1.14-8.el7                             base                        30 k
 libXdamage                                   x86_64                    1.1.4-4.1.el7                            base                        20 k
 libXext                                      x86_64                    1.3.3-3.el7                              base                        39 k
 libXfixes                                    x86_64                    5.0.3-1.el7                              base                        18 k
 libXfont                                     x86_64                    1.5.2-1.el7                              base                       152 k
 libXft                                       x86_64                    2.3.2-2.el7                              base                        58 k
 libXi                                        x86_64                    1.7.9-1.el7                              base                        40 k
 libXinerama                                  x86_64                    1.1.3-2.1.el7                            base                        14 k
 libXmu                                       x86_64                    1.1.2-2.el7                              base                        71 k
 libXpm                                       x86_64                    3.5.12-1.el7                             base                        55 k
 libXrandr                                    x86_64                    1.5.1-2.el7                              base                        27 k
 libXrender                                   x86_64                    0.9.10-1.el7                             base                        26 k
 libXt                                        x86_64                    1.1.5-3.el7                              base                       173 k
 libXtst                                      x86_64                    1.2.3-1.el7                              base                        20 k
 libXxf86vm                                   x86_64                    1.1.4-1.el7                              base                        18 k
 libepoxy                                     x86_64                    1.3.1-1.el7                              base                       196 k
 libfontenc                                   x86_64                    1.1.3-3.el7                              base                        31 k
 libgusb                                      x86_64                    0.2.9-1.el7                              base                        40 k
 libjpeg-turbo                                x86_64                    1.2.90-5.el7                             base                       134 k
 liblockfile                                  x86_64                    1.08-17.el7                              base                        21 k
 libmodman                                    x86_64                    2.0.1-8.el7                              base                        28 k
 libotf                                       x86_64                    0.9.13-4.el7                             base                        92 k
 libpng                                       x86_64                    2:1.5.13-7.el7_2                         base                       213 k
 libproxy                                     x86_64                    0.4.11-10.el7                            base                        64 k
 librsvg2                                     x86_64                    2.40.16-1.el7                            base                       128 k
 libsoup                                      x86_64                    2.56.0-4.el7_4                           updates                    398 k
 libthai                                      x86_64                    0.1.14-9.el7                             base                       187 k
 libtiff                                      x86_64                    4.0.3-27.el7_3                           base                       170 k
 libtool-ltdl                                 x86_64                    2.4.2-22.el7_3                           base                        49 k
 libusbx                                      x86_64                    1.0.20-1.el7                             base                        61 k
 libwmf-lite                                  x86_64                    0.2.8.4-41.el7_1                         base                        66 k
 libxcb                                       x86_64                    1.12-1.el7                               base                       211 k
 libxshmfence                                 x86_64                    1.2-1.el7                                base                       7.2 k
 m17n-db                                      noarch                    1.6.4-3.el7                              base                       224 k
 m17n-lib                                     x86_64                    1.6.4-14.el7                             base                       185 k
 mesa-libEGL                                  x86_64                    17.0.1-6.20170307.el7                    base                        82 k
 mesa-libGL                                   x86_64                    17.0.1-6.20170307.el7                    base                       155 k
 mesa-libgbm                                  x86_64                    17.0.1-6.20170307.el7                    base                        32 k
 mesa-libglapi                                x86_64                    17.0.1-6.20170307.el7                    base                        41 k
 nettle                                       x86_64                    2.7.1-8.el7                              base                       327 k
 pango                                        x86_64                    1.40.4-1.el7                             base                       275 k
 pixman                                       x86_64                    0.34.0-1.el7                             base                       248 k
 poppler-data                                 noarch                    0.4.6-3.el7                              base                       2.2 M
 psmisc                                       x86_64                    22.20-15.el7                             base                       141 k
 rest                                         x86_64                    0.8.0-1.el7                              base                        63 k
 trousers                                     x86_64                    0.3.14-2.el7                             base                       289 k
 urw-fonts                                    noarch                    2.4-16.el7                               base                       3.0 M
 xorg-x11-font-utils                          x86_64                    1:7.5-20.el7                             base                        87 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+93 Abhängige Pakete)

Gesamte Downloadgröße: 64 M
Installationsgröße: 207 M
Is this ok [y/d/N]: y
Downloading packages:
(1/94): adwaita-cursor-theme-3.22.0-1.el7.noarch.rpm                                                                       | 641 kB  00:00:00     
(2/94): at-spi2-atk-2.22.0-2.el7.x86_64.rpm                                                                                |  80 kB  00:00:00     
(3/94): at-spi2-core-2.22.0-1.el7.x86_64.rpm                                                                               | 157 kB  00:00:00     
(4/94): GConf2-3.2.6-8.el7.x86_64.rpm                                                                                      | 1.0 MB  00:00:00     
(5/94): OpenEXR-libs-1.7.1-7.el7.x86_64.rpm                                                                                | 217 kB  00:00:00     
(6/94): avahi-libs-0.6.31-17.el7.x86_64.rpm                                                                                |  61 kB  00:00:00     
(7/94): cairo-gobject-1.14.8-2.el7.x86_64.rpm                                                                              |  25 kB  00:00:00     
(8/94): atk-2.22.0-3.el7.x86_64.rpm                                                                                        | 258 kB  00:00:00     
(9/94): colord-libs-1.3.4-1.el7.x86_64.rpm                                                                                 | 185 kB  00:00:00     
(10/94): dconf-0.26.0-2.el7.x86_64.rpm                                                                                     | 107 kB  00:00:00     
(11/94): dejavu-fonts-common-2.33-6.el7.noarch.rpm                                                                         |  64 kB  00:00:00     
(12/94): cups-libs-1.6.3-29.el7.x86_64.rpm                                                                                 | 356 kB  00:00:00     
(13/94): desktop-file-utils-0.23-1.el7.x86_64.rpm                                                                          |  67 kB  00:00:00     
(14/94): cairo-1.14.8-2.el7.x86_64.rpm                                                                                     | 713 kB  00:00:01     
(15/94): dejavu-sans-mono-fonts-2.33-6.el7.noarch.rpm                                                                      | 433 kB  00:00:00     
(16/94): emacs-filesystem-24.3-20.el7_4.noarch.rpm                                                                         |  58 kB  00:00:00     
(17/94): fontconfig-2.10.95-11.el7.x86_64.rpm                                                                              | 229 kB  00:00:00     
(18/94): fontpackages-filesystem-1.44-8.el7.noarch.rpm                                                                     | 9.9 kB  00:00:00     
(19/94): ImageMagick-6.7.8.9-15.el7_2.x86_64.rpm                                                                           | 2.1 MB  00:00:03     
(20/94): gdk-pixbuf2-2.36.5-1.el7.x86_64.rpm                                                                               | 567 kB  00:00:00     
(21/94): ghostscript-fonts-5.50-32.el7.noarch.rpm                                                                          | 324 kB  00:00:00     
(22/94): giflib-4.1.6-9.el7.x86_64.rpm                                                                                     |  40 kB  00:00:00     
(23/94): glib-networking-2.50.0-1.el7.x86_64.rpm                                                                           | 131 kB  00:00:00     
(24/94): emacs-24.3-20.el7_4.x86_64.rpm                                                                                    | 2.9 MB  00:00:03     
(25/94): graphite2-1.3.10-1.el7_3.x86_64.rpm                                                                               | 115 kB  00:00:00     
(26/94): gnutls-3.3.26-9.el7.x86_64.rpm                                                                                    | 677 kB  00:00:01     
(27/94): gtk-update-icon-cache-3.22.10-5.el7_4.x86_64.rpm                                                                  |  28 kB  00:00:00     
(28/94): gsettings-desktop-schemas-3.22.0-1.el7.x86_64.rpm                                                                 | 550 kB  00:00:01     
(29/94): harfbuzz-1.3.2-1.el7.x86_64.rpm                                                                                   | 177 kB  00:00:00     
(30/94): hicolor-icon-theme-0.12-7.el7.noarch.rpm                                                                          |  42 kB  00:00:00     
(31/94): ilmbase-1.0.3-7.el7.x86_64.rpm                                                                                    | 100 kB  00:00:00     
(32/94): adwaita-icon-theme-3.22.0-1.el7.noarch.rpm                                                                        |  11 MB  00:00:08     
(33/94): jasper-libs-1.900.1-31.el7.x86_64.rpm                                                                             | 150 kB  00:00:00     
(34/94): jbigkit-libs-2.0-11.el7.x86_64.rpm                                                                                |  46 kB  00:00:00     
(35/94): lcms2-2.6-3.el7.x86_64.rpm                                                                                        | 150 kB  00:00:00     
(36/94): json-glib-1.2.6-1.el7.x86_64.rpm                                                                                  | 133 kB  00:00:00     
(37/94): libICE-1.0.9-9.el7.x86_64.rpm                                                                                     |  66 kB  00:00:00     
(38/94): libSM-1.2.2-2.el7.x86_64.rpm                                                                                      |  39 kB  00:00:00     
(39/94): libX11-common-1.6.5-1.el7.noarch.rpm                                                                              | 164 kB  00:00:00     
(40/94): libXau-1.0.8-2.1.el7.x86_64.rpm                                                                                   |  29 kB  00:00:00     
(41/94): libX11-1.6.5-1.el7.x86_64.rpm                                                                                     | 606 kB  00:00:00     
(42/94): libXcomposite-0.4.4-4.1.el7.x86_64.rpm                                                                            |  22 kB  00:00:00     
(43/94): libXaw-1.0.13-4.el7.x86_64.rpm                                                                                    | 192 kB  00:00:00     
(44/94): libXcursor-1.1.14-8.el7.x86_64.rpm                                                                                |  30 kB  00:00:00     
(45/94): libXext-1.3.3-3.el7.x86_64.rpm                                                                                    |  39 kB  00:00:00     
(46/94): libXdamage-1.1.4-4.1.el7.x86_64.rpm                                                                               |  20 kB  00:00:00     
(47/94): libXfixes-5.0.3-1.el7.x86_64.rpm                                                                                  |  18 kB  00:00:00     
(48/94): libXft-2.3.2-2.el7.x86_64.rpm                                                                                     |  58 kB  00:00:00     
(49/94): libXi-1.7.9-1.el7.x86_64.rpm                                                                                      |  40 kB  00:00:00     
(50/94): libXinerama-1.1.3-2.1.el7.x86_64.rpm                                                                              |  14 kB  00:00:00     
(51/94): libXmu-1.1.2-2.el7.x86_64.rpm                                                                                     |  71 kB  00:00:00     
(52/94): libXpm-3.5.12-1.el7.x86_64.rpm                                                                                    |  55 kB  00:00:00     
(53/94): libXfont-1.5.2-1.el7.x86_64.rpm                                                                                   | 152 kB  00:00:00     
(54/94): libXrandr-1.5.1-2.el7.x86_64.rpm                                                                                  |  27 kB  00:00:00     
(55/94): libXrender-0.9.10-1.el7.x86_64.rpm                                                                                |  26 kB  00:00:00     
(56/94): libXtst-1.2.3-1.el7.x86_64.rpm                                                                                    |  20 kB  00:00:00     
(57/94): libXxf86vm-1.1.4-1.el7.x86_64.rpm                                                                                 |  18 kB  00:00:00     
(58/94): libXt-1.1.5-3.el7.x86_64.rpm                                                                                      | 173 kB  00:00:00     
(59/94): libfontenc-1.1.3-3.el7.x86_64.rpm                                                                                 |  31 kB  00:00:00     
(60/94): libgusb-0.2.9-1.el7.x86_64.rpm                                                                                    |  40 kB  00:00:00     
(61/94): libjpeg-turbo-1.2.90-5.el7.x86_64.rpm                                                                             | 134 kB  00:00:00     
(62/94): libepoxy-1.3.1-1.el7.x86_64.rpm                                                                                   | 196 kB  00:00:00     
(63/94): liblockfile-1.08-17.el7.x86_64.rpm                                                                                |  21 kB  00:00:00     
(64/94): libmodman-2.0.1-8.el7.x86_64.rpm                                                                                  |  28 kB  00:00:00     
(65/94): libotf-0.9.13-4.el7.x86_64.rpm                                                                                    |  92 kB  00:00:00     
(66/94): ghostscript-9.07-28.el7_4.2.x86_64.rpm                                                                            | 4.3 MB  00:00:07     
(67/94): libproxy-0.4.11-10.el7.x86_64.rpm                                                                                 |  64 kB  00:00:00     
(68/94): libpng-1.5.13-7.el7_2.x86_64.rpm                                                                                  | 213 kB  00:00:00     
(69/94): librsvg2-2.40.16-1.el7.x86_64.rpm                                                                                 | 128 kB  00:00:00     
(70/94): libsoup-2.56.0-4.el7_4.x86_64.rpm                                                                                 | 398 kB  00:00:00     
(71/94): libthai-0.1.14-9.el7.x86_64.rpm                                                                                   | 187 kB  00:00:00     
(72/94): libtiff-4.0.3-27.el7_3.x86_64.rpm                                                                                 | 170 kB  00:00:00     
(73/94): libusbx-1.0.20-1.el7.x86_64.rpm                                                                                   |  61 kB  00:00:00     
(74/94): gtk3-3.22.10-5.el7_4.x86_64.rpm                                                                                   | 4.2 MB  00:00:05     
(75/94): libtool-ltdl-2.4.2-22.el7_3.x86_64.rpm                                                                            |  49 kB  00:00:00     
(76/94): libwmf-lite-0.2.8.4-41.el7_1.x86_64.rpm                                                                           |  66 kB  00:00:00     
(77/94): libxshmfence-1.2-1.el7.x86_64.rpm                                                                                 | 7.2 kB  00:00:00     
(78/94): libxcb-1.12-1.el7.x86_64.rpm                                                                                      | 211 kB  00:00:00     
(79/94): mesa-libEGL-17.0.1-6.20170307.el7.x86_64.rpm                                                                      |  82 kB  00:00:00     
(80/94): mesa-libgbm-17.0.1-6.20170307.el7.x86_64.rpm                                                                      |  32 kB  00:00:00     
(81/94): mesa-libGL-17.0.1-6.20170307.el7.x86_64.rpm                                                                       | 155 kB  00:00:00     
(82/94): m17n-lib-1.6.4-14.el7.x86_64.rpm                                                                                  | 185 kB  00:00:00     
(83/94): mesa-libglapi-17.0.1-6.20170307.el7.x86_64.rpm                                                                    |  41 kB  00:00:00     
(84/94): m17n-db-1.6.4-3.el7.noarch.rpm                                                                                    | 224 kB  00:00:00     
(85/94): nettle-2.7.1-8.el7.x86_64.rpm                                                                                     | 327 kB  00:00:00     
(86/94): pixman-0.34.0-1.el7.x86_64.rpm                                                                                    | 248 kB  00:00:00     
(87/94): pango-1.40.4-1.el7.x86_64.rpm                                                                                     | 275 kB  00:00:00     
(88/94): rest-0.8.0-1.el7.x86_64.rpm                                                                                       |  63 kB  00:00:00     
(89/94): psmisc-22.20-15.el7.x86_64.rpm                                                                                    | 141 kB  00:00:00     
(90/94): xorg-x11-font-utils-7.5-20.el7.x86_64.rpm                                                                         |  87 kB  00:00:00     
(91/94): trousers-0.3.14-2.el7.x86_64.rpm                                                                                  | 289 kB  00:00:00     
(92/94): poppler-data-0.4.6-3.el7.noarch.rpm                                                                               | 2.2 MB  00:00:01     
(93/94): urw-fonts-2.4-16.el7.noarch.rpm                                                                                   | 3.0 MB  00:00:01     
(94/94): emacs-common-24.3-20.el7_4.x86_64.rpm                                                                             |  20 MB  00:00:19     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            3.0 MB/s |  64 MB  00:00:21     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : libICE-1.0.9-9.el7.x86_64                                                                                               1/94 
  Installieren     : 2:libpng-1.5.13-7.el7_2.x86_64                                                                                          2/94 
  Installieren     : libSM-1.2.2-2.el7.x86_64                                                                                                3/94 
  Installieren     : libjpeg-turbo-1.2.90-5.el7.x86_64                                                                                       4/94 
  Installieren     : atk-2.22.0-3.el7.x86_64                                                                                                 5/94 
  Installieren     : jasper-libs-1.900.1-31.el7.x86_64                                                                                       6/94 
  Installieren     : libfontenc-1.1.3-3.el7.x86_64                                                                                           7/94 
  Installieren     : lcms2-2.6-3.el7.x86_64                                                                                                  8/94 
  Installieren     : libthai-0.1.14-9.el7.x86_64                                                                                             9/94 
  Installieren     : libxshmfence-1.2-1.el7.x86_64                                                                                          10/94 
  Installieren     : fontpackages-filesystem-1.44-8.el7.noarch                                                                              11/94 
  Installieren     : pixman-0.34.0-1.el7.x86_64                                                                                             12/94 
  Installieren     : libusbx-1.0.20-1.el7.x86_64                                                                                            13/94 
  Installieren     : 1:emacs-filesystem-24.3-20.el7_4.noarch                                                                                14/94 
  Installieren     : mesa-libglapi-17.0.1-6.20170307.el7.x86_64                                                                             15/94 
  Installieren     : ilmbase-1.0.3-7.el7.x86_64                                                                                             16/94 
  Installieren     : OpenEXR-libs-1.7.1-7.el7.x86_64                                                                                        17/94 
  Installieren     : mesa-libgbm-17.0.1-6.20170307.el7.x86_64                                                                               18/94 
  Installieren     : desktop-file-utils-0.23-1.el7.x86_64                                                                                   19/94 
  Installieren     : libgusb-0.2.9-1.el7.x86_64                                                                                             20/94 
  Installieren     : colord-libs-1.3.4-1.el7.x86_64                                                                                         21/94 
  Installieren     : dejavu-fonts-common-2.33-6.el7.noarch                                                                                  22/94 
  Installieren     : dejavu-sans-mono-fonts-2.33-6.el7.noarch                                                                               23/94 
  Installieren     : fontconfig-2.10.95-11.el7.x86_64                                                                                       24/94 
  Installieren     : libXfont-1.5.2-1.el7.x86_64                                                                                            25/94 
  Installieren     : 1:xorg-x11-font-utils-7.5-20.el7.x86_64                                                                                26/94 
  Installieren     : ghostscript-fonts-5.50-32.el7.noarch                                                                                   27/94 
  Installieren     : urw-fonts-2.4-16.el7.noarch                                                                                            28/94 
  Installieren     : jbigkit-libs-2.0-11.el7.x86_64                                                                                         29/94 
  Installieren     : libtiff-4.0.3-27.el7_3.x86_64                                                                                          30/94 
  Installieren     : json-glib-1.2.6-1.el7.x86_64                                                                                           31/94 
  Installieren     : poppler-data-0.4.6-3.el7.noarch                                                                                        32/94 
  Installieren     : hicolor-icon-theme-0.12-7.el7.noarch                                                                                   33/94 
  Installieren     : trousers-0.3.14-2.el7.x86_64                                                                                           34/94 
  Installieren     : m17n-db-1.6.4-3.el7.noarch                                                                                             35/94 
  Installieren     : m17n-lib-1.6.4-14.el7.x86_64                                                                                           36/94 
  Installieren     : graphite2-1.3.10-1.el7_3.x86_64                                                                                        37/94 
  Installieren     : harfbuzz-1.3.2-1.el7.x86_64                                                                                            38/94 
  Installieren     : gsettings-desktop-schemas-3.22.0-1.el7.x86_64                                                                          39/94 
  Installieren     : libmodman-2.0.1-8.el7.x86_64                                                                                           40/94 
  Installieren     : libproxy-0.4.11-10.el7.x86_64                                                                                          41/94 
  Installieren     : libepoxy-1.3.1-1.el7.x86_64                                                                                            42/94 
  Installieren     : psmisc-22.20-15.el7.x86_64                                                                                             43/94 
  Installieren     : GConf2-3.2.6-8.el7.x86_64                                                                                              44/94 
  Installieren     : liblockfile-1.08-17.el7.x86_64                                                                                         45/94 
  Installieren     : 1:emacs-common-24.3-20.el7_4.x86_64                                                                                    46/94 
  Installieren     : avahi-libs-0.6.31-17.el7.x86_64                                                                                        47/94 
  Installieren     : 1:cups-libs-1.6.3-29.el7.x86_64                                                                                        48/94 
  Installieren     : adwaita-cursor-theme-3.22.0-1.el7.noarch                                                                               49/94 
  Installieren     : adwaita-icon-theme-3.22.0-1.el7.noarch                                                                                 50/94 
  Installieren     : libXau-1.0.8-2.1.el7.x86_64                                                                                            51/94 
  Installieren     : libxcb-1.12-1.el7.x86_64                                                                                               52/94 
  Installieren     : libX11-common-1.6.5-1.el7.noarch                                                                                       53/94 
  Installieren     : libX11-1.6.5-1.el7.x86_64                                                                                              54/94 
  Installieren     : libXext-1.3.3-3.el7.x86_64                                                                                             55/94 
  Installieren     : libXrender-0.9.10-1.el7.x86_64                                                                                         56/94 
  Installieren     : libXt-1.1.5-3.el7.x86_64                                                                                               57/94 
  Installieren     : gdk-pixbuf2-2.36.5-1.el7.x86_64                                                                                        58/94 
  Installieren     : libXfixes-5.0.3-1.el7.x86_64                                                                                           59/94 
  Installieren     : libXdamage-1.1.4-4.1.el7.x86_64                                                                                        60/94 
  Installieren     : libXmu-1.1.2-2.el7.x86_64                                                                                              61/94 
  Installieren     : libXft-2.3.2-2.el7.x86_64                                                                                              62/94 
  Installieren     : libXi-1.7.9-1.el7.x86_64                                                                                               63/94 
  Installieren     : libXpm-3.5.12-1.el7.x86_64                                                                                             64/94 
  Installieren     : mesa-libEGL-17.0.1-6.20170307.el7.x86_64                                                                               65/94 
  Installieren     : libXaw-1.0.13-4.el7.x86_64                                                                                             66/94 
  Installieren     : libotf-0.9.13-4.el7.x86_64                                                                                             67/94 
  Installieren     : libXtst-1.2.3-1.el7.x86_64                                                                                             68/94 
  Installieren     : at-spi2-core-2.22.0-1.el7.x86_64                                                                                       69/94 
  Installieren     : at-spi2-atk-2.22.0-2.el7.x86_64                                                                                        70/94 
  Installieren     : libXcursor-1.1.14-8.el7.x86_64                                                                                         71/94 
  Installieren     : gtk-update-icon-cache-3.22.10-5.el7_4.x86_64                                                                           72/94 
  Installieren     : ghostscript-9.07-28.el7_4.2.x86_64                                                                                     73/94 
  Installieren     : libXrandr-1.5.1-2.el7.x86_64                                                                                           74/94 
  Installieren     : libXinerama-1.1.3-2.1.el7.x86_64                                                                                       75/94 
  Installieren     : libXxf86vm-1.1.4-1.el7.x86_64                                                                                          76/94 
  Installieren     : mesa-libGL-17.0.1-6.20170307.el7.x86_64                                                                                77/94 
  Installieren     : cairo-1.14.8-2.el7.x86_64                                                                                              78/94 
  Installieren     : pango-1.40.4-1.el7.x86_64                                                                                              79/94 
  Installieren     : librsvg2-2.40.16-1.el7.x86_64                                                                                          80/94 
  Installieren     : cairo-gobject-1.14.8-2.el7.x86_64                                                                                      81/94 
  Installieren     : giflib-4.1.6-9.el7.x86_64                                                                                              82/94 
  Installieren     : libXcomposite-0.4.4-4.1.el7.x86_64                                                                                     83/94 
  Installieren     : libtool-ltdl-2.4.2-22.el7_3.x86_64                                                                                     84/94 
  Installieren     : dconf-0.26.0-2.el7.x86_64                                                                                              85/94 
  Installieren     : libwmf-lite-0.2.8.4-41.el7_1.x86_64                                                                                    86/94 
  Installieren     : ImageMagick-6.7.8.9-15.el7_2.x86_64                                                                                    87/94 
  Installieren     : nettle-2.7.1-8.el7.x86_64                                                                                              88/94 
  Installieren     : gnutls-3.3.26-9.el7.x86_64                                                                                             89/94 
  Installieren     : glib-networking-2.50.0-1.el7.x86_64                                                                                    90/94 
  Installieren     : libsoup-2.56.0-4.el7_4.x86_64                                                                                          91/94 
  Installieren     : rest-0.8.0-1.el7.x86_64                                                                                                92/94 
  Installieren     : gtk3-3.22.10-5.el7_4.x86_64                                                                                            93/94 
  Installieren     : 1:emacs-24.3-20.el7_4.x86_64                                                                                           94/94 
  Überprüfung läuft: libXext-1.3.3-3.el7.x86_64                                                                                              1/94 
  Überprüfung läuft: libXi-1.7.9-1.el7.x86_64                                                                                                2/94 
  Überprüfung läuft: cairo-gobject-1.14.8-2.el7.x86_64                                                                                       3/94 
  Überprüfung läuft: libXrender-0.9.10-1.el7.x86_64                                                                                          4/94 
  Überprüfung läuft: 1:cups-libs-1.6.3-29.el7.x86_64                                                                                         5/94 
  Überprüfung läuft: ilmbase-1.0.3-7.el7.x86_64                                                                                              6/94 
  Überprüfung läuft: 2:libpng-1.5.13-7.el7_2.x86_64                                                                                          7/94 
  Überprüfung läuft: nettle-2.7.1-8.el7.x86_64                                                                                               8/94 
  Überprüfung läuft: 1:emacs-common-24.3-20.el7_4.x86_64                                                                                     9/94 
  Überprüfung läuft: libwmf-lite-0.2.8.4-41.el7_1.x86_64                                                                                    10/94 
  Überprüfung läuft: dconf-0.26.0-2.el7.x86_64                                                                                              11/94 
  Überprüfung läuft: libproxy-0.4.11-10.el7.x86_64                                                                                          12/94 
  Überprüfung läuft: libXaw-1.0.13-4.el7.x86_64                                                                                             13/94 
  Überprüfung läuft: libtool-ltdl-2.4.2-22.el7_3.x86_64                                                                                     14/94 
  Überprüfung läuft: desktop-file-utils-0.23-1.el7.x86_64                                                                                   15/94 
  Überprüfung läuft: libXpm-3.5.12-1.el7.x86_64                                                                                             16/94 
  Überprüfung läuft: libX11-common-1.6.5-1.el7.noarch                                                                                       17/94 
  Überprüfung läuft: pango-1.40.4-1.el7.x86_64                                                                                              18/94 
  Überprüfung läuft: mesa-libglapi-17.0.1-6.20170307.el7.x86_64                                                                             19/94 
  Überprüfung läuft: gtk3-3.22.10-5.el7_4.x86_64                                                                                            20/94 
  Überprüfung läuft: 1:emacs-filesystem-24.3-20.el7_4.noarch                                                                                21/94 
  Überprüfung läuft: libXau-1.0.8-2.1.el7.x86_64                                                                                            22/94 
  Überprüfung läuft: ImageMagick-6.7.8.9-15.el7_2.x86_64                                                                                    23/94 
  Überprüfung läuft: 1:xorg-x11-font-utils-7.5-20.el7.x86_64                                                                                24/94 
  Überprüfung läuft: libX11-1.6.5-1.el7.x86_64                                                                                              25/94 
  Überprüfung läuft: libICE-1.0.9-9.el7.x86_64                                                                                              26/94 
  Überprüfung läuft: giflib-4.1.6-9.el7.x86_64                                                                                              27/94 
  Überprüfung läuft: adwaita-cursor-theme-3.22.0-1.el7.noarch                                                                               28/94 
  Überprüfung läuft: libXfont-1.5.2-1.el7.x86_64                                                                                            29/94 
  Überprüfung läuft: m17n-lib-1.6.4-14.el7.x86_64                                                                                           30/94 
  Überprüfung läuft: harfbuzz-1.3.2-1.el7.x86_64                                                                                            31/94 
  Überprüfung läuft: avahi-libs-0.6.31-17.el7.x86_64                                                                                        32/94 
  Überprüfung läuft: mesa-libgbm-17.0.1-6.20170307.el7.x86_64                                                                               33/94 
  Überprüfung läuft: liblockfile-1.08-17.el7.x86_64                                                                                         34/94 
  Überprüfung läuft: libotf-0.9.13-4.el7.x86_64                                                                                             35/94 
  Überprüfung läuft: librsvg2-2.40.16-1.el7.x86_64                                                                                          36/94 
  Überprüfung läuft: libXtst-1.2.3-1.el7.x86_64                                                                                             37/94 
  Überprüfung läuft: libusbx-1.0.20-1.el7.x86_64                                                                                            38/94 
  Überprüfung läuft: psmisc-22.20-15.el7.x86_64                                                                                             39/94 
  Überprüfung läuft: libepoxy-1.3.1-1.el7.x86_64                                                                                            40/94 
  Überprüfung läuft: libmodman-2.0.1-8.el7.x86_64                                                                                           41/94 
  Überprüfung läuft: libxcb-1.12-1.el7.x86_64                                                                                               42/94 
  Überprüfung läuft: mesa-libGL-17.0.1-6.20170307.el7.x86_64                                                                                43/94 
  Überprüfung läuft: ghostscript-fonts-5.50-32.el7.noarch                                                                                   44/94 
  Überprüfung läuft: libXmu-1.1.2-2.el7.x86_64                                                                                              45/94 
  Überprüfung läuft: 1:emacs-24.3-20.el7_4.x86_64                                                                                           46/94 
  Überprüfung läuft: pixman-0.34.0-1.el7.x86_64                                                                                             47/94 
  Überprüfung läuft: rest-0.8.0-1.el7.x86_64                                                                                                48/94 
  Überprüfung läuft: gsettings-desktop-schemas-3.22.0-1.el7.x86_64                                                                          49/94 
  Überprüfung läuft: dejavu-sans-mono-fonts-2.33-6.el7.noarch                                                                               50/94 
  Überprüfung läuft: fontconfig-2.10.95-11.el7.x86_64                                                                                       51/94 
  Überprüfung läuft: graphite2-1.3.10-1.el7_3.x86_64                                                                                        52/94 
  Überprüfung läuft: glib-networking-2.50.0-1.el7.x86_64                                                                                    53/94 
  Überprüfung läuft: m17n-db-1.6.4-3.el7.noarch                                                                                             54/94 
  Überprüfung läuft: at-spi2-core-2.22.0-1.el7.x86_64                                                                                       55/94 
  Überprüfung läuft: libXfixes-5.0.3-1.el7.x86_64                                                                                           56/94 
  Überprüfung läuft: libtiff-4.0.3-27.el7_3.x86_64                                                                                          57/94 
  Überprüfung läuft: trousers-0.3.14-2.el7.x86_64                                                                                           58/94 
  Überprüfung läuft: libjpeg-turbo-1.2.90-5.el7.x86_64                                                                                      59/94 
  Überprüfung läuft: colord-libs-1.3.4-1.el7.x86_64                                                                                         60/94 
  Überprüfung läuft: fontpackages-filesystem-1.44-8.el7.noarch                                                                              61/94 
  Überprüfung läuft: hicolor-icon-theme-0.12-7.el7.noarch                                                                                   62/94 
  Überprüfung läuft: libXcomposite-0.4.4-4.1.el7.x86_64                                                                                     63/94 
  Überprüfung läuft: jasper-libs-1.900.1-31.el7.x86_64                                                                                      64/94 
  Überprüfung läuft: gnutls-3.3.26-9.el7.x86_64                                                                                             65/94 
  Überprüfung läuft: urw-fonts-2.4-16.el7.noarch                                                                                            66/94 
  Überprüfung läuft: mesa-libEGL-17.0.1-6.20170307.el7.x86_64                                                                               67/94 
  Überprüfung läuft: adwaita-icon-theme-3.22.0-1.el7.noarch                                                                                 68/94 
  Überprüfung läuft: poppler-data-0.4.6-3.el7.noarch                                                                                        69/94 
  Überprüfung läuft: libXrandr-1.5.1-2.el7.x86_64                                                                                           70/94 
  Überprüfung läuft: at-spi2-atk-2.22.0-2.el7.x86_64                                                                                        71/94 
  Überprüfung läuft: libxshmfence-1.2-1.el7.x86_64                                                                                          72/94 
  Überprüfung läuft: libSM-1.2.2-2.el7.x86_64                                                                                               73/94 
  Überprüfung läuft: libXdamage-1.1.4-4.1.el7.x86_64                                                                                        74/94 
  Überprüfung läuft: atk-2.22.0-3.el7.x86_64                                                                                                75/94 
  Überprüfung läuft: libXinerama-1.1.3-2.1.el7.x86_64                                                                                       76/94 
  Überprüfung läuft: libXxf86vm-1.1.4-1.el7.x86_64                                                                                          77/94 
  Überprüfung läuft: libsoup-2.56.0-4.el7_4.x86_64                                                                                          78/94 
  Überprüfung läuft: libXt-1.1.5-3.el7.x86_64                                                                                               79/94 
  Überprüfung läuft: gtk-update-icon-cache-3.22.10-5.el7_4.x86_64                                                                           80/94 
  Überprüfung läuft: cairo-1.14.8-2.el7.x86_64                                                                                              81/94 
  Überprüfung läuft: OpenEXR-libs-1.7.1-7.el7.x86_64                                                                                        82/94 
  Überprüfung läuft: dejavu-fonts-common-2.33-6.el7.noarch                                                                                  83/94 
  Überprüfung läuft: libthai-0.1.14-9.el7.x86_64                                                                                            84/94 
  Überprüfung läuft: libXft-2.3.2-2.el7.x86_64                                                                                              85/94 
  Überprüfung läuft: json-glib-1.2.6-1.el7.x86_64                                                                                           86/94 
  Überprüfung läuft: libXcursor-1.1.14-8.el7.x86_64                                                                                         87/94 
  Überprüfung läuft: libgusb-0.2.9-1.el7.x86_64                                                                                             88/94 
  Überprüfung läuft: jbigkit-libs-2.0-11.el7.x86_64                                                                                         89/94 
  Überprüfung läuft: GConf2-3.2.6-8.el7.x86_64                                                                                              90/94 
  Überprüfung läuft: gdk-pixbuf2-2.36.5-1.el7.x86_64                                                                                        91/94 
  Überprüfung läuft: lcms2-2.6-3.el7.x86_64                                                                                                 92/94 
  Überprüfung läuft: ghostscript-9.07-28.el7_4.2.x86_64                                                                                     93/94 
  Überprüfung läuft: libfontenc-1.1.3-3.el7.x86_64                                                                                          94/94 

Installiert:
  emacs.x86_64 1:24.3-20.el7_4                                                                                                                    
Abhängigkeit installiert:
  GConf2.x86_64 0:3.2.6-8.el7                  ImageMagick.x86_64 0:6.7.8.9-15.el7_2             OpenEXR-libs.x86_64 0:1.7.1-7.el7               
  adwaita-cursor-theme.noarch 0:3.22.0-1.el7   adwaita-icon-theme.noarch 0:3.22.0-1.el7          at-spi2-atk.x86_64 0:2.22.0-2.el7               
  at-spi2-core.x86_64 0:2.22.0-1.el7           atk.x86_64 0:2.22.0-3.el7                         avahi-libs.x86_64 0:0.6.31-17.el7               
  cairo.x86_64 0:1.14.8-2.el7                  cairo-gobject.x86_64 0:1.14.8-2.el7               colord-libs.x86_64 0:1.3.4-1.el7                
  cups-libs.x86_64 1:1.6.3-29.el7              dconf.x86_64 0:0.26.0-2.el7                       dejavu-fonts-common.noarch 0:2.33-6.el7         
  dejavu-sans-mono-fonts.noarch 0:2.33-6.el7   desktop-file-utils.x86_64 0:0.23-1.el7            emacs-common.x86_64 1:24.3-20.el7_4             
  emacs-filesystem.noarch 1:24.3-20.el7_4      fontconfig.x86_64 0:2.10.95-11.el7                fontpackages-filesystem.noarch 0:1.44-8.el7     
  gdk-pixbuf2.x86_64 0:2.36.5-1.el7            ghostscript.x86_64 0:9.07-28.el7_4.2              ghostscript-fonts.noarch 0:5.50-32.el7          
  giflib.x86_64 0:4.1.6-9.el7                  glib-networking.x86_64 0:2.50.0-1.el7             gnutls.x86_64 0:3.3.26-9.el7                    
  graphite2.x86_64 0:1.3.10-1.el7_3            gsettings-desktop-schemas.x86_64 0:3.22.0-1.el7   gtk-update-icon-cache.x86_64 0:3.22.10-5.el7_4  
  gtk3.x86_64 0:3.22.10-5.el7_4                harfbuzz.x86_64 0:1.3.2-1.el7                     hicolor-icon-theme.noarch 0:0.12-7.el7          
  ilmbase.x86_64 0:1.0.3-7.el7                 jasper-libs.x86_64 0:1.900.1-31.el7               jbigkit-libs.x86_64 0:2.0-11.el7                
  json-glib.x86_64 0:1.2.6-1.el7               lcms2.x86_64 0:2.6-3.el7                          libICE.x86_64 0:1.0.9-9.el7                     
  libSM.x86_64 0:1.2.2-2.el7                   libX11.x86_64 0:1.6.5-1.el7                       libX11-common.noarch 0:1.6.5-1.el7              
  libXau.x86_64 0:1.0.8-2.1.el7                libXaw.x86_64 0:1.0.13-4.el7                      libXcomposite.x86_64 0:0.4.4-4.1.el7            
  libXcursor.x86_64 0:1.1.14-8.el7             libXdamage.x86_64 0:1.1.4-4.1.el7                 libXext.x86_64 0:1.3.3-3.el7                    
  libXfixes.x86_64 0:5.0.3-1.el7               libXfont.x86_64 0:1.5.2-1.el7                     libXft.x86_64 0:2.3.2-2.el7                     
  libXi.x86_64 0:1.7.9-1.el7                   libXinerama.x86_64 0:1.1.3-2.1.el7                libXmu.x86_64 0:1.1.2-2.el7                     
  libXpm.x86_64 0:3.5.12-1.el7                 libXrandr.x86_64 0:1.5.1-2.el7                    libXrender.x86_64 0:0.9.10-1.el7                
  libXt.x86_64 0:1.1.5-3.el7                   libXtst.x86_64 0:1.2.3-1.el7                      libXxf86vm.x86_64 0:1.1.4-1.el7                 
  libepoxy.x86_64 0:1.3.1-1.el7                libfontenc.x86_64 0:1.1.3-3.el7                   libgusb.x86_64 0:0.2.9-1.el7                    
  libjpeg-turbo.x86_64 0:1.2.90-5.el7          liblockfile.x86_64 0:1.08-17.el7                  libmodman.x86_64 0:2.0.1-8.el7                  
  libotf.x86_64 0:0.9.13-4.el7                 libpng.x86_64 2:1.5.13-7.el7_2                    libproxy.x86_64 0:0.4.11-10.el7                 
  librsvg2.x86_64 0:2.40.16-1.el7              libsoup.x86_64 0:2.56.0-4.el7_4                   libthai.x86_64 0:0.1.14-9.el7                   
  libtiff.x86_64 0:4.0.3-27.el7_3              libtool-ltdl.x86_64 0:2.4.2-22.el7_3              libusbx.x86_64 0:1.0.20-1.el7                   
  libwmf-lite.x86_64 0:0.2.8.4-41.el7_1        libxcb.x86_64 0:1.12-1.el7                        libxshmfence.x86_64 0:1.2-1.el7                 
  m17n-db.noarch 0:1.6.4-3.el7                 m17n-lib.x86_64 0:1.6.4-14.el7                    mesa-libEGL.x86_64 0:17.0.1-6.20170307.el7      
  mesa-libGL.x86_64 0:17.0.1-6.20170307.el7    mesa-libgbm.x86_64 0:17.0.1-6.20170307.el7        mesa-libglapi.x86_64 0:17.0.1-6.20170307.el7    
  nettle.x86_64 0:2.7.1-8.el7                  pango.x86_64 0:1.40.4-1.el7                       pixman.x86_64 0:0.34.0-1.el7                    
  poppler-data.noarch 0:0.4.6-3.el7            psmisc.x86_64 0:22.20-15.el7                      rest.x86_64 0:0.8.0-1.el7                       
  trousers.x86_64 0:0.3.14-2.el7               urw-fonts.noarch 0:2.4-16.el7                     xorg-x11-font-utils.x86_64 1:7.5-20.el7         

Komplett!
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode