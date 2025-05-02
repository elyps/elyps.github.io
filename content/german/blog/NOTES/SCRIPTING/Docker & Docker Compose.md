Hier ist ein Skript, das automatisch die neuesten Versionen von Docker und Docker Compose installiert. Es verwendet das offizielle Docker-Installationsskript und ergänzt die Installation von Docker Compose mit der neuesten Version.

### Automatisches Installationsskript

#!/bin/bash

# Sicherstellen, dass das Skript mit Root-Rechten ausgeführt wird
if [ "$EUID" -ne 0 ]; then
    echo "Bitte führen Sie dieses Skript mit Root-Rechten aus."
    exit 1
fi

echo "Automatische Installation der neuesten Versionen von Docker und Docker Compose..."

# 1. Docker installieren (offizielles Skript)
echo "Docker wird installiert..."
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

# 2. Überprüfen, ob Docker erfolgreich installiert wurde
docker --version
if [ $? -ne 0 ]; then
    echo "Docker konnte nicht erfolgreich installiert werden."
    exit 1
fi

# 3. Neueste Version von Docker Compose installieren
echo "Die neueste Version von Docker Compose wird installiert..."
DOCKER_COMPOSE_URL="https://api.github.com/repos/docker/compose/releases/latest"
DOCKER_COMPOSE_VERSION=$(curl -s $DOCKER_COMPOSE_URL | grep '"tag_name":' | sed -E 's/.*"([^"]+)".*/\1/')

if [ -z "$DOCKER_COMPOSE_VERSION" ]; then
    echo "Die neueste Version von Docker Compose konnte nicht abgerufen werden."
    exit 1
fi

curl -SL "https://github.com/docker/compose/releases/download/$DOCKER_COMPOSE_VERSION/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# 4. Überprüfen, ob Docker Compose erfolgreich installiert wurde
docker-compose --version
if [ $? -ne 0 ]; then
    echo "Docker Compose konnte nicht erfolgreich installiert werden."
    exit 1
fi

# 5. Benutzer zur Docker-Gruppe hinzufügen (optional)
read -p "Möchten Sie Ihren Benutzer zur Docker-Gruppe hinzufügen? (y/n): " ADD_USER_TO_GROUP
if [ "$ADD_USER_TO_GROUP" == "y" ]; then
    usermod -aG docker $USER
    echo "Der Benutzer wurde zur Docker-Gruppe hinzugefügt. Bitte melden Sie sich ab und wieder an, damit die Änderungen wirksam werden."
fi

echo "Installation abgeschlossen! Docker und Docker Compose sind jetzt auf dem neuesten Stand."

### Schritte zur Verwendung

1. Speichere das Skript in einer Datei, z. B. `install_docker_latest.sh`.
2. Mache die Datei ausführbar:
    
    ```bash
    chmod +x install_docker_latest.sh
    ```
    
3. Führe das Skript aus:
    
    ```bash
    sudo ./install_docker_latest.sh
    ```
    

### Funktionsweise des Skripts

- Das Skript verwendet das offizielle Docker-Installationsskript, um die neueste Docker-Version zu installieren.
- Es ruft die neueste Version von Docker Compose über die GitHub-API ab und installiert diese.
- Es bietet eine Option, den Benutzer zur Docker-Gruppe hinzuzufügen.

### Vorteile

- Immer aktuell: Es lädt immer die neuesten Versionen herunter.
- Benutzerfreundlich: Automatische Installation und Konfiguration.
- Flexibilität: Docker Compose wird separat installiert und ist dadurch unabhängig vom Docker-Paket.
