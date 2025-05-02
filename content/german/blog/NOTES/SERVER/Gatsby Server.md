Hier eine Schritt-für-Schritt-Anleitung, um Gatsby auf deinem Debian-Server zu installieren:

1. **System aktualisieren:**
    
    ```bash
    sudo apt update
    sudo apt upgrade
    ```
    
2. **Node.js und npm installieren:**
    
    Gatsby benötigt Node.js (Version 14 oder höher, idealerweise 16+). Falls Node.js noch nicht installiert ist, kannst du es über den NodeSource-Installer installieren:
    
    ```bash
    curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
    sudo apt-get install -y nodejs
    ```
    
    Überprüfe die installierten Versionen:
    
    ```bash
    node -v
    npm -v
    ```
    
3. **Gatsby CLI global installieren:**
    
    Mit npm installierst du den Gatsby CLI global:
    
    ```bash
    sudo npm install -g gatsby-cli
    ```
    
    Prüfe die Installation:
    
    ```bash
    gatsby --version
    ```
    
4. **(Optional) Ein neues Gatsby-Projekt erstellen:**
    
    Wenn du direkt ein neues Projekt starten möchtest, kannst du das Starter-Template verwenden:
    
    ```bash
    gatsby new my-gatsby-site
    cd my-gatsby-site
    gatsby develop
    ```
    

Damit solltest du Gatsby erfolgreich auf deinem Debian-Server installiert haben.


Um per Browser auf deine Gatsby-Anwendung zuzugreifen, musst du sicherstellen, dass der Entwicklungsserver auf alle Netzwerk-Interfaces lauscht und der entsprechende Port freigegeben ist. Hier die Schritte:

1. **Gatsby im Entwicklungsmodus starten:**
    
    Standardmäßig startet `gatsby develop` den Server nur auf `localhost` (127.0.0.1), was nur auf dem Server selbst zugänglich ist. Um den Server so zu starten, dass er auch von außen erreichbar ist, verwende:
    
    ```bash
    gatsby develop --host 0.0.0.0
    ```
    
    Dadurch wird der Server auf allen verfügbaren Netzwerk-Interfaces gebunden.
    
2. **Auf den richtigen Port achten:**
    
    Gatsby verwendet im Entwicklungsmodus standardmäßig den Port 8000. In deinem Browser greifst du dann über die IP-Adresse deines Servers zu:
    
    ```
    http://<Server-IP>:8000
    ```
    
    Ersetze `<Server-IP>` durch die öffentliche oder lokale IP-Adresse deines Debian-Servers.
    
3. **Firewall-Konfiguration:**
    
    Stelle sicher, dass der Port 8000 in deiner Firewall geöffnet ist, damit externe Zugriffe möglich sind. Zum Beispiel, falls du `ufw` nutzt, kannst du folgenden Befehl verwenden:
    
    ```bash
    sudo ufw allow 8000/tcp
    ```
    
4. **Produktivmodus (optional):**
    
    Wenn du deine Gatsby-Anwendung in Produktion nutzen möchtest, kannst du mit `gatsby build` eine statische Version erstellen und diese mit einem Webserver wie Nginx oder Apache ausliefern. In diesem Fall würdest du den Webserver so konfigurieren, dass er den Inhalt bedient, und im Browser dann die Domain oder IP ohne Portangabe (oder mit einem Standardport) aufrufen.
    

Mit diesen Schritten solltest du in der Lage sein, deine Gatsby-Anwendung per Browser von einem entfernten Rechner aus zu erreichen.
