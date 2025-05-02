## Initial Setup Docker Compose

Below is an example Docker Compose setup to run the Calibre‑web interface using the LinuxServer.io image.

1. **Create a Working Directory**
    
    Create a directory (for example, `calibre-web`) to store your Docker Compose file and configuration data:
    
    ```bash
    mkdir ~/calibre-web
    cd ~/calibre-web
    ```
    
2. **Create the docker-compose.yml File**
    
    Create a file named `docker-compose.yml` with the following content. Adjust the volume paths, timezone, and user/group IDs to suit your environment.
    
    ```yaml
    version: "3.8"
    services:
      calibre-web:
        image: linuxserver/calibre-web
        container_name: calibre-web
        environment:
          - PUID=1000            # Replace with your host user id
          - PGID=1000            # Replace with your host group id
          - TZ=Europe/Berlin     # Replace with your local timezone
        volumes:
          - ./config:/config           # Persists Calibre‑web configuration
          - /path/to/your/calibre/library:/books   # Your Calibre library
        ports:
          - 8083:8083          # Maps container port 8083 to host port 8083
        restart: unless-stopped
    ```
    
    **Notes:**
    
    - **PUID/PGID:** To find your UID and GID, run `id $USER` on your host system.
    - **Volumes:**
        - The `./config` directory will store Calibre‑web’s configuration data.
        - Replace `/path/to/your/calibre/library` with the actual path to your Calibre library on your host.
    - **Ports:** Calibre‑web listens on port 8083 by default.
3. **Start the Container**
    
    From the directory containing your `docker-compose.yml`, start the container in detached mode:
    
    ```bash
    docker-compose up -d
    ```
    
4. **Access Calibre‑web**
    
    Once the container is running, open your browser and navigate to:
    
    ```
    http://localhost:8083
    ```
    
    If running on a remote server, replace `localhost` with the server’s IP or hostname.
    
5. **Initial Setup**
    
    The first time you access Calibre‑web, you'll be guided through the initial setup process. Point it to your Calibre library (mounted at `/books` in the container), and configure any additional settings as needed.
    

This setup provides a reproducible environment for Calibre‑web using Docker Compose. Adjust any settings to fit your particular needs.


## Anmeldung

Standardmäßig lauten die Anmeldedaten beim ersten Zugriff auf Calibre‑web:

- **Benutzername:** admin
- **Passwort:** admin123

Nach der erstmaligen Anmeldung empfiehlt es sich, diese Daten zur Erhöhung der Sicherheit umgehend zu ändern. Sollte es zu Abweichungen kommen oder du während des Setups zur Einrichtung eines neuen Administratorkontos aufgefordert werden, folge bitte den angezeigten Anweisungen.



## Fehlerbehebungen

### calibre DB is not Writeable

Die Fehlermeldung „calibre DB is not Writeable“ weist darauf hin, dass der Container keine Schreibrechte auf die Calibre‑Web-Datenbank bzw. das zugehörige Konfigurationsverzeichnis hat. Folgende Schritte können helfen, das Problem zu beheben:

---

#### 1. Überprüfe den Volume-Mount

- **Konfigurationsverzeichnis:**  
    Das Verzeichnis (z. B. `./config`), das in der docker-compose‑YML als `/config` gemountet wird, muss für den Container beschreibbar sein.
    
- **Beispiel in der docker-compose‑YML:**
    
    ```yaml
    services:
      calibre-web:
        image: linuxserver/calibre-web
        container_name: calibre-web
        environment:
          - PUID=1000            # Passe dies an deinen Benutzer an
          - PGID=1000            # Passe dies an deine Gruppe an
          - TZ=Europe/Berlin
        volumes:
          - ./config:/config
          - /pfad/zu/deiner/calibre/library:/books
        ports:
          - 8083:8083
        restart: unless-stopped
    ```
    

---

#### 2. Passe die Dateiberechtigungen an

- **Überprüfe den Besitzer des Konfigurationsverzeichnisses:**  
    Im Terminal kannst du die Berechtigungen mit folgendem Befehl prüfen:
    
    ```bash
    ls -la ./config
    ```
    
    Achte darauf, dass der Besitzer (User und Gruppe) den in der docker-compose‑YML gesetzten `PUID` und `PGID` entspricht.
    
- **Berechtigungen setzen:**  
    Wenn beispielsweise `PUID=1000` und `PGID=1000` gelten sollen, kannst du das Verzeichnis und dessen Inhalt mit folgendem Befehl anpassen:
    
    ```bash
    sudo chown -R 1000:1000 /pfad/zu/deinem/config
    ```
    
    Zusätzlich kannst du sicherstellen, dass das Verzeichnis beschreibbar ist:
    
    ```bash
    chmod -R 755 /pfad/zu/deinem/config
    ```
    

---

#### 3. SELinux (falls aktiv)

- Falls dein System SELinux verwendet, kann es sein, dass trotz korrekter Besitzer- und Rechte-Einstellungen der Container nicht schreiben darf.
- **Lösung:**
    - Setze die richtigen SELinux-Kontexte, oder
    - Testweise kannst du SELinux (temporär) deaktivieren, um zu prüfen, ob dies die Ursache ist.

---

#### 4. Container neu starten

Nachdem du die Berechtigungen angepasst hast, starte den Container neu, damit die Änderungen greifen:

docker-compose down
docker-compose up -d

---

Mit diesen Schritten sollte der Container Schreibzugriff auf die Datenbank und das Konfigurationsverzeichnis erhalten, wodurch die Fehlermeldung „calibre DB is not Writeable“ behoben wird. Falls das Problem weiterhin besteht, prüfe auch, ob auf dem Host ausreichend Speicherplatz vorhanden ist oder ob weitere Fehlermeldungen in den Container-Logs (`docker logs calibre-web`) Hinweise liefern.


### Reasoned about Calibre-Web 500 Internal Server Error/AttributeError: 'NoneType' object has no attribute 'query'

Der Fehler

AttributeError: 'NoneType' object has no attribute 'query'

weist darauf hin, dass die Datenbanksession (also das Objekt, das für den Zugriff auf die Datenbank zuständig ist) nicht korrekt initialisiert wurde – sprich, `self.session` ist `None`. Das kann verschiedene Ursachen haben. Hier ein paar Ansätze zur Fehlersuche und -behebung:

---

#### 1. Überprüfung des gemounteten Konfigurationsverzeichnisses

- **Persistente Daten:**  
    Calibre‑Web speichert seine Konfiguration und Daten (einschließlich der SQLite‑Datenbank, meist `calibre-web.db`) im gemounteten `/config`-Verzeichnis.
- **Mögliche Ursache:**  
    Falls in diesem Verzeichnis bereits eine (eventuell beschädigte) Konfiguration oder Datenbank vorhanden ist, wird diese verwendet – und bei Fehlern in der Datenbankinitialisierung kann es zu einem fehlenden `session`-Objekt kommen.
- **Lösung:**  
    Testweise kannst du das Konfigurationsverzeichnis leeren (oder umbenennen), um einen kompletten Neuaufbau der Konfiguration zu erzwingen.
    
    ```bash
    rm -rf ./config/*
    ```
    
    Anschließend den Container neu starten:
    
    ```bash
    docker-compose down
    docker-compose up -d
    ```
    

---

#### 2. Dateiberechtigungen und PUID/PGID

- **Berechtigungen:**  
    Stelle sicher, dass der Benutzer, unter dem der Container läuft (definiert durch `PUID` und `PGID`), Lese- und Schreibrechte auf den gemounteten Verzeichnissen (`/config` und ggf. `/books`) hat.
- **Mögliche Ursache:**  
    Fehlende Schreibrechte können verhindern, dass die Datenbank initialisiert bzw. geschrieben wird.
- **Lösung:**  
    Prüfe die Rechte der Verzeichnisse auf deinem Host-System und passe sie gegebenenfalls an (z. B. mit `chown` oder `chmod`).

---

#### 3. Container-Logs genauer analysieren

- **Logs prüfen:**  
    Nutze den Befehl
    
    ```bash
    docker logs calibre-web
    ```
    
    um weitere Hinweise zu erhalten – oft gibt es vor dem Fehler Meldungen zur Datenbankinitialisierung oder anderen Problemen.

---

#### 4. Abhängigkeiten und Datenbankdatei

- **SQLite-Datenbank:**  
    Calibre‑Web verwendet standardmäßig SQLite. Sollte die Datenbankdatei (`calibre-web.db`) beschädigt oder unvollständig sein, kann das zu dem beschriebenen Fehler führen.
- **Mögliche Ursache:**  
    Ein plötzlicher Container-Neustart oder Dateisystemprobleme können eine beschädigte Datenbank hinterlassen.
- **Lösung:**  
    Erstelle ein Backup der bestehenden Datenbank (falls notwendig) und lösche sie, um einen Neuaufbau zu erzwingen.

---

#### Zusammenfassung

Der Fehler entsteht, weil das Datenbankobjekt (`self.session`) nicht korrekt initialisiert wurde. Dies kann an einer beschädigten oder fehlerhaft geladenen Konfiguration liegen oder an Berechtigungsproblemen. Durch das Leeren bzw. Neuaufsetzen des Konfigurationsverzeichnisses und das Überprüfen der Dateiberechtigungen sollte das Problem in vielen Fällen behoben werden.

Falls der Fehler nach diesen Maßnahmen weiterhin auftritt, lohnt es sich, die detaillierten Logs zu prüfen und zu überprüfen, ob die Umgebung (einschließlich aller Umgebungsvariablen und Volume-Mappings) korrekt konfiguriert ist.

