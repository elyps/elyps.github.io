# Running Passbolt Container

## Create working directories

1. Continue with the following commands in a terminal window

**create working directories**
   ```
   mkdir /home/$USER/docker/passbolt -p && mkdir /home/$USER/docker/mariadb -p
   ```

## Set owner of working directories

**set owner of working directories**
   ```
   sudo chown "$USER":"$USER" /home/"$USER"/docker -R
   ```


## Create GPG Key

Du kannst das mit folgendem Befehl tun:

docker run --rm --entrypoint gpg passbolt/passbolt:latest --generate-key

Speichere den privaten und öffentlichen Schlüssel für die spätere Verwendung.

## Manual container start

### DB Container

**run the mariadb docker container**
docker run --name mariadb-passbolt \
-e MYSQL_ROOT_PASSWORD=r00tp@$$ \
-e MYSQL_USER=passbolt_rw \
-e MYSQL_PASSWORD=r00tp@$$ \
-e MYSQL_DATABASE=passbolt \
-p 3333:3306 \
-d mariadb

### Passbolt Container

**run the passbolt docker container, replace 10.10.27.11 with your docker host IP address**
docker run --name passbolt \
-p 8081:80 \
-p 4433:443 \
-e DATASOURCES_DEFAULT_HOST=192.168.2.164:3333 \
-e DATASOURCES_DEFAULT_PASSWORD=r00tp@$$ \
-e DATASOURCES_DEFAULT_USERNAME=passbolt_rw \
-e DATASOURCES_DEFAULT_DATABASE=passbolt \
-e APP_FULL_BASE_URL=https://passbolt.bastian-fischer.dev \
-d passbolt/passbolt

## Create an admin user account

**create an admin user account**
docker exec passbolt su \
-m -c "bin/cake passbolt register_user \
-u kontakt@bastian-fischer.dev \
-f Bastian \
-l Fischer \
-r admin" \
-s /bin/sh www-data

2. Copy the registration link generated

3. Open a web browser and navigate to http\://DNSorIP:8081/\<registration link copied above>

4. The Passbolt setup screen should be displayed

5. Enter a passphrase

6. Click the download button to download the key and store it in a safe place > Click the Next button

7. Set a color and 3 letter security token > Click the Next button

8. Follow the steps to install the Passbolt browser extension

9. Click the link to refresh after installing the extension

10. Voila! Welcome to Passbolt Password Vault running in a Docker container

**Passbolt Ports:**
443/tcp, 0.0.0.0:8081->80/tcp, :::8081->80/tcp

**MariaDB Ports:**
0.0.0.0:3306->3306/tcp, :::3306->3306/tcp

## Nginx conf

server {
    listen 80;
    server_name passbolt.bastian-fischer.dev;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name passbolt.bastian-fischer.dev;

    ssl_certificate /etc/letsencrypt/live/passbolt.bastian-fischer.dev/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/passbolt.bastian-fischer.dev/privkey.pem;

    # Weitere SSL-Konfigurationen hier, z.B., starke Ciphers und Protokolle

    location / {
        proxy_pass http://192.168.2.164:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

## Docker compose

version: '3'

services:
  mariadb-passbolt:
    image: mariadb:latest
    container_name: mariadb-passbolt
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: p85HRT2h59U46q64
      MYSQL_USER: passbolt_rw
      MYSQL_PASSWORD: h57E842567vy4Cso
      MYSQL_DATABASE: passbolt
    ports:
      - "3306:3306"
    volumes:
      - /home/$USER/docker/mariadb:/var/lib/mysql

  passbolt:
    image: passbolt/passbolt
    container_name: passbolt
    restart: always
    ports:
      - "8081:80"
      - "4433:443"
    environment:
      DATASOURCES_DEFAULT_HOST: 192.168.2.217:3306
      DATASOURCES_DEFAULT_PASSWORD: h57E842567vy4Cso
      DATASOURCES_DEFAULT_USERNAME: passbolt_rw
      DATASOURCES_DEFAULT_DATABASE: passbolt
      APP_FULL_BASE_URL: https://pass.bastian-fischer.dev
      EMAIL_DEFAULT_FROM: "noreply@passbolt.lokal"
      EMAIL_TRANSPORT_DEFAULT_HOST: "smtp.gmail.com"
      EMAIL_TRANSPORT_DEFAULT_PORT: 587
      EMAIL_TRANSPORT_DEFAULT_USERNAME: "kontakt@bastian-fischer.dev"
      EMAIL_TRANSPORT_DEFAULT_PASSWORD: "uceu ejys xdzt pdoo"
      EMAIL_TRANSPORT_DEFAULT_TLS: "true"
      PASSBOLT_KEYRING: "/etc/passbolt/gpg"
    volumes:
      - ./passbolt:/var/www/passbolt
	  - ./passbolt/app.php:/etc/passbolt/app.php 
	  - ./passbolt/passbolt.php:/etc/passbolt/passbolt.php 
	  - ./passbolt/serverkey.asc:/etc/passbolt/gpg/serverkey.asc 
	  - ./passbolt/serverkey_private.asc:/etc/passbolt/gpg/serverkey_private.asc 
	  - ./passbolt/images:/usr/share/php/passbolt/webroot/img/public/images
	  - gpg_data:/etc/passbolt/gpg
      - jwt_data:/etc/passbolt/jwt
  
  volumes:
    gpg_data:
    jwt_data:
## Delete Passbolt (complete)

**Find all Passbolt Files**
sudo find . -type d -name passbolt

**Delete All Passbolt Files
sudo find . -type d -name passbolt -exec rm -r {} +

**Remove MariaDB**
sudo rm -r /home/$USER/docker/mariadb

Source: 
<https://help.passbolt.com/hosting/install/ce/docker>
<https://hub.docker.com/r/passbolt/passbolt>
