Um Gitblit mit Docker Compose bereitzustellen, kannst du ein Setup erstellen, das Gitblit in einem Docker-Container ausführt. Hier ist ein Beispiel für eine `docker-compose.yml`-Datei, die Gitblit bereitstellt:

### Schritte:

1. **Erstelle die Datei `docker-compose.yml`:**
    
    ```yaml
    version: '3.8'
    
    services:
      gitblit:
        image: gitblit/gitblit
        container_name: gitblit
        ports:
          - "8080:8080"   # Web-GUI Port
          - "8443:8443"   # HTTPS Port
        volumes:
          - ./data:/opt/gitblit/data   # Persistente Daten
          - ./repositories:/opt/gitblit/git/repositories # Git-Repositories
        environment:
          JAVA_OPTS: "-Xmx512m" # Java-Optionen für Gitblit
        restart: unless-stopped
    ```
    
2. **Verzeichnisse vorbereiten:** Erstelle die folgenden Verzeichnisse in dem Ordner, in dem die `docker-compose.yml` liegt:
    
    - `data`: Speichert Konfigurations- und Benutzerdaten.
    - `repositories`: Hier werden die Git-Repositories gespeichert.
    
    ```bash
    mkdir data repositories
    ```
    
3. **Docker Compose ausführen:** Führe den folgenden Befehl im Verzeichnis aus, in dem sich die `docker-compose.yml` befindet:
    
    ```bash
    docker-compose up -d
    ```
    
4. **Zugriff auf Gitblit:**
    
    - Öffne einen Browser und gehe zu: `http://localhost:8080`
    - Standard-Benutzername: `admin`
    - Standard-Passwort: `admin`
5. **Konfiguration anpassen:** Die Konfigurationen von Gitblit befinden sich im `data`-Ordner. Du kannst sie bei Bedarf anpassen und den Container mit `docker-compose restart` neu starten, um Änderungen zu übernehmen.
    

### Optional:

- **Daten sichern:** Die Volumes sind bereits so eingerichtet, dass deine Konfigurations- und Repository-Daten in `./data` und `./repositories` gespeichert werden.
- **SSL verwenden:** Du kannst eigene Zertifikate in das `data`-Verzeichnis legen und die HTTPS-Konfiguration von Gitblit anpassen.
