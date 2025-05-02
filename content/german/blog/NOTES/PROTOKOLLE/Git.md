## Kommandozeilenbefehle

Du kannst vorhandene Dateien auch von deinem Computer aus hochladen, indem du die folgenden Anweisungen verwendst.

### Git-Identität konfigurieren

[Get started with Git](https://git.bastian-fischer.dev/help/topics/git/get_started.md) and learn [how to configure it](https://git.bastian-fischer.dev/help/topics/git/how_to_install_git/index.md#configure-git).

*   [Lokal](https://git.bastian-fischer.dev/private-projekte/notebooks/obsidian#)
*   [Global](https://git.bastian-fischer.dev/private-projekte/notebooks/obsidian#)

#### Lokale Git-Konfiguration

Konfiguriere deine Git-Identität lokal, um sie nur für dieses Projekt zu verwenden:

git config --local user.name "Bastian Fischer"
git config --local user.email "kontakt@bastian-fischer.dev"


#### Globale Git-Konfiguration

Konfiguriere deine Git-Identität global, um sie für alle aktuellen und zukünftigen Projekte auf deiner Maschine zu verwenden:

git config --global user.name "Bastian Fischer"
git config --global user.email "kontakt@bastian-fischer.dev"


### Add files

Übertrage Dateien mit SSH oder HTTPS in dieses Repository. Wenn du dir nicht sicher bist, empfehlen wir SSH.

*   [SSH](https://git.bastian-fischer.dev/private-projekte/notebooks/obsidian#)
*   [HTTPS](https://git.bastian-fischer.dev/private-projekte/notebooks/obsidian#)

[How to use SSH keys](https://git.bastian-fischer.dev/help/user/ssh.md)?


### SSH
#### Neues Repository erstellen

git clone git@git.bastian-fischer.dev:private-projekte/notebooks/obsidian.git
cd obsidian
git switch --create main
touch README.md
git add README.md
git commit -m "add README"
git push --set-upstream origin main


#### push eines vorhandenen Ordners

##### Go to your folder

cd existing\_folder

##### Git-Repository konfigurieren

git init --initial-branch=main
git remote add origin git@git.bastian-fischer.dev:private-projekte/notebooks/obsidian.git
git add .
git commit -m "Initial commit"
git push --set-upstream origin main


#### push an ein existierendes Git-Repository

##### Go to your repository

cd existing_repo

##### Git-Repository konfigurieren

git remote rename origin old-origin
git remote add origin git@git.bastian-fischer.dev:private-projekte/notebooks/obsidian.git
git push --set-upstream origin --all
git push --set-upstream origin --tags




### HTTPS

#### Neues Repository erstellen

git clone https://git.bastian-fischer.dev/private-projekte/notebooks/obsidian.git
cd obsidian
git switch --create main
touch README.md
git add README.md
git commit -m "add README"
git push --set-upstream origin main

#### push eines vorhandenen Ordners

##### Go to your folder

cd existing_folder

##### Git-Repository konfigurieren

git init --initial-branch=main
git remote add origin https://git.bastian-fischer.dev/private-projekte/notebooks/obsidian.git
git add .
git commit -m "Initial commit"
git push --set-upstream origin main

#### push an ein existierendes Git-Repository

##### Go to your repository

cd existing_repo

##### Git-Repository konfigurieren

git remote rename origin old-origin
git remote add origin https://git.bastian-fischer.dev/private-projekte/notebooks/obsidian.git
git push --set-upstream origin --all
git push --set-upstream origin --tags


## Fehlerbehebungen

### git push -u origin main ssh: connect to host git.bastian-fischer.dev port 22: Connection timed out fatal: Could not read from remote repository. Please make sure you have the correct access rights and the repository exists.


This error indicates that your local machine is unable to establish an SSH connection to `git.bastian-fischer.dev` on port 22. Here are some steps to diagnose and resolve the issue:

1. **Verify Network Connectivity**
    
    - Test if port 22 is reachable from your machine:
        
        ```bash
        nc -vz git.bastian-fischer.dev 22
        ```
        
        or
        
        ```bash
        telnet git.bastian-fischer.dev 22
        ```
        
    - If the connection fails, it might be due to network restrictions (e.g., firewall rules or ISP blocking port 22).
2. **Check SSH Service on the Remote Host**
    
    - Confirm that the remote server is running an SSH daemon on port 22. Sometimes, services run on a non-standard port.
    - If you suspect a different port is in use, verify the correct SSH port for your repository host.
3. **Review Your Git Remote URL**
    
    - Run the following command to inspect your remote configuration:
        
        ```bash
        git remote -v
        ```
        
    - Ensure that the URL is correct. If the service uses a non-standard port, you may need to adjust the URL or your SSH configuration.
4. **Update Your SSH Configuration (if needed)**
    
    - If the remote SSH service is running on a port other than 22, add an entry to your `~/.ssh/config` file:
        
        ```ssh
        Host git.bastian-fischer.dev
            HostName git.bastian-fischer.dev
            Port <correct-port-number>
            User git
            IdentityFile ~/.ssh/id_rsa
        ```
        
    - Replace `<correct-port-number>` with the actual port number.
5. **Confirm Access Rights and Repository Existence**
    
    - Double-check that your SSH public key is added to the remote server’s authorized keys.
    - Ensure that the repository exists and that your user has the necessary permissions.
6. **Test SSH Connection Directly**
    
    - Use verbose mode to debug the SSH connection:
        
        ```bash
        ssh -vT git.bastian-fischer.dev
        ```
        
    - The output should help pinpoint where the connection is failing.

Following these steps should help identify and resolve the issue preventing you from pushing to the repository.
