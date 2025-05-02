**Linkwarden** ist ein Open-Source-Bookmark-Manager, der in Docker-Containern laufen kann. Hier ist ein Beispiel für eine **`docker-compose.yml`**-Datei, die du für Linkwarden verwenden kannst:

### Docker Compose für Linkwarden

version: '3.8'

services:
  linkwarden:
    image: ghostdad/linkwarden:latest
    container_name: linkwarden
    restart: unless-stopped
    ports:
      - "3000:3000" # Port, auf dem Linkwarden verfügbar ist
    environment:
      - NODE_ENV=production
      - ADMIN_EMAIL=admin@example.com # E-Mail für den Admin-Account
      - ADMIN_PASSWORD=yoursecurepassword # Passwort für den Admin
    volumes:
      - linkwarden-data:/data # Speichere Daten persistiert im Volumen
    depends_on:
      - mongo

  mongo:
    image: mongo:5
    container_name: linkwarden-mongo
    restart: unless-stopped
    volumes:
      - mongo-data:/data/db # MongoDB-Datenvolumen
    environment:
      - MONGO_INITDB_ROOT_USERNAME=mongoadmin
      - MONGO_INITDB_ROOT_PASSWORD=securepassword # Passwort für MongoDB

volumes:
  linkwarden-data:
  mongo-data:

### Schritte zur Einrichtung

1. **Docker und Docker Compose installieren** (falls noch nicht geschehen):
    
    - [Installationsanleitung für Docker](https://docs.docker.com/get-docker/)
    - [Installationsanleitung für Docker Compose](https://docs.docker.com/compose/install/)
2. **`docker-compose.yml` erstellen**: Speichere die obige Konfiguration in einer Datei namens `docker-compose.yml`.
    
3. **Docker Compose starten**: Führe im Verzeichnis der `docker-compose.yml` den folgenden Befehl aus:
    
    ```bash
    docker-compose up -d
    ```
    
4. **Linkwarden öffnen**: Besuche [http://localhost:3000](http://localhost:3000/) in deinem Browser, um auf Linkwarden zuzugreifen.
    
5. **Admin-Zugangsdaten eingeben**: Verwende die in der `docker-compose.yml` angegebenen `ADMIN_EMAIL` und `ADMIN_PASSWORD`.
    

### Optional: Daten sichern

Stelle sicher, dass die Volumes `linkwarden-data` und `mongo-data` regelmäßig gesichert werden, da sie alle deine gespeicherten Links und Daten enthalten.

Wenn du weitere Anpassungen benötigst, lass es mich wissen! 😊
