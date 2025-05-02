**Fangfrisch Paket suchen**

Das Paket "fangfrisch" scheint nicht im Standard-Repository von Debian (Bookworm) verfügbar zu sein. Es könnte in "bookworm-backports" oder einer früheren Debian-Version wie "bullseye" oder in Debian "unstable" (Sid) vorhanden sein. Vielleicht ist es auch in Kali Linux verfügbar. Ich werde das Internet durchsuchen, um herauszufinden, wo es zu finden ist und Alternativen wie die Installation via pip oder aus dem Quellcode vorzuschlagen.

**Fangfrisch in Debian repos**

Es scheint, dass "fangfrisch" in Debian "sid" und "testing" (Trixie) vorhanden ist, jedoch nicht in der stabilen Version "bookworm". Da Tails 6.x auf Debian 12 basiert, fehlt es dort im stabilen Repository, weshalb der apt-Installationsbefehl fehlschlägt. Eine Möglichkeit wäre, das "bookworm-backports"-Repository zu aktivieren, falls es dort verfügbar ist. Ich werde nun nachsehen, ob die Version von "fangfrisch" in den Backports vorhanden ist.

**Fangfrisch prüfen**

Es scheint, dass das "fangfrisch"-Paket nicht in Debian Stable verfügbar ist, weshalb die Fehlermeldung auftritt. Die Lösung könnte darin bestehen, das "bookworm-backports"-Repository zu aktivieren, falls es dort vorhanden ist, oder alternativ das Paket aus dem Quellcode zu kompilieren oder mit pip zu installieren. Ich werde nun nachsehen, ob "fangfrisch" in den Backports von Bookworm zu finden ist, um die beste Lösung zu klären.

**Fangfrisch Installation Optionen**

Für die Installation von "fangfrisch" gibt es mehrere Ansätze. Die benötigten Abhängigkeiten wie "python3" und "python3-requests" sind in Bookworm verfügbar. Wir könnten ein .deb-Paket aus der Testing-Version herunterladen, aber das könnte zu unbefriedigten Abhängigkeiten führen. Alternativ könnte "pip" eine einfachere Lösung bieten, auch wenn es zusätzliche Netzwerkanforderungen und Root-Rechte nötig machen könnte. Ich schlage vor, entweder das Testing-Repository hinzuzufügen oder die Installation über "pip" zu versuchen.

**Antwort erstellen**

Die Lösung für "fangfrisch":

- Es ist in Debian Testing/Unstable verfügbar, aber nicht in Stable. Der Befehl `apt install` schlägt fehl.
    
- Option 1: Mit `pip install` installieren.
    
- Option 2: Manuelles Herunterladen des .deb-Pakets.
    
- Option 3: Auf kommende Tails-Version mit Debian Trixie warten.
    

Wichtige Quellen:

- Debian Testing-Seite zeigt Verfügbarkeit.
    
- Pypi-Seite für "fangfrisch" zeigt, dass es über `pip` verfügbar ist. Ich werde sicherstellen, dass die Antwort im passenden Ton auf Deutsch formuliert wird.
    

Nachgedacht für 47 Sekunden

**Grund für den Fehler**

Tails 6.x verwendet Debian 12 _bookworm_.  
Das Paket **`fangfrisch`** ist erst in Debian 13 _trixie_ (Testing / Unstable) in den offiziellen Repositories vorhanden, deshalb kann `apt` es in _bookworm_ nicht finden. citeturn7search0

---

## Lösungswege

### 1  Pakete aus _trixie_ manuell nachinstallieren

`fangfrisch` hat nur reine Python‑Abhängigkeiten ( `python3‑requests`, `python3‑sqlalchemy` ), die bereits in _bookworm_ verfügbar sind. Du kannst daher einfach das aktuelle .deb aus _trixie_ einspielen:

# Voraussetzung: Administrations­passwort beim Boot gesetzt
sudo apt update
sudo apt install --yes python3-requests python3-sqlalchemy wget

wget https://deb.debian.org/debian/pool/main/f/fangfrisch/fangfrisch_1.9.0-3_all.deb
sudo dpkg -i fangfrisch_1.9.0-3_all.deb

Danach den bekannten Dialog **“Additional Software”** bestätigen oder per Hand hinzufügen:

sudo tails-additional-software-config --add fangfrisch

### 2  Installation per `pip` (empfohlen, wenn du nichts aus Testing mischen willst)

sudo apt update
sudo apt install --yes python3-pip      # nur ~20 MB
pip install --break-system-packages --no-cache-dir --upgrade fangfrisch

- Die aktuelle Version 1.9.2 wird direkt von PyPI installiert. citeturn2search0
    
- Das Skript landet in `~/.local/bin`. Füge diesen Pfad ggf. deiner `~/.bashrc` hinzu:
    
    ```bash
    export PATH="$HOME/.local/bin:$PATH"
    ```
    

Um `fangfrisch` bei jedem Start verfügbar zu haben, aktiviere **Persistent Storage** für dein Home‑Verzeichnis **und** das Verzeichnis `~/.local`.

### 3  Abwarten

Sobald Tails auf Debian 13 (_trixie_) umgestellt wird (geplant für Tails 7.x), ist `fangfrisch` regulär per `apt install fangfrisch` verfügbar und die obigen Workarounds sind nicht mehr nötig.

---

## Danach konfigurieren (egal, welche Installationsmethode)

# Grundkonfiguration übernehmen
sudo cp /usr/share/doc/fangfrisch/examples/fangfrisch.conf /etc/fangfrisch/
sudo nano /etc/fangfrisch/fangfrisch.conf   # Mirrors / Signaturen anpassen

# Erste Signatur‑Synchronisierung
sudo fangfrisch --download

Lege zusätzlich `/var/lib/fangfrisch` als persistenten Ordner an, damit die herunter­geladenen ClamAV‑Signaturen den Neustart überleben.

Damit ist `fangfrisch` unter Tails einsatzbereit – wahlweise per Testing‑.deb oder per PyPI‑Paket.
