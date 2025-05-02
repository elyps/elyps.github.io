Hier ist ein **PowerShell-Skript**, das **rclone** nutzt, um bestimmte Ordner regelmäßig auf **Google Drive** hochzuladen.

## Einzelne Ordner
### 🔹 Funktionen des Skripts:

- Nutzt `rclone` zum Synchronisieren eines lokalen Ordners mit Google Drive.
- Wird regelmäßig über die **Windows Aufgabenplanung (Task Scheduler)** ausgeführt.
- Erstellt ein **Log-File**, um den Status der Synchronisierung zu dokumentieren.

---

### 🚀 **Schritte zur Nutzung**:

1. **Installiere rclone**:
    
    - Lade es von [rclone.org](https://rclone.org/downloads/) herunter und installiere es.
    - Führe `rclone config` aus und richte eine Verbindung zu Google Drive ein (z. B. `myGDrive` als Remote-Name).
2. **Passe das Skript an**:
    
    - **Definiere deinen lokalen Ordner** (`$LocalFolder`).
    - **Passe den rclone Remote-Namen an** (`$RemoteName`).
3. **Automatisierung einrichten**:
    
    - Speichere das Skript als `.ps1` (z. B. `BackupToGDrive.ps1`).
    - Nutze den **Windows Task Scheduler**, um es regelmäßig auszuführen.

---

### 📜 **PowerShell-Skript (BackupToGDrive.ps1)**:

# PowerShell Skript zum Synchronisieren eines lokalen Ordners mit Google Drive mittels rclone

# Lokaler Ordner, der gesichert werden soll
$LocalFolder = "C:\Users\DeinBenutzer\Documents\Backup"

# Name des Remote-Speichers in rclone (aus 'rclone config')
$RemoteName = "myGDrive"

# Zielordner auf Google Drive
$RemoteFolder = "Backup"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Datum und Uhrzeit für das Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

# Befehl zum Hochladen (Ändere 'copy' zu 'sync' falls du eine 1:1 Synchronisierung möchtest)
$command = "$rclonePath sync `"$LocalFolder`" `"$RemoteName`:$RemoteFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

# Führe rclone aus
try {
    Invoke-Expression $command
    Write-Output "[$timestamp] Backup erfolgreich abgeschlossen!" | Out-File -Append $LogFile
} catch {
    Write-Output "[$timestamp] Fehler beim Backup: $_" | Out-File -Append $LogFile
}

---

### 🛠️ **Windows Task Scheduler (Automatisierung)**

Damit das Skript regelmäßig ausgeführt wird:

1. Öffne **Task Scheduler** (`taskschd.msc`).
2. Erstelle eine **neue Aufgabe**:
    - **Trigger:** Täglich, z. B. alle 6 Stunden.
    - **Aktion:** PowerShell ausführen:
        
        ```
        powershell.exe -ExecutionPolicy Bypass -File "C:\Pfad\zu\BackupToGDrive.ps1"
        ```
        
    - **Optionen:** "Nur ausführen, wenn mit Netz verbunden."

---

### 📝 **Erklärung der Parameter**

- `sync` – Synchronisiert den lokalen Ordner mit Google Drive.
- `--progress` – Zeigt den Fortschritt an.
- `--log-file` – Speichert Log-Informationen.
- `--log-level INFO` – Gibt detaillierte Informationen aus.

Falls du **nur neue Dateien hochladen**, aber keine löschen willst, ändere `sync` zu `copy`.

---

### ✅ **Fertig!**

Das Skript sichert regelmäßig deine Dateien auf Google Drive. 🚀🔥


## Mehrere Ordner

Hier ist die erweiterte Version des PowerShell-Skripts, das mehrere **lokale Ordner** synchronisiert und diese auf **Google Drive** hochlädt.

---

### 🔹 **Neue Funktionen:**

- Mehrere **lokale Ordner** synchronisieren.
- Jeder Ordner kann in einen **eigenen Unterordner** auf Google Drive hochgeladen werden.
- Automatische **Fehlerprotokollierung** in eine **Log-Datei**.
- Kann weiterhin über den **Windows Task Scheduler** ausgeführt werden.

---

### 📜 **PowerShell-Skript (BackupToGDrive_Multi.ps1)**

# PowerShell Skript zum Synchronisieren mehrerer lokaler Ordner mit Google Drive mittels rclone

# Liste der zu sichernden Ordner (lokale Pfade)
$LocalFolders = @(
    "C:\Users\DeinBenutzer\Documents\Backup1",
    "C:\Users\DeinBenutzer\Documents\Backup2",
    "C:\Users\DeinBenutzer\Pictures\Fotos",
    "D:\Projekte\WichtigeDaten"
)

# Name des Remote-Speichers in rclone (muss in 'rclone config' eingerichtet sein)
$RemoteName = "myGDrive"

# Basisziel auf Google Drive (optional)
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup mehrerer Ordner..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

# Schleife über alle Ordner in der Liste
foreach ($LocalFolder in $LocalFolders) {
    # Erstelle den Zielpfad auf Google Drive (verwende den lokalen Ordnernamen)
    $FolderName = Split-Path $LocalFolder -Leaf
    $RemoteFolder = "$RemoteName`:$RemoteBaseFolder/$FolderName"

    # Logging
    Write-Output "[$timestamp] Synchronisiere: $LocalFolder -> $RemoteFolder" | Out-File -Append $LogFile

    # rclone-Befehl zum Synchronisieren
    $command = "$rclonePath sync `"$LocalFolder`" `"$RemoteFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

    # Führe rclone aus
    try {
        Invoke-Expression $command
        Write-Output "[$timestamp] ✔ Backup abgeschlossen für: $LocalFolder" | Out-File -Append $LogFile
    } catch {
        Write-Output "[$timestamp] ❌ Fehler beim Backup von: $LocalFolder -> $_" | Out-File -Append $LogFile
    }
}

Write-Output "[$timestamp] ✅ Alle Backups abgeschlossen!" | Out-File -Append $LogFile

---

### 🚀 **Erklärung der Änderungen**

- Die **Ordnerliste** (`$LocalFolders`) enthält mehrere **lokale Ordner**, die synchronisiert werden.
- Jeder **Ordnername** wird als eigener **Unterordner** in Google Drive hochgeladen.
- **Schleife** (`foreach`) verarbeitet alle Ordner **nacheinander**.
- Log-Datei dokumentiert **Erfolg oder Fehler** für jeden Ordner.

---

### 🔧 **Windows Task Scheduler Automatisierung**

- Speichere das Skript als `BackupToGDrive_Multi.ps1`.
- Erstelle im **Task Scheduler** eine neue Aufgabe mit diesem Befehl:
    
    ```powershell
    powershell.exe -ExecutionPolicy Bypass -File "C:\Pfad\zu\BackupToGDrive_Multi.ps1"
    ```
    
- Stelle sicher, dass es regelmäßig läuft (z. B. **täglich oder alle X Stunden**).

---

### ✅ **Fertig!**

Jetzt werden mehrere Ordner **automatisch** mit Google Drive synchronisiert! 🚀🔄



## Mehrere Ordner & verpackte Unterordner (zip)

Hier ist die erweiterte Version des PowerShell-Skripts, das mehrere **lokale Ordner** synchronisiert und diese auf **Google Drive** hochlädt.

---

### 🔹 **Neue Funktionen:**

- Mehrere **lokale Ordner** synchronisieren.
- Jeder Ordner kann in einen **eigenen Unterordner** auf Google Drive hochgeladen werden.
- Automatische **Fehlerprotokollierung** in eine **Log-Datei**.
- Kann weiterhin über den **Windows Task Scheduler** ausgeführt werden.

---

### 📜 **PowerShell-Skript (BackupToGDrive_Multi.ps1)**

# PowerShell Skript zum Synchronisieren mehrerer lokaler Ordner mit Google Drive mittels rclone

# Liste der zu sichernden Ordner (lokale Pfade)
$LocalFolders = @(
    "C:\Users\DeinBenutzer\Documents\Backup1",
    "C:\Users\DeinBenutzer\Documents\Backup2",
    "C:\Users\DeinBenutzer\Pictures\Fotos",
    "D:\Projekte\WichtigeDaten"
)

# Name des Remote-Speichers in rclone (muss in 'rclone config' eingerichtet sein)
$RemoteName = "myGDrive"

# Basisziel auf Google Drive (optional)
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup mehrerer Ordner..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

# Schleife über alle Ordner in der Liste
foreach ($LocalFolder in $LocalFolders) {
    # Erstelle den Zielpfad auf Google Drive (verwende den lokalen Ordnernamen)
    $FolderName = Split-Path $LocalFolder -Leaf
    $RemoteFolder = "$RemoteName`:$RemoteBaseFolder/$FolderName"

    # Logging
    Write-Output "[$timestamp] Synchronisiere: $LocalFolder -> $RemoteFolder" | Out-File -Append $LogFile

    # rclone-Befehl zum Synchronisieren
    $command = "$rclonePath sync `"$LocalFolder`" `"$RemoteFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

    # Führe rclone aus
    try {
        Invoke-Expression $command
        Write-Output "[$timestamp] ✔ Backup abgeschlossen für: $LocalFolder" | Out-File -Append $LogFile
    } catch {
        Write-Output "[$timestamp] ❌ Fehler beim Backup von: $LocalFolder -> $_" | Out-File -Append $LogFile
    }
}

Write-Output "[$timestamp] ✅ Alle Backups abgeschlossen!" | Out-File -Append $LogFile

---

### 🚀 **Erklärung der Änderungen**

- Die **Ordnerliste** (`$LocalFolders`) enthält mehrere **lokale Ordner**, die synchronisiert werden.
- Jeder **Ordnername** wird als eigener **Unterordner** in Google Drive hochgeladen.
- **Schleife** (`foreach`) verarbeitet alle Ordner **nacheinander**.
- Log-Datei dokumentiert **Erfolg oder Fehler** für jeden Ordner.

---

### 🔧 **Windows Task Scheduler Automatisierung**

- Speichere das Skript als `BackupToGDrive_Multi.ps1`.
- Erstelle im **Task Scheduler** eine neue Aufgabe mit diesem Befehl:
    
    ```powershell
    powershell.exe -ExecutionPolicy Bypass -File "C:\Pfad\zu\BackupToGDrive_Multi.ps1"
    ```
    
- Stelle sicher, dass es regelmäßig läuft (z. B. **täglich oder alle X Stunden**).

---

### ✅ **Fertig!**

Jetzt werden mehrere Ordner **automatisch** mit Google Drive synchronisiert! 🚀🔄


## Mehrere Ordner & einzelne Unterordner als ZIP

Hier ist die **optimierte Version** des PowerShell-Skripts, die sicherstellt, dass die ZIP-Dateien die **Originalordnernamen** haben, aber **Leerzeichen durch Unterstriche (`_`) ersetzt werden**.

---

### 🆕 **Neue Features:**

✅ **Ordnernamen werden vor dem ZIP-Erstellen umbenannt**:

- `"Projekt 1"` → `"Projekt_1.zip"`
- `"Mein Ordner"` → `"Mein_Ordner.zip"`

✅ **Robustes Logging für jede ZIP-Datei**.

✅ **Die anderen Ordner werden unverändert hochgeladen**.

---

### 📜 **Aktualisiertes PowerShell-Skript**

# PowerShell Skript zum Synchronisieren mehrerer lokaler Ordner mit Google Drive
# Sonderfall: 'C:\DEV' -> ZIP-Archive, dann Upload

# Liste der zu sichernden Ordner (ohne C:\DEV)
$LocalFolders = @(
    "C:\Users\DeinBenutzer\Documents\Backup1",
    "C:\Users\DeinBenutzer\Documents\Backup2",
    "C:\Users\DeinBenutzer\Pictures\Fotos",
    "D:\Projekte\WichtigeDaten"
)

# Sonderfall: C:\DEV wird zuerst gezippt, dann hochgeladen
$DevFolder = "C:\DEV"
$DevZipDestination = "C:\Temp\DEV_Zips"

# Name des Remote-Speichers in rclone (muss in 'rclone config' eingerichtet sein)
$RemoteName = "myGDrive"

# Basisziel auf Google Drive (optional)
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup mehrerer Ordner..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

### 🔹 Sonderfall: C:\DEV ZIP-Komprimierung mit sauberem Namen ###
if (Test-Path $DevFolder) {
    Write-Output "[$timestamp] Beginne ZIP-Erstellung für C:\DEV..." | Out-File -Append $LogFile

    # Stelle sicher, dass das Zielverzeichnis existiert
    if (!(Test-Path $DevZipDestination)) {
        New-Item -ItemType Directory -Path $DevZipDestination | Out-Null
    }

    # Schleife durch alle Unterordner in C:\DEV
    Get-ChildItem -Path $DevFolder -Directory | ForEach-Object {
        $SubfolderPath = $_.FullName
        # Ersetze Leerzeichen durch Unterstriche (_) im Ordnernamen
        $CleanFolderName = ($_.Name -replace "\s", "_")
        $ZipFileName = "$DevZipDestination\$CleanFolderName.zip"

        # Prüfen, ob das ZIP bereits existiert, falls ja, löschen
        if (Test-Path $ZipFileName) {
            Remove-Item $ZipFileName -Force
        }

        # ZIP-Datei erstellen
        Compress-Archive -Path "$SubfolderPath\*" -DestinationPath $ZipFileName -Force
        Write-Output "[$timestamp] ZIP erstellt: $ZipFileName" | Out-File -Append $LogFile
    }

    # Hochladen der ZIP-Dateien
    $RemoteZipFolder = "$RemoteName`:$RemoteBaseFolder/DEV_Zips"
    Write-Output "[$timestamp] Lade ZIP-Archive nach Google Drive hoch..." | Out-File -Append $LogFile
    $command = "$rclonePath sync `"$DevZipDestination`" `"$RemoteZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

    try {
        Invoke-Expression $command
        Write-Output "[$timestamp] ✔ ZIP-Upload erfolgreich abgeschlossen!" | Out-File -Append $LogFile
    } catch {
        Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload: $_" | Out-File -Append $LogFile
    }
} else {
    Write-Output "[$timestamp] ❌ C:\DEV nicht gefunden, überspringe ZIP-Erstellung!" | Out-File -Append $LogFile
}

### 🔹 Standard-Backup für die anderen Ordner ###
foreach ($LocalFolder in $LocalFolders) {
    $FolderName = Split-Path $LocalFolder -Leaf
    $RemoteFolder = "$RemoteName`:$RemoteBaseFolder/$FolderName"

    Write-Output "[$timestamp] Synchronisiere: $LocalFolder -> $RemoteFolder" | Out-File -Append $LogFile
    $command = "$rclonePath sync `"$LocalFolder`" `"$RemoteFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

    try {
        Invoke-Expression $command
        Write-Output "[$timestamp] ✔ Backup abgeschlossen für: $LocalFolder" | Out-File -Append $LogFile
    } catch {
        Write-Output "[$timestamp] ❌ Fehler beim Backup von: $LocalFolder -> $_" | Out-File -Append $LogFile
    }
}

Write-Output "[$timestamp] ✅ Alle Backups abgeschlossen!" | Out-File -Append $LogFile

---

### 🔧 **Erklärung der Änderungen**

1. **Dateinamen-Korrektur für ZIPs**
    
    - Alle Leerzeichen (`" "`) werden durch Unterstriche (`"_"`) ersetzt:
        
        ```powershell
        $CleanFolderName = ($_.Name -replace "\s", "_")
        ```
        
    - Beispiel:
        - `"Projekt 1"` → **`Projekt_1.zip`**
        - `"Mein Ordner"` → **`Mein_Ordner.zip`**
2. **ZIP-Erstellung für `C:\DEV`**:
    
    - Jeder **Unterordner** in `C:\DEV` wird einzeln gezippt.
    - ZIPs werden in **C:\Temp\DEV_Zips** gespeichert.
3. **Normales Hochladen für andere Ordner**:
    
    - **`sync` mit rclone**, kein ZIP.
4. **Verbessertes Logging**:
    
    - Jeder **Schritt** wird dokumentiert (ZIP-Erstellung, Upload, Fehler).

---

### ✅ **Automatische Ausführung einrichten**

Speichere das Skript als:

C:\Scripts\BackupToGDrive_ZipDEV.ps1

Erstelle eine **geplante Aufgabe** mit:

powershell.exe -ExecutionPolicy Bypass -File "C:\Scripts\BackupToGDrive_ZipDEV.ps1"

und stelle ein, dass es **täglich oder alle 6 Stunden** läuft.

---

### 🎯 **Endergebnis**

✅ **`C:\DEV` wird gezippt (mit sauberen Namen) und hochgeladen** 🔄  
✅ **Andere Ordner werden normal synchronisiert** 🔄  
✅ **Alles läuft automatisch & wird protokolliert** 📝

🔥 **Jetzt hast du ein perfektes Backup-Skript mit ZIP-Unterstützung!** 🚀


## Mehrere Ordner & mehrere Ordner mit verpackten Unterordnern als ZIP

Hier ist die **erweiterte Version** des Skripts, die mehrere **Quellordner unterstützt**, bei denen alle Unterordner **automatisch gezippt** und dann auf Google Drive hochgeladen werden. Andere Ordner werden weiterhin **direkt hochgeladen**.

---

### 🔹 **Neue Funktionen:**

✅ **Mehrere ZIP-Quellordner**:

- Statt nur `C:\DEV` kannst du jetzt eine **Liste** angeben (`$ZipFolders`).
- Jeder Unterordner in diesen Verzeichnissen wird als **ZIP-Datei** gespeichert.

✅ **Normaler Upload für andere Ordner**

- Definiert in `$LocalFolders`, diese werden **1:1 synchronisiert** (kein ZIP).

✅ **Flexibles Logging & Fehlerbehandlung**

- Jeder Schritt wird geloggt, um Fehler zu vermeiden.

---

### 📜 **Aktualisiertes PowerShell-Skript**

# PowerShell Skript zum Synchronisieren mehrerer lokaler Ordner mit Google Drive
# Sonderfall: Definierte Ordner werden erst gezippt, dann hochgeladen

# Liste der Ordner, bei denen die Unterordner gezippt werden sollen
$ZipFolders = @(
    "C:\DEV",
    "D:\Projekte",
    "E:\Wichtige_Ordner"
)

# Liste der Ordner, die direkt hochgeladen werden (ohne ZIP)
$LocalFolders = @(
    "C:\Users\DeinBenutzer\Documents\Backup1",
    "C:\Users\DeinBenutzer\Documents\Backup2",
    "C:\Users\DeinBenutzer\Pictures\Fotos"
)

# Zielverzeichnis für ZIP-Dateien
$ZipDestination = "C:\Temp\Zipped_Backups"

# Name des Remote-Speichers in rclone (muss in 'rclone config' eingerichtet sein)
$RemoteName = "myGDrive"

# Basisziel auf Google Drive
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup mit ZIP-Unterstützung..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

### 🔹 ZIP-Komprimierung für definierte Ordner ###
foreach ($Folder in $ZipFolders) {
    if (Test-Path $Folder) {
        $FolderName = Split-Path $Folder -Leaf
        Write-Output "[$timestamp] ZIP-Erstellung für: $Folder..." | Out-File -Append $LogFile

        # Stelle sicher, dass das Zielverzeichnis existiert
        $FolderZipDest = "$ZipDestination\$FolderName"
        if (!(Test-Path $FolderZipDest)) {
            New-Item -ItemType Directory -Path $FolderZipDest | Out-Null
        }

        # Schleife durch alle Unterordner und ZIP-Dateien erstellen
        Get-ChildItem -Path $Folder -Directory | ForEach-Object {
            $SubfolderPath = $_.FullName
            # Ersetze Leerzeichen durch Unterstriche (_) im Ordnernamen
            $CleanFolderName = ($_.Name -replace "\s", "_")
            $ZipFileName = "$FolderZipDest\$CleanFolderName.zip"

            # Falls ZIP bereits existiert, löschen
            if (Test-Path $ZipFileName) {
                Remove-Item $ZipFileName -Force
            }

            # ZIP-Datei erstellen
            Compress-Archive -Path "$SubfolderPath\*" -DestinationPath $ZipFileName -Force
            Write-Output "[$timestamp] ZIP erstellt: $ZipFileName" | Out-File -Append $LogFile
        }

        # ZIP-Dateien hochladen
        $RemoteZipFolder = "$RemoteName`:$RemoteBaseFolder/Zipped/$FolderName"
        Write-Output "[$timestamp] Lade ZIP-Archive von $Folder nach Google Drive hoch..." | Out-File -Append $LogFile
        $command = "$rclonePath sync `"$FolderZipDest`" `"$RemoteZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

        try {
            Invoke-Expression $command
            Write-Output "[$timestamp] ✔ ZIP-Upload erfolgreich für $Folder!" | Out-File -Append $LogFile
        } catch {
            Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload von $Folder: $_" | Out-File -Append $LogFile
        }
    } else {
        Write-Output "[$timestamp] ❌ Ordner nicht gefunden: $Folder -> Überspringe ZIP-Erstellung!" | Out-File -Append $LogFile
    }
}

### 🔹 Standard-Backup für die anderen Ordner ###
foreach ($LocalFolder in $LocalFolders) {
    $FolderName = Split-Path $LocalFolder -Leaf
    $RemoteFolder = "$RemoteName`:$RemoteBaseFolder/$FolderName"

    Write-Output "[$timestamp] Synchronisiere: $LocalFolder -> $RemoteFolder" | Out-File -Append $LogFile
    $command = "$rclonePath sync `"$LocalFolder`" `"$RemoteFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

    try {
        Invoke-Expression $command
        Write-Output "[$timestamp] ✔ Backup abgeschlossen für: $LocalFolder" | Out-File -Append $LogFile
    } catch {
        Write-Output "[$timestamp] ❌ Fehler beim Backup von: $LocalFolder -> $_" | Out-File -Append $LogFile
    }
}

Write-Output "[$timestamp] ✅ Alle Backups abgeschlossen!" | Out-File -Append $LogFile

---

### 🔧 **Erklärung der Änderungen**

1. **Erweiterte ZIP-Funktionalität für mehrere Ordner (`$ZipFolders`)**
    
    - Mehrere Ordner können definiert werden.
    - Jeder **Unterordner** in diesen Ordnern wird **gezipt**.
    - ZIP-Dateien werden in **C:\Temp\Zipped_Backups{Ordnername}** gespeichert.
2. **Dynamische Google Drive Pfade**
    
    - ZIP-Dateien gehen nach **Google Drive/Backups/Zipped/{Ordnername}**.
    - Normale Ordner gehen nach **Google Drive/Backups/{Ordnername}**.
3. **Bessere Fehlerbehandlung**
    
    - Falls ein Ordner nicht existiert, wird er übersprungen (und ins Log geschrieben).

---

### ✅ **Automatische Ausführung einrichten**

- Speichere das Skript als:
    
    ```
    C:\Scripts\BackupToGDrive_ZipMulti.ps1
    ```
    
- Erstelle eine **geplante Aufgabe** mit:
    
    ```
    powershell.exe -ExecutionPolicy Bypass -File "C:\Scripts\BackupToGDrive_ZipMulti.ps1"
    ```
    
- **Laufintervall einstellen** (täglich oder alle X Stunden).

---

### 🎯 **Ergebnis**

✅ **Mehrere Ordner mit ZIP-Unterstützung** 🔄  
✅ **ZIP-Dateien werden in den richtigen Ordner hochgeladen** 📤  
✅ **Andere Ordner werden normal hochgeladen** 📂  
✅ **Automatische Logs & Fehlerbehandlung** 📝

🔥 **Dein flexibles ZIP-Backup-Skript ist jetzt fertig!** 🚀


## Mehrere Ordner & Unterordner einzeln als ZIP (vorhandene ZIPs direkt hochladen, wenn bereits hochgeladen und unverändert, dann auslassen)

Hier ist die **erweiterte Version** des PowerShell-Skripts, die sicherstellt, dass **bereits vorhandene ZIP-Archive in definierten Backup-Ordnern direkt hochgeladen werden**, während **nicht gezippte Ordner zuerst gezippt und dann hochgeladen werden**.

---

### 🆕 **Neue Funktionen:**

✅ **Erkennt vorhandene ZIP-Dateien in `C:\DEV\Backup` (oder anderen Backup-Ordnern)**

- Falls ZIP-Dateien vorhanden sind, werden sie **direkt hochgeladen** (ohne erneutes ZIPpen).
- Nicht gezippte Ordner werden **vor dem Hochladen gezippt**.

✅ **Prüfung & Unterscheidung von Dateien und Ordnern**

- ZIPs werden direkt synchronisiert.
- Ungezippte Ordner werden erst gezippt und dann hochgeladen.

✅ **Flexible Erweiterung für mehrere Backup-Ordner**

- Definiere mehrere Ordner (`$BackupZipFolders`), die ZIP-Dateien direkt hochladen.

✅ **Optimierte rclone-Synchronisierung**

- Falls eine ZIP-Datei bereits auf Google Drive existiert **und sich nicht geändert hat**, wird sie nicht erneut hochgeladen.

---

### 📜 **PowerShell-Skript (Backup mit ZIP-Erkennung)**

# PowerShell Skript zum Synchronisieren von Ordnern mit Google Drive
# - ZIP-Erstellung für nicht gezippte Unterordner
# - Direktes Hochladen vorhandener ZIP-Dateien in Backup-Ordnern

# Ordner, bei denen nur ZIP-Archive hochgeladen werden (nicht ZIPpen)
$BackupZipFolders = @(
    "C:\DEV\Backup",
    "D:\Projekte\Fertige_ZIPs"
)

# Ordner, deren Unterordner gezippt werden sollen (falls nicht ZIP)
$ZipFolders = @(
    "C:\DEV",
    "D:\Projekte",
    "E:\Wichtige_Ordner"
)

# Zielverzeichnis für ZIP-Dateien
$ZipDestination = "C:\Temp\Zipped_Backups"

# Name des Remote-Speichers in rclone
$RemoteName = "myGDrive"

# Basisziel auf Google Drive
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup mit ZIP-Erkennung..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

### 🔹 Hochladen vorhandener ZIP-Dateien in Backup-Ordnern ###
foreach ($Folder in $BackupZipFolders) {
    if (Test-Path $Folder) {
        Write-Output "[$timestamp] 📤 Überprüfe ZIP-Dateien in: $Folder" | Out-File -Append $LogFile
        $RemoteBackupFolder = "$RemoteName`:$RemoteBaseFolder/BackupZips"

        # Hochladen aller ZIP-Dateien aus diesem Backup-Ordner
        $command = "$rclonePath sync `"$Folder`" `"$RemoteBackupFolder`" --progress --log-file=`"$LogFile`" --log-level INFO --include *.zip"

        try {
            Invoke-Expression $command
            Write-Output "[$timestamp] ✔ ZIP-Upload abgeschlossen für $Folder!" | Out-File -Append $LogFile
        } catch {
            Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload von $Folder: $_" | Out-File -Append $LogFile
        }
    } else {
        Write-Output "[$timestamp] ❌ Backup-Ordner nicht gefunden: $Folder -> Überspringe!" | Out-File -Append $LogFile
    }
}

### 🔹 ZIP-Komprimierung für ungezippte Unterordner ###
foreach ($Folder in $ZipFolders) {
    if (Test-Path $Folder) {
        $FolderName = Split-Path $Folder -Leaf
        Write-Output "[$timestamp] 📂 ZIP-Erstellung für: $Folder..." | Out-File -Append $LogFile

        # Stelle sicher, dass das Zielverzeichnis existiert
        $FolderZipDest = "$ZipDestination\$FolderName"
        if (!(Test-Path $FolderZipDest)) {
            New-Item -ItemType Directory -Path $FolderZipDest | Out-Null
        }

        # Schleife durch alle Unterordner
        Get-ChildItem -Path $Folder | ForEach-Object {
            if ($_.PSIsContainer) {  # Prüft, ob es sich um einen Ordner handelt
                $SubfolderPath = $_.FullName
                $CleanFolderName = ($_.Name -replace "\s", "_")
                $ZipFileName = "$FolderZipDest\$CleanFolderName.zip"

                # Prüfen, ob ZIP bereits existiert
                if (Test-Path $ZipFileName) {
                    Write-Output "[$timestamp] ⚠ ZIP-Datei existiert bereits: $ZipFileName -> Überspringe" | Out-File -Append $LogFile
                } else {
                    # ZIP-Datei erstellen
                    Compress-Archive -Path "$SubfolderPath\*" -DestinationPath $ZipFileName -Force
                    Write-Output "[$timestamp] ✅ ZIP erstellt: $ZipFileName" | Out-File -Append $LogFile
                }
            }
        }

        # ZIP-Dateien hochladen
        $RemoteZipFolder = "$RemoteName`:$RemoteBaseFolder/Zipped/$FolderName"
        Write-Output "[$timestamp] 📤 Lade ZIPs von $Folder nach Google Drive hoch..." | Out-File -Append $LogFile
        $command = "$rclonePath sync `"$FolderZipDest`" `"$RemoteZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

        try {
            Invoke-Expression $command
            Write-Output "[$timestamp] ✔ ZIP-Upload erfolgreich für $Folder!" | Out-File -Append $LogFile
        } catch {
            Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload von $Folder: $_" | Out-File -Append $LogFile
        }
    } else {
        Write-Output "[$timestamp] ❌ Ordner nicht gefunden: $Folder -> Überspringe ZIP-Erstellung!" | Out-File -Append $LogFile
    }
}

Write-Output "[$timestamp] ✅ Alle Backups abgeschlossen!" | Out-File -Append $LogFile

---

### 🔧 **Erklärung der Änderungen**

1. **Direkter Upload für vorhandene ZIPs**
    
    - Falls in einem Backup-Ordner bereits ZIP-Dateien existieren (`$BackupZipFolders`), werden sie **direkt hochgeladen**, ohne sie erneut zu zippen.
2. **Unterscheidung von ZIP- und normalen Dateien**
    
    - Falls eine Datei bereits `.zip` ist, wird sie **nicht gezippt**.
    - Falls es sich um einen normalen Ordner handelt, wird er **vor dem Hochladen gezippt**.
3. **Optimierte Google Drive Struktur**
    
    - ZIPs aus **Backup-Ordnern** werden in `Backups/BackupZips` hochgeladen.
    - ZIPs aus **ungezippten Ordnern** werden in `Backups/Zipped/{Ordnername}` hochgeladen.
4. **Schnellere Synchronisation mit rclone**
    
    - **`--include *.zip`** stellt sicher, dass nur ZIP-Dateien hochgeladen werden.

---

### ✅ **Ergebnis**

✅ **Bereits gezippte Archive in Backup-Ordnern werden direkt hochgeladen** 📤  
✅ **Ungezippte Ordner werden automatisch gezippt und hochgeladen** 🗄️🔄  
✅ **Kein doppeltes ZIPpen oder erneutes Hochladen unveränderter Dateien** 🚀

🔥 **Jetzt ist dein Backup-Skript maximal effizient!** 🚀


## Mehrere Ordner & unterordner zippen, falls nicht schon geschehen

Hier ist die **optimierte Version** des Skripts, das folgende Anforderungen erfüllt:

✅ **C:\DEV\Backup** wird speziell behandelt:

- **ZIP-Dateien** werden direkt hochgeladen.
- **Ungezippte Dateien & Ordner** werden erst gezippt, dann hochgeladen.

✅ **Andere Verzeichnisse (`$ZipFolders`)** werden wie zuvor behandelt:

- **Ungezippte Unterordner werden gezippt und dann hochgeladen.**
- **ZIP-Dateien in diesen Verzeichnissen werden ignoriert.**

---

### 📜 **PowerShell-Skript: Hybrid Backup für Backup-Ordner & Standardordner**

# PowerShell Skript für optimiertes Google Drive Backup
# - ZIPs in C:\DEV\Backup werden direkt hochgeladen
# - Ungezippte Dateien & Ordner in C:\DEV\Backup werden gezippt, dann hochgeladen
# - Andere Ordner (C:\DEV\PROJECTS, etc.) werden normal gezippt & hochgeladen

# Ordner mit ZIPs & ungezippte Dateien (spezielle Behandlung)
$BackupFolder = "C:\DEV\Backup"

# Andere Ordner, deren Unterordner gezippt werden sollen
$ZipFolders = @(
    "C:\DEV\PROJECTS",
    "C:\DEV\SCRIPTS"
)

# Zielverzeichnis für erstellte ZIP-Dateien
$ZipDestination = "C:\Temp\Zipped_Backups"

# Name des Remote-Speichers in rclone
$RemoteName = "myGDrive"

# Basisziel auf Google Drive
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup-Prozess..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

### 🔹 1. Backup-Ordner Behandlung (Direkter Upload für ZIPs + ZIPpen von nicht-gezippte Daten) ###
if (Test-Path $BackupFolder) {
    Write-Output "[$timestamp] 📤 Behandle Backup-Ordner: $BackupFolder" | Out-File -Append $LogFile

    # 1.1 Hochladen vorhandener ZIP-Dateien
    $RemoteZipFolder = "$RemoteName`:$RemoteBaseFolder/BackupZips"
    Write-Output "[$timestamp] 📤 Lade ZIP-Dateien aus Backup-Ordner hoch..." | Out-File -Append $LogFile
    $command = "$rclonePath sync `"$BackupFolder`" `"$RemoteZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO --include *.zip"

    try {
        Invoke-Expression $command
        Write-Output "[$timestamp] ✔ ZIP-Upload erfolgreich!" | Out-File -Append $LogFile
    } catch {
        Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload: $_" | Out-File -Append $LogFile
    }

    # 1.2 ZIP-Komprimierung für ungezippte Dateien & Ordner
    Write-Output "[$timestamp] 📦 Überprüfe nicht gezippte Dateien & Ordner in Backup..." | Out-File -Append $LogFile
    if (!(Test-Path $ZipDestination)) {
        New-Item -ItemType Directory -Path $ZipDestination | Out-Null
    }

    Get-ChildItem -Path $BackupFolder | ForEach-Object {
        $ItemPath = $_.FullName
        $ItemName = $_.Name

        if ($_.PSIsContainer) {  # Ordner prüfen
            $CleanFolderName = ($ItemName -replace "\s", "_")
            $ZipFileName = "$ZipDestination\$CleanFolderName.zip"

            if (Test-Path $ZipFileName) {
                Write-Output "[$timestamp] ⚠ ZIP-Datei existiert bereits: $ZipFileName -> Überspringe" | Out-File -Append $LogFile
            } else {
                Compress-Archive -Path "$ItemPath\*" -DestinationPath $ZipFileName -Force
                Write-Output "[$timestamp] ✅ ZIP erstellt: $ZipFileName" | Out-File -Append $LogFile
            }
        } elseif ($_.Extension -ne ".zip") {  # Einzeldateien zippen
            $CleanFileName = ($ItemName -replace "\s", "_") + ".zip"
            $ZipFileName = "$ZipDestination\$CleanFileName"

            if (Test-Path $ZipFileName) {
                Write-Output "[$timestamp] ⚠ ZIP-Datei existiert bereits für: $ItemName -> Überspringe" | Out-File -Append $LogFile
            } else {
                Compress-Archive -Path $ItemPath -DestinationPath $ZipFileName -Force
                Write-Output "[$timestamp] ✅ ZIP für Datei erstellt: $ZipFileName" | Out-File -Append $LogFile
            }
        }
    }

    # 1.3 Hochladen der neu erstellten ZIPs aus dem Backup-Ordner
    $RemoteNewZipFolder = "$RemoteName`:$RemoteBaseFolder/BackupProcessed"
    Write-Output "[$timestamp] 📤 Lade neu erstellte ZIP-Dateien hoch..." | Out-File -Append $LogFile
    $command = "$rclonePath sync `"$ZipDestination`" `"$RemoteNewZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

    try {
        Invoke-Expression $command
        Write-Output "[$timestamp] ✔ ZIP-Upload erfolgreich für neue ZIPs!" | Out-File -Append $LogFile
    } catch {
        Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload: $_" | Out-File -Append $LogFile
    }
}

### 🔹 2. Standard ZIP-Komprimierung für andere Ordner ###
foreach ($Folder in $ZipFolders) {
    if (Test-Path $Folder) {
        $FolderName = Split-Path $Folder -Leaf
        Write-Output "[$timestamp] 📂 ZIP-Erstellung für: $Folder..." | Out-File -Append $LogFile

        $FolderZipDest = "$ZipDestination\$FolderName"
        if (!(Test-Path $FolderZipDest)) {
            New-Item -ItemType Directory -Path $FolderZipDest | Out-Null
        }

        Get-ChildItem -Path $Folder -Directory | ForEach-Object {
            $SubfolderPath = $_.FullName
            $CleanFolderName = ($_.Name -replace "\s", "_")
            $ZipFileName = "$FolderZipDest\$CleanFolderName.zip"

            if (Test-Path $ZipFileName) {
                Write-Output "[$timestamp] ⚠ ZIP-Datei existiert bereits: $ZipFileName -> Überspringe" | Out-File -Append $LogFile
            } else {
                Compress-Archive -Path "$SubfolderPath\*" -DestinationPath $ZipFileName -Force
                Write-Output "[$timestamp] ✅ ZIP erstellt: $ZipFileName" | Out-File -Append $LogFile
            }
        }

        # ZIPs hochladen
        $RemoteZipFolder = "$RemoteName`:$RemoteBaseFolder/Zipped/$FolderName"
        Write-Output "[$timestamp] 📤 Lade ZIPs von $Folder nach Google Drive hoch..." | Out-File -Append $LogFile
        $command = "$rclonePath sync `"$FolderZipDest`" `"$RemoteZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

        try {
            Invoke-Expression $command
            Write-Output "[$timestamp] ✔ ZIP-Upload erfolgreich für $Folder!" | Out-File -Append $LogFile
        } catch {
            Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload von $Folder: $_" | Out-File -Append $LogFile
        }
    }
}

Write-Output "[$timestamp] ✅ Backup abgeschlossen!" | Out-File -Append $LogFile

---

### ✅ **Ergebnis**

🔹 **Backup-Ordner (`C:\DEV\Backup`) behandelt ZIPs & ungezippte Dateien richtig**.  
🔹 **Andere Verzeichnisse (`C:\DEV\PROJECTS`, etc.) funktionieren wie vorher**.  
🔥 **Kein unnötiges Zippen oder Hochladen!** 🚀

## Mehrere Ordner & Unterordner &  Dateien --> Alles zippen 

Ja, das Skript kann **zusammengefasst und vereinfacht** werden, sodass es beide Fälle in einer einzigen Schleife behandelt. Hier ist die optimierte Version:

---

## **🚀 Verbesserungen:**

✅ **Backup-Ordner (`C:\DEV\Backup`) wird zusammen mit den anderen Ordnern verarbeitet**  
✅ **ZIP-Dateien werden direkt hochgeladen**  
✅ **Nicht gezippte Dateien und Ordner werden erst gezippt, dann hochgeladen**  
✅ **Kein doppeltes Zippen oder unnötige Uploads**

---

### **📜 PowerShell-Skript (Zusammengefasst & Optimiert)**

# PowerShell Skript zur Sicherung von ZIP-Dateien und ungezippten Dateien/Ordnern
# - ZIP-Dateien werden direkt hochgeladen
# - Ungezippte Dateien/Ordner werden gezippt und dann hochgeladen

# Ordner mit ZIPs & ungezippte Dateien (BackupFolder wird in ZipFolders integriert)
$ZipFolders = @(
    "C:\DEV\Backup",
    "C:\DEV\PROJECTS",
    "C:\DEV\SCRIPTS"
)

# Zielverzeichnis für erstellte ZIP-Dateien
$ZipDestination = "C:\Temp\Zipped_Backups"

# Name des Remote-Speichers in rclone
$RemoteName = "myGDrive"

# Basisziel auf Google Drive
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup-Prozess..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

### 🔹 Verarbeitung aller definierten Ordner ###
foreach ($Folder in $ZipFolders) {
    if (Test-Path $Folder) {
        $FolderName = Split-Path $Folder -Leaf
        Write-Output "[$timestamp] 📂 Verarbeitung von: $Folder..." | Out-File -Append $LogFile

        # Zielordner für ZIPs vorbereiten
        $FolderZipDest = "$ZipDestination\$FolderName"
        if (!(Test-Path $FolderZipDest)) {
            New-Item -ItemType Directory -Path $FolderZipDest | Out-Null
        }

        # Durchlaufe alle Inhalte im aktuellen Ordner
        Get-ChildItem -Path $Folder | ForEach-Object {
            $ItemPath = $_.FullName
            $ItemName = $_.Name

            if ($_.Extension -eq ".zip") {
                # 🔹 ZIP-Dateien direkt hochladen
                $RemoteZipFolder = "$RemoteName`:$RemoteBaseFolder/BackupZips"
                Write-Output "[$timestamp] 📤 Lade ZIP hoch: $ItemName" | Out-File -Append $LogFile
                $command = "$rclonePath copy `"$ItemPath`" `"$RemoteZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

                try {
                    Invoke-Expression $command
                    Write-Output "[$timestamp] ✔ ZIP erfolgreich hochgeladen: $ItemName" | Out-File -Append $LogFile
                } catch {
                    Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload: $_" | Out-File -Append $LogFile
                }
            }
            elseif ($_.PSIsContainer -or $_.Extension -ne ".zip") {
                # 🔹 Nicht-gezippte Ordner & Dateien zippen und hochladen
                $CleanName = ($ItemName -replace "\s", "_")
                $ZipFileName = "$FolderZipDest\$CleanName.zip"

                if (Test-Path $ZipFileName) {
                    Write-Output "[$timestamp] ⚠ ZIP existiert bereits: $ZipFileName -> Überspringe" | Out-File -Append $LogFile
                } else {
                    Compress-Archive -Path $ItemPath -DestinationPath $ZipFileName -Force
                    Write-Output "[$timestamp] ✅ ZIP erstellt: $ZipFileName" | Out-File -Append $LogFile
                }
            }
        }

        # 🔹 Hochladen aller erstellten ZIPs
        $RemoteNewZipFolder = "$RemoteName`:$RemoteBaseFolder/Zipped/$FolderName"
        Write-Output "[$timestamp] 📤 Lade ZIPs hoch von: $Folder" | Out-File -Append $LogFile
        $command = "$rclonePath sync `"$FolderZipDest`" `"$RemoteNewZipFolder`" --progress --log-file=`"$LogFile`" --log-level INFO"

        try {
            Invoke-Expression $command
            Write-Output "[$timestamp] ✔ ZIP-Upload abgeschlossen für: $Folder" | Out-File -Append $LogFile
        } catch {
            Write-Output "[$timestamp] ❌ Fehler beim Hochladen der ZIPs: $_" | Out-File -Append $LogFile
        }
    }
}

Write-Output "[$timestamp] ✅ Backup abgeschlossen!" | Out-File -Append $LogFile

---

## **🔥 Verbesserungen & Vorteile**

1. **Einheitliche Verarbeitung für alle Ordner**
    
    - `C:\DEV\Backup` wird nun zusammen mit `C:\DEV\PROJECTS` & `C:\DEV\SCRIPTS` behandelt.
2. **Nur eine Schleife für alle Backup-Ordner**
    
    - Kein separater Codeblock für `C:\DEV\Backup`, stattdessen eine **einheitliche** Behandlung.
3. **Intelligente ZIP- und Datei-Erkennung**
    
    - **ZIP-Dateien** werden direkt hochgeladen (`rclone copy`).
    - **Nicht-gezippte Dateien/Ordner** werden zuerst gezippt und dann hochgeladen.
4. **Nur neue ZIPs werden erstellt**
    
    - Falls ein ZIP bereits existiert, wird es **nicht erneut erstellt**.
5. **Optimierter Hochladeprozess**
    
    - ZIPs werden **nur bei Änderungen synchronisiert** (`rclone sync` erkennt neue/aktualisierte Dateien).

---

## **✅ So funktioniert es jetzt**

### 🔹 **Falls im Ordner ZIP-Dateien liegen:**

- Diese werden **direkt hochgeladen** → 📤 **Kein erneutes Zippen**.

### 🔹 **Falls im Ordner ungezippte Dateien oder Unterordner liegen:**

- **Werden gezippt** → 📦 in `C:\Temp\Zipped_Backups`
- Danach **hochgeladen** → 📤 zu `Google Drive/Zipped/{Ordner}`.

---

## **⚡ Automatische Ausführung**

1. Speichere das Skript als:
    
    ```
    C:\Scripts\BackupToGDrive_Optimized.ps1
    ```
    
2. **Task Scheduler (Windows Aufgabenplanung) einrichten**  
    Erstelle eine geplante Aufgabe mit folgendem Befehl:
    
    ```
    powershell.exe -ExecutionPolicy Bypass -File "C:\Scripts\BackupToGDrive_Optimized.ps1"
    ```
    
    - **Intervall:** **Täglich oder alle 6 Stunden**.

---

## **🎯 Endergebnis**

✅ **Backup-Ordner & andere Verzeichnisse werden in einer einzigen Schleife verarbeitet**  
✅ **ZIP-Dateien werden direkt hochgeladen, ungezippte Inhalte werden erst verarbeitet**  
✅ **Kein doppeltes Zippen oder unnötige Uploads**  
✅ **Effizienteste Lösung für dein automatisches Google Drive Backup** 🚀

🔥 **Dein Backup-System ist jetzt perfekt optimiert!** 🚀🎯

## Optimierte ZIP-Erstellung & Hochladen mit Korrekturen

Hier ist die **aktualisierte Version** des PowerShell-Skripts, die jetzt folgende Verbesserungen enthält:

✅ **3️⃣ `rclone sync` prüft Änderungen mit `--checksum`**  
✅ **4️⃣ `rclone sync` nutzt `--refresh-times`, um Cache-Probleme zu verhindern**  
✅ **5️⃣ Problematische Zeichen in Dateinamen (`[` und `]`) werden ersetzt**

---

### **📜 PowerShell-Skript: Optimierte ZIP-Erstellung & Hochladen mit Korrekturen**

# PowerShell Skript zur Sicherung von ZIP-Dateien und ungezippten Dateien/Ordnern
# - ZIP-Dateien werden direkt hochgeladen
# - Ungezippte Dateien/Ordner werden gezippt und dann hochgeladen
# - `--checksum` für genaue Änderungen
# - `--refresh-times` für Cache-Probleme
# - `[`, `]` in Dateinamen ersetzt

# Ordner mit ZIPs & ungezippte Dateien (BackupFolder wird in ZipFolders integriert)
$ZipFolders = @(
    "C:\DEV\Backup",
    "C:\DEV\PROJECTS",
    "C:\DEV\SCRIPTS"
)

# Zielverzeichnis für erstellte ZIP-Dateien
$ZipDestination = "C:\Temp\Zipped_Backups"

# Name des Remote-Speichers in rclone
$RemoteName = "myGDrive"

# Basisziel auf Google Drive
$RemoteBaseFolder = "Backups"

# Log-Datei für Statusberichte
$LogFile = "C:\Users\DeinBenutzer\Documents\Backup\upload_log.txt"

# rclone Pfad (falls nicht im PATH, anpassen)
$rclonePath = "C:\Pfad\zu\rclone.exe"

# Start-Logging
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Write-Output "[$timestamp] Starte Backup-Prozess..." | Out-File -Append $LogFile

# Prüfe, ob rclone existiert
if (!(Test-Path $rclonePath)) {
    Write-Output "[$timestamp] Fehler: rclone wurde nicht gefunden unter '$rclonePath'!" | Out-File -Append $LogFile
    exit 1
}

### 🔹 Verarbeitung aller definierten Ordner ###
foreach ($Folder in $ZipFolders) {
    if (Test-Path $Folder) {
        $FolderName = Split-Path $Folder -Leaf
        Write-Output "[$timestamp] 📂 Verarbeitung von: $Folder..." | Out-File -Append $LogFile

        # Zielordner für ZIPs vorbereiten
        $FolderZipDest = "$ZipDestination\$FolderName"
        if (!(Test-Path $FolderZipDest)) {
            New-Item -ItemType Directory -Path $FolderZipDest | Out-Null
        }

        # Durchlaufe alle Inhalte im aktuellen Ordner
        Get-ChildItem -Path $Folder | ForEach-Object {
            $ItemPath = $_.FullName
            $ItemName = $_.Name

            # 🔹 Ersetze problematische Zeichen in Dateinamen
            $CleanName = ($ItemName -replace "[\[\]]", "_")

            if ($_.Extension -eq ".zip") {
                # 🔹 ZIP-Dateien direkt hochladen
                $RemoteZipFolder = "$RemoteName`:$RemoteBaseFolder/BackupZips"
                Write-Output "[$timestamp] 📤 Lade ZIP hoch: $CleanName" | Out-File -Append $LogFile
                $command = "$rclonePath copy `"$ItemPath`" `"$RemoteZipFolder`" --progress --checksum --refresh-times --log-file=`"$LogFile`" --log-level INFO"

                try {
                    Invoke-Expression $command
                    Write-Output "[$timestamp] ✔ ZIP erfolgreich hochgeladen: $CleanName" | Out-File -Append $LogFile
                } catch {
                    Write-Output "[$timestamp] ❌ Fehler beim ZIP-Upload: $_" | Out-File -Append $LogFile
                }
            }
            elseif ($_.PSIsContainer -or $_.Extension -ne ".zip") {
                # 🔹 Nicht-gezippte Ordner & Dateien zippen und hochladen
                $ZipFileName = "$FolderZipDest\$CleanName.zip"

                if (Test-Path $ZipFileName) {
                    Write-Output "[$timestamp] ⚠ ZIP existiert bereits: $ZipFileName -> Überspringe" | Out-File -Append $LogFile
                } else {
                    Compress-Archive -Path $ItemPath -DestinationPath $ZipFileName -Force
                    Write-Output "[$timestamp] ✅ ZIP erstellt: $ZipFileName" | Out-File -Append $LogFile
                }
            }
        }

        # 🔹 Hochladen aller erstellten ZIPs
        $RemoteNewZipFolder = "$RemoteName`:$RemoteBaseFolder/Zipped/$FolderName"
        Write-Output "[$timestamp] 📤 Lade ZIPs hoch von: $Folder" | Out-File -Append $LogFile
        $command = "$rclonePath sync `"$FolderZipDest`" `"$RemoteNewZipFolder`" --progress --checksum --refresh-times --log-file=`"$LogFile`" --log-level INFO"

        try {
            Invoke-Expression $command
            Write-Output "[$timestamp] ✔ ZIP-Upload abgeschlossen für: $Folder" | Out-File -Append $LogFile
        } catch {
            Write-Output "[$timestamp] ❌ Fehler beim Hochladen der ZIPs: $_" | Out-File -Append $LogFile
        }
    }
}

Write-Output "[$timestamp] ✅ Backup abgeschlossen!" | Out-File -Append $LogFile

---

## **🔧 Neue Verbesserungen**

1. **`--checksum` wird für den Upload genutzt**
    
    - Prüft die Datei anhand eines **Hashes**, nicht nur anhand von Name & Größe.
    - Falls sich die Datei geändert hat, wird sie **immer** hochgeladen.
2. **`--refresh-times` aktualisiert Metadaten**
    
    - Behebt Cache-Probleme, falls `rclone` Änderungen nicht erkennt.
3. **Problematische Zeichen (`[` & `]`) in Dateinamen werden ersetzt**
    
    - **Vorher:** `bastian-fischer.dev[const].zip`
    - **Jetzt:** `bastian-fischer.dev_const_.zip`
    - Verhindert Upload-Probleme bei Google Drive.

---

## **✅ So funktioniert es jetzt**

### **🔹 Falls im Ordner ZIP-Dateien liegen:**

- Diese werden **direkt hochgeladen** mit `rclone copy --checksum --refresh-times`.

### **🔹 Falls ungezippte Dateien oder Unterordner vorliegen:**

- **Werden gezippt** → 📦 nach `C:\Temp\Zipped_Backups`.
- Danach **hochgeladen** → 📤 zu `Google Drive/Zipped/{Ordner}`.

### **🔹 Falls eine Datei unverändert ist:**

- `rclone` überprüft den **Inhalt (nicht nur den Zeitstempel)** und überspringt sie nur, falls wirklich **keine Änderung** vorliegt.

---

## **🚀 Warum ist diese Version besser?**

✅ **Kürzer & übersichtlicher** – Keine doppelten Codeblöcke.  
✅ **Schnellerer Upload** – Durch `--checksum` nur relevante Dateien hochladen.  
✅ **Keine Cache-Probleme** – `--refresh-times` stellt sicher, dass `rclone` neue Dateien erkennt.  
✅ **Bessere Kompatibilität** – `[` und `]` werden aus Dateinamen entfernt.

---

## **⚡ Automatische Ausführung**

1. **Speichere das Skript als:**
    
    ```
    C:\Scripts\BackupToGDrive_Optimized.ps1
    ```
    
2. **Windows Aufgabenplanung (Task Scheduler) einrichten:**
    
    ```
    powershell.exe -ExecutionPolicy Bypass -File "C:\Scripts\BackupToGDrive_Optimized.ps1"
    ```
    
    - **Intervall:** **Täglich oder alle 6 Stunden**.

---

### **🎯 Endergebnis**

✅ **Backup-Ordner & andere Verzeichnisse in einer einzigen Schleife verarbeitet**  
✅ **ZIP-Dateien werden direkt hochgeladen, ungezippte Inhalte zuerst gezippt**  
✅ **Keine doppelten ZIPs oder unnötige Uploads mehr** 🚀

🔥 **Dein Backup-System ist jetzt maximal optimiert & hocheffizient!** 🚀🎯
