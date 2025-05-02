Wenn du einen **NGINX Webserver** in einem **Proxmox-Container (LXC oder VM)** betreiben möchtest, gibt es mehrere Möglichkeiten, dies einzurichten. Hier sind die wesentlichen Schritte für beide Varianten:

---

## **1. LXC-Container mit NGINX in Proxmox**

Ein LXC-Container ist ressourcenschonender als eine VM, hat aber gewisse Einschränkungen, z. B. in Bezug auf Kernel-Module und Systemd-Dienste.

### **Schritt 1: LXC-Container in Proxmox erstellen**

1. **Erstelle einen neuen LXC-Container** über die Proxmox-Web-GUI.
2. **Nutze ein Debian- oder Ubuntu-Template**, da diese offiziell von Proxmox unterstützt werden.
3. **Netzwerk konfigurieren** (z. B. eine statische IP oder DHCP).
4. **Unprivilegierter Container?** (empfohlen für Sicherheit, aber beachte, dass bestimmte Dienste Root-Rechte brauchen).
5. **Root-Filesystem vergrößern**, falls notwendig.

### **Schritt 2: In den Container einloggen**

Nach der Erstellung kannst du dich per `pct enter <CT-ID>` in den Container einloggen oder per SSH verbinden.

pct enter 100

(Ersetze `100` durch die tatsächliche ID deines Containers.)

### **Schritt 3: NGINX installieren**

apt update && apt install -y nginx
systemctl enable nginx
systemctl start nginx

Falls `systemctl` nicht funktioniert (weil LXC-Container keine eigenen `systemd`-Instanzen haben), kannst du `service` verwenden:

service nginx start

### **Schritt 4: Portweiterleitung in Proxmox konfigurieren**

Falls dein Container eine private IP hat, kannst du Portweiterleitungen im Proxmox-Host mit `iptables` oder `firewalld` setzen.

Beispiel für eine Portweiterleitung von Port 80 auf den LXC-Container mit der IP `192.168.1.100`:

iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.1.100:80
iptables -t nat -A POSTROUTING -j MASQUERADE

**Alternativ:** Falls du eine Proxmox Bridge (`vmbr0`) nutzt, kannst du die IP direkt in deinem Container setzen.

---

## **2. NGINX in einer Proxmox-VM (vollwertiges Debian/Ubuntu)**

Falls du mehr Kontrolle über dein System benötigst oder Systemd-abhängige Dienste hast, solltest du eine VM nutzen.

### **Schritt 1: VM in Proxmox erstellen**

- Erstelle eine neue VM mit Debian oder Ubuntu.
- Weise ihr genügend Speicher und CPU-Kerne zu.
- Setze eine Netzwerkbrücke (`vmbr0`) für eine direkte Verbindung ins LAN.

### **Schritt 2: NGINX auf der VM installieren**

Verbinde dich per SSH oder über die Proxmox-Konsole:

apt update && apt install -y nginx
systemctl enable nginx
systemctl start nginx

### **Schritt 3: Ports freigeben**

Falls UFW oder eine Firewall aktiv ist, erlaube HTTP- und HTTPS-Zugriffe:

ufw allow 80/tcp
ufw allow 443/tcp
ufw enable

Falls dein NGINX-Server öffentlich erreichbar sein soll, stelle sicher, dass dein Router Portweiterleitungen für 80/443 auf die VM-IP setzt.

---

## **3. NGINX als Reverse Proxy für weitere Container oder VMs**

Falls du NGINX als Reverse Proxy verwenden willst (z. B. um mehrere Webdienste über eine Domain anzubieten):

Bearbeite `/etc/nginx/sites-available/default` und setze einen `proxy_pass`:

server {
    listen 80;
    server_name meine-domain.de;

    location / {
        proxy_pass http://192.168.1.101:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

Anschließend NGINX neu starten:

systemctl restart nginx

Falls SSL benötigt wird, kannst du mit Let’s Encrypt und Certbot ein Zertifikat holen:

apt install certbot python3-certbot-nginx
certbot --nginx -d meine-domain.de

---

## **Fazit**

- **LXC-Container:** Schnell & leichtgewichtig, aber mit Systemd-Einschränkungen.
- **VM:** Volle Kontrolle, aber höherer Ressourcenverbrauch.
- **Reverse Proxy mit NGINX:** Ideal für mehrere Webdienste hinter einer IP.



## Docker Compose

Ja, du kannst **NGINX in einem Proxmox-Container mit Docker und Docker Compose** betreiben. Hier ist eine Schritt-für-Schritt-Anleitung, um **NGINX mit Docker Compose in einem Proxmox LXC-Container oder einer VM** zu nutzen.

---

## **1. Proxmox-Container oder VM mit Docker-Unterstützung einrichten**

Falls du Docker nutzen willst, solltest du entweder:

- **Eine VM (Debian/Ubuntu)** verwenden **ODER**
- **Einen LXC-Container mit aktivierter Nesting-Option (`features: nesting=1`)** nutzen.

Falls du Docker direkt in einem LXC-Container nutzen willst:

pct set <CT-ID> -features nesting=1,keyctl=1

Dann den Container neustarten:

pct stop <CT-ID>
pct start <CT-ID>

Falls du eine **VM verwendest**, kannst du direkt mit der Docker-Installation fortfahren.

---

## **2. Docker & Docker Compose installieren**

### **Für Debian/Ubuntu (VM oder LXC-Container mit Nesting)**

apt update && apt install -y \
    ca-certificates curl gnupg

install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | tee /etc/apt/keyrings/docker.asc > /dev/null
chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

apt update
apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Teste, ob Docker läuft:

docker run hello-world

Falls `docker` nur mit `sudo` funktioniert, füge deinen Nutzer zur Docker-Gruppe hinzu:

usermod -aG docker $(whoami)
newgrp docker

---

## **3. Docker-Compose-Datei für NGINX erstellen**

Erstelle ein Verzeichnis für dein NGINX-Projekt:

mkdir -p ~/nginx-proxy && cd ~/nginx-proxy

Erstelle die `docker-compose.yml`:

# version: '3.8'

services:
  nginx:
    image: nginx:latest
    container_name: nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      # - ./html:/usr/share/nginx/html:ro
      - /var/www/html:/usr/share/nginx/html
      - ./certs:/etc/nginx/certs:ro
    environment:
      - TZ=Europe/Berlin

---

## **4. NGINX-Konfiguration anpassen**

Erstelle eine Datei `nginx.conf` im selben Ordner:

events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        server_name _;

        location / {
            root /usr/share/nginx/html;
            index index.html;
        }
    }
}

Falls du **NGINX als Reverse Proxy** für weitere Container oder Services nutzen möchtest, ersetze `location /` mit:

location / {
    proxy_pass http://192.168.1.101:8080;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}

(Ersetze `192.168.1.101:8080` mit dem Service, den du weiterleiten möchtest.)

---

## **5. Web-Inhalte bereitstellen**

Erstelle ein Verzeichnis `html` und eine Beispiel-HTML-Datei:

mkdir html
echo "<h1>NGINX läuft in Docker auf Proxmox!</h1>" > html/index.html

---

## **6. Container starten**

Starte den NGINX-Container mit:

docker compose up -d

Überprüfe den Status:

docker ps

Teste, ob der Server läuft, indem du im Browser auf die **Proxmox-Container-IP** oder **VM-IP** gehst.

Falls du Änderungen an `nginx.conf` machst, starte den Container neu:

docker compose down
docker compose up -d

---

## **7. (Optional) Let's Encrypt für HTTPS mit Certbot**

Falls du ein SSL-Zertifikat für HTTPS benötigst, kannst du das Certbot-Docker-Image verwenden:

  certbot:
    image: certbot/certbot
    container_name: certbot
    volumes:
      - ./certs:/etc/letsencrypt
    command: certonly --webroot --webroot-path=/usr/share/nginx/html --email deine@email.de --agree-tos --no-eff-email -d deine-domain.de

Dann rufe es mit:

docker compose run --rm certbot

Danach musst du `nginx.conf` für HTTPS anpassen.

---

## **Fazit**

✅ **Proxmox LXC oder VM** → Docker installieren  
✅ **NGINX mit Docker Compose betreiben**  
✅ **Reverse Proxy & SSL möglich**  
✅ **Leicht erweiterbar für weitere Dienste**


## Troubleshooting FTP Cennection
## Verzeichnis verschieben

### **Empfohlener Ansatz: Verzeichnis verschieben**

Falls möglich, ist es aus Sicherheitsgründen ratsam, das Verzeichnis außerhalb von `/root` zu platzieren. Zum Beispiel kannst du Webinhalte typischerweise in `/var/www/html` ablegen:

1. **Verzeichnis verschieben:**

    `sudo mv /root/html /var/www/html`

- **Neuen Benutzer erstellen:**

    `sudo adduser BENUTZERNAME`
    
    Folge den Eingabeaufforderungen, um das Passwort und weitere Details festzulegen.
    
- **Benutzer zur `sudo`-Gruppe hinzufügen:**

    `sudo usermod -aG sudo BENUTZERNAME`
    
    Dadurch erhält der Benutzer alle Rechte, die in der `/etc/sudoers`-Datei für die `sudo`-Gruppe definiert sind.
    
1. **Berechtigungen und Eigentümer anpassen (falls erforderlich):** Zum Beispiel, um dem Benutzer `USERNAME` den Zugriff zu ermöglichen:

    `sudo chown -R USERNAME:USERNAME /var/www/html 
    `sudo chmod -R 755 /var/www/html`
    
    Damit erhält der Benutzer Lese- und Ausführungsrechte, und Schreibrechte, falls nötig, kannst du mit 775 oder 777 (letzteres allerdings nur in speziellen Fällen) einstellen.


**Überprüfe die Dateirechte:**  
    Stelle sicher, dass die Datei auf dem Host (`/var/www/html/index.html`) die richtigen Berechtigungen hat. Typischerweise sollten die Berechtigungen auf `644` gesetzt sein:
    
    `chmod 644 /var/www/html/index.html`
    `chmod 644 -R /var/www/html/`
    
- **Überprüfe die Verzeichnisrechte:**  
    Auch das Verzeichnis `/var/www/html` (und dessen übergeordnete Verzeichnisse) muss so gesetzt sein, dass der Nginx-Prozess darin „eintreten“ kann. Für Verzeichnisse sind in der Regel `755`-Rechte ausreichend:
    
    `chmod 755 /var/www/html`


`sudo chown -R USERNAME:USERNAME /var/www/html`
`sudo chmod -R 755 /var/www/html`
