# Linux Fedora 27 Flatpak | flatpak 卸载 org.kde.krita/x86_64/stable

> 原文：<https://dev.to/psnebc/linux-fedora-27-flatpack--flatpak-uninstall-orgkdekritax8664stable-23hk>

```
 psnebc@localhost  ~  flatpak list  
Ref                                             Options       
org.blender.Blender/x86_64/stable               system,current
org.kde.krita/x86_64/stable                     system,current
org.freedesktop.Platform.VAAPI.Intel/x86_64/1.6 system,runtime
org.freedesktop.Platform.ffmpeg/x86_64/1.6      system,runtime
org.freedesktop.Platform/x86_64/1.6             system,runtime
org.kde.Platform/x86_64/5.10                    system,runtime
 psnebc@localhost  ~  flatpak uninstall org.kde.krita/x86_64/stable
Uninstalling: org.kde.krita/x86_64/stable
Uninstalling: org.kde.krita.Locale/x86_64/stable
 psnebc@localhost  ~  flatpak list                                 
Ref                                             Options       
org.blender.Blender/x86_64/stable               system,current
org.freedesktop.Platform.VAAPI.Intel/x86_64/1.6 system,runtime
org.freedesktop.Platform.ffmpeg/x86_64/1.6      system,runtime
org.freedesktop.Platform/x86_64/1.6             system,runtime
org.kde.Platform/x86_64/5.10                    system,runtime
 psnebc@localhost  ~  
```

Enter fullscreen mode Exit fullscreen mode

```
 ✘ psnebc@localhost  ~  flatpak     
Aufruf:
  flatpak [OPTION …] BEFEHL

Eingebaute Befehle:
 Installierte Anwendungen und Laufzeitumgebungen verwalten
  install             Eine Anwendung oder Laufzeitumgebung installieren
  update              Eine installierte Anwendung oder Laufzeitumgebung aktualisieren
  uninstall           Eine installierte Anwendung oder Laufzeitumgebung deinstallieren
  list                Installierte Anwendungen und/oder Laufzeitumgebungen auflisten
  info                Informationen zu installierter Anwendung oder Laufzeit anzeigen
  config              Configure flatpak

 Finding applications and runtimes
  search              Search for remote apps/runtimes

 Anwendungen ausführen
  run                 Eine Anwendung ausführen
  override            Berechtigungen für eine Anwendung ersetzen
  make-current        Die als Standard auszuführende Version angeben
  enter               In den Namensraum einer laufenden Anwendung wechseln

 Dateizugriff verwalten
  document-export     Einer Anwendung den Zugriff auf eine bestimmte Datei gewähren
  document-unexport   Den Zugriff auf eine bestimmte Datei widerrufen
  document-info       Informationen zu einer bestimmten Datei anzeigen
  document-list       Exportierte Dateien auflisten

 Entfernte Quellen verwalten
  remotes             Alle konfigurierten entfernten Quellen auflisten
  remote-add          Eine neue entfernte Quelle hinzufügen (mittels Adresse)
  remote-modify       Eigenschaften einer konfigurierten entfernten Quelle verändern
  remote-delete       Eine konfigurierte entfernte Quelle löschen
  remote-ls           Inhalt einer konfigurierten entfernten Quelle auflisten
  remote-info         Show information about a remote app or runtime

 Anwendungen erstellen
  build-init          Einen Ordner für die Erstellung initialisieren
  build               Einen Erstellungsbefehl innerhalb des Erstellungsordners ausführen
  build-finish        Einen Erstellungsordner für den Export finalisieren
  build-export        Erstellungsordner in eine Quelle exportieren
  build-bundle        Eine Bündel-Datei aus einem Erstellungsordner erzeugen
  build-import-bundle Eine Bündel-Datei importieren
  build-sign          Eine Anwendung oder Laufzeitumgebung signieren
  build-update-repo   Zusammenfassungsdatei in einer Quelle aktualisieren
  build-commit-from   Neuen Commit basierend auf existierendem Ref erstellen
  repo                Print information about a repo

Hilfeoptionen:
  -h, --help             Hilfeoptionen anzeigen

Anwendungsoptionen:
  --version              Versionsinformationen ausgeben und beenden
  --default-arch         Standard-Architektur ausgeben und beenden
  --supported-arches     Unterstützte Architekturen ausgeben und beenden
  --gl-drivers           Aktiven GL-Treiber ausgeben und beenden
  -v, --verbose          Print debug information during command processing, -vv for more detail
  --ostree-verbose       OSTree-Fehlerdiagnoseinformationen während der Befehlsverarbeitung ausgeben 
```

Enter fullscreen mode Exit fullscreen mode