**Optionen für Malware-Scan auf Tails Linux**

Tails verwendet eine Debian-basierte Architektur, bietet jedoch nur eingeschränkte Pakete. Du kannst aber zusätzliche Software auf der aktuellen Sitzung mit der Funktion „Zusätzliche Software“ oder über ein persistenten Volume installieren. Für ein umfassenderes Scannen kannst du „rkhunter“ oder „chkrootkit“ ausprobieren, die auf Rootkits ausgelegt sind, jedoch keine Viren scannen. Eine andere Möglichkeit wäre, zusätzliche Tools wie „Freshclam“ zur Aktualisierung von ClamAV zu nutzen. Du könntest außerdem portable Apps in AppImage-Form verwenden.

**Alternativen für Malware-Scanner auf Tails Linux**

Für eine Alternative zu ClamAV auf Tails könnte man „Comodo Antivirus für Linux“ nutzen, das in einer kostenlosen Version verfügbar ist. Eine weitere Option wäre „Sophos Antivirus für Linux“, das ebenfalls kostenlos angeboten wird, allerdings hast du bereits erwähnt, dass du eine andere Lösung möchtest. Tools wie „Lynis“ bieten Sicherheits-Audits, jedoch keine Malware-Erkennung. „Chkrootkit“ und „Rkhunter“ sind gut für Rootkit-Detektion, aber für Malware könnten auch „BitDefender“ und „Eset NOD32“ in Frage kommen, jedoch sind diese nicht immer kostenlos.

**Beste kostenlose Programme für Tails**

Unter Tails Linux könntest du „Linux Malware Detect (LMD, maldet)“ für die Malware-Erkennung nutzen, das gut mit ClamAV zusammenarbeitet. Zusätzlich bieten Programme wie „Chkrootkit“ und „Rkhunter“ eine gute Option zur Rootkit-Erkennung. LMD ist ein leicht nutzbares CLI-Tool und optimal für die Integration mit ClamAV. Wenn du eine zusätzliche Sicherheitsüberprüfung möchtest, könnte auch „Lynis“ zur Systemanalyse nützlich sein. Aber insgesamt wäre LMD wohl die einfachste Lösung für die Virensuche.

Nachgedacht für 15 Sekunden

Unter Tails (Debian‑basiert, rein RAM‑basiert) hast du prinzipiell zwei Wege:

1. **Bitdefender Antivirus Scanner für Unices (Free Edition)**
    
2. **Linux Malware Detect („maldet“) als ergänzende Signatur‑Engine**
    

---

## 1. Bitdefender Antivirus Scanner für Unices

– **Erkennungsrate**: sehr hoch, nutzt proprietäre Signaturen plus Heuristik  
– **Lizenz**: kostenlos für private Nutzung  
– **Installation (RAM‑Session)**

# 1. Datei herunterladen (aktuelle Version prüfen!)
wget https://download.bitdefender.com/repos/apt/pool/free/bdscanner/bdscanner_7.7.0.887_amd64.deb

# 2. Abhängigkeiten installieren
sudo apt-get update
sudo apt-get install -y libicu66 libssl1.1

# 3. .deb installieren
sudo dpkg -i bdscanner_7.7.0.887_amd64.deb

# 4. Lizenz akzeptieren & Signaturen updaten
sudo bdscan --accept-license --update

– **Scan eines 2‑GB‑Archivs**

# heuristische Untersuchung + ausführliches Log
sudo bdscan \
  --accepteula \
  --heuristic \
  --recursive \
  --logfile /tmp/bdscan.log \
  /pfad/zu/deiner/datei_2GB

Nach Ende siehst du unter `/tmp/bdscan.log`, ob etwas aufgefallen ist.

---

## 2. Linux Malware Detect (LMD / maldet)

– **Open‑Source**, verbindet eigene Signaturen mit ClamAV‑Daten  
– **leichtgewichtig**, gut automatisierbar

### Installation ohne Paketmanager (direkt aus Tarball)

# 1. Herunterladen & entpacken
wget https://www.rfxn.com/downloads/maldetect-current.tar.gz
tar xzf maldetect-current.tar.gz
cd maldetect-*/

# 2. Installation ins RAM-Dateisystem
sudo ./install.sh

# 3. Signaturen aktualisieren
sudo maldet --update

### Scan starten

# einzelnes File (oder Pfad) prüfen
sudo maldet --scan-all /pfad/zu/deiner/datei_2GB

# Ergebnis anzeigen
sudo maldet --report SCANID

– **SCANID** steht für die bei `--scan-all` ausgegebene ID (z.B. 123456).  
– LMD arbeitet zusätzlich mit ClamAV‑Signaturen, deckt aber auch PHP‑Backdoors und Webshells ab.

---

### Kurz‑Zusammenfassung

|Tool|Vorteil|Befehl|
|---|---|---|
|**Bitdefender Scanner for Unices**|Top‑Erkennungsraten, Heuristik|`bdscan --heuristic --recursive /pfad`|
|**Linux Malware Detect (maldet)**|Open‑Source, ClamAV‑Kombi|`maldet --scan-all /pfad`|

**Empfehlung:** Für Windows‑/Cross‑Malware-Checks nimm Bitdefender Scanner; für schnelle Linux‑Backdoor‑Audits zusätzlich maldet. So hast du zwei unabhängige Engines neben ClamAV im Einsatz.
