## Systemanforderung

Hier eine Zusammenfassung der Mindestanforderungen für eine selbstverwaltete GitLab‑Installation – wobei zu beachten ist, dass die konkreten Werte je nach Nutzerzahl, Repository-Größe und CI/CD-Last variieren können:

**Für kleine oder Test-Umgebungen**  
• **Arbeitsspeicher (RAM):** Mindestens 4 GB (wobei 4 GB zwar das absolute Minimum darstellen, jedoch häufig mehr empfohlen werden, um eine flüssige Performance zu erzielen).  
• **CPU:** Mindestens 2 vCPUs (bei Produktionssystemen oder steigender Auslastung werden 4 oder mehr Kerne empfohlen).  
• **Festplattenspeicher:** Ein Mindestwert von ca. 20 GB freiem Speicherplatz für die Installation und laufende Daten (Projekte, Logs, Datenbank, Container-Registry etc.); in der Praxis sind – je nach Wachstum – deutlich mehr Kapazitäten (beispielsweise SSDs) sinnvoll.

**Für produktive Einsätze (bei mehreren Benutzern und umfangreicher Nutzung)**  
• **RAM:** 8 GB oder mehr (bei größeren Instanzen oft 16 GB und mehr, um alle integrierten Dienste wie PostgreSQL, Redis, Sidekiq etc. zuverlässig zu betreiben).  
• **CPU:** 4 oder mehr vCPUs, um parallele Anfragen und CI/CD-Aufgaben zu bewältigen.  
• **Speicher:** Ausreichend SSD-basierten Speicher, um die Reaktionszeiten zu verbessern – hier sollte die Kapazität an das erwartete Datenvolumen angepasst werden.

**Weitere Hinweise:**

- Die offizielle Omnibus‑GitLab-Paketlösung bündelt alle notwendigen Komponenten (PostgreSQL, Redis, NGINX etc.), sodass die Ressourcenbedarfsschätzung alle diese Prozesse berücksichtigen muss.
- Je nach Einsatz (z. B. intensiver CI/CD-Betrieb oder umfangreiche Repositorys) sind die Systemressourcen entsprechend zu skalieren.
- Es empfiehlt sich, zunächst mit den Mindestanforderungen zu starten und die Systemauslastung – etwa über Monitoring und Nutzerstatistiken – im Betrieb regelmäßig zu prüfen, um bei Bedarf nachzurüsten.

Für detaillierte und aktuelle Informationen empfiehlt es sich, die offizielle Dokumentation (z. B. unter [GitLab Requirements](https://docs.gitlab.com/ee/install/requirements.html)) zu Rate zu ziehen.


## Omnibus‑GitLab-Paketlösung mit docker compose

Hier ist ein Beispiel, wie du die Omnibus‑GitLab‑Paketlösung in einem Docker‑Compose‑Setup verwenden kannst. Dabei wird der offizielle GitLab‑Omnibus‑Container genutzt, in dem alle nötigen Komponenten (Datenbank, Redis, NGINX usw.) bereits integriert sind. Du kannst die Konfiguration über die Umgebungsvariable GITLAB_OMNIBUS_CONFIG in der docker‑compose.yml vornehmen.

Beispiel (für eine einfache Installation):

version: '3.7'

services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    hostname: "gitlab.example.com"
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://gitlab.example.com'
        # Beispiel: Aktivierung des integrierten SMTP für E-Mails
        # gitlab_rails['smtp_enable'] = true
        # gitlab_rails['smtp_address'] = "smtp.example.com"
        # gitlab_rails['smtp_port'] = 587
        # gitlab_rails['smtp_user_name'] = "[email protected]"
        # gitlab_rails['smtp_password'] = "geheimespasswort"
        # gitlab_rails['smtp_domain'] = "example.com"
        # gitlab_rails['smtp_authentication'] = "login"
        # gitlab_rails['smtp_enable_starttls_auto'] = true
    ports:
      - "80:80"
      - "443:443"
      - "22:22"
    volumes:
      - "./gitlab/config:/etc/gitlab"
      - "./gitlab/logs:/var/log/gitlab"
      - "./gitlab/data:/var/opt/gitlab"

networks:
  default:
    name: gitlab-network

**Erklärung der wichtigsten Punkte:**

- **Image & Container:** Wir nutzen den offiziellen GitLab‑Omnibus‑Container (z. B. `gitlab/gitlab-ce:latest`). Dabei ist auch die Enterprise Edition verfügbar (gitlab/gitlab-ee).
- **GITLAB_OMNIBUS_CONFIG:** In diesem Block kannst du beliebige Einstellungen konfigurieren, die normalerweise in der Datei gitlab.rb stehen würden. Achte darauf, dass die Einrückungen korrekt sind (jedes neue Konfigurations-Statement muss eingerückt sein).
- **Ports:** Der Container leitet den HTTP‑(80), HTTPS‑(443) und SSH‑(22) Port weiter.
- **Volumes:** Die persistente Speicherung der Konfiguration, Logs und Daten erfolgt über gemappte Verzeichnisse auf dem Host.
- **Netzwerk:** Hier wird optional ein benannter Docker‑Netzwerk definiert, in dem du weitere Services (z. B. einen Reverse Proxy) hinzufügen kannst.

**Starten der Installation:**

1. Speichere den oben stehenden Inhalt in einer Datei namens `docker-compose.yml` im gewünschten Verzeichnis.
    
2. Erstelle die benötigten Unterverzeichnisse (z. B. `gitlab/config`, `gitlab/logs`, `gitlab/data`) oder passe den Pfad an deine Gegebenheiten an.
    
3. Starte den Container mit:
    
    ```bash
    docker-compose up -d
    ```
    
4. Nach dem ersten Start (dies kann einige Minuten dauern) ist GitLab unter der in `external_url` konfigurierten Adresse erreichbar.
    

**Upgrade-Prozess:**

- Zum Upgrade ziehst du einfach das neue Image mit:
    
    ```bash
    docker-compose pull gitlab
    docker-compose up -d
    ```
    
- Da alle Daten in den gemappten Volumes persistieren, bleibt deine Konfiguration erhalten.
    

Dieses Setup bietet einen einfachen Weg, GitLab als Omnibus‑Paketlösung mithilfe von Docker Compose zu betreiben – ideal für kleine bis mittelgroße Installationen oder Testumgebungen. Für produktive Einsätze solltest du die Hardware-Ressourcen (RAM, CPU, Storage) entsprechend anpassen und ggf. weitere Services (z. B. Backup, Monitoring) integrieren.

Weitere Details und Best Practices findest du in der offiziellen [GitLab-Dokumentation zu Docker](https://docs.gitlab.com/ee/install/docker/)


## Account Aktivierung ohne SMTP-Server aktivierung

In neueren GitLab‑Versionen ist die Methode `confirm!` nicht mehr vorhanden. Stattdessen kannst du das Attribut `confirmed_at` manuell setzen, um den Account zu aktivieren. Beispiel:

docker exec -it gitlab gitlab-rails runner "u = User.find_by(email: 'kontakt@bastian-fischer.dev'); u.confirmed_at = Time.now; u.save!"

Dadurch wird der Benutzer als bestätigt markiert. Alternativ könntest du – falls verfügbar – auch `skip_confirmation!` verwenden, also:

docker exec -it gitlab gitlab-rails runner "u = User.find_by(email: 'kontakt@bastian-fischer.dev'); u.skip_confirmation!; u.save!"

Überprüfe, welche Methode in deiner GitLab-Version unterstützt wird. Beachte, dass diese Workaround-Lösungen vor allem in Umgebungen ohne SMTP sinnvoll sind.

Und dann noch den Account aktivieren:

docker exec -it gitlab gitlab-rails runner "u = User.find_by(email: 'kontakt@bastian-fischer.dev'); u.update_attribute(:state, 'active')"

Dadurch wird der Status des Benutzers von „blocked“ (pending approval) auf „active“ gesetzt.


## Fehlerbehebungen

### Your account is pending approval from your GitLab administrator and hence blocked. Please contact your GitLab administrator if you think this is an error.

Der Fehler „Your account is pending approval from your GitLab administrator and hence blocked“ weist darauf hin, dass dein Account zwar erstellt und (falls du den Bestätigungsvorgang schon über die Konsole angestoßen hast) als „confirmed“ markiert wurde, aber weiterhin in einem Status „pending approval“ bzw. „blocked“ verbleibt – weil in deiner GitLab‑Instanz neue Anmeldungen standardmäßig von einem Administrator freigegeben werden müssen.

Um das zu beheben, hast du grundsätzlich zwei Ansätze:

1. **Manuelle Freigabe über die Admin-Oberfläche:**  
    Melde dich als Administrator im Webinterface an (unter „Admin Area“ > „Users“). Suche den betroffenen Benutzer (z. B. mit der E‑Mail „[kontakt@bastian-fischer.dev](mailto:kontakt@bastian-fischer.dev)“) und klicke auf den Button „Unblock“ oder „Approve“. Damit wird der Account aktiviert.
    
2. **Freischaltung über die Rails-Konsole:**  
    Falls du den Benutzer über die Konsole freischalten möchtest (z. B. wenn du keinen SMTP-Server konfiguriert hast und deshalb keine E‑Mail-Bestätigung erfolgen kann), kannst du in den GitLab‑Container eine Rails‑Runner‑Session starten und den Status des Benutzers direkt ändern.  
    Versuche folgenden Befehl:
    
    ```bash
    docker exec -it gitlab gitlab-rails runner "u = User.find_by(email: 'kontakt@bastian-fischer.dev'); u.update_attribute(:state, 'active')"
    ```
    
    Dadurch wird der Status des Benutzers von „blocked“ (pending approval) auf „active“ gesetzt.
    

**Zusätzlicher Hinweis:**  
Wenn du künftig nicht möchtest, dass neue Benutzer zur manuellen Freigabe blockiert werden, kannst du in der GitLab‑Konfiguration (z. B. in deiner `gitlab.rb` oder via der Umgebungsvariable `GITLAB_OMNIBUS_CONFIG`) die Einstellung zur Admin‑Freigabe von neuen Accounts anpassen. Dies erfolgt in der Regel über die Admin-Oberfläche unter „Settings“ > „General“ > „Sign-up restrictions“ – dort kannst du die Option „Require admin approval for new users“ deaktivieren, sofern das deinem Sicherheitskonzept entspricht.

Beachte aber, dass in Produktionsumgebungen oft bewusst eine manuelle Freigabe gewünscht ist, um unerwünschte Registrierungen zu verhindern.


### SSH Port Ändern

Es gibt keine dedizierte Environment-Variable nur für den SSH-Port. Stattdessen kannst du die Konfiguration über die omnibus-Umgebungsvariable `GITLAB_OMNIBUS_CONFIG` vornehmen. Beispiel für eine `docker-compose.yml`:

version: "3"
services:
  gitlab:
    image: gitlab/gitlab-ee:latest
    ports:
      - "222:22"
      - "80:80"
      - "443:443"
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://gitlab.example.com'
        gitlab_rails['gitlab_shell_ssh_port'] = 222
    volumes:
      - gitlab_config:/etc/gitlab
      - gitlab_logs:/var/log/gitlab
      - gitlab_data:/var/opt/gitlab

volumes:
  gitlab_config:
  gitlab_logs:
  gitlab_data:

In diesem Beispiel wird beim Containerstart der SSH-Port in GitLab auf 222 gesetzt. Die Ports 80 und 443 werden für HTTP/HTTPS weiterhin wie gewohnt verwendet. Beachte, dass du auch die `external_url` entsprechend anpassen solltest.


## .env File nutzen

Die `.env`-Datei für deine **GitLab SMTP-Konfiguration mit PrivateEmail** sollte wie folgt aussehen:

### **Beispiel `.env`-Datei**

# SMTP-Konfiguration für PrivateEmail
GITLAB_SMTP_ADDRESS=mail.privateemail.com
GITLAB_SMTP_PORT=587
GITLAB_SMTP_USER=kontakt@bastian-fischer.dev
GITLAB_SMTP_PASSWORD=#Pr5541*  # Sicherer: Speichere dieses Passwort in einer sicheren Umgebung!
GITLAB_SMTP_DOMAIN=bastian-fischer.dev
GITLAB_SMTP_AUTHENTICATION=login
GITLAB_SMTP_TLS=false
GITLAB_SMTP_ENABLE_STARTTLS_AUTO=true

---

### **Schritt-für-Schritt Nutzung in `docker-compose.yml`**

Um die Werte aus der `.env`-Datei in **Docker Compose** zu nutzen, passe deine `docker-compose.override.yml` oder `docker-compose.yml` wie folgt an:

version: '3.8'

services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    restart: always
    env_file:
      - .env  # Hier wird die .env-Datei eingelesen
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        gitlab_rails['smtp_enable'] = true
        gitlab_rails['smtp_address'] = "${GITLAB_SMTP_ADDRESS}"
        gitlab_rails['smtp_port'] = "${GITLAB_SMTP_PORT}"
        gitlab_rails['smtp_user_name'] = "${GITLAB_SMTP_USER}"
        gitlab_rails['smtp_password'] = "${GITLAB_SMTP_PASSWORD}"
        gitlab_rails['smtp_domain'] = "${GITLAB_SMTP_DOMAIN}"
        gitlab_rails['smtp_authentication'] = "${GITLAB_SMTP_AUTHENTICATION}"
        gitlab_rails['smtp_tls'] = ${GITLAB_SMTP_TLS}
        gitlab_rails['smtp_enable_starttls_auto'] = ${GITLAB_SMTP_ENABLE_STARTTLS_AUTO}

---

### **Wichtige Hinweise**

✅ **Sicherheitsaspekt**

- **Speichere sensible Daten nicht direkt in `docker-compose.yml`**, sondern in `.env`, damit sie nicht im Git-Repository landen.
- Falls du **Docker Secrets** nutzt, kannst du das Passwort alternativ dort speichern.

✅ **`.env`-Datei in `.gitignore` hinzufügen**  
Falls dein Projekt versioniert wird (z. B. mit Git), solltest du die `.env`-Datei aus dem Repository ausschließen:

echo ".env" >> .gitignore

✅ **GitLab nach Änderungen neu starten**  
Nach Anpassung der `.env`-Datei und `docker-compose.yml` solltest du GitLab neu starten:

docker-compose down
docker-compose up -d

Das sorgt dafür, dass **SMTP-Umgebungsvariablen** korrekt geladen und verwendet werden. 🚀
