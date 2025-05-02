**Bereitstellung der Installation für sav-linux-free**

Der Benutzer möchte wissen, wie man das Paket „sav-linux-free“ installiert. Es scheint, dass das Paket Sophos Anti-Virus für Linux ist. Es wird nicht in den Standard-Repositories gefunden, aber man kann das Tar.gz-Paket von der Sophos-Website herunterladen, entpacken und den Installations-Skript ausführen.

Ich sollte die Schritte klar in einer Anleitung darstellen, aber da die Installation nicht ganz einfach ist, überlege ich, ob ich mehrere Quellen zitieren sollte, um die Genauigkeit zu gewährleisten.

**Überlegungen zur Anzahl der Zitate**

Ich denke darüber nach, wie viele Zitate ich für die Anleitung zur Installation von „sav-linux-free“ verwenden sollte. Es scheint nicht extrem einfach zu sein, aber das bedeutet, dass ich mindestens 3 Zitate verwenden könnte. Ich sollte auf jeden Fall nach aktuellen Quellen suchen, um die Installation zu verifizieren, vor allem von der Sophos-Website. Diverse Quellen sind wichtig, also werde ich sicherstellen, dass ich zumindest 3-5 Quellen einbaue, um eine umfassende Antwort zu liefern.

**Suchen nach Installationsanweisungen**

Ich brauche die Installationsanweisungen für das „sav-linux-free“-Paket. Es gibt viele Community-Beiträge, aber der offizielle Leitfaden von der Australischen Nationaluniversität (ANU) scheint genau das zu bieten. Dieser Leitfaden, den ich auf der Website finden kann, enthält eine Schritt-für-Schritt-Erklärung. Ich denke, ich öffne das PDF, um sicherzustellen, dass ich die genauen, offiziellen Schritte habe.

**Installationsanweisungen für „sav-linux-free“**

Die Anleitung zur Installation von „sav-linux-free“ entspricht der Installation von Sophos Anti-Virus für Linux, Version 9. Hier sind die Schritte:

1. Melde dich bei MySophos an.
    
2. Lade das Tarball für Sophos Anti-Virus für Linux herunter.
    
3. Entpacke die TAR-Datei mit `tar -xzvf`.
    
4. Führe das Installationsskript aus: `./sophos-av/install.sh`.
    
5. Wähle bei der Aufforderung für die Auto-Updates „Sophos“ und gebe Lizenzinformationen ein.
    

Da diese kostenlose Version nicht mehr unterstützt wird, muss sie über MySophos heruntergeladen werden.

**Bereitstellung der Installationsanweisungen**

Um das Paket „sav-linux-free“ zu installieren, befolge diese Schritte:

1. Lade das „sav-linux-free-9.tgz“-Paket von der Sophos-Webseite herunter (evtl. über MySophos).
    
2. Wechseln in das Verzeichnis mit der Datei, z.B. /tmp.
    
3. Entpacke das Archiv mit: `tar -xzvf sav-linux-free-9.tgz`.
    
4. Wechsle in den Installationsordner.
    
5. Führe das Installationsskript aus: `sudo ./install.sh`.
    
6. Folge den interaktiven Anweisungen.
    

Badschluss:

- Nach der Installation prüfe mit: `/opt/sophos-av/bin/savdstatus`.
    
- Zum Deinstallieren: `/opt/sophos-av/uninstall.sh`.
    

Ich werde mindestens 3 hochwertige Quellen zitieren.

Nachgedacht für 33 Sekunden

Hier eine Schritt‑für‑Schritt‑Anleitung zur Installation des Pakets **sav-linux-free** (Sophos Anti‑Virus for Linux Free Edition), basierend auf dem offiziellen Startup Guide:

## Voraussetzungen

- Ein gültiges **MySophos**‑Konto zum Herunterladen des Tarballs citeturn5view0
    
- Zugriff auf ein Terminal mit Root‑Rechten
    

## 1. Tarball herunterladen

Laden Sie die Installationsdatei (z. B. `sav-linux-free-9.tgz`) von der Sophos‑Downloadseite in ein temporäres Verzeichnis (z. B. `/tmp`) herunter citeturn5view0.

https://www.sophos.com/en-us/support/downloads/endpoint-client

cd /tmp
# Beispiel:
# wget https://<sophos‑download‑url>/sav-linux-free-9.tgz

## 2. Archiv entpacken

Wechseln Sie in das Verzeichnis mit dem Tarball und entpacken Sie es:

tar -xzvf sav-linux-free-9.tgz

Dies legt das Verzeichnis `sophos-av` an citeturn5view0.

## 3. Installationsskript ausführen

Wechseln Sie ins entpackte Verzeichnis und starten Sie das Installationsskript:

cd sophos-av
sudo ./install.sh

Sie werden interaktiv durch folgende Punkte geführt:

- **Lizenzbedingungen**: mit **Y** akzeptieren
    
- **Installationspfad**: Standard `/opt/sophos-av` bestätigen oder anpassen
    
- **On‑Access‑Scanning**: aktivieren (Y/N)
    
- **Update‑Typ**: „Sophos“ auswählen und den bereitgestellten Benutzername/Passwort eingeben citeturn5view0.
    

## 4. Automatische Updates

Nach der Installation aktualisiert sich Sophos Anti‑Virus standardmäßig alle 60 Minuten über das Internet citeturn5view0.

## 5. Installation prüfen

- **On‑Access‑Service-Status**:
    
    ```bash
    sudo /opt/sophos-av/bin/savdstatus
    ```
    
- **On‑Demand‑Scan** (komplettes System):
    
    ```bash
    sudo savscan /
    ```
    

Damit ist **sav-linux-free** installiert und einsatzbereit citeturn5view0.
