# 在 Linux Fedora 27 工作站上安装 docker-compose

> 原文：<https://dev.to/psnebc/install-docker-compose-on-linux-fedora-27-workstation-4cjc>

```
 psnebc@localhost  ~  sudo dnf install docker-compose
[sudo] Passwort für psnebc: 
Fedora 27 - x86_64 - Updates                                                                                                  1.9 MB/s |  22 MB     00:11    
Zwischenspeicher für Paketquelle »Dropbox« konnte nicht synchronisiert werden, wird deaktiviert.
Letzte Prüfung auf abgelaufene Metadaten: vor 0:00:00 am Do 19 Apr 2018 20:05:06 CEST.
Abhängigkeiten sind aufgelöst.
==============================================================================================================================================================
 Paket                                            Arch                           Version                                Paketquelle                     Größe
==============================================================================================================================================================
Installieren:
 docker-compose                                   noarch                         1.17.1-1.fc27                          updates                         210 k
Installiere Abhängigkeiten:
 python3-cached_property                          noarch                         1.3.0-9.fc27                           fedora                           19 k
 python3-docker                                   noarch                         2.6.1-1.fc27                           updates                         182 k
 python3-docker-pycreds                           noarch                         0.2.1-5.fc27                           fedora                           19 k
 python3-dockerpty                                noarch                         0.4.1-7.fc27                           fedora                           32 k
 python3-docopt                                   noarch                         0.6.2-6.fc27                           fedora                           32 k
 python3-jsonschema                               noarch                         2.6.0-2.fc27                           fedora                           81 k
 python3-texttable                                noarch                         0.9.1-1.fc27                           fedora                           24 k
 python3-websocket-client                         noarch                         0.47.0-1.fc27                          updates                          64 k

Transaktionsübersicht
==============================================================================================================================================================
Installieren  9 Pakete

Gesamte Downloadgröße: 662 k
Installationsgröße: 2.4 M
Ist dies in Ordnung? [j/N]:y
Pakete werden heruntergeladen:
(1/9): python3-dockerpty-0.4.1-7.fc27.noarch.rpm                                                                              238 kB/s |  32 kB     00:00    
(2/9): python3-cached_property-1.3.0-9.fc27.noarch.rpm                                                                        117 kB/s |  19 kB     00:00    
(3/9): python3-docopt-0.6.2-6.fc27.noarch.rpm                                                                                 509 kB/s |  32 kB     00:00    
(4/9): python3-jsonschema-2.6.0-2.fc27.noarch.rpm                                                                             800 kB/s |  81 kB     00:00    
(5/9): python3-texttable-0.9.1-1.fc27.noarch.rpm                                                                              325 kB/s |  24 kB     00:00    
(6/9): docker-compose-1.17.1-1.fc27.noarch.rpm                                                                                657 kB/s | 210 kB     00:00    
(7/9): python3-docker-pycreds-0.2.1-5.fc27.noarch.rpm                                                                         246 kB/s |  19 kB     00:00    
(8/9): python3-docker-2.6.1-1.fc27.noarch.rpm                                                                                 1.5 MB/s | 182 kB     00:00    
(9/9): python3-websocket-client-0.47.0-1.fc27.noarch.rpm                                                                      580 kB/s |  64 kB     00:00    
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                                        652 kB/s | 662 kB     00:01     
Transaktionsüberprüfung wird ausgeführt
Transaktionsprüfung war erfolgreich.
Transaktion wird getestet
Transaktionstest war erfolgreich.
Transaktion wird ausgeführt
  Vorbereitung läuft    :                                                                                                                                 1/1 
  Installieren          : python3-websocket-client-0.47.0-1.fc27.noarch                                                                                   1/9 
  Installieren          : python3-docker-pycreds-0.2.1-5.fc27.noarch                                                                                      2/9 
  Installieren          : python3-docker-2.6.1-1.fc27.noarch                                                                                              3/9 
  Installieren          : python3-texttable-0.9.1-1.fc27.noarch                                                                                           4/9 
  Installieren          : python3-jsonschema-2.6.0-2.fc27.noarch                                                                                          5/9 
  Installieren          : python3-docopt-0.6.2-6.fc27.noarch                                                                                              6/9 
  Installieren          : python3-dockerpty-0.4.1-7.fc27.noarch                                                                                           7/9 
  Installieren          : python3-cached_property-1.3.0-9.fc27.noarch                                                                                     8/9 
  Installieren          : docker-compose-1.17.1-1.fc27.noarch                                                                                             9/9 
  Überprüfung läuft     : docker-compose-1.17.1-1.fc27.noarch                                                                                             1/9 
  Überprüfung läuft     : python3-cached_property-1.3.0-9.fc27.noarch                                                                                     2/9 
  Überprüfung läuft     : python3-dockerpty-0.4.1-7.fc27.noarch                                                                                           3/9 
  Überprüfung läuft     : python3-docopt-0.6.2-6.fc27.noarch                                                                                              4/9 
  Überprüfung läuft     : python3-jsonschema-2.6.0-2.fc27.noarch                                                                                          5/9 
  Überprüfung läuft     : python3-texttable-0.9.1-1.fc27.noarch                                                                                           6/9 
  Überprüfung läuft     : python3-docker-2.6.1-1.fc27.noarch                                                                                              7/9 
  Überprüfung läuft     : python3-docker-pycreds-0.2.1-5.fc27.noarch                                                                                      8/9 
  Überprüfung läuft     : python3-websocket-client-0.47.0-1.fc27.noarch                                                                                   9/9 

Installiert:
  docker-compose.noarch 1.17.1-1.fc27                python3-cached_property.noarch 1.3.0-9.fc27         python3-docker.noarch 2.6.1-1.fc27                   
  python3-docker-pycreds.noarch 0.2.1-5.fc27         python3-dockerpty.noarch 0.4.1-7.fc27               python3-docopt.noarch 0.6.2-6.fc27                   
  python3-jsonschema.noarch 2.6.0-2.fc27             python3-texttable.noarch 0.9.1-1.fc27               python3-websocket-client.noarch 0.47.0-1.fc27        

Fertig. 
```

Enter fullscreen mode Exit fullscreen mode