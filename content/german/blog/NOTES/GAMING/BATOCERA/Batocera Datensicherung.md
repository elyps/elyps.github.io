Hier eine detaillierte Anleitung zur Datensicherung unter Batocera Linux, wobei der Fokus vor allem auf dem Sichern des /userdata-Verzeichnisses liegt, in dem alle persönlichen Einstellungen, Emulator-Konfigurationen, Spielstände und ROMs gespeichert werden.

---

## 1. Vorbereitung

- **Sauberes Herunterfahren:**  
    Bevor Sie mit der Sicherung beginnen, fahren Sie Batocera sauber herunter. Dies verhindert, dass während des Kopiervorgangs Dateien verändert werden.
    
- **Sicherungsspeicher bereitstellen:**  
    Schließen Sie einen USB-Stick oder ein anderes externes Speichermedium an oder planen Sie, das Medium (etwa eine SD-Karte) direkt in einen PC einzulegen, um darauf die Sicherung abzulegen.
    

---

## 2. Wesentliche Verzeichnisse

Die wichtigsten Daten befinden sich in:

- **/userdata/**  
    Dieses Verzeichnis enthält alle benutzerspezifischen Daten: Konfigurationsdateien, Spielstände und ROMs.
    

Falls Sie zusätzliche Anpassungen oder Pakete installiert haben, vergewissern Sie sich, dass Sie auch diese Verzeichnisse berücksichtigen, die sich gegebenenfalls außerhalb von /userdata befinden könnten.

---

## 3. Backup-Methoden

### A. Komplettes SD-Karten-Image erstellen

Falls Sie Batocera von einer SD-Karte betreiben, können Sie ein komplettes Abbild der Karte erstellen. Das sichert alle Daten, inklusive des Systembereichs, und ermöglicht eine Wiederherstellung des gesamten Systems.

- **Unter Linux (Beispiel mit dd):**
    
    ```bash
    sudo dd if=/dev/sdX of=/Pfad/zur/sicherung.img bs=4M status=progress
    ```
    
    Ersetzen Sie `/dev/sdX` durch das entsprechende Gerät (z. B. `/dev/sdb`). Prüfen Sie vorher mithilfe von Tools wie `lsblk` oder `fdisk -l`, welches Gerät Ihre SD-Karte ist.
    
- **Unter Windows:**  
    Tools wie _Win32DiskImager_ oder _balenaEtcher_ können genutzt werden, um ein komplettes Image der SD-Karte zu erstellen.
    

### B. Manuelle Kopie des /userdata-Verzeichnisses

Eine Alternative besteht darin, nur das /userdata-Verzeichnis zu sichern, sofern keine Systemanpassungen erfolgt sind.

#### 1. Backup über den integrierten Datei-Explorer oder Netzwerkfreigabe

- Verbinden Sie ein USB-Laufwerk mit Batocera über den internen Datei-Explorer (sofern diese Option aktiviert ist) und kopieren Sie das gesamte /userdata-Verzeichnis auf das USB-Laufwerk.
    

#### 2. Backup über SSH

- **SSH-Zugriff aktivieren:**  
    Stellen Sie sicher, dass SSH in Batocera aktiviert ist (in der Regel ist es standardmäßig aktiviert).
    
- **Mit dem Host verbinden und kopieren:**  
    Loggen Sie sich auf Ihrem Batocera-Gerät ein (standardmäßig `root` ohne Passwort) und kopieren Sie die Daten ins externe Medium. Beispiel:
    
    ```bash
    ssh root@<IP-Adresse-von-Batocera>
    cp -r /userdata /mnt/usb/backup_userdata
    ```
    
    _Hinweis:_ Der genaue Pfad zum USB-Laufwerk kann variieren (z. B. `/mnt/usb` oder `/media/usb`). Überprüfen Sie dies ggf. per Konsolenbefehl wie `lsblk`.
    

#### 3. Backup mit rsync

- **rsync verwenden:**  
    Eine flexible Methode, um /userdata zu synchronisieren, ist rsync. Dies ermöglicht zudem inkrementelle Backups:
    
    ```bash
    rsync -avz /userdata /mnt/usb/backup_userdata
    ```
    
    Dieses Kommando kopiert rekursiv alle Dateien und Verzeichnisse mit Erhaltung der Dateiberechtigungen und zeigt den Fortschritt an.
    

---

## 4. Wiederherstellung

### A. Wiederherstellung eines SD-Karten-Images

- **Image zurückschreiben:**  
    Nutzen Sie wieder `dd` oder ein entsprechendes Tool, um das Image zurück auf die SD-Karte zu schreiben:
    
    ```bash
    sudo dd if=/Pfad/zur/sicherung.img of=/dev/sdX bs=4M status=progress
    ```
    

### B. Wiederherstellung des /userdata-Verzeichnisses

- Kopieren Sie im Fall eines manuellen Backups das gesicherte /userdata-Verzeichnis zurück in den entsprechenden Pfad:
    
    ```bash
    cp -r /mnt/usb/backup_userdata /userdata
    ```
    

Stellen Sie sicher, dass Batocera im Zeitpunkt des Kopiervorgangs nicht aktiv auf diese Dateien zugreift. Ein Neustart in den Wiederherstellungsmodus oder ein erneutes sauberes Herunterfahren vor und nach dem Kopiervorgang können hier hilfreich sein.

---

## 5. Automatisierung und Tipps

- **Regelmäßige Backups:**  
    Erstellen Sie regelmäßige Backups, um bei Systemaktualisierungen oder Änderungen nicht den Überblick zu verlieren.
    
- **Automatisierte Skripte:**  
    Sie können ein Bash-Skript schreiben, das mithilfe von rsync oder cp automatisierte Backups ausführt. Beispiel:
    
    ```bash
    #!/bin/bash
    BACKUP_DIR="/mnt/usb/backup_userdata_$(date +%Y%m%d)"
    mkdir -p "$BACKUP_DIR"
    rsync -avz /userdata/ "$BACKUP_DIR/"
    ```
    
- **Prüfung der Backups:**  
    Testen Sie ab und zu das Wiederherstellen der Backups in einer Testumgebung, um sicherzustellen, dass im Ernstfall alles reibungslos funktioniert.
    

---

Mit diesen Schritten können Sie eine umfassende Datensicherung Ihres Batocera Linux Systems durchführen und bei Bedarf auch wiederherstellen. Falls noch spezifische Fragen zu einzelnen Schritten oder zusätzlichen Optionen auftreten, können Sie diese gerne im Detail erfragen.
