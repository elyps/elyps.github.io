Hier ist ein Beispiel für eine Docker-Compose-Datei, die einen OpenVPN-Server einrichtet. Dieses Beispiel nutzt das Image von [kylemanna/openvpn](https://hub.docker.com/r/kylemanna/openvpn/), das eine beliebte Wahl für OpenVPN-Server ist.

### Voraussetzungen

1. **Docker** und **Docker Compose** sind installiert.
2. Du hast grundlegende Kenntnisse im Umgang mit OpenVPN und Docker.
3. Du arbeitest auf einem Host-System, das die notwendigen Ports für OpenVPN freigeben kann (standardmäßig UDP 1194).

---

### `docker-compose.yml`

version: '3.8'

services:
  openvpn:
    image: kylemanna/openvpn
    container_name: openvpn
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
    ports:
      - "1194:1194/udp"
    volumes:
      - ./openvpn-data:/etc/openvpn
    environment:
      - TZ=Europe/Berlin

---

### Anleitung

4. **Verzeichnisstruktur vorbereiten** Erstelle ein Verzeichnis, z. B. `openvpn-setup`, und navigiere dorthin:
    
    ```bash
    mkdir openvpn-setup && cd openvpn-setup
    ```
    
5. **Docker-Compose-Datei erstellen** Speichere die oben gezeigte `docker-compose.yml` in diesem Verzeichnis.
    
6. **Datenverzeichnis initialisieren** Starte den Container und initialisiere die OpenVPN-Konfiguration:
    
    ```bash
    docker run --rm -v $(pwd)/openvpn-data:/etc/openvpn kylemanna/openvpn ovpn_genconfig -u udp://<deine-domain-oder-ip>
    docker run --rm -v $(pwd)/openvpn-data:/etc/openvpn -it kylemanna/openvpn ovpn_initpki
    ```
    
    Ersetze `<deine-domain-oder-ip>` durch die öffentliche IP-Adresse oder Domain deines Servers.
    
7. **Docker-Compose starten** Nachdem die Konfiguration abgeschlossen ist, kannst du den OpenVPN-Server starten:
    
    ```bash
    docker-compose up -d
    ```
    
8. **Benutzer erstellen** Um einen neuen Benutzer hinzuzufügen und eine `.ovpn`-Konfigurationsdatei zu generieren:
    
    ```bash
    docker run --rm -v $(pwd)/openvpn-data:/etc/openvpn -it kylemanna/openvpn easyrsa build-client-full <benutzername> nopass
    docker run --rm -v $(pwd)/openvpn-data:/etc/openvpn kylemanna/openvpn ovpn_getclient <benutzername> > <benutzername>.ovpn
    ```
    
    Die Datei `<benutzername>.ovpn` kann dann an den Benutzer weitergegeben werden.
    
9. **Firewall-Konfiguration** Stelle sicher, dass der UDP-Port 1194 auf deinem Host-System offen ist:
    
    ```bash
    sudo ufw allow 1194/udp
    ```
    

---

### Hinweise

- Du kannst das `TZ`-Environment-Variable in der Compose-Datei an deine Zeitzone anpassen.
- Für den produktiven Einsatz wird empfohlen, ein sicheres Passwort bei der Zertifikatserstellung zu verwenden.
- Falls du den TCP-Modus benötigst, ändere `udp://` in `tcp://` in Schritt 3 und aktualisiere die `ports`-Zeile entsprechend (`1194:1194/tcp`).

Falls du spezifische Anpassungen benötigst, lass es mich wissen!
