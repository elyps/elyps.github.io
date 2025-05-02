## **✅ Verbesserungen & Features**

✅ **Sichere Secrets-Verwaltung für Docker & SSH**\
✅ **Code Linter mit ESLint**\
✅ **Docker-Build & Push zu Docker Hub**\
✅ **Deployment auf Remote-Server via SSH**\
✅ **E-Mail-Benachrichtigungen für Fehlschläge & Erfolge**

## 🔥 **Beliebte & Nützliche Plugins**

✅ **Pipeline** → `workflow-aggregator` (Für deklarative Pipelines)\
✅ **Blue Ocean** → `blueocean` (Moderne UI für Pipelines)\
✅ **Git & GitHub** → `git`, `github`, `github-branch-source`\
✅ **Docker Support** → `docker-plugin`, `docker-workflow`\
✅ **Credentials Management** → `credentials`, `credentials-binding`\
✅ **Job Management** → `job-dsl`, `matrix-auth`, `ldap`\
✅ **CI/CD Erweiterung** → `build-timeout`, `timestamper`, `ansicolor`

## **🚀 1. `docker-compose.yml` mit Plugin-Installation**

version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: unless-stopped
    privileged: true
    user: root
    ports:
      - "8080:8080"
      - "50000:50000"
    environment:
      - JENKINS_OPTS="--prefix=/jenkins"
      - JAVA_OPTS=-Djenkins.install.runSetupWizard=false
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/bin/docker:/usr/bin/docker
      - ./jenkins.yaml:/var/jenkins_home/casc_configs/jenkins.yaml
    networks:
      - jenkins_network
    entrypoint: >
      /bin/bash -c "
      /usr/local/bin/jenkins-plugin-cli --plugins 
      workflow-aggregator 
      blueocean 
      git 
      github 
      github-branch-source 
      docker-plugin 
      docker-workflow 
      credentials 
      credentials-binding 
      job-dsl 
      matrix-auth 
      ldap 
      build-timeout 
      timestamper 
      ansicolor 
      email-ext &&
      exec /usr/bin/tini -- /usr/local/bin/jenkins.sh
      "

  docker:
    image: docker:dind
    container_name: docker_dind
    privileged: true
    restart: unless-stopped
    environment:
      - DOCKER_TLS_CERTDIR=
    volumes:
      - jenkins_home:/var/jenkins_home
    networks:
      - jenkins_network

volumes:
  jenkins_home:

networks:
  jenkins_network:
    driver: bridge


### **Anleitung zur Nutzung**

1. **Docker & Docker-Compose installieren (falls nicht vorhanden)**

   ```sh
   sudo apt update
   sudo apt install docker.io docker-compose -y
   ```

2. **Datei speichern und Jenkins starten**

   ```sh
   docker-compose up -d
   ```

3. **Jenkins Web-UI aufrufen**\
   👉 **[http://localhost:8080/jenkins]()**

4. **Erstes Passwort auslesen (optional)**

   ```sh
   docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
   ```

5. **Admin-Account einrichten & loslegen 🚀**

---

## **📜 2. `jenkins.yaml` – Automatische SMTP-Konfiguration**

Diese Datei wird in **`./jenkins.yaml`** gespeichert und von Jenkins beim Start eingelesen.

jenkins:
  systemMessage: "Jenkins ist automatisch konfiguriert 🎉"
  globalNodeProperties:
    - envVars:
        env:
          - key: SMTP_SERVER
            value: "smtp.example.com"
          - key: SMTP_PORT
            value: "587"
          - key: SMTP_USER
            value: "jenkins@example.com"
          - key: SMTP_PASSWORD
            value: "geheimes-passwort"
  mailer:
    smtpHost: "${SMTP_SERVER}"
    smtpPort: "${SMTP_PORT}"
    useSsl: false
    useTls: true
    smtpUsername: "${SMTP_USER}"
    smtpPassword: "${SMTP_PASSWORD}"
    charset: "UTF-8"
    defaultSuffix: "@example.com"

---

## **🔧 `Jenkinsfile` (Mit E-Mail Alerting)**
pipeline {
    agent {
        docker {
            image 'node:18' // Node.js als Basis (anpassbar)
        }
    }

    environment {
        DOCKER_IMAGE = "meinrepo/meinapp"
        DOCKER_TAG = "latest"
        DEPLOY_SERVER = "meine-server-ip"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/meinuser/meinprojekt.git'
            }
        }

        stage('Code Linter (ESLint)') {
            steps {
                script {
                    sh 'npm install eslint --save-dev'
                    sh './node_modules/.bin/eslint . --ext .js,.jsx'
                }
            }
        }

        stage('Build & Test') {
            steps {
                script {
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $DOCKER_IMAGE:$DOCKER_TAG ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push $DOCKER_IMAGE:$DOCKER_TAG"
                        sh "docker logout"
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'ssh-server-credentials', keyFileVariable: 'SSH_KEY')]) {
                        sh """
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no user@$DEPLOY_SERVER <<EOF
                        docker pull $DOCKER_IMAGE:$DOCKER_TAG
                        docker stop meinapp || true
                        docker rm meinapp || true
                        docker run -d --name meinapp -p 80:3000 $DOCKER_IMAGE:$DOCKER_TAG
                        EOF
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            script {
                emailext subject: "✅ Jenkins Build SUCCESS - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                         body: """
                         🎉 Erfolg! Die Pipeline wurde erfolgreich ausgeführt. 🎉
                         
                         - Job: ${env.JOB_NAME}
                         - Build-Nummer: ${env.BUILD_NUMBER}
                         - Repository: ${env.GIT_URL}
                         - Branch: ${env.GIT_BRANCH}
                         - Docker Image: ${DOCKER_IMAGE}:${DOCKER_TAG}

                         🔗 Details: ${env.BUILD_URL}
                         """,
                         to: 'dev-team@example.com',
                         from: 'jenkins@example.com'
            }
        }
        failure {
            script {
                emailext subject: "❌ Jenkins Build FAILURE - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                         body: """
                         ⚠️ Fehler! Die Pipeline ist fehlgeschlagen. ⚠️

                         - Job: ${env.JOB_NAME}
                         - Build-Nummer: ${env.BUILD_NUMBER}
                         - Repository: ${env.GIT_URL}
                         - Branch: ${env.GIT_BRANCH}
                         - Docker Image: ${DOCKER_IMAGE}:${DOCKER_TAG}

                         🔗 Details: ${env.BUILD_URL}

                         Bitte überprüfe die Logs und behebe das Problem. 🚨
                         """,
                         to: 'dev-team@example.com',
                         from: 'jenkins@example.com'
            }
        }
    }
}

### **Anleitung zur Nutzung**

1️⃣ **Repository in Jenkins einbinden**

* **Freestyle Job** → "Pipeline-Skript aus SCM" wählen
* **Repo-URL hinzufügen**
* **Branch setzen (`main` oder `master`)**
* **Jenkinsfile im Root des Repos ablegen**

2️⃣ **Jenkins vorbereiten**

* **Anmeldeinformationen (`Manage Jenkins` > `Manage Credentials`)**

  * Docker Hub: `docker-hub-credentials` (Benutzer/Passwort)
  * SSH Key: `ssh-server-credentials` (SSH-Zugang zum Server)

3️⃣ **Pipeline starten & Deployment genießen!** 🚀

---

## **🔒 Sicherheit: Secrets in Jenkins einrichten**

Damit Secrets sicher bleiben, müssen sie **in Jenkins hinterlegt** werden.

1️⃣ **Gehe zu** `Manage Jenkins` → `Manage Credentials`\
2️⃣ **Neue Credentials hinzufügen:**

* **Docker Hub:**

  * `ID: docker-hub-credentials`
  * **Type:** Username & Passwort

* **SSH-Zugang zum Server:**

  * `ID: ssh-server-credentials`
  * **Type:** SSH Private Key


---


## **🔧 3. Starten & Testen**

1️⃣ **Docker-Compose neu starten**

docker-compose down && docker-compose up -d

2️⃣ **Überprüfen, ob E-Mail-Plugin installiert ist**

- **Gehe zu:** `Manage Jenkins` → `Manage Plugins` → `Installed`
- Suche nach **Email Extension Plugin** (`email-ext`)

3️⃣ **Test-Mail senden**

- **Gehe zu:** `Manage Jenkins` → `Configure System`
- Suche nach `Extended E-mail Notification`
- Setze eine Test-Mail-Adresse und sende eine Test-Mail


## **Extras**

💡 Falls du noch spezifische Plugins benötigst, einfach hinzufügen:

/usr/local/bin/jenkins-plugin-cli --plugins PLUGIN_NAME
