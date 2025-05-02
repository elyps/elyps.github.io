## **Windows Netzlaufwerk in WSL mounten**

Um dein Netzlaufwerk im Windows Subsystem für Linux (WSL) zu verbinden, kannst du mit den folgenden Befehlen arbeiten:

Für CIFS (Windows Freigabe), benutze:

sudo mount -t cifs //192.168.2.220/files/ROMS/PS3 /mnt/ps3 -o username=DEIN_BENUTZERNAME

Wenn du `drvfs` verwenden möchtest, kannst du Folgendes ausprobieren:

sudo mount -t drvfs '\\192.168.2.220\files\ROMS\PS3' /mnt/ps3

Für WSL2 könnte `mount.cifs` die passende Wahl sein, weil `drvfs` möglicherweise UNC-Pfade nicht direkt unterstützt.

Nachgedacht für 12 Sekunden

1. **Vorbereitung**  
    Stelle sicher, dass in deiner WSL-Distribution das Paket `cifs-utils` installiert ist. Öffne ein WSL-Terminal und gib ein:
    
    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```
    
2. **Mount-Punkt anlegen**  
    Lege einen Ordner an, in dem das Netzlaufwerk eingehängt wird, z. B.:
    
    ```bash
    sudo mkdir -p /mnt/ps3
    ```
    
3. **Netzlaufwerk mounten**  
    Verwende `mount.cifs`, um die UNC-Adresse einzuhängen. Ersetze `BENUTZER` und `PASSWORT` durch deine Zugangsdaten (falls benötigt):
    
    ```bash
    sudo mount -t cifs \
      //192.168.2.220/files/ROMS/PS3 \
      /mnt/ps3 \
      -o username=BENUTZER,password=PASSWORT,uid=$(id -u),gid=$(id -g),vers=3.0
    ```
    
    **Erklärung der Optionen**
    
    - `username`, `password`: Anmeldedaten für das Share (wenn anonymes Lesen möglich ist, kannst du diese weglassen).
        
    - `uid=$(id -u),gid=$(id -g)`: sorgt dafür, dass du im gemounteten Verzeichnis dein eigener Linux-Benutzer bist.
        
    - `vers=3.0`: Protokollversion (ggf. auf `2.1` oder `2.0` ändern, je nach Server).
        
4. **Überprüfung**
    
    ```bash
    ls /mnt/ps3
    ```
    
    Du solltest jetzt den Inhalt des Netzlaufwerks sehen.
    
5. **Automatisches Mounten beim Start (optional)**  
    Öffne deine `/etc/fstab` mit einem Editor:
    
    ```bash
    sudo nano /etc/fstab
    ```
    
    Füge am Ende folgende Zeile hinzu (achte auf gerade Slashes und Anführungszeichen):
    
    ```
    //192.168.2.220/files/ROMS/PS3  /mnt/ps3  cifs  username=BENUTZER,password=PASSWORT,uid=1000,gid=1000,vers=3.0  0  0
    ```
    
    Speichere und schließe die Datei. Mit
    
    ```bash
    sudo mount -a
    ```
    
    wird geprüft, ob alles korrekt eingebunden wird.
    

---

### Alternative: Netzlaufwerk in Windows als Laufwerkbuchstabe einbinden

1. **In Windows**:
    
    - Öffne den Explorer → Rechtsklick auf "Dieser PC" → "Netzlaufwerk verbinden".
        
    - Wähle z. B. Laufwerk `Z:`, gib als Ordner `\\192.168.2.220\files\ROMS\PS3` ein, melde dich an.
        
2. **In WSL**:
    
    ```bash
    cd /mnt/z/     # dort liegt dann das gemappte Netzlaufwerk
    ls             # Inhalte anzeigen
    ```
    

Beide Methoden führen dazu, dass dein Share unter WSL verfügbar ist – wähle, was für deinen Workflow besser passt.

# Ich habe die alternative art gewählt, jedoch wird z nicht angezeigt in wsl

**Beschreibung der WSL-Lösung**

Um in WSL auf das Netzlaufwerk Z: zuzugreifen, muss es manuell gemountet werden, da Netzwerklaufwerke nicht automatisch gemountet werden. Der Befehl lautet: `mount -t drvfs Z: /mnt/z`. Alternativ kann WSL so konfiguriert werden, dass Netzwerklaufwerke automatisch gemountet werden. Dazu muss die Datei `/etc/wsl.conf` bearbeitet werden, um ein entsprechendes Konfigurationssetup einzufügen. Beachte, dass das Hinzufügen der Option `metadata` erforderlich ist, da Netzwerklaufwerke nicht mit der Einstellung „drives = true“ alleine angezeigt werden.

Nachgedacht für 10 Sekunden

Um ein in Windows gemapptes Netzlaufwerk unter WSL sichtbar zu machen, gibt es zwei Wege:

---

## 1. Manuelles Mounten in WSL

1. **Drive in Windows prüfen**  
    Öffne PowerShell und gib ein:
    
    ```powershell
    net use
    ```
    
    Stelle sicher, dass `Z:` auf `\\192.168.2.220\files\ROMS\PS3` zeigt.
    
2. **Mount-Punkt in WSL anlegen**
    
    ```bash
    sudo mkdir -p /mnt/z
    ```
    
3. **Mount mit `drvfs`**
    
    ```bash
    sudo mount -t drvfs Z: /mnt/z -o uid=$(id -u),gid=$(id -g)
    ```
    
    – `drvfs` sorgt für die Windows-Treibereinbindung.  
    – `uid=$(id -u),gid=$(id -g)` setzt dich als Besitzer.
    
4. **Zugriff prüfen**
    
    ```bash
    ls /mnt/z
    ```
    
    Du solltest nun die ROMS-Ordner sehen.
    

---

## 2. Automatisches Mounten beim WSL-Start

### a) `/etc/wsl.conf` anpassen

1. In WSL editieren:
    
    ```bash
    sudo nano /etc/wsl.conf
    ```
    
2. Anhängen:
    
    ```
    [automount]
    enabled = true
    root = /mnt/
    options = "metadata,uid=1000,gid=1000,umask=022,fmask=111"
    drives = true
    ```
    
3. WSL komplett neu starten (in PowerShell):
    
    ```powershell
    wsl --shutdown
    ```
    

### b) Eintrag in `/etc/fstab` (optional)

1. Öffne `/etc/fstab`:
    
    ```bash
    sudo nano /etc/fstab
    ```
    
2. Füge hinzu:
    
    ```
    Z:  /mnt/z  drvfs  uid=1000,gid=1000  0  0
    ```
    
3. Test-Mount:
    
    ```bash
    sudo mount -a
    ```
    

---

**Hinweis:** Unter WSL 2 werden Netzlaufwerke nicht automatisch mit den lokalen Laufwerken synchronisiert. Das manuelle `mount -t drvfs` oder die fstab-Lösung ist hier verlässlich.

Jetzt sollte `/mnt/z` in WSL den Inhalt von `\\192.168.2.220\files\ROMS\PS3` abbilden.
