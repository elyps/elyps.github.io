## Erstellen eines SSH Key Pairs

Um ein SSH-Key-Paar zu generieren, folge diesen Schritten:

1. **Git Bash öffnen**  
    Starte Git Bash (oder eine andere Shell, die SSH-Befehle unterstützt).
    
2. **Befehl zum Generieren ausführen**  
    Für moderne Systeme wird der Ed25519-Algorithmus empfohlen. Gib folgenden Befehl ein und ersetze `deine.email@example.com` durch deine E-Mail-Adresse:
    
    ```bash
    ssh-keygen -t ed25519 -C "deine.email@example.com"
    ```
    
    Falls dein System Ed25519 nicht unterstützt, kannst du alternativ RSA verwenden:
    
    ```bash
    ssh-keygen -t rsa -b 4096 -C "deine.email@example.com"
    ```
    
3. **Speicherort und Dateiname bestätigen**  
    Nach Ausführung des Befehls wirst du gefragt, wo der Schlüssel gespeichert werden soll. Drücke einfach **Enter**, um den Standardpfad (`C:\Users\<DeinBenutzername>\.ssh\id_ed25519` bzw. `id_rsa`) zu verwenden, oder gib einen alternativen Pfad/Namen an, falls du mehrere Schlüssel nutzen möchtest.
    
4. **Passphrase festlegen (optional)**  
    Anschließend wirst du aufgefordert, eine Passphrase einzugeben. Diese schützt deinen privaten Schlüssel zusätzlich. Wenn du keine Passphrase nutzen möchtest, drücke einfach **Enter**.
    
5. **Schlüssel überprüfen**  
    Nach Abschluss des Vorgangs findest du zwei Dateien im angegebenen Verzeichnis:
    
    - **Privater Schlüssel:** z. B. `id_ed25519`
    - **Öffentlicher Schlüssel:** z. B. `id_ed25519.pub`
6. **Öffentlichen Schlüssel zu GitHub hinzufügen**  
    Kopiere den Inhalt der Datei `id_ed25519.pub` (z. B. mit:
    
    ```bash
    cat ~/.ssh/id_ed25519.pub
    ```
    
    ) und füge ihn in GitHub unter **Settings > SSH and GPG keys** als neuen SSH-Key ein.
    

Mit diesen Schritten hast du erfolgreich ein SSH-Key-Paar generiert und kannst es zur Authentifizierung bei GitHub verwenden.


## SSH Config

Um in deinem lokalen Repository einen spezifischen SSH-Key für GitHub zu verwenden, kannst du die SSH-Konfiguration anpassen. Der gängige Ansatz besteht darin, in der Datei `~/.ssh/config` (unter Windows in der Regel `C:\Users\<DeinBenutzername>\.ssh\config`) einen Host-Alias anzulegen, der den gewünschten Key verwendet. So gehst du vor:

1. **SSH-Key vorbereiten**  
    Stelle sicher, dass der gewünschte SSH-Key (z. B. `mygithubkey`) in deinem SSH-Verzeichnis liegt (z. B. `C:\Users\<DeinBenutzername>\.ssh\mygithubkey`). Falls du den Key noch nicht erstellt hast, kannst du ihn mit folgendem Befehl (in Git Bash) generieren:
    
    ```bash
    ssh-keygen -t ed25519 -C "deine.email@example.com" -f ~/.ssh/mygithubkey
    ```
    
    (Alternativ kannst du `rsa` verwenden, falls nötig.)
    
2. **SSH-Konfiguration anpassen**  
    Öffne (oder erstelle, falls noch nicht vorhanden) die Datei `C:\Users\<DeinBenutzername>\.ssh\config` in einem Texteditor und füge einen neuen Host-Eintrag hinzu. Dabei legst du einen Alias (z. B. `github-custom`) fest, der auf GitHub verweist und den spezifischen SSH-Key nutzt:
    
    ```ssh-config
    Host github-custom
        HostName github.com
        User git
        IdentityFile C:/Users/<DeinBenutzername>/.ssh/mygithubkey
    ```
    
    _(Hinweis: Unter Windows funktionieren in der Regel Vorwärtsschrägstriche in Pfaden, oder du kannst doppelte Backslashes verwenden.)_
    
3. **Remote-URL im lokalen Repository anpassen**  
    Damit Git diesen Alias verwendet, musst du die Remote-URL deines Repositories anpassen. Öffne dein Repository in der Kommandozeile und führe folgenden Befehl aus:
    
    ```bash
    git remote set-url origin git@github-custom:<Benutzername>/<RepoName>.git
    ```
    
    Beispiel:
    
    ```bash
    git remote set-url origin git@github-custom:deinBenutzername/mein-projekt.git
    ```
    
    Dadurch wird Git angewiesen, beim Pushen und Pullen den Host-Alias `github-custom` zu verwenden, der in deiner SSH-Konfiguration definiert ist.
    
4. **Verbindung testen**  
    Teste die Verbindung mit:
    
    ```bash
    ssh -T git@github-custom
    ```
    
    Du solltest eine Meldung von GitHub erhalten, die bestätigt, dass die Authentifizierung funktioniert (z. B. "Hi `<deinBenutzername>`! You've successfully authenticated...").
    

Mit dieser Konfiguration wird beim Pushen auf GitHub in diesem Repository dein spezifizierter SSH-Key verwendet.


## Fehlerbehebung

### git@github.com: Permission denied (publickey).

Die Fehlermeldung bedeutet, dass GitHub deinen privaten Schlüssel nicht akzeptiert – entweder weil er nicht genutzt wird oder weil der dazugehörige öffentliche Schlüssel nicht (richtig) hinterlegt ist. Folgende Schritte helfen bei der Fehlersuche:

1. **SSH-Agent prüfen**  
    Stelle sicher, dass der richtige Schlüssel im SSH-Agent geladen ist.
    
    ```bash
    ssh-add -l
    ```
    
    Falls dein Schlüssel nicht angezeigt wird, füge ihn hinzu:
    
    ```bash
    ssh-add C:/Users/bfischer/.ssh/rsa-key-20250208_obsidian_git
    ```
    
2. **SSH-Verbindung debuggen**  
    Führe den Debug-Befehl aus, um zu sehen, welcher Schlüssel tatsächlich angeboten wird:
    
    ```bash
    ssh -vvvT git@github.com
    ```
    
    Im Debug-Output solltest du eine Zeile finden, die anzeigt, welcher `IdentityFile` genutzt wird. So kannst du überprüfen, ob dein konfigurierter Schlüssel verwendet wird.
    
3. **Öffentlichen Schlüssel vergleichen**  
    Erzeuge den öffentlichen Schlüssel aus deinem privaten Schlüssel, um ihn mit dem in GitHub hinterlegten zu vergleichen:
    
    ```bash
    ssh-keygen -y -f C:/Users/bfischer/.ssh/rsa-key-20250208_obsidian_git
    ```
    
    Kopiere den ausgegebenen Schlüssel und vergleiche ihn mit dem in GitHub (unter **Settings > SSH and GPG keys**) eingetragenen. Eine Abweichung führt zu „Permission denied (publickey)“.
    
4. **SSH-Konfiguration prüfen**  
    Wenn du einen Host-Alias in deiner `~/.ssh/config` (z. B. `github-custom`) eingerichtet hast, überprüfe, ob dieser korrekt definiert ist:
    
    ```ssh-config
    Host github-custom
        HostName github.com
        User git
        IdentityFile C:/Users/bfischer/.ssh/rsa-key-20250208_obsidian_git
    ```
    
    **Wichtig:** Wenn du einen Alias verwendest, muss auch die Remote-URL diesen Alias nutzen. Überprüfe die Remote-URL:
    
    ```bash
    git remote -v
    ```
    
    Falls noch `git@github.com` steht, ändere sie mit:
    
    ```bash
    git remote set-url origin git@github-custom:<deinBenutzername>/<deinRepo>.git
    ```
    
5. **Schlüsselkonvertierung überprüfen**  
    Falls du den Schlüssel ins PEM-Format konvertiert hast, vergewissere dich, dass die Konvertierung erfolgreich war und in der Konfiguration der korrekte Dateipfad eingetragen ist.
    

Nachdem du diese Punkte überprüft hast, sollte der SSH-Client den richtigen Schlüssel an GitHub übermitteln. Wenn der Debug-Output aus Schritt 2 bestätigt, dass dein gewünschter Schlüssel verwendet wird und der öffentliche Schlüssel in GitHub exakt übereinstimmt, sollte der Push funktionieren. Falls weiterhin Probleme auftreten, liefert der detaillierte Debug-Output in der Regel konkrete Hinweise darauf, wo das Problem liegt.


### Load key "C:/Users/bfischer/.ssh/rsa-key-20250208_obsidian_git": error in libcrypto

Der Fehler

Load key "C:/Users/bfischer/.ssh/rsa-key-20250208_obsidian_git": error in libcrypto

weist darauf hin, dass dein SSH-Client den Schlüssel nicht korrekt laden kann – in der Regel, weil das Format des Schlüssels von der eingesetzten libcrypto-Version nicht unterstützt wird. Oft tritt das auf, wenn der Schlüssel im neuen OpenSSH-Format gespeichert wurde, während einige Clients (oder Bibliotheken) das klassische PEM-Format erwarten.

Um das Problem zu beheben, solltest du den Schlüssel in das PEM-Format konvertieren. Das geht folgendermaßen:

1. **Schlüssel ins PEM-Format konvertieren**  
    Öffne Git Bash (oder deine bevorzugte Shell) und führe den folgenden Befehl aus:
    
    ```bash
    ssh-keygen -p -m PEM -f C:/Users/bfischer/.ssh/rsa-key-20250208_obsidian_git
    ```
    
    Dabei passiert Folgendes:
    
    - `-p` fordert eine Passphrase-Änderung an (wobei du einfach Enter drückst, falls der Schlüssel passwortlos ist).
    - `-m PEM` gibt an, dass der Schlüssel im PEM-Format gespeichert werden soll.
    - `-f` gibt den Pfad zu deinem Schlüssel an.
2. **Verbindung testen**  
    Nachdem der Schlüssel konvertiert wurde, teste die SSH-Verbindung:
    
    ```bash
    ssh -vT git@github-custom
    ```
    
    (Ersetze `github-custom` durch den in deiner `~/.ssh/config` definierten Alias, oder nutze `git@github.com` falls du keinen Alias eingerichtet hast.)  
    Im Debug-Output sollte nun ersichtlich sein, dass der Schlüssel erfolgreich geladen wird.
    
3. **Push erneut versuchen**  
    Falls der Test erfolgreich war, kannst du im Repository erneut versuchen, den Push auszuführen:
    
    ```bash
    git push -u origin main
    ```
    
    Sollte die Konfiguration korrekt sein, sollte der Push nun ohne Passwortabfrage funktionieren.
    

Falls der Fehler weiterhin auftritt, prüfe bitte:

- Ob der in der `.ssh/config` definierte Pfad exakt zum konvertierten Schlüssel führt.
- Ob eventuell andere SSH-Agenten oder Umgebungsvariablen (wie `GIT_SSH`) im Spiel sind, die einen anderen Schlüssel laden könnten.

Diese Schritte sollten das Formatproblem beheben und Git erlauben, den richtigen Schlüssel zu verwenden.

### Git bei einem Push nach einem Passwort fragt, obwohl dein Schlüssel keinen Passwortschutz hat

Wenn Git bei einem Push nach einem Passwort fragt, obwohl dein Schlüssel keinen Passwortschutz hat, liegt das meist daran, dass entweder der falsche Key genutzt wird oder die Konfiguration nicht (vollständig) greift. Hier einige Schritte zur Fehlersuche und -behebung:

1. **Prüfe den Remote-URL**  
    Stelle sicher, dass in deinem lokalen Repository wirklich der Alias verwendet wird.
    
    ```bash
    git remote -v
    ```
    
    Der Remote-URL sollte so aussehen:
    
    ```
    git@github-custom:Benutzername/RepoName.git
    ```
    
    Falls hier noch `github.com` steht, wird die SSH-Konfiguration nicht angewendet.
    
2. **Überprüfe die SSH-Konfiguration**  
    Öffne deine Datei `C:\Users\<DeinBenutzername>\.ssh\config` und stelle sicher, dass der Eintrag korrekt ist. Beispiel:
    
    ```ssh-config
    Host github-custom
        HostName github.com
        User git
        IdentityFile C:/Users/<DeinBenutzername>/.ssh/mygithubkey
    ```
    
    Achte darauf, dass der Pfad zum Key stimmt und dass du entweder Vorwärtsschrägstriche oder korrekt escapen Backslashes verwendest.
    
3. **Debugging mit SSH**  
    Führe in Git Bash den Debug-Befehl aus, um zu sehen, welchen Schlüssel SSH tatsächlich verwendet:
    
    ```bash
    ssh -vT git@github-custom
    ```
    
    Im Debug-Output solltest du nach einer Zeile suchen, die den verwendeten `IdentityFile` anzeigt. Falls ein anderer Schlüssel geladen wird oder gar keiner, ist entweder deine `config`-Datei nicht im erwarteten Pfad oder es gibt ein anderes Problem.
    
4. **SSH-Agent prüfen und Key hinzufügen**  
    Manchmal kann es helfen, den gewünschten Schlüssel explizit im SSH-Agenten zu registrieren. Starte den Agenten (falls noch nicht geschehen) und füge den Key hinzu:
    
    ```bash
    eval $(ssh-agent -s)
    ssh-add ~/.ssh/mygithubkey
    ```
    
    Auch wenn der Schlüssel passwortlos ist, stellt dies sicher, dass der SSH-Agent ihn im Speicher hat.
    
5. **Dateirechte und -format**  
    Vergewissere dich, dass der Schlüssel im richtigen Format vorliegt und die Zugriffsrechte stimmen. Unter Windows sind Zugriffsrechte meist weniger restriktiv als unter Unix, dennoch kann es nicht schaden, den Key im Editor zu überprüfen (insbesondere, wenn er z. B. mit CRLF statt LF gespeichert wurde).
    
6. **GIT_SSH Variable prüfen**  
    Falls die Umgebungsvariable `GIT_SSH` gesetzt ist, könnte Git einen anderen SSH-Client verwenden. Überprüfe die Umgebungsvariablen und entferne gegebenenfalls `GIT_SSH`, um sicherzustellen, dass Git den richtigen SSH-Client nutzt.
    

Wenn du all diese Punkte überprüft hast und im Debug-Output (Punkt 3) der richtige `IdentityFile` geladen wird, sollte Git beim Push nicht mehr nach einem Passwort fragen. Falls dennoch Probleme auftreten, kann der Debug-Output weitere Hinweise liefern, welcher Schritt nicht wie erwartet funktioniert.
