# 在 Fedora - sudo 上安装 asci NEMA

> 原文：<https://dev.to/psnebc/install-asciinema-on-fedora---sudo-dnf-install-asciinema-1jo2>

*   [https://asciinema.org](https://asciinema.org)
*   安装[https://asciinema.org/a/v9BUHrbztF5PpbaCbtWAHJ1tH](https://asciinema.org/a/v9BUHrbztF5PpbaCbtWAHJ1tH)后测试

```
 psnebc@localhost  ~  sudo dnf install asciinema
[sudo] Passwort für psnebc: 
Zwischenspeicher für Paketquelle »Dropbox« konnte nicht synchronisiert werden, wird deaktiviert.
Letzte Prüfung auf abgelaufene Metadaten: vor 3:06:23 am Fr 06 Apr 2018 18:52:23 CEST.
Abhängigkeiten sind aufgelöst.
==================================================================================================================================================
 Paket                              Arch                            Version                                 Paketquelle                     Größe
==================================================================================================================================================
Installieren:
 asciinema                          noarch                          1.4.0-2.fc27                            fedora                           58 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket

Gesamte Downloadgröße: 58 k
Installationsgröße: 144 k
Ist dies in Ordnung? [j/N]:y
Pakete werden heruntergeladen:
asciinema-1.4.0-2.fc27.noarch.rpm                                                                                 313 kB/s |  58 kB     00:00    
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                             82 kB/s |  58 kB     00:00     
Transaktionsüberprüfung wird ausgeführt
Transaktionsprüfung war erfolgreich.
Transaktion wird getestet
Transaktionstest war erfolgreich.
Transaktion wird ausgeführt
  Vorbereitung läuft    :                                                                                                                     1/1 
  Installieren          : asciinema-1.4.0-2.fc27.noarch                                                                                       1/1 
  Ausgeführtes Scriptlet: asciinema-1.4.0-2.fc27.noarch                                                                                       1/1 
Running as unit: run-rbb99b1c138904e95a58f8f88cb81024a.service
  Überprüfung läuft     : asciinema-1.4.0-2.fc27.noarch                                                                                       1/1 

Installiert:
  asciinema.noarch 1.4.0-2.fc27                                                                                                                   

Fertig. 
```

Enter fullscreen mode Exit fullscreen mode

```
 psnebc@localhost  ~  asciinema 
usage: asciinema [-h] [--version] {rec,play,cat,upload,auth} ...

Record and share your terminal sessions, the right way.

positional arguments:
  {rec,play,cat,upload,auth}
    rec                 Record terminal session
    play                Replay terminal session
    cat                 Print full output of terminal session
    upload              Upload locally saved terminal session to asciinema.org
    auth                Manage recordings on asciinema.org account

optional arguments:
  -h, --help            show this help message and exit
  --version             show program's version number and exit

example usage:
  Record terminal and upload it to asciinema.org:
    asciinema rec
  Record terminal to local file:
    asciinema rec demo.cast
  Record terminal and upload it to asciinema.org, specifying title:
    asciinema rec -t "My git tutorial"
  Record terminal to local file, limiting idle time to max 2.5 sec:
    asciinema rec -i 2.5 demo.cast
  Replay terminal recording from local file:
    asciinema play demo.cast
  Replay terminal recording hosted on asciinema.org:
    asciinema play https://asciinema.org/a/difqlgx86ym6emrmd8u62yqu8
  Print full output of recorded session:
    asciinema cat demo.cast

For help on a specific command run:
  asciinema <command> -h 
```

Enter fullscreen mode Exit fullscreen mode