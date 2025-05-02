Der empfohlene Weg, ein solches Skript beim Systemstart automatisch im Hintergrund auszuführen, ist die Nutzung eines systemd-Dienstes. Hier ein Beispiel, wie du das einrichten kannst:

1. **Skript ausführbar machen:**  
    Stelle sicher, dass dein Skript ausführbar ist. Falls nicht, führe aus:
    
    ```bash
    chmod +x /pfad/zu/deinem/dns-update.sh
    ```
    
2. **Systemd Service-Datei erstellen:**  
    Erstelle eine Datei (z. B. `/etc/systemd/system/dns-update.service`) mit folgendem Inhalt. Passe dabei den Pfad zu deinem Skript und gegebenenfalls den Benutzer (`User`) sowie das Arbeitsverzeichnis (`WorkingDirectory`) an:
    
[Unit]
Description=Cloudflare DNS Update Service
After=network.target

[Service]
Type=simple
ExecStart=/pfad/zu/deinem/dns-update.sh
Restart=always
User=dein-benutzername
WorkingDirectory=/pfad/zu/deinem

[Install]
WantedBy=multi-user.target
    
3. **Service aktivieren und starten:**  
    Lade die systemd-Konfiguration neu, aktiviere den Service für den automatischen Start und starte ihn sofort:
    
sudo systemctl daemon-reload
sudo systemctl enable dns-update.service
sudo systemctl start dns-update.service
    

Mit dieser Konfiguration wird dein Skript bei jedem Systemstart automatisch im Hintergrund ausgeführt. Den Status kannst du jederzeit mit folgendem Befehl überprüfen:

sudo systemctl status dns-update.service


## Troubleshooting

### Bad Settings

Das Problem liegt daran, dass du den Pfad mit `~` angegeben hast. Systemd akzeptiert keine Tilde zur Darstellung des Home-Verzeichnisses. Du musst den absoluten Pfad zu deinem Skript angeben.

Beispiel:  
Wenn dein Skript unter `/root/cloudflare_dns_update.sh` liegt, ändere die Zeile in der Unit-Datei zu:

ExecStart=/root/cloudflare_dns_update.sh

Danach führe aus:

sudo systemctl daemon-reload
sudo systemctl start dns-update.service

Damit sollte der Service korrekt starten.


### status=217/USER

Der Fehler `status=217/USER` weist darauf hin, dass der im Unit-File angegebene Benutzer ungültig oder nicht vorhanden ist. Vermutlich steht in deiner Unit-Datei noch:

User=dein-benutzername

Um das zu beheben, hast du zwei Optionen:

1. **Als Root ausführen:**  
    Wenn du den Dienst als Root starten möchtest, ändere die Zeile zu:
    
    ```ini
    User=root
    ```
    
    oder entferne die `User`-Direktive komplett (dann läuft der Dienst standardmäßig als Root).
    
2. **Einen gültigen Benutzer angeben:**  
    Stelle sicher, dass du den Namen eines existierenden Benutzers einträgst. Zum Beispiel:
    
    ```ini
    User=deinAktuellerBenutzername
    ```
    

Hier ein Beispiel für eine korrigierte Unit-Datei:

[Unit]
Description=Cloudflare DNS Update Service
After=network.target

[Service]
Type=simple
ExecStart=/root/cloudflare_dns_update.sh
Restart=always
User=root
WorkingDirectory=/root

[Install]
WantedBy=multi-user.target

Speichere die Änderungen, führe anschließend ein:

sudo systemctl daemon-reload
sudo systemctl start dns-update.service

Überprüfe mit:

sudo systemctl status dns-update.service

Damit sollte der Dienst ohne den Fehler starten.


Ja, wenn du den Service mit `systemctl enable dns-update.service` aktiviert hast, wird er beim nächsten Server-Neustart automatisch gestartet. Du kannst das mit

sudo systemctl is-enabled dns-update.service

überprüfen.
