Hier ist eine **detaillierte Schritt-für-Schritt-Anleitung** basierend auf dem Video. Ich werde alle **Befehle** mit einfügen, damit du die Installation von **AetherSX2 auf RetroPie** für den **Raspberry Pi 4 oder 5** selbst durchführen kannst.

---

## **AetherSX2 auf RetroPie installieren (Raspberry Pi 4/5)**

Mit dieser Anleitung kannst du **PlayStation 2-Spiele** auf deinem **Raspberry Pi 4 oder 5** mit **RetroPie** emulieren. Es gibt wenig offizielle Dokumentation dazu, aber dieser Prozess funktioniert.

---

## **1️⃣ Voraussetzungen**

### **Hardware:**

- **Raspberry Pi 4 oder 5**
- **MicroSD-Karte (empfohlen: 32 GB oder größer)**
- **Netzteil für den Raspberry Pi**
- **Kühllösung (z. B. Kühlkörper oder Lüfter), da PS2-Emulation hohe Leistung erfordert**

### **Software:**

- **RetroPie 64-Bit (muss manuell installiert werden!)**
- **SSH-Zugriff auf den Raspberry Pi**
- **Ein PS2-BIOS (muss aus einer echten PlayStation 2 extrahiert oder anderweitig besorgt werden)**
- **PS2-ROMs (Spiele), die als **ISO- oder BIN-Dateien** vorliegen**

---

## **2️⃣ RetroPie 64-Bit auf dem Raspberry Pi installieren**

Da es **kein offizielles RetroPie-64-Bit-Image** gibt, muss es manuell installiert werden.

### **Schritt 1: Raspberry Pi OS 64-Bit installieren**

Lade das **Raspberry Pi Imager**-Tool herunter und installiere ein **64-Bit OS**:  
[🔗 Raspberry Pi Imager](https://www.raspberrypi.com/software/)

1. Wähle **Raspberry Pi OS (64-bit)** aus.
2. Installiere es auf einer **MicroSD-Karte**.
3. Nach der Installation: **Starte den Raspberry Pi** und richte das System ein.

### **Schritt 2: RetroPie installieren**

Führe die folgenden Befehle aus:

sudo apt update && sudo apt upgrade -y

Dann lade und installiere **RetroPie**:

cd
git clone --depth=1 https://github.com/RetroPie/RetroPie-Setup.git
cd RetroPie-Setup
chmod +x retropie_setup.sh
sudo ./retropie_setup.sh

Folge den Anweisungen, um **RetroPie zu installieren**.

---

## **3️⃣ Raspberry Pi vorbereiten**

Bevor du AetherSX2 installierst, musst du einige **Kernel-Änderungen vornehmen**.

### **Schritt 1: Boot-Konfiguration anpassen**

Öffne die Datei `config.txt`:

sudo nano /boot/config.txt

Scrolle nach unten und füge **folgende Zeilen** am Ende hinzu:

arm_64bit=1
gpu_mem=128

Speichern mit `CTRL + X`, `Y` und `ENTER`.

---

## **4️⃣ Vulkan-Treiber installieren**

AetherSX2 benötigt **Vulkan**, um PS2-Spiele mit guter Performance auszuführen.

Installiere Vulkan mit:

sudo apt install -y mesa-vulkan-drivers

Wenn gefragt wird, drücke `Y`, um die Installation zu bestätigen.

---

## **5️⃣ RetroPie-Extras installieren**

Da AetherSX2 nicht offiziell in RetroPie enthalten ist, musst du die **RetroPie-Extras** installieren.

Führe die folgenden Befehle aus:

cd
git clone https://github.com/Exarkuniv/RetroPie-Extra.git
cd RetroPie-Extra
cp -R scriptmodules/* ~/RetroPie-Setup/scriptmodules/

Starte dann das **RetroPie-Setup-Skript**:

cd ~/RetroPie-Setup
sudo ./retropie_setup.sh

Wähle:

- **"Manage Packages"**
- **"exp" (Experimental Packages)**
- Scrolle nach unten zu **"aethersx2.sh"** und **drücke die Leertaste**, um es auszuwählen.
- **Drücke ENTER** und bestätige mit **"Install from Source"**.

Die Installation dauert **einige Minuten**.

---

## **6️⃣ Samba für Dateiübertragung einrichten**

Damit du später **BIOS und Spiele einfach übertragen kannst**, aktiviere **Samba**:

1. Gehe im **RetroPie-Setup-Menü** zu:
    - **"Configuration / Tools"**
    - **Scrolle zu "samba" und wähle "Install RetroPie samba Shares"**
    - Warte, bis die Installation abgeschlossen ist.

Jetzt kannst du Dateien über das Netzwerk auf deinen Pi übertragen.

---

## **7️⃣ Raspberry Pi neustarten**

Führe einen Neustart durch:

sudo reboot

Falls der Raspberry Pi nicht von selbst neustartet, nutze:

sudo shutdown -r now

---

## **8️⃣ BIOS und Spiele hinzufügen**

### **Schritt 1: PS2-BIOS auf den Raspberry Pi kopieren**

Das **BIOS** ist erforderlich, um PS2-Spiele zu starten.

Verwende einen **FTP-Client** wie **FileZilla** oder nutze das Netzwerk:

1. Öffne den Datei-Explorer auf deinem PC.
2. Gib `\\<RaspberryPi-IP>` in die Adresszeile ein (ersetze `<RaspberryPi-IP>` mit der echten IP).
3. Navigiere zu **"/home/pi/RetroPie/BIOS"** und kopiere dort dein PS2-BIOS hinein.

Das BIOS sollte eine Datei wie **scph1001.bin** oder **scph70012.bin** sein.

### **Schritt 2: PS2-ROMs auf den Raspberry Pi kopieren**

Speichere deine **PS2-ISO-Dateien** unter:

/home/pi/RetroPie/roms/ps2/

Nutze wieder **FTP oder das Netzwerk**, um deine **Spiele als .ISO-Dateien** zu übertragen.

---

## **9️⃣ RetroPie neustarten und Emulator auswählen**

Starte **RetroPie neu**, indem du im **Hauptmenü auf "Quit" → "Restart EmulationStation"** gehst.

Nun sollte **PS2** als **System in der RetroPie-Spieleliste** erscheinen.

Beim ersten Start eines Spiels fragt RetroPie, **welcher Emulator verwendet werden soll**.  
Wähle **AetherSX2** als Standard-Emulator.

---

## **🔟 Performance-Tipps**

- **Raspberry Pi 5**: Die meisten PS2-Spiele laufen mit **45–55 FPS** bei voller Auflösung.
- **Raspberry Pi 4**: Einige Spiele laufen **flüssig mit niedrigeren Einstellungen**.

Falls Spiele **ruckeln**, kannst du in den AetherSX2-Einstellungen:

- **"Rendering Resolution" auf 1x oder 2x** setzen.
- **Frameskip aktivieren**.

---

## **🎉 Fazit**

Diese Anleitung zeigt, wie du **AetherSX2 auf RetroPie für den Raspberry Pi 4 oder 5** installierst. Nach der Installation kannst du **PS2-Spiele direkt auf deinem Raspberry Pi genießen**.

Falls du Fragen hast, lass es mich wissen! 🎮🚀


## ps2-0230a-20080220.bin & ROMS download

[![Favicon](https://www.google.com/s2/favicons?domain=https://archive.org\&sz=32)](https://archive.org/download/mame-0.221-roms-merged/ps2.zip)

### ROMS

[file](https://archive.org/download/mame-0.221-roms-merged/ps2.zip)

[archive.org](https://archive.org/download/mame-0.221-roms-merged/ps2.zip)

The file `ps2-0230a-20080220.bin` is a BIOS image for the Sony PlayStation 2 console, specifically corresponding to the SCPH-90001 and SCPH-90010 models (Version 5.0, dated 02/20/08 for the Americas region).

[wiki.recalbox.com](https://wiki.recalbox.com/en/emulators/consoles/playstation-2/pcsx2?utm_source=chatgpt.com)

For emulation purposes, such as using the PCSX2 emulator, this BIOS file is required to replicate the original console's functionality. You can obtain this BIOS file from the following sources:

1. **Internet Archive**: The PS2 BIOS Megadump available here includes a comprehensive collection of BIOS images, including `ps2-0230a-20080220.bin`.

   [archive.org](https://archive.org/details/ps2-bios-megadump?utm_source=chatgpt.com)

2. **GitHub Repository**: The RetroPieBIOS repository maintains a collection of BIOS files for various systems, including the PlayStation 2. You can find the `ps2-0230a-20080220.bin` file in the `pcsx2/bios/` directory.

   [github.com](https://github.com/archtaurus/RetroPieBIOS?utm_source=chatgpt.com)

3. **Myrient Archive**: This archive provides BIOS images for the PlayStation 2, with `ps2-0230a-20080220.zip` available for download.

   [myrient.erista.me](https://myrient.erista.me/files/Redump/Sony%20-%20PlayStation%202%20-%20BIOS%20Images/?utm_source=chatgpt.com)

After downloading the BIOS file, place it in the appropriate directory of your emulator (e.g., the `/bios/` folder for PCSX2) to ensure proper functionality.

[cfwaifu.com](https://www.cfwaifu.com/pcsx2/?utm_source=chatgpt.com)

Please note that while these sources provide access to BIOS files, downloading and using them may be subject to legal restrictions depending on your jurisdiction. It's recommended to dump the BIOS from your own PlayStation 2 console to comply with local laws and regulations.

