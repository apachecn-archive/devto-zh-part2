# sudo dnf 安装纱线

> 原文：<https://dev.to/psnebc/sudo-dnf-install-yarn-3dmg>

```
psnebc@localhost  ~  curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo

[sudo] Passwort für psnebc: 
[yarn]
name=Yarn Repository
baseurl=https://dl.yarnpkg.com/rpm/
enabled=1
gpgcheck=1
gpgkey=https://dl.yarnpkg.com/rpm/pubkey.gpg
 psnebc@localhost  ~  sudo dnf install yarn
Yarn Repository                                                                                                   709 kB/s | 362 kB     00:00    
Zwischenspeicher für Paketquelle »Dropbox« konnte nicht synchronisiert werden, wird deaktiviert.
Letzte Prüfung auf abgelaufene Metadaten: vor 0:00:00 am Do 29 Mär 2018 10:28:11 CEST.
Abhängigkeiten sind aufgelöst.
==================================================================================================================================================
 Paket                            Arch                               Version                               Paketquelle                      Größe
==================================================================================================================================================
Installieren:
 yarn                             noarch                             1.5.1-1                               yarn                             910 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket

Gesamte Downloadgröße: 910 k
Installationsgröße: 4.1 M
Ist dies in Ordnung? [j/N]:y
Pakete werden heruntergeladen:
yarn-1.5.1-1.noarch.rpm                                                                                           2.1 MB/s | 910 kB     00:00    
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            2.1 MB/s | 910 kB     00:00     
Warnung: /var/cache/dnf/yarn-39eb54bf733530c3/packages/yarn-1.5.1-1.noarch.rpm: Header V4 RSA/SHA512 Signature, Schlüssel-ID 6963f07f: NOKEY
Importing GPG key 0x6963F07F:
 Userid     : "Yarn RPM Packaging <yarn@dan.cx>"
 Fingerprint: 9A6F 73F3 4BEB 7473 4D8C 6914 9CBB B558 6963 F07F
 From       : https://dl.yarnpkg.com/rpm/pubkey.gpg
Ist dies in Ordnung? [j/N]:y
Schlüssel erfolgreich importiert
Transaktionsüberprüfung wird ausgeführt
Transaktionsprüfung war erfolgreich.
Transaktion wird getestet
Transaktionstest war erfolgreich.
Transaktion wird ausgeführt
  Vorbereitung läuft    :                                                                                                                     1/1 
  Installieren          : yarn-1.5.1-1.noarch                                                                                                 1/1 
  Überprüfung läuft     : yarn-1.5.1-1.noarch                                                                                                 1/1 

Installiert:
  yarn.noarch 1.5.1-1                                                                                                                             

Fertig.
 psnebc@localhost  ~  yarn --version
1.5.1 
```

Enter fullscreen mode Exit fullscreen mode