**Platzierung von PS3-Spielen**

Um PS3-Spiele in Batocera laufen zu lassen, müssen sie in einem bestimmten Ordner platziert werden, damit der Emulator sie erkennt. Legen Sie die Spiele in einen „ROMs/PS3“-Ordner, wobei die Spiele im ISO-Format oder als extrahierte Verzeichnisse vorliegen müssen. Da Batocera den RPCS3-Emulator für PS3-Spiele nutzt, sollten die Spiele möglicherweise zuerst im Emulator installiert werden, bevor sie in Batocera funktionieren. Achten Sie darauf, dass die Ordnerstruktur und Dateinamen stimmen.

**Zu PS3-Spielen auf Batocera**

Um PS3-Spiele auf Batocera zu verwenden, sollten Nutzer einen Ordner unter /userdata/roms/ps3/ erstellen. Darin kommen die Spiele als .iso, .pkg oder als Ordner mit PS3_GAME und der entsprechenden Disc-Datei. Um die Spiele zu starten, muss der RPCS3-Emulator genutzt werden, der nur auf x86_64-Bauten funktioniert. Benutzer müssen die PS3 unter "Experimentelle Funktionen" in EmulationStation aktivieren, Spiele in das Verzeichnis kopieren und dann scannen. Batocera kann .iso und vielleicht .bin akzeptieren, aber ich werde die genauen Infos noch suchen.

**PS3-Spiele auf Batocera platzieren**

Um PS3-Spiele korrekt in Batocera hinzuzufügen, legen Sie Disc-basierte Spiele in den Ordner /userdata/roms/ps3/ mit der Endung .ps3 ab. Das Spiel sollte die Ordner PS3_GAME, PS3_UPDATE und PS3_DISC.SFB enthalten. Der PS3_GAME-Ordner benötigt Unterordner wie LICDIR, TROPDIR, und USRDIR. PSN-Spiele erfordern die Installation über den RPCS3-Emulator und das Erstellen einer .psn-Datei im gleichen Ordner. Alternativ kann man auch .squashfs verwenden und die x86_64-Bauversion sicherstellen.

**PS3-Spiele auf Batocera verwenden**

Um PS3-Spiele unter Batocera funktionstüchtig zu machen, müssen Spiele in das Verzeichnis /userdata/roms/ps3 kopiert werden. Jeder Disc-basierte Titel sollte als Ordner mit der Endung .ps3 abgelegt werden. Der Ordner benötigt eine Struktur mit einem PS3_GAME-Ordner und Unterverzeichnissen wie USRDIR, LICDIR und TROPDIR sowie der Datei PS3_DISC.SFB. PSN-Spiele können über die RPCS3-Konfiguration im F1-Menü installiert werden, mit einer .psn-Datei im roms/ps3-Ordner. Optional können ab Batocera v33 Spiele als SquashFS komprimiert werden.

**Zitieren und Dokumentieren der Anleitung**

Für die PS3-Emulation müssen x86_64-Systeme verwendet werden, nicht Raspberry Pi oder andere SBCs. Die PS3-Firmware (PS3UPDAT.PUP) muss im BIOS-Verzeichnis vorhanden sein.

Disc-basierte Spiele sollten im Verzeichnis /userdata/roms/ps3 abgelegt werden, wobei der Ordner mit .ps3 endet und ein PS3_GAME-Ordner enthalten muss.

PSN-Spiele werden mit rpcs3-config installiert. Eine Textdatei mit der Game-ID im Format ‘Spielname.psn’ wird im roms/ps3 erstellt.

Ab Batocera v33 können Ordner mittels SquashFS verlustfrei komprimiert werden.

Häufige Fehler beinhalten ungeeignete Ordnerstrukturen und fehlende EBOOT.BIN-Dateien.

Nachgedacht für 2m 11s

## Zusammenfassung

Um PlayStation 3-Spiele in Batocera lauffähig zu machen, legen Sie Disc-basierte Spiele als Ordner mit der Endung `.ps3` im Verzeichnis `/userdata/roms/ps3/` ab, wobei jeder Ordner eine Unterstruktur mit mindestens einem `PS3_GAME`-Verzeichnis und der Datei `PS3_DISC.SFB` enthalten muss citeturn3view0. Für digitale PSN-Spiele installieren Sie zuerst die PKG-Datei über das RPCS3-Konfigurationsmenü (F1 → Applications → rpcs3-config → Install Packages), erstellen dann in `roms/ps3/` eine Textdatei mit der Endung `.psn`, deren einziger Inhalt die Game-ID (z. B. `NPUB30162`) ist citeturn3view0. Ab Batocera v33 können Sie die `.ps3`-Ordner auch verlustfrei zu SquashFS-Images komprimieren und direkt abspielen citeturn3view0. Stellen Sie zudem sicher, dass Sie eine x86_64-Version von Batocera verwenden und die PS3-Firmware (`PS3UPDAT.PUP`) im BIOS-Verzeichnis abgelegt haben citeturn3view0.

---

## Voraussetzungen

1. **x86_64-System**  
    PS3-Emulation via RPCS3 ist nur auf PC-basierten Batocera-Installationen mit x86_64-Architektur möglich, nicht auf Raspberry Pi oder anderen Single-Board-Computern citeturn3view0.
    
2. **PS3-Firmware**  
    Laden Sie die offizielle Sony-Firmware `PS3UPDAT.PUP` herunter und speichern Sie sie unter `/userdata/bios/PS3UPDAT.PUP` auf Ihrem Batocera-System citeturn3view0.
    

---

## Disc-basierte Spiele ablegen

1. **Verzeichnis erstellen**  
    Erstellen Sie im Dateimanager oder per Netzwerkzugriff (z. B. `\\batocera\share\roms\ps3`) einen Ordner mit dem genauen Spielnamen und der Endung `.ps3`, z. B. `God of War.ps3` citeturn7view0.
    
2. **Struktur des `.ps3`-Ordners**
    
    ```text
    /userdata/roms/ps3/
    └─ Spielname.ps3/
       ├─ PS3_GAME/
       │  ├─ LICDIR/
       │  ├─ TROPDIR/
       │  ├─ USRDIR/
       │  ├─ ICON0.PNG
       │  ├─ PARAM.SFO
       │  ├─ PIC0.PNG
       │  └─ PS3LOGO.DAT
       ├─ PS3_UPDATE/  (optional, wenn Firmware-Update im Spiel enthalten)
       └─ PS3_DISC.SFB
    ```
    
    Ohne die `.ps3`-Endung oder die Datei `PS3_DISC.SFB` erkennt der RPCS3-Emulator das Spiel nicht citeturn8view0.
    
3. **Front-End-Unterstützung**  
    Batocera zeigt nur Retail-Disc-Rips im EmulationStation-Frontend an – PSN-Titel erscheinen dort nicht automatisch citeturn7view0.
    

---

## PSN-Spiele ablegen

1. **Installation im RPCS3-Menü**
    
    - Drücken Sie [F1], wählen Sie **Applications → rpcs3-config**.
        
    - Unter **File → Install Packages, Raps, Edats** installieren Sie nacheinander die PSN-PKG-Datei(en). citeturn3view0
        
2. **Erstellen der `.psn`-Datei**
    
    - Wechseln Sie per [Alt]+[Tab] zurück zum Dateimanager.
        
    - Legen Sie in `roms/ps3/` eine Textdatei an, z. B. `Scott Pilgrim vs. the World.psn`.
        
    - Öffnen Sie die Datei und fügen Sie nur die Game-ID (z. B. `NPUB30162`) ein, speichern und schließen citeturn3view0.
        
3. **Speicherort der Spieldaten**  
    Die eigentlichen Spieldaten liegen danach unter  
    `/userdata/system/configs/rpcs3/dev_hdd0/game/<GAMEID>/` citeturn3view0.
    

---

## SquashFS-Kompression (optional)

Ab Batocera v33 können Sie Ihre `.ps3`-Ordner mittels `mksquashfs` verlustfrei komprimieren, um Platz zu sparen und Ladezeiten zu optimieren citeturn3view0. Beispiel:

cd /userdata/roms/ps3
mksquashfs "Little Big Planet.ps3" "Little Big Planet.squashfs"

RPCS3 liest das `.squashfs`-Image so, als wäre es ein normaler Ordner citeturn3view0.

---

## Häufige Fehler & Lösungen

- **Unmittelbares Zurückspringen in ES**  
    Prüfen Sie, ob der Spielordner wirklich die Endung `.ps3` besitzt und nicht nur `Spielname` heißt citeturn7view0.
    
- **Fehlende `EBOOT.BIN`**  
    Achten Sie darauf, dass die Datei `EBOOT.BIN` im Unterordner `PS3_GAME/USRDIR/` vorhanden ist. Fehlt sie, kopieren Sie sie aus einem funktionierenden Spielordner citeturn5view0.
    
- **Compiler-Cache erneuern**  
    Starten Sie das Spiel einmal über F1 → RPCS3 und löschen Sie anschließend den Cache über Rechtsklick auf den Titel → **Remove PPU Cache**, damit RPCS3 alles neu erstellt citeturn0search13.
    

Mit dieser Ordnerstruktur und den korrekten Schritten sollten Ihre PS3-Spiele reibungslos unter Batocera starten.

# SQUASHFS WSL

**Beantwortung der Frage zu mksquashfs**

Die Frage ist, ob der Befehl mksquashfs unter Windows verfügbar ist. Normalerweise gehört dieser Befehl zu den SquashFS-Tools und ist vor allem auf Linux verfügbar. Auf Windows kann er über das Windows Subsystem für Linux (WSL) nach Installation der SquashFS-Tools genutzt werden. Alternativ können Projekte wie Cygwin oder vorkompilierte Windows-Binärdateien verwendet werden. Es gibt jedoch keine offizielle Windows-Binary von SquashFS, jedoch Ports wie bei https.

**Antwort zu mksquashfs unter Windows**

Der Befehl mksquashfs ist nicht nativ unter Windows verfügbar, aber es gibt mehrere Möglichkeiten, ihn zu nutzen:

1. Cygwin: aus dem Quellcode erstellen.
    
2. WSL: Ubuntu installieren, dann mit `apt install squashfs-tools` das Tool installieren.
    
3. Vorgefertigte Binärdateien: von GitHub (z. B. `sqfs43-win32.zip`).
    
4. squashfs-tools-ng Windows-Version: vorgefertigt auf SourceForge oder anderen Quellen.
    
5. 7-Zip unterstützt das Lesen von SquashFS, aber nicht das Erstellen.
    

Diese Optionen ermöglichen die Nutzung von mksquashfs auf Windows.

Nachgedacht für 31 Sekunden

Hier die Möglichkeiten, wie Sie unter Windows den Befehl **mksquashfs** nutzen können:

## 1. Windows Subsystem for Linux (WSL)

Unter WSL installieren Sie einfach eine Linux-Distribution (z. B. Ubuntu) und ziehen mksquashfs dort ganz normal nach:

# in der WSL-Shell (Ubuntu etc.)
sudo apt update
sudo apt install squashfs-tools

Danach stehen Ihnen `mksquashfs` und `unsquashfs` direkt in der WSL-Umgebung zur Verfügung citeturn1search0.

## 2. Cygwin-Umgebung

Mit Cygwin können Sie die Linux-Tools unter Windows kompilieren und ausführen:

1. Cygwin installieren.
    
2. Die Pakete `gcc-core`, `xz`, `liblzma-devel` (ggf. `liblzma5`) nachinstallieren.
    
3. Den Quellcode von [squashfs-tools](https://github.com/plougher/squashfs-tools) laden.
    
4. In der `Makefile` die Unterstützung für XZ/LZMA aktivieren (`XZ_SUPPORT=1`, `LZMA_XZ_SUPPORT=1`).
    
5. Kompilieren mit
    
    ```bash
    make EXTRA_CFLAGS="-Dlinux -DFNM_EXTMATCH='(1<<5)' \
        -D'sigtimedwait(a,b,c)=sigwaitinfo(a,b)'"
    make install
    ```
    
    Anschließend finden Sie `mksquashfs.exe` im Cygwin-Pfad citeturn0search0.
    

## 3. Vorgefertigte Windows-Binärpakete

- **pmq20’s ZIP**: Laden Sie von GitHub das Archiv `sqfs43-win32.zip` herunter und entpacken Sie es nach `C:\Windows` (oder in einen anderen Pfad, der in Ihrer `PATH`-Variablen liegt). Dann steht `mksquashfs.exe` systemweit zur Verfügung citeturn0search1.
    
- **squashfs-tools-ng**: Das „ng“-Projekt bietet experimentelle MinGW-Builds an. Beispiel-Download:
    
    ```
    https://infraroot.at/pub/squashfs/windows/squashfs-tools-ng-0.8-mingw32.zip
    ```
    
    Nach Entpacken haben Sie eine native Windows-Version von `mksquashfs.exe` und Co. citeturn0search7.
    

## 4. Weitere Hinweise

- **7-Zip** kann SquashFS-Images lesen, aber nicht erzeugen citeturn1search0.
    
- Wenn Sie nur gelegentlich unter Windows arbeiten, ist WSL (Option 1) meist am einfachsten und am besten gepflegt.
    

Mit diesen Methoden können Sie unter Windows genauso wie unter Linux SquashFS-Images erstellen.

# CREATE PS3 GAME

**Beantworten der Benutzerfrage zu .ISO Dateien**

Für PS3-Spiele im .ISO-Format müssen diese normalerweise extrahiert oder in das benötigte Format umgewandelt werden. Wenn du den Emulator RPCS3 verwendest, unterstützt dieser .ISO-Dateien. In Batocera nutzt der Emulator jedoch die Ordnerstruktur mit der Endung .ps3 und kann keine .ISO-Dateien im Ordner /roms/ps3 lesen. Du musst die .ISO-Datei entweder extrahieren oder in den richtigen Ordner umwandeln.

**Antwort zu .ISO-Dateien in Batocera**

Wenn du eine .ISO-Datei hast, musst du den Inhalt in die richtige Ordnerstruktur extrahieren. Batocera erwartet, dass du die Datei in einen Ordner mit der Endung .ps3 umwandelst. Du kannst die .ISO-Datei entweder entpacken oder die gesamte Datei aus der ISO mounten und dort die Inhalte platzieren. Eine einfache und schnelle Antwort ist: Ja, du musst die ISO-Datei extrahieren, nicht einfach kopieren.

**Antwort zur Behandlung von .ISO-Dateien**

Die Antwort auf die Frage, ob du die .ISO-Datei entpacken oder einfach kopieren musst, ist klar: Du musst die Datei zunächst mounten oder extrahieren und dann die Inhalte in einen Ordner mit dem Namen "Title.ps3" verschieben. Du kannst die .ISO-Datei nicht einfach kopieren, da die Struktur des Ordners erforderlich ist. Es geht darum, den Inhalt korrekt zu extrahieren und in die richtige Ordnerstruktur zu bringen, nicht nur eine Datei zu kopieren.

Nachgedacht für 1m 22s

## Kurzfassung

PS3-Disc-ISOs können nicht direkt in Batocera gestartet werden. Sie müssen sie auf Ihrem PC oder Mac **bereitstellen** (mounten) bzw. **entpacken**, den Inhalt in einen Ordner mit der Endung `.ps3` verschieben und diesen Ordner dann in Ihr Batocera-`roms/ps3`-Verzeichnis kopieren . Eine reine Kopie der `.iso`-Datei funktioniert nicht, da RPCS3 nur Spiele in der Ordnerstruktur mit `PS3_GAME` und `PS3_DISC.SFB` erkennt .

---

## Warum reines Kopieren der .iso nicht reicht

Batocera nutzt für PS3-Emulation den RPCS3-Emulator, der **Disc-Rips** erwartet, keine reinen Image-Dateien.

- RPCS3 sucht beim Start nach der Unterordnerstruktur `PS3_GAME/` und der Datei `PS3_DISC.SFB` im Stamm des `.ps3`-Ordners.
    
- Eine `.iso` allein enthält zwar alle Daten, liefert sie aber gebündelt statt als entpackte Verzeichnisstruktur .
    

---

## ISO entpacken und Ordner anlegen

1. **ISO mounten/entpacken**
    
    - Unter Windows: Rechtsklick → **Bereitstellen**, oder mit einem Tool wie WinCDEmu.
        
    - Unter macOS: Doppelklick im Finder.
        
    - Alternativ Archiv-Tools wie 7-Zip nutzen, um direkt den Inhalt zu extrahieren.
        
2. **Ordner umbenennen**
    
    - Kopieren Sie den gemounteten bzw. entpackten Ordner, der typischerweise `PS3_GAME`, `PS3_UPDATE` etc. enthält.
        
    - Benennen Sie diesen Ordner um, indem Sie `.ps3` an den Namen anhängen: z. B.
        
        ```text
        God of War.ps3
        ```
        
3. **In Batocera übertragen**
    
    - Kopieren Sie den Ordner `God of War.ps3` in Ihr Batocera-Share-Volume:
        
        ```
        \\BATOCERA\share\roms\ps3\
        ```
        
    - Starten Sie Batocera neu oder aktualisieren Sie die Spiel-Liste in EmulationStation. Das Spiel sollte nun erscheinen und über RPCS3 startbar sein .
        

---

## Zusätzlicher Hinweis: Verschlüsselung/Decrypt

Manche selbst erstellten ISOs sind noch verschlüsselt und benötigen ein Entschlüsselungstool (z. B. **PSN Liberator** oder entsprechende Scripts auf GitHub), damit RPCS3 sie korrekt einlesen kann. Tritt beim Start ein Fehler wie `Invalid or unsupported file format` auf, prüfen Sie, ob die EBOOT.BIN noch verschlüsselt ist und entschlüsseln Sie die Dateien entsprechend .

---

**Fazit:**  
Sie dürfen die `.iso` nicht direkt ins `ps3`-Verzeichnis kopieren. Mounten oder entpacken Sie die ISO, benennen Sie den entstehenden Ordner mit `.ps3` am Ende und kopieren Sie diesen in `roms/ps3`. Dann erkennt Batocera Ihr PS3-Spiel korrekt.
