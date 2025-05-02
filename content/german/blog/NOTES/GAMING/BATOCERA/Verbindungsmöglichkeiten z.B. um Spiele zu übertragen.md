Um Spiele auf deinen **Batocera-PC** zu kopieren, hast du mehrere Möglichkeiten. Hier sind die gängigsten Methoden:

### **1. Netzwerkfreigabe (empfohlen)**

Batocera stellt eine **Netzwerkfreigabe** bereit, die du über einen anderen PC oder Mac erreichen kannst.

#### **Von Windows aus:**

1. Stelle sicher, dass dein **Batocera-PC** und dein **Windows-PC** im selben Netzwerk sind.
2. Öffne den **Datei-Explorer** und gib in die Adressleiste ein:
    
    ```
    \\BATOCERA\
    ```
    
    oder, falls das nicht funktioniert:
    
    ```
    \\192.168.X.X\
    ```
    
    _(Ersetze `192.168.X.X` mit der IP-Adresse deines Batocera-PCs, die du im Menü unter "Netzwerkeinstellungen" findest.)_
3. Falls eine Anmeldung erforderlich ist:
    - Benutzer: `root`
    - Passwort: `linux`
4. Du siehst nun verschiedene Ordner. Kopiere deine Spiele in den jeweiligen **ROMs-Ordner** (z. B. `\\BATOCERA\share\roms\`).

#### **Von macOS oder Linux aus:**

1. Öffne den **Dateimanager**.
2. Wähle **"Mit Server verbinden"** und gib ein:
    
    ```
    smb://BATOCERA/
    ```
    
    oder
    
    ```
    smb://192.168.X.X/
    ```
    
3. Falls eine Anmeldung verlangt wird:
    - Benutzer: `root`
    - Passwort: `linux`
4. Navigiere in den Ordner `share/roms/` und kopiere deine Spiele hinein.

---

### **2. USB-Stick oder externe Festplatte**

Falls du die Spiele nicht über das Netzwerk kopieren möchtest, kannst du sie auch mit einem **USB-Stick oder einer externen Festplatte** übertragen:

1. Formatiere den Stick/Festplatte im **exFAT- oder NTFS-Format** (damit Batocera ihn lesen kann).
2. Erstelle auf dem Stick einen Ordner `roms` und kopiere deine Spiele hinein.
3. Stecke den Stick in den Batocera-PC.
4. Starte Batocera und rufe im Menü unter `Dateimanager` den Stick auf.
5. Kopiere die Spiele in den internen `roms`-Ordner.

---

### **3. FTP- oder SFTP-Verbindung**

Falls du SSH aktiviert hast, kannst du Spiele auch per **SFTP** kopieren:

1. Aktiviere SSH in Batocera (`Einstellungen → System → SSH aktivieren`).
2. Verbinde dich mit einem SFTP-Client wie **WinSCP** oder **FileZilla**.
    - **Host**: `192.168.X.X` (IP-Adresse von Batocera)
    - **Benutzername**: `root`
    - **Passwort**: `linux`
    - **Protokoll**: SFTP (nicht FTP!)
3. Navigiere zum Ordner `/userdata/roms/` und lade deine Spiele hoch.

---

### **4. Direkt auf die Batocera-Festplatte schreiben**

Falls du direkten Zugriff auf die Batocera-SSD/HDD hast, kannst du sie an einen anderen PC anschließen:

1. Starte einen Linux-PC oder nutze ein Tool wie **Ext2Fsd** (Windows).
2. Navigiere zu `/userdata/roms/` und kopiere deine Spiele hinein.
3. Schließe die Festplatte wieder an den Batocera-PC an und starte ihn.

---

### **Fazit**

Die einfachste Methode ist die **Netzwerkfreigabe** (`\\BATOCERA\` oder `smb://BATOCERA/`), da sie keinen zusätzlichen Speicher erfordert. Falls das nicht geht, kannst du eine **USB-Festplatte** oder **SFTP** nutzen.
