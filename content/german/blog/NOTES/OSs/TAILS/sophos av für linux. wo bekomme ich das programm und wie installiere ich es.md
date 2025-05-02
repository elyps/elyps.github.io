**Zu Sophos AV für Linux**

Die Frage des Nutzers bezieht sich auf Sophos Antivirus für Linux – wie es heruntergeladen und installiert wird. Um eine genaue Antwort zu geben, werde ich zuerst nach den neuesten Informationen suchen und dann eine Schritt-für-Schritt-Anleitung samt Link zur Sophos-Website sowie Installationskommandos bereitstellen. Ich muss sicherstellen, dass ich eine Vielzahl an Quellen zitiere und die Informationen gut strukturiere, um dem erklärten Bedarf des Nutzers gerecht zu werden.

**Anleitung für Sophos AV für Linux**

Um Sophos AV für Linux zu installieren, müssen Sie die Linux Server Installer-Datei von der Sophos-Website herunterladen. Nach dem Herunterladen:

1. Erteilen Sie die Berechtigung mit `chmod +x SophosSetup.sh`.
    
2. Führen Sie das Installationsskript mit `./SophosSetup.sh` aus.
    

Die Installation erfolgt standardmäßig nach `/opt/sophos-spl`. Für die Lizenz benötigen Sie ein Sophos Central-Konto, wobei die kostenlose Version inzwischen eingestellt wurde. Es ist unklar, ob die Anfrage nach der kostenlosen oder der kommerziellen Version des Programms fragt.

**Sophos AV für Linux Voraussetzungen**

Um Sophos AV für Linux zu installieren, benötigen Sie ein 64-Bit-Betriebssystem, Root-Rechte und eine Netzwerkverbindung zu den Sophos-Update-Servern. Stellen Sie sicher, dass Sie die Download- und Installationsanweisungen auf der offiziellen Sophos-Website befolgen. Weitere Quellen, wie die Sophos-Community und LinuxConfig.org, empfehlen ebenfalls Sophos AV. Diese Domains bieten verschiedene Hinweise zur Installation und bieten eine nützliche zusätzliche Perspektive. Achten Sie darauf, alle Quellen korrekt zu zitieren, um einen vollständigen Überblick zu haben.

Nachgedacht für 46 Sekunden

## Zusammenfassung

Sophos Antivirus für Linux, heute als Sophos Protection for Linux bekannt, ist nicht mehr als Free Edition verfügbar – die Free Edition wurde eingestellt und steht nicht mehr zum Download bereit citeturn0search17. Zum Herunterladen benötigen Sie ein Sophos-Konto, eine gültige Lizenz (kostenlose Testlizenz möglich) und die Akzeptanz des Endbenutzer-Lizenzvertrags citeturn0search0turn4view0. Der Download erfolgt über Sophos Central unter **Devices > Installers > Linux Server Installer** oder alternativ über einen direkt kopierten Link per `wget` citeturn2view0. Die Installation führen Sie durch Ausführbarmachen des Skripts (`chmod +x SophosSetup.sh`) und Ausführen mit Root-Rechten (`sudo ./SophosSetup.sh`) durch – standardmäßig wird unter `/opt/sophos-spl` installiert citeturn2view0. Für erweiterte Anleitungen zu Installation und Deinstallation verweisen Fachblogs wie LinuxConfig und KernelTalks auf zusätzliche Tipps und Beispiele citeturn0search2turn0search12.

## 1. Bezugsquelle

Sophos stellt den aktuellen Linux-Installer in Sophos Central bereit.  
Der schnellste Weg ist, sich in Sophos Central anzumelden, **Devices > Installers** zu wählen und dort unter **Server Protection > Download Linux Server Installer** zu klicken citeturn2view0.  
Alternativ können Sie per Rechtsklick auf den Link die URL kopieren und auf dem Linux-Server mit `wget <Link>` herunterladen citeturn2view0.

## 2. Voraussetzungen

- **Root-/sudo‑Rechte** auf dem Zielsystem.
    
- **Netzwerkzugang** zu den Sophos-Update- und Kommunikationsservern (Firewall-Regeln für `*.sophos.com`, `*.amazonaws.com` etc.) müssen gesetzt sein citeturn3view0.
    

## 3. Installation

### 3.1 Herunterladen

1. Melden Sie sich in Sophos Central an und navigieren Sie zu **Devices > Installers > Server Protection > Download Linux Server Installer** citeturn0search1.
    
2. Speichern Sie das Skript (z. B. `SophosSetup.sh`) in Ihrem Home- oder einem Download-Verzeichnis.
    

### 3.2 Ausführbarmachen und Ausführen

cd ~/Downloads       # oder dort, wo Sie das Skript abgelegt haben citeturn4view0
chmod +x SophosSetup.sh  # Skript ausführbar machen citeturn2view0
sudo ./SophosSetup.sh    # Installer starten citeturn2view0

Folgen Sie den Anweisungen im Installations-Assistenten. Bei Bedarf können Sie mit der Option `--install-dir=<Pfad>` ein abweichendes Installationsverzeichnis festlegen.

### 3.3 Installationspfad

Standardmäßig wird Sophos Protection for Linux unter `/opt/sophos-spl` installiert citeturn2view0.

## 4. Test und Betrieb

Nach erfolgreicher Installation können Sie in Sophos Central einen **On-Demand-Scan** anstoßen oder lokal mit dem CLI-Tool `savscan` prüfen, ob die Engine korrekt arbeitet citeturn1view0. Weitere Informationen zu Kommandozeilen-Optionen finden Sie unter **Installer command-line options for Linux** in der Sophos-Dokumentation.

## 5. Alternative und Hinweise

- **LinuxConfig** führt Sophos Antivirus for Linux als eine der beliebten Optionen für Linux-Systeme auf und gibt einen Überblick zur Wirksamkeit von AV unter Linux citeturn0search2.
    
- **KernelTalks** bietet eine kompakte Anleitung zum Installieren und Deinstallieren von Sophos Antivirus auf verschiedenen Distributionen citeturn0search12.
    

**Hinweis:** Da die Free Edition eingestellt wurde, empfiehlt Sophos für Privatnutzer den Einsatz alternativer Lösungen (z. B. ClamAV), sofern keine Lizenz für Sophos Protection for Linux zur Verfügung steht.
