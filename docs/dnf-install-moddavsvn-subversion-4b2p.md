# sudo dnf 安装 mod_dav_svn 版本

> 原文：<https://dev.to/psnebc/dnf-install-moddavsvn-subversion-4b2p>

```
psnebc@localhost  ~  sudo dnf install mod_dav_svn subversion
[sudo] Passwort für psnebc: 
Zwischenspeicher für Paketquelle »Dropbox« konnte nicht synchronisiert werden, wird deaktiviert.
Letzte Prüfung auf abgelaufene Metadaten: vor 0:00:26 am Do 29 Mär 2018 11:07:21 CEST.
Abhängigkeiten sind aufgelöst.
==================================================================================================================================================
 Paket                                Arch                            Version                               Paketquelle                     Größe
==================================================================================================================================================
Installieren:
 mod_dav_svn                          x86_64                          1.9.7-1.fc27                          fedora                          117 k
 subversion                           x86_64                          1.9.7-1.fc27                          fedora                          1.1 M

Transaktionsübersicht
==================================================================================================================================================
Installieren  2 Pakete

Gesamte Downloadgröße: 1.2 M
Installationsgröße: 5.2 M
Ist dies in Ordnung? [j/N]:y
Pakete werden heruntergeladen:
(1/2): mod_dav_svn-1.9.7-1.fc27.x86_64.rpm                                                                        399 kB/s | 117 kB     00:00    
(2/2): subversion-1.9.7-1.fc27.x86_64.rpm                                                                         1.3 MB/s | 1.1 MB     00:00    
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            784 kB/s | 1.2 MB     00:01     
Transaktionsüberprüfung wird ausgeführt
Transaktionsprüfung war erfolgreich.
Transaktion wird getestet
Transaktionstest war erfolgreich.
Transaktion wird ausgeführt
  Vorbereitung läuft    :                                                                                                                     1/1 
  Installieren          : subversion-1.9.7-1.fc27.x86_64                                                                                      1/2 
  Ausgeführtes Scriptlet: subversion-1.9.7-1.fc27.x86_64                                                                                      1/2 
  Installieren          : mod_dav_svn-1.9.7-1.fc27.x86_64                                                                                     2/2 
  Ausgeführtes Scriptlet: mod_dav_svn-1.9.7-1.fc27.x86_64                                                                                     2/2 
Running as unit: run-r2b1b1c0bd50a4244b8c6f1e7964145f4.service
  Überprüfung läuft     : mod_dav_svn-1.9.7-1.fc27.x86_64                                                                                     1/2 
  Überprüfung läuft     : subversion-1.9.7-1.fc27.x86_64                                                                                      2/2 

Installiert:
  mod_dav_svn.x86_64 1.9.7-1.fc27                                          subversion.x86_64 1.9.7-1.fc27                                         

Fertig.

 ✘ psnebc@localhost  ~  svn --version
svn, Version 1.9.7 (r1800392)
   übersetzt am Aug 11 2017, um 07:37:53 auf x86_64-redhat-linux-gnu

Copyright (C) 2017 The Apache Software Foundation.
This software consists of contributions made by many people;
see the NOTICE file for more information.
Subversion is open source software, see http://subversion.apache.org/

Die folgenden ZugriffsModule (ZM) für Projektarchive stehen zur Verfügung:

* ra_svn : Modul zum Zugriff auf ein Projektarchiv über das svn-Netzwerkprotokoll.
  - mit Cyrus-SASL-Authentifizierung
  - behandelt Schema »svn«
* ra_local : Modul zum Zugriff auf ein Projektarchiv auf der lokalen Festplatte
  - behandelt Schema »file«
* ra_serf : Module for accessing a repository via WebDAV protocol using serf.
  - using serf 1.3.9 (compiled with 1.3.9)
  - behandelt Schema »http«
  - behandelt Schema »https«

Die folgenden Speicherbereiche für Zugangsdaten sind verfügbar:

* Klartext-Speicher in /home/psnebc/.subversion
* Gnome Keyring
* GPG-Agent
* KWallet (KDE) 
```

Enter fullscreen mode Exit fullscreen mode