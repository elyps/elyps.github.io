Hier eine schrittweise Anleitung, wie du in einem LXC-Container auf deinem Proxmox-Server ein Netzwerklaufwerk (via Samba) einrichten kannst, das du für Windows-Backups nutzen kannst.

---
## Samba Server Installation & Konfiguration
### 1. Container erstellen

- **Proxmox Webinterface:**  
    Erstelle einen neuen LXC-Container, z. B. mit einem Debian- oder Ubuntu-Template.
- **Ressourcen & Netzwerk:**  
    Weise dem Container genügend Ressourcen zu und konfiguriere eine statische IP (oder stelle sicher, dass er im gleichen Netzwerksegment wie deine Windows-Clients liegt).

---

### 2. Samba im Container installieren

Melde dich per Konsole oder SSH im Container an und führe folgende Befehle aus:

apt update
apt install samba -y

Dies installiert den Samba-Server, der für die Freigabe von Verzeichnissen unter Windows benötigt wird.

---

### 3. Freigabeverzeichnis anlegen und Berechtigungen setzen

Erstelle ein Verzeichnis, das du als Backup-Speicher nutzen möchtest:

mkdir -p /srv/samba/backup
chown nobody:nogroup /srv/samba/backup
chmod 0777 /srv/samba/backup

_Hinweis:_ Mit diesen Einstellungen ist das Verzeichnis für Gäste zugänglich. Für mehr Sicherheit kannst du statt "guest ok" auch benutzerbasierte Authentifizierung einrichten.

---

### 4. Samba konfigurieren

Bearbeite die Konfigurationsdatei `/etc/samba/smb.conf`:

nano /etc/samba/smb.conf

Füge am Ende der Datei folgenden Abschnitt hinzu:

[Backup]
   path = /srv/samba/backup
   browseable = yes
   read only = no
   guest ok = yes

_Optional:_  
Falls du eine Authentifizierung wünschst, kannst du "guest ok" entfernen und stattdessen Benutzer und Passwörter konfigurieren (z. B. mit `smbpasswd -a <benutzername>`).

---

### 5. Samba-Dienste starten und aktivieren

Starte den Samba-Dienst neu, damit die Änderungen wirksam werden:

systemctl restart smbd
systemctl enable smbd

---

### 6. Firewall und Netzwerkeinstellungen prüfen

- **Firewall im Container:**  
    Stelle sicher, dass Samba-Portfreigaben (Ports 137–139 und 445) erlaubt sind.
- **Proxmox-Netzwerk:**  
    Überprüfe, ob keine zusätzlichen Netzwerksperren den Zugriff von Windows-Clients verhindern.

---

### 7. Zugriff von Windows testen

- Öffne auf einem Windows-Client den Datei-Explorer.
- Gib in der Adressleiste den Pfad ein, z. B.:
    
    ```
    \\<IP-Adresse-des-Containers>\Backup
    ```
    
- Bei Bedarf kannst du diesen Pfad auch als Netzlaufwerk verbinden.

---

Diese Anleitung sollte dir helfen, ein Netzwerklaufwerk in einem Container auf deinem Proxmox-Server einzurichten. Falls du weitere Anpassungen (z. B. benutzerbasierte Zugriffe oder erweiterte Sicherheitsoptionen) benötigst, können diese Schritte als Basis genutzt und entsprechend erweitert werden.


## Benutzer-Authentifizierung

Hier eine erweiterte Anleitung, um die Samba-Freigabe mit Benutzer-Authentifizierung abzusichern:

---

### 1. Benutzer im System anlegen

Falls du noch keinen passenden Systembenutzer hast, lege einen neuen Benutzer an (zum Beispiel "backupuser"):

adduser backupuser

Folge den Eingabeaufforderungen, um ein Passwort und weitere Details festzulegen.

---

### 2. Samba-Benutzer hinzufügen

Füge den Systembenutzer auch der Samba-Benutzerdatenbank hinzu:

smbpasswd -a backupuser

Gib ein starkes Passwort ein, das später beim Zugriff auf die Freigabe verwendet wird.

---

### 3. Samba-Konfiguration anpassen

Bearbeite die Datei `/etc/samba/smb.conf` und passe den Abschnitt für deine Freigabe an. Ersetze den bisherigen Abschnitt (mit „guest ok“) durch:

[Backup]
   path = /srv/samba/backup
   browseable = yes
   read only = no
   valid users = backupuser

Diese Einstellung sorgt dafür, dass nur der Benutzer `backupuser` auf die Freigabe zugreifen kann.  
_Optional:_ Falls du mehreren Benutzern den Zugriff erlauben möchtest, kannst du diese als kommaseparierte Liste hinter `valid users` eintragen (z. B. `valid users = user1, user2`).

---

### 4. Samba-Dienst neu starten

Damit die Änderungen wirksam werden, starte den Samba-Dienst neu:

systemctl restart smbd

---

### 5. Zugriff von Windows testen

Auf dem Windows-Client öffnest du den Datei-Explorer und gibst den Pfad ein:

\\<IP-Adresse-des-Containers>\Backup

Beim Zugriff wirst du nun zur Eingabe von Benutzername und Passwort aufgefordert. Verwende hierbei den Benutzernamen `backupuser` und das zuvor festgelegte Passwort.

---

Mit diesen Schritten hast du die Samba-Freigabe so konfiguriert, dass eine Authentifizierung erforderlich ist.
