**Lösungen für rsync auf Windows**

Der Benutzer hat versucht, `rsync` unter Windows zu verwenden, was nicht standardmäßig verfügbar ist. Um `rsync` zu installieren, gibt es einige Optionen: Entweder über WSL (Windows Subsystem for Linux) mit `apt install rsync`, oder durch cwRsync oder den Windows-Port via `choco install rsync`. Alternativ könnte der Benutzer auch `scp` weiterhin verwenden, falls keine Installation gewünscht ist. Eine andere Möglichkeit wäre, WinSCP für Dateiübertragungen per SSH zu verwenden.

Nachgedacht für 6 Sekunden

Der Fehler bedeutet schlicht, dass Windows kein `rsync` im PATH hat – unter Windows ist `rsync` nicht vorinstalliert. Du hast nun im Wesentlichen zwei Wege:

---

## 1. `rsync` über WSL (Windows Subsystem for Linux)

1. **WSL installieren** (falls noch nicht geschehen)  
    PowerShell als Administrator:
    
    ```powershell
    wsl --install
    ```
    
    Nach Neustart Ubuntu (oder eine andere Distribution) aus dem Startmenü öffnen.
    
2. **In WSL `rsync` und SSH-Client nachinstallieren**
    
    ```bash
    sudo apt update
    sudo apt install rsync openssh-client
    ```
    
3. **Windows-Laufwerk mounten & Befehl ausführen**  
    In WSL findest du dein Z-Laufwerk unter `/mnt/z`:
    
    ```bash
    rsync -avz -e "ssh -p22" \
      "/mnt/z/ROMS/PS3/Devil May Cry 4.squashfs" \
      root@192.168.2.149:/userdata/roms/ps3/
    ```
    

---

## 2. Native Windows-Port (cwRsync / Chocolatey)

### a) cwRsync

1. Download und Installation von cwRsync (z.B. von [https://itefix.net/cwrsync](https://itefix.net/cwrsync))
    
2. Während der Installation darauf achten, dass das Installations-Verzeichnis (z.B. `C:\Program Files\cwRsync\bin`) in deiner System-PATH aufgenommen wird.
    
3. Dann in PowerShell:
    
    ```powershell
    rsync -avz -e "C:/Program Files/cwRsync/bin/ssh.exe -p22" `
      "Z:/ROMS/PS3/Devil May Cry 4.squashfs" `
      root@192.168.2.149:/userdata/roms/ps3/
    ```
    

### b) Chocolatey

1. (Nur wenn Du Chocolatey nutzen willst und es noch nicht installiert ist.)
    
2. Chocolatey einrichten (als Admin-PowerShell):
    
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; `
      [System.Net.ServicePointManager]::SecurityProtocol = 'Tls12'; `
      iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
    
3. `rsync` installieren:
    
    ```powershell
    choco install rsync
    ```
    
4. Danach kannst du in jeder PowerShell-Session normal `rsync` verwenden.
    

---

## 3. Alternative: Weiter mit `scp`

Wenn dir die Installation von rsync zu aufwändig ist, kannst du weiter mit `scp` arbeiten – sobald Port 22 und SSH-Dienst auf 192.168.2.149 erreichbar sind:

scp -P 22 `
  "Z:\ROMS\PS3\Devil May Cry 4.squashfs" `
  root@192.168.2.149:/userdata/roms/ps3/

Dabei auf korrekte Anführungszeichen bzw. Backticks achten, damit PowerShell Leerzeichen im Pfad richtig handhabt.

---

Wähle den Weg, der dir am einfachsten ist. Mit WSL erhältst du eine “echte” Linux-Umgebung, in der `rsync` sofort funktioniert; mit cwRsync/Chocolatey bleibt du in nativer Windows-Umgebung.
