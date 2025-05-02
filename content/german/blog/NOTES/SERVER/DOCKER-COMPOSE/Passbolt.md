## Mit Nginx Web Server und MariaDB

Hier ist eine vollständige `docker-compose.yml`-Datei für die **Passbolt Community Edition**, einschließlich **MariaDB** als Datenbank und einer Nginx-Instanz für HTTPS-Unterstützung.

---

### **Voraussetzungen**

Bevor du das `docker-compose.yml` ausführst, stelle sicher, dass:  
✅ **Docker** und **Docker Compose** installiert sind.  
✅ Du eine gültige **GPG-Schlüssel-Paar** für Passbolt generierst.

Du kannst das mit folgendem Befehl tun:

docker run --rm --entrypoint gpg passbolt/passbolt:latest --generate-key

Speichere den privaten und öffentlichen Schlüssel für die spätere Verwendung.

---

### **`docker-compose.yml` für Passbolt**

version: "3.8"

services:
  mariadb:
    image: mariadb:10.11
    container_name: passbolt-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: passbolt
      MYSQL_USER: passbolt_user
      MYSQL_PASSWORD: passbolt_password
    volumes:
      - mariadb_data:/var/lib/mysql

  passbolt:
    image: passbolt/passbolt:latest-ce
    container_name: passbolt
    depends_on:
      - mariadb
    environment:
      APP_FULL_BASE_URL: "https://passbolt.example.com"
      DATASOURCES_DEFAULT_HOST: mariadb
      DATASOURCES_DEFAULT_USERNAME: passbolt_user
      DATASOURCES_DEFAULT_PASSWORD: passbolt_password
      DATASOURCES_DEFAULT_DATABASE: passbolt
      EMAIL_DEFAULT_FROM: "noreply@example.com"
      EMAIL_TRANSPORT_DEFAULT_HOST: "smtp.example.com"
      EMAIL_TRANSPORT_DEFAULT_PORT: 587
      EMAIL_TRANSPORT_DEFAULT_USERNAME: "smtp_user"
      EMAIL_TRANSPORT_DEFAULT_PASSWORD: "smtp_password"
      EMAIL_TRANSPORT_DEFAULT_TLS: "true"
      PASSBOLT_KEYRING: "/etc/passbolt/gpg"
    volumes:
      - gpg_data:/etc/passbolt/gpg
      - jwt_data:/etc/passbolt/jwt
    restart: always

  nginx:
    image: nginx:latest
    container_name: passbolt-nginx
    depends_on:
      - passbolt
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - certs:/etc/ssl/certs
      - certs:/etc/ssl/private
    ports:
      - "80:80"
      - "443:443"
    restart: always

volumes:
  mariadb_data:
  gpg_data:
  jwt_data:
  certs:

---

### **Nginx Konfiguration (`nginx.conf`)**

Erstelle eine **nginx.conf** Datei im selben Ordner:

events {}

http {
    server {
        listen 80;
        server_name passbolt.example.com;
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl;
        server_name passbolt.example.com;

        ssl_certificate /etc/ssl/certs/passbolt.crt;
        ssl_certificate_key /etc/ssl/private/passbolt.key;

        location / {
            proxy_pass http://passbolt:80;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}

---

### **SSL-Zertifikat generieren (optional)**

Falls du kein gültiges SSL-Zertifikat hast, kannst du ein **Let's Encrypt** Zertifikat generieren:

sudo apt install certbot
sudo certbot certonly --standalone -d passbolt.example.com

Danach speichere die Zertifikate in den `/certs` Ordner.

---

### **Passbolt starten**

Starte den Stack mit:

docker-compose up -d

Zugriff auf **Passbolt**:  
➡ **[https://passbolt.example.com](https://passbolt.example.com/)**

---



## Ohne Nginx Web Server

Nein, wenn du bereits **Nginx Proxy Manager (NPM)** auf deinem System nutzt, brauchst du den **zusätzlichen Nginx-Container** in der `docker-compose.yml` nicht.

Stattdessen kannst du den **Passbolt-Container direkt hinter NPM betreiben** und dort die SSL-Zertifikate und Weiterleitungen verwalten.

---

### **Anpassungen an `docker-compose.yml`**

Entferne den **nginx**-Service und passe die Ports für **Passbolt** an, damit er intern von NPM weitergeleitet werden kann.

version: "3.8"

services:
  mariadb:
    image: mariadb:10.11
    container_name: passbolt-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: passbolt
      MYSQL_USER: passbolt_user
      MYSQL_PASSWORD: passbolt_password
    volumes:
      - mariadb_data:/var/lib/mysql

  passbolt:
    image: passbolt/passbolt:latest-ce
    container_name: passbolt
    depends_on:
      - mariadb
    environment:
      APP_FULL_BASE_URL: "https://passbolt.example.com"  # Passe die Domain an
      DATASOURCES_DEFAULT_HOST: mariadb
      DATASOURCES_DEFAULT_USERNAME: passbolt_user
      DATASOURCES_DEFAULT_PASSWORD: passbolt_password
      DATASOURCES_DEFAULT_DATABASE: passbolt
      EMAIL_DEFAULT_FROM: "noreply@example.com"
      EMAIL_TRANSPORT_DEFAULT_HOST: "smtp.example.com"
      EMAIL_TRANSPORT_DEFAULT_PORT: 587
      EMAIL_TRANSPORT_DEFAULT_USERNAME: "smtp_user"
      EMAIL_TRANSPORT_DEFAULT_PASSWORD: "smtp_password"
      EMAIL_TRANSPORT_DEFAULT_TLS: "true"
      PASSBOLT_KEYRING: "/etc/passbolt/gpg"
    volumes:
      - gpg_data:/etc/passbolt/gpg
      - jwt_data:/etc/passbolt/jwt
    restart: always
    networks:
      - npm_network

networks:
  npm_network:
    external: true

volumes:
  mariadb_data:
  gpg_data:
  jwt_data:

---

### **Konfiguration im Nginx Proxy Manager**

1. Öffne die Weboberfläche deines **Nginx Proxy Managers**.
2. Erstelle einen neuen Proxy-Host mit folgenden Einstellungen:
    - **Domain:** `passbolt.example.com`
    - **Ziel-IP/Hostname:** `passbolt`
    - **Ziel-Port:** `80`
    - **Cache deaktivieren**
    - **Websockets aktivieren**
    - **HSTS aktivieren**
3. **SSL-Zertifikat hinzufügen**
    - Nutze **Let's Encrypt**, um ein **kostenloses SSL-Zertifikat** auszustellen.

---

### **Passbolt starten**

docker-compose up -d

Danach kannst du **Passbolt über den Proxy-Host aufrufen**:  
➡ **[https://passbolt.example.com](https://passbolt.example.com/)**

Das sorgt für eine **saubere Reverse-Proxy-Integration**, ohne unnötige zusätzliche Nginx-Container.



## Mit SQLite anstatt MariaDB

Ja, **Passbolt Community Edition** unterstützt auch **SQLite** als Datenbank.  
Das macht die Einrichtung **leichter**, ist aber eher für **kleine Teams** oder **Tests** geeignet, da SQLite nicht so performant ist wie MariaDB/MySQL.

---

## **🚀 Anpassung der `docker-compose.yml` für SQLite**

Falls du von MariaDB auf SQLite umsteigen möchtest, entferne den **mariadb-Service** und passe die **Datenbank-Einstellungen** für Passbolt an:

version: "3.8"

services:
  passbolt:
    image: passbolt/passbolt:latest-ce
    container_name: passbolt
    environment:
      APP_FULL_BASE_URL: "https://passbolt.example.com"
      DATASOURCES_DEFAULT_URL: "sqlite:///var/www/passbolt/db/passbolt.sqlite"
      EMAIL_DEFAULT_FROM: "noreply@example.com"
      EMAIL_TRANSPORT_DEFAULT_HOST: "smtp.example.com"
      EMAIL_TRANSPORT_DEFAULT_PORT: 587
      EMAIL_TRANSPORT_DEFAULT_USERNAME: "smtp_user"
      EMAIL_TRANSPORT_DEFAULT_PASSWORD: "smtp_password"
      EMAIL_TRANSPORT_DEFAULT_TLS: "true"
      PASSBOLT_KEYRING: "/etc/passbolt/gpg"
    volumes:
      - passbolt_data:/var/www/passbolt/db
      - gpg_data:/etc/passbolt/gpg
      - jwt_data:/etc/passbolt/jwt
    restart: always
    networks:
      - npm_network

networks:
  npm_network:
    external: true

volumes:
  passbolt_data:
  gpg_data:
  jwt_data:

---

## **🔧 Änderungen & Vorteile**

✅ **Kein separater Datenbank-Container** nötig  
✅ **Datenbank wird in einem Volume gespeichert** (`/var/www/passbolt/db/passbolt.sqlite`)  
✅ **Ideal für kleine Teams oder private Nutzung**  
⚠️ **Nicht so performant oder skalierbar wie MariaDB**

---

## **📌 Einrichtung in Nginx Proxy Manager**

Da sich nur die Datenbank geändert hat, bleibt die **Nginx Proxy Manager Konfiguration** gleich.  
Falls du noch keinen Proxy-Host für Passbolt hast, folge diesen Schritten:

1. **Neuen Proxy-Host in NPM erstellen**
    - **Domain:** `passbolt.example.com`
    - **Ziel-IP/Hostname:** `passbolt`
    - **Ziel-Port:** `80`
    - **Websockets aktivieren**
    - **HSTS aktivieren**
2. **SSL-Zertifikat über Let's Encrypt aktivieren**
    - Falls noch nicht geschehen, kannst du in NPM ein **kostenloses SSL-Zertifikat** generieren.

---

## **🚀 Starten von Passbolt mit SQLite**

docker-compose up -d

Danach kannst du auf **Passbolt zugreifen**:  
➡ **[https://passbolt.example.com](https://passbolt.example.com/)**

---

## **📌 SQLite oder MariaDB?**

|Feature|SQLite|MariaDB|
|---|---|---|
|Einfache Einrichtung ✅|✅|❌|
|Performance für kleine Teams ✅|✅|✅|
|Performance für große Teams ❌|❌|✅|
|Skalierbarkeit ❌|❌|✅|
|Backups & Migration|✅|✅|

Falls du **Passbolt nur privat oder für ein kleines Team** nutzt, ist **SQLite okay**.  
Falls du **mehr Performance oder Skalierbarkeit** brauchst, bleib besser bei **MariaDB**.

---
