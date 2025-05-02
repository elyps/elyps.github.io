Cloudflare blockt port für ssh Connection

Cloudflare blockiert standardmäßig **SSH (Port 22)**, da es sich um einen **non-HTTP(S) Dienst** handelt. Cloudflare bietet nur **Reverse Proxy für Web-Traffic (HTTP/HTTPS, Ports 80, 443 und einige andere für Webanwendungen)**.

### **Lösungen für SSH über Cloudflare:**

#### **1. Cloudflare Tunnel (Cloudflare Access / Cloudflare Zero Trust)**

Cloudflare bietet mit **Cloudflare Tunnel (früher Argo Tunnel)** eine Möglichkeit, SSH sicher über Cloudflare zu routen. Damit kannst du SSH-Zugriffe über einen von Cloudflare verwalteten Tunnel leiten.

**Setup:**

1. Installiere `cloudflared` auf dem Server:
    
    ```sh
    wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -O /usr/local/bin/cloudflared
    chmod +x /usr/local/bin/cloudflared
    ```
    
2. Authentifiziere `cloudflared` mit deinem Cloudflare-Account:
    
    ```sh
    cloudflared tunnel login
    ```
    
3. Erstelle einen neuen Tunnel:
    
    ```sh
    cloudflared tunnel create my-ssh-tunnel
    ```
    
4. Konfiguriere den Tunnel für SSH:
    
    ```sh
    nano ~/.cloudflared/config.yml
    ```
    
    Inhalt:
    
    ```yaml
    tunnel: my-ssh-tunnel
    credentials-file: /root/.cloudflared/my-ssh-tunnel.json
    ingress:
      - hostname: ssh.example.com
        service: ssh://localhost:22
      - service: http_status:404
    ```
    
5. Starte den Tunnel:
    
    ```sh
    cloudflared tunnel run my-ssh-tunnel
    ```
    
6. **Client-seitige Verbindung:**
    
    - Auf dem Client `cloudflared` installieren.
    - SSH über den Tunnel nutzen:
        
        ```sh
        cloudflared access ssh --hostname ssh.example.com
        ssh user@ssh.example.com
        ```
        

#### **2. Alternativen: Port-Wechsel oder VPN**

Falls du Cloudflare nicht für SSH brauchst:

- **Ändere den SSH-Port** auf z. B. **2222** (`/etc/ssh/sshd_config`) und nutze ihn direkt:
    
    ```sh
    ssh -p 2222 user@server-ip
    ```
    
- **Nutze VPN** oder **WireGuard/Tailscale**, um den SSH-Zugang zu sichern.

Möchtest du Cloudflare für SSH oder einfach einen anderen Port nutzen?


Cloudflare blockiert viele nicht-HTTP(S)-Ports, aber es gibt einige **zulässige Ports** für Webdienste. SSH funktioniert auf diesen standardmäßig nicht, aber du kannst versuchen, **einen alternativen Port für SSH einzurichten**, der von Cloudflare nicht blockiert wird.

### **1. Cloudflare-kompatible Ports für TCP-Dienste**

Cloudflare erlaubt nur bestimmte Ports für den Reverse Proxy:

- **HTTP:** 80, 8080, 8880, 2052, 2082, 2086, 2095
- **HTTPS:** 443, 2053, 2083, 2087, 2096, 8443

Diese sind für Webanwendungen gedacht, aber du kannst SSH auf einen dieser Ports legen, wenn du es als WebSocket oder über Cloudflare Tunnel betreibst.

---

### **2. SSH auf einen Nicht-blockierten Port ändern**

Falls du Cloudflare **nur als DNS-Anbieter** nutzt (ohne Proxy-Modus aktiviert), kannst du SSH einfach auf einen anderen offenen Port legen.

#### **Schritt 1: SSH auf einen anderen Port setzen**

1. Öffne die SSH-Konfigurationsdatei auf deinem Server:
    
    ```sh
    sudo nano /etc/ssh/sshd_config
    ```
    
2. Ändere oder füge folgende Zeile hinzu:
    
    ```plaintext
    Port 2222
    ```
    
    (Du kannst auch einen anderen Port wählen, z. B. **2222, 8022, 22000**.)
3. Starte den SSH-Dienst neu:
    
    ```sh
    sudo systemctl restart sshd
    ```
    

#### **Schritt 2: Firewall anpassen (falls nötig)**

Falls du `ufw` (Ubuntu/Debian) nutzt:

sudo ufw allow 2222/tcp
sudo ufw reload

Falls du `firewalld` (RHEL/CentOS) nutzt:

sudo firewall-cmd --permanent --add-port=2222/tcp
sudo firewall-cmd --reload

#### **Schritt 3: Verbindung testen**

Verbinde dich vom Client aus mit:

ssh -p 2222 user@deine-domain.com

---

### **3. Prüfen, ob Cloudflare DNS oder Proxy aktiv ist**

Falls du Cloudflare als DNS-Provider nutzt, aber der **"Proxy" (orange Cloud) aktiv ist**, leitet Cloudflare den gesamten Traffic über HTTP/S um und blockiert SSH.

- **Lösung:** Deaktiviere den Cloudflare-Proxy für deine Domain unter **DNS → Proxy-Status (graue Wolke aktivieren)**. Dann läuft der DNS direkt auf deine Server-IP.

---

### **Fazit**

- Falls du Cloudflare **nur als DNS nutzt**, kannst du einfach SSH auf einen anderen Port (z. B. **2222, 22000**) ändern.
- Falls du den Cloudflare **Proxy aktiv hast**, blockiert Cloudflare SSH-Traffic – außer du nutzt **Cloudflare Tunnel**.
- Falls du den Cloudflare **Proxy deaktivierst**, wird SSH über den neuen Port problemlos funktionieren.

Hast du Cloudflare als **Proxy aktiv** oder nur als **DNS**?


Cloudflare erlaubt nur bestimmte Ports für HTTP(S)-Dienste, blockiert aber standardmäßig **nicht-HTTP(S)-Dienste** wie SSH (Port 22).

### **Lösung: SSH auf einen anderen Port umstellen**

Cloudflare blockiert den Standard-SSH-Port (22), aber du kannst SSH auf einen erlaubten Port legen, den Cloudflare nicht filtert.

#### **Schritt 1: Neuen Port für SSH festlegen**

1. Öffne die SSH-Server-Konfigurationsdatei:
    
    ```sh
    sudo nano /etc/ssh/sshd_config
    ```
    
2. Ändere oder ergänze die Zeile:
    
    ```sh
    Port 2222  # Beispiel: Port auf 2222 ändern
    ```
    
3. Speichere die Datei und starte den SSH-Dienst neu:
    
    ```sh
    sudo systemctl restart sshd
    ```
    
4. **Firewall anpassen** (falls aktiv, z. B. `ufw` oder `iptables`):
    
    ```sh
    sudo ufw allow 2222/tcp
    ```
    

#### **Schritt 2: SSH über neuen Port verbinden**

Nutze den neuen Port für die Verbindung:

ssh -p 2222 user@your-server-ip

Falls Cloudflare weiter blockt, verwende einen der **nicht blockierten Cloudflare-Ports**:

|**Port**|**Protokoll**|**Zweck**|
|---|---|---|
|2052|HTTP|Alternativer HTTP-Port|
|2053|HTTPS|Alternativer HTTPS-Port|
|2082|HTTP|Alternativer HTTP-Port|
|2083|HTTPS|Alternativer HTTPS-Port|
|2087|HTTPS|Alternativer cPanel-Port|
|2095|HTTP|Webmail|
|2096|HTTPS|Webmail|
|8443|HTTPS|Alternativer HTTPS-Port|

Falls du z. B. **Port 2082** für SSH verwenden möchtest, ändere die `sshd_config`:

Port 2082

Und verbinde dich dann mit:

ssh -p 2082 user@your-server-ip

**⚠️ Achtung:**

- Diese Methode ist ein **Workaround**, da diese Ports eigentlich für Webdienste gedacht sind.
- Cloudflare kann diesen Verkehr später trotzdem blockieren.
- Falls du maximale Sicherheit willst, solltest du stattdessen **Cloudflare Tunnel für SSH** nutzen.

Soll ich dir beim Einrichten eines **Cloudflare Tunnels für SSH** helfen?


Hier ist eine Schritt-für-Schritt-Anleitung, um **SSH über Cloudflare Tunnel** (früher Argo Tunnel) einzurichten. Dies ermöglicht den sicheren SSH-Zugriff über Cloudflare, ohne direkt eine öffentliche IP zu verwenden.

---

## **1. Voraussetzungen**

- Ein Server mit **SSH** (Linux)
- Ein **Cloudflare-Konto**
- Eine **Domain** in Cloudflare (z. B. `example.com`)

---

## **2. Cloudflare Tunnel einrichten**

### **Schritt 1: Cloudflared auf dem Server installieren**

Auf dem Server, der per SSH erreichbar sein soll, installiere `cloudflared`:

#### **Ubuntu/Debian**

curl -fsSL https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -o cloudflared
sudo chmod +x cloudflared
sudo mv cloudflared /usr/local/bin/

#### **CentOS/RHEL**

sudo yum install epel-release -y
sudo yum install cloudflared -y

#### **Arch Linux**

sudo pacman -S cloudflared

Verifiziere die Installation:

cloudflared --version

---

### **Schritt 2: Cloudflared mit Cloudflare verbinden**

Melde dich bei Cloudflare an und verbinde `cloudflared` mit deiner Domain:

cloudflared tunnel login

Dies öffnet einen Link in deinem Browser, um Cloudflare Zugriff zu gewähren. Sobald das abgeschlossen ist, kannst du fortfahren.

---

### **Schritt 3: Tunnel erstellen**

Jetzt erstellst du einen neuen Tunnel:

cloudflared tunnel create my-ssh-tunnel

Dies erzeugt eine Tunnel-ID und speichert eine **JSON-Datei mit Zugangsdaten**.

Speichere den Pfad zur JSON-Datei (z. B. `/root/.cloudflared/my-ssh-tunnel.json`).

---

### **Schritt 4: Tunnel-Konfiguration für SSH**

Erstelle die Cloudflared-Konfigurationsdatei:

sudo mkdir -p /etc/cloudflared
sudo nano /etc/cloudflared/config.yml

Füge diesen Inhalt ein:

tunnel: my-ssh-tunnel
credentials-file: /root/.cloudflared/my-ssh-tunnel.json
ingress:
  - hostname: ssh.example.com
    service: ssh://localhost:22
  - service: http_status:404

Speichere und schließe die Datei (`CTRL + X`, dann `Y` und `Enter`).

---

### **Schritt 5: Tunnel als Dienst starten**

Starte den Tunnel:

cloudflared tunnel run my-ssh-tunnel

Um den Tunnel beim Systemstart automatisch zu starten:

sudo cloudflared service install
sudo systemctl enable --now cloudflared

---

## **3. SSH-Zugriff über den Tunnel**

### **Schritt 1: Cloudflare Access SSH einrichten**

1. **Gehe in die Cloudflare-Web-Konsole**.
2. Wähle deine Domain (`example.com`).
3. Navigiere zu **Zero Trust > Access > Applications**.
4. Klicke auf **"Create an application"**.
5. Wähle **"Self-hosted"**.
6. Gib die Subdomain an (z. B. `ssh.example.com`).
7. Konfiguriere eine **Zugriffsregel** (z. B. "Nur bestimmte E-Mail-Adressen erlauben").
8. Speichere die Einstellungen.

---

### **Schritt 2: Cloudflared auf dem Client installieren**

Auf dem Rechner, von dem du SSH nutzen willst, installiere ebenfalls `cloudflared` (siehe oben).

Dann verbinde dich über Cloudflare mit:

cloudflared access ssh --hostname ssh.example.com
ssh user@ssh.example.com

Falls du eine direkte Konfiguration bevorzugst, kannst du in `~/.ssh/config` Folgendes hinzufügen:

Host ssh.example.com
    ProxyCommand cloudflared access ssh --hostname %h
    User user

Dann kannst du dich einfach mit `ssh ssh.example.com` verbinden.

---

## **4. Fazit**

✅ **Sicherer SSH-Zugang ohne offene Ports**  
✅ **Kein direkter Zugriff auf deine Server-IP nötig**  
✅ **Cloudflare schützt gegen Angriffe & Brute-Force**

Falls du noch Fragen hast oder etwas nicht klappt, sag Bescheid!
