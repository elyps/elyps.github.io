Um Docker Compose für **Paperless-ngx**, ein Dokumentenmanagementsystem, einzurichten, kannst du eine `docker-compose.yml`-Datei verwenden. Nachfolgend ein Beispiel für eine vollständige Konfiguration:

### Docker Compose Datei erstellen

Erstelle eine Datei namens `docker-compose.yml` und füge den folgenden Inhalt hinzu:

version: '3.8'

services:
  paperless-ng:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
    container_name: paperless
    restart: always
    environment:
      - PUID=1000                  # Passe die User-ID (UID) deines Benutzers an
      - PGID=1000                  # Passe die Group-ID (GID) deines Benutzers an
      - TZ=Europe/Berlin           # Zeitzone
      - PAPERLESS_REDIS=redis      # Redis-Dienstname
    volumes:
      - ./data:/usr/src/paperless/data    # Daten
      - ./media:/usr/src/paperless/media  # Medien (PDFs, Dokumente, etc.)
      - ./consume:/usr/src/paperless/consume # Eingangsordner
    ports:
      - 8000:8000                 # Port für den Zugriff auf Paperless-ngx
    depends_on:
      - redis
    networks:
      - paperless-network

  redis:
    image: redis:alpine
    container_name: redis
    restart: always
    networks:
      - paperless-network

networks:
  paperless-network:
    driver: bridge

### Schritte zur Einrichtung

1. **Dateien vorbereiten**:  
    Stelle sicher, dass die Ordner `data`, `media` und `consume` im gleichen Verzeichnis wie die `docker-compose.yml` existieren.  
    Erstelle sie mit:
    
    ```bash
    mkdir -p data media consume
    ```
    
2. **Docker Compose starten**:  
    Starte die Dienste mit:
    
    ```bash
    docker-compose up -d
    ```
    
3. **Zugriff auf die Weboberfläche**:  
    Öffne einen Browser und rufe `http://localhost:8000` auf.
    
4. **Admin-Zugang erstellen**:  
    Führe folgenden Befehl aus, um einen Admin-Benutzer zu erstellen:
    
    ```bash
    docker exec -it paperless python manage.py createsuperuser
    ```
    
5. **Dokumente importieren**:  
    Dateien, die in den `consume`-Ordner gelegt werden, werden automatisch verarbeitet.
    

---

### Zusätzliche Hinweise

- **Backup der Daten**: Sichere den `data`- und `media`-Ordner regelmäßig.
- **Passwortschutz**: Verwende sichere Passwörter und setze gegebenenfalls ein Reverse Proxy (z. B. Nginx oder Traefik) ein.
- **Redis-Konfiguration**: Redis ist erforderlich, um die Warteschlange für die Verarbeitung zu managen.

Falls du Anpassungen oder eine Integration in bestehende Systeme benötigst, lass es mich wissen!


## Fehlerbehebungen

### der paperless ordner restartet immer wieder

Da sich der **Paperless-ngx**-Container ständig neustartet, gibt es vermutlich ein Problem mit der Konfiguration. Wir können das genauer analysieren und beheben.

#### **1. Logs Prüfen**

Um herauszufinden, was genau schiefläuft, überprüfe die Logs des Containers:

docker logs -f paperless

Falls der Fehler unklar ist, kannst du auch den Gesamtstatus prüfen:

docker ps -a

Falls der Container im Status `Restarting` hängt, könnte es ein Problem mit fehlenden Umgebungsvariablen, Dateiberechtigungen oder Abhängigkeiten (z. B. Redis) sein.

---

#### **2. Fehleranalyse & Lösungen**

##### **A) Fehlende Umgebungsvariablen**

Falls Paperless-ngx nicht starten kann, könnte es daran liegen, dass einige Umgebungsvariablen fehlen.

**Lösung:** Füge die notwendigen Variablen hinzu:

environment:
  - PAPERLESS_REDIS=redis
  - PAPERLESS_SECRET_KEY=geheimes_passwort # Zufälligen Key generieren!
  - PAPERLESS_ADMIN_USER=admin
  - PAPERLESS_ADMIN_PASSWORD=supergeheimespasswort

Teste anschließend mit:

docker-compose down
docker-compose up -d

Falls ein Fehler bzgl. der `SECRET_KEY`-Variable auftritt, kannst du einen sicheren Schlüssel generieren mit:

openssl rand -base64 32

---

##### **B) Berechtigungen prüfen**

Falls Paperless-ngx auf Verzeichnisse nicht zugreifen kann, liegt es oft an falschen Berechtigungen.

**Lösung:** Setze die richtigen Benutzerrechte für die **Datenverzeichnisse**:

sudo chown -R 1000:1000 data media consume
sudo chmod -R 755 data media consume

Falls der Container als **root** läuft, probiere:

sudo chown -R $(id -u):$(id -g) data media consume

---

##### **C) Redis-Container läuft nicht**

Paperless-ngx benötigt Redis als Cache. Falls Redis nicht richtig läuft, starte es manuell:

docker-compose up -d redis
docker logs -f redis

Falls Redis sich auch neu startet, versuche diesen Redis-Container zu starten:

docker run -d --name redis_test redis:alpine
docker logs -f redis_test

Falls Redis nicht funktioniert, könnte es an **fehlendem Speicher** liegen (`Out of Memory`-Fehler).

---

#### **3. Alternativer Fix: Nutze eine `.env`-Datei**

Manchmal ist es besser, eine **`.env`-Datei** zu verwenden, um Umgebungsvariablen zu setzen.

Erstelle eine Datei `.env`:

PAPERLESS_REDIS=redis
PAPERLESS_SECRET_KEY=mein_geheimer_schlüssel
PAPERLESS_ADMIN_USER=admin
PAPERLESS_ADMIN_PASSWORD=supergeheim

Dann ändere die `docker-compose.yml`:

version: '3.8'

services:
  paperless-ng:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
    container_name: paperless
    restart: unless-stopped
    env_file:
      - .env
    volumes:
      - ./data:/usr/src/paperless/data
      - ./media:/usr/src/paperless/media
      - ./consume:/usr/src/paperless/consume
    ports:
      - 8000:8000
    depends_on:
      - redis
    networks:
      - paperless-network

  redis:
    image: redis:alpine
    container_name: redis
    restart: always
    networks:
      - paperless-network

networks:
  paperless-network:
    driver: bridge

Dann starte alles neu:

docker-compose down
docker-compose up -d

---

#### **4. Container Manuell Starten & Debuggen**

Falls alles nicht hilft, versuche den Container interaktiv zu starten:

docker run --rm -it ghcr.io/paperless-ngx/paperless-ngx:latest bash

Dann kannst du Fehler manuell analysieren.

---

Falls das Problem weiter besteht, teile mir bitte die **genauen Log-Ausgaben** von:

docker logs paperless


### Fehlende oder falschen Redis-URL

Das Problem liegt an der **fehlenden oder falschen Redis-URL**. Der `PAPERLESS_REDIS`-Wert ist entweder nicht korrekt gesetzt oder wird nicht richtig ausgelesen.

---

#### **Lösung: PAPERLESS_REDIS korrekt setzen**

Dein `PAPERLESS_REDIS`-Wert in der `docker-compose.yml` ist derzeit:

environment:
  - PAPERLESS_REDIS=redis

Dieser Wert muss als vollständige Redis-URL angegeben werden:

environment:
  - PAPERLESS_REDIS=redis://redis:6379

Ersetze in der `docker-compose.yml` den **alten Wert** durch diesen neuen Eintrag und starte die Container neu:

docker-compose down
docker-compose up -d

---

#### **Falls das Problem weiterhin besteht:**

1. **Überprüfe, ob der Redis-Container läuft**  
    Führe folgenden Befehl aus:
    
    ```bash
    docker ps
    ```
    
    Falls `redis` nicht in der Liste ist, versuche ihn manuell zu starten:
    
    ```bash
    docker-compose up -d redis
    ```
    
2. **Prüfe die Logs von Redis**
    
    ```bash
    docker logs redis
    ```
    
    Falls Redis nicht startet oder Fehler ausgibt, liegt das Problem möglicherweise an zu wenig Speicher oder Berechtigungen.
    
3. **Teste Redis direkt in einem temporären Container**  
    Starte einen Redis-Client innerhalb eines separaten Containers und prüfe, ob er sich mit Redis verbinden kann:
    
    ```bash
    docker run --rm -it --network=paperless-network redis:alpine redis-cli -h redis ping
    ```
    
    Falls die Antwort **PONG** zurückkommt, läuft Redis korrekt. Falls nicht, gibt es ein Problem mit dem Netzwerk.
    

---

#### **Zusammenfassung**

- Setze `PAPERLESS_REDIS=redis://redis:6379`
- Stelle sicher, dass der Redis-Container läuft (`docker ps`)
- Überprüfe Redis-Logs mit `docker logs redis`
- Teste die Verbindung mit `docker run --rm -it --network=paperless-network redis:alpine redis-cli -h redis ping`

Falls du weiterhin Probleme hast, sende mir die **aktualisierten Logs von Paperless und Redis**, dann analysiere ich das weiter! 🚀


### CSRF-Verifizierung fehlgeschlagen (403)

Das Problem mit **"CSRF-Verifizierung fehlgeschlagen (403)"** tritt normalerweise auf, wenn **Paperless-ngx** die CSRF-Token-Überprüfung nicht korrekt durchführen kann. Dies kann mehrere Ursachen haben:

---

#### **Lösung 1: CSRF-Whitelist für Reverse Proxy oder externe Zugriffe**

Falls du Paperless über einen **Reverse Proxy** (z. B. Nginx oder Traefik) betreibst oder über eine andere Domain zugreifst (z. B. `http://meinserver:8000`), musst du die entsprechende Domain in `PAPERLESS_CSRF_TRUSTED_ORIGINS` setzen.

##### **Schritt 1: `docker-compose.yml` anpassen**

Füge die folgende Zeile in die `environment`-Sektion von `paperless-ng` hinzu:

environment:
  - PAPERLESS_CSRF_TRUSTED_ORIGINS=http://localhost:8000,http://meinserver:8000

Falls du eine **HTTPS**-Domain verwendest, setze:

  - PAPERLESS_CSRF_TRUSTED_ORIGINS=https://meinserver.de,https://subdomain.meinserver.de

Falls du mit **IP-Adressen** arbeitest:

  - PAPERLESS_CSRF_TRUSTED_ORIGINS=http://192.168.1.100:8000

**Wichtig:**  
Achte darauf, dass **alle URLs exakt** mit dem übereinstimmen, was du in der Browser-Adresszeile siehst.

##### **Schritt 2: Container neu starten**

Speichere die Änderungen und starte Paperless neu:

docker-compose down
docker-compose up -d

---

#### **Lösung 2: CSRF-Schutz deaktivieren (nur für Tests)**

Falls du das Problem nur temporär umgehen möchtest, kannst du den CSRF-Schutz deaktivieren:

environment:
  - PAPERLESS_CSRF_TRUSTED_ORIGINS=*

Aber **Achtung**: Dies ist unsicher und sollte nicht dauerhaft genutzt werden!

---

#### **Lösung 3: Cookies und Cache im Browser löschen**

Falls du bereits `PAPERLESS_CSRF_TRUSTED_ORIGINS` gesetzt hast und das Problem weiterhin besteht, versuche:

1. **Cookies löschen**
    
    - Öffne Paperless in deinem Browser.
    - Drücke `F12` → Wechsle zum Tab **"Anwendung"** oder **"Storage"**.
    - Lösche alle Cookies für die Paperless-Domain.
    - Lade die Seite neu (`STRG + F5` oder `CMD + Shift + R` auf macOS).
2. **In einem anderen Browser testen**  
    Falls du Chrome nutzt, versuche Firefox oder Edge, um zu prüfen, ob das Problem browserabhängig ist.
    

---

#### **Lösung 4: Paperless Admin-URL richtig setzen**

Falls du Paperless über eine andere URL als `localhost:8000` aufrufst, setze:

environment:
  - PAPERLESS_URL=https://meinserver.de

Dann Container neu starten:

docker-compose down
docker-compose up -d

---

##### **Zusammenfassung**

- **CSRF-Whitelist** setzen (`PAPERLESS_CSRF_TRUSTED_ORIGINS=http://localhost:8000,http://meinserver:8000`)
- **Container neu starten**
- **Cookies & Cache löschen**
- **Admin-URL (`PAPERLESS_URL`) setzen**, falls Zugriff von einer anderen Domain

