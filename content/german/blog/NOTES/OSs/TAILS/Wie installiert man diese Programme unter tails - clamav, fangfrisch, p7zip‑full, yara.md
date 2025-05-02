**Überlegungen zur Installation auf Tails**

Der Benutzer fragt, wie man Programme wie clamav, fangfrisch, p7zip-full und yara auf Tails installiert. Tails ist eine Sicherheitsdistribution, die speziell für Datenschutz und Anonymität entwickelt wurde und auf einem Live-System läuft. Es gibt Einschränkungen, wie etwa, dass Tails beim Neustart den Zustand zurücksetzt und Pakete nur durch die "Zusätzliche Software"-Beibehaltungsfunktion oder manuell mit apt-get installiert werden können. Einige Repositories sind deaktiviert. Ich muss herausfinden, wie man diese Pakete am besten installiert, da bestimmte Repos möglicherweise fehlen.

**Anleitung zur Installation in Tails**

Der Benutzer möchte wissen, wie man Programme unter Tails installiert. Tails bietet die Möglichkeit, Pakete über „Zusätzliche Software“ oder apt zu installieren. Zuerst muss man das Persistente Speicherfeature aktivieren und dann Pakete wie `clamav`, `fangfrisch`, `p7zip-full` und `yara` über das Terminal installieren. Es gibt eine Besonderheit: Einige Pakete sind nicht immer in den Tails-Repositories enthalten, aber mit apt und dem richtigen Setup können sie installiert und nach dem Neustart erhalten werden.

Nachgedacht für 10 Sekunden

### Vorgehensweise Schritt für Schritt

> Die folgenden Befehle funktionieren ab **Tails 6.x** (Debian 12 _bookworm_‑Basis).  
> Setze beim Start von Tails im Willkommens‑Fenster das **Administrations­passwort**, damit `sudo` verfügbar ist.

---

#### 1  Persistent Storage & „Additional Software“ aktivieren

1. **Applications → Tails → Configure Persistent Storage** öffnen.
    
2. den Schalter **Additional Software** einschalten.
    
3. Persistent Storage schließen und neu starten (nicht zwingend, aber sauber).
    

Ab jetzt fragt Tails nach jeder Paketinstallation, ob das Paket zur _Additional Software_-Liste hinzugefügt werden soll. Bestätigst du, wird es bei jedem Boot automatisch nachinstalliert.

---

#### 2  Paketlisten aktualisieren

sudo apt update

---

#### 3  Pakete installieren

sudo apt install --yes \
  clamav clamav-daemon clamav-freshclam \
  fangfrisch \
  p7zip-full \
  yara

_Damit landen die Programme im RAM‑Dateisystem (tmpfs)._

Beim Ausloggen oder Reboot sind sie ohne Persistent‑Eintrag wieder weg – deshalb sogleich Schritt 4.

---

#### 4  Pakete in „Additional Software“ übernehmen

Bei der ersten Installation erscheint automatisch ein Dialog:

> _“Die gerade installierten Pakete dauerhaft als Additional Software merken?”_

→ **Yes** wählen.  
Falls du den Dialog verpasst hast:

sudo tails-additional-software-config --add clamav clamav-daemon clamav-freshclam fangfrisch p7zip-full yara

---

#### 5  ClamAV & Fangfrisch einrichten

# Virendefinitionen aktualisieren und Dienst aktivieren
sudo systemctl enable --now clamav-freshclam.service

# Fangfrisch Grundkonfiguration erzeugen
sudo cp /usr/share/doc/fangfrisch/examples/fangfrisch.conf /etc/fangfrisch/
sudo nano /etc/fangfrisch/fangfrisch.conf   # Mirrors / Signaturen auswählen

# Einmalige Signatur­aktualisierung
sudo fangfrisch --download

Tipp: Füge `/var/lib/clamav` (ClamAV‑Signaturen) und `/var/lib/fangfrisch` deiner **Persistent Storage**‑Konfiguration hinzu, damit die großen Datenbanken nicht bei jedem Start neu geladen werden müssen.

---

#### 6  Verwendung prüfen

clamscan --version            # ClamAV‑Version anzeigen
yara --version                # Yara‑Version anzeigen
7z a test.7z /etc/hosts       # 7‑Zip Testarchiv erzeugen

---

Fertig! Bei jedem Neustart lädt Tails nun automatisch die oben genannten Pakete; Signaturen bleiben erhalten, wenn die betreffenden Verzeichnisse persistent sind.
