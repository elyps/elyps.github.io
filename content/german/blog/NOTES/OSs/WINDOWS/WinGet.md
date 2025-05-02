## **WinGet installieren und nutzen**

WinGet ist der Windows Package Manager von Microsoft. Er ermöglicht das Installieren, Aktualisieren, Entfernen und Verwalten von Software über die Befehlszeile.

---

### **1. WinGet installieren**

WinGet ist in **Windows 10 (ab Version 1809) und Windows 11** vorinstalliert. Falls es fehlt oder aktualisiert werden muss:

#### **Methode 1: Über Microsoft Store**

1. Öffne den **Microsoft Store**.
2. Suche nach **App-Installer**.
3. Falls verfügbar, klicke auf **Aktualisieren** oder **Installieren**.

#### **Methode 2: Manuelle Installation über GitHub**

1. Lade die neueste `.msixbundle` von [GitHub Releases](https://github.com/microsoft/winget-cli/releases) herunter.
2. Installiere die Datei durch Doppelklick.

#### **Installation überprüfen**

winget --version

Falls der Befehl nicht gefunden wird, melde dich ab und wieder an oder starte den PC neu.

---

### **2. Wichtige Befehle von WinGet**

#### **Software suchen**

winget search <programmname>

Beispiel:

winget search vscode

Zeigt eine Liste mit verfügbaren Paketen, Namen und IDs.

#### **Programm installieren**

winget install <id/programmname>

Beispiel:

winget install Microsoft.VisualStudioCode

Falls ein Fehler auftritt, versuche:

winget install --id Microsoft.VisualStudioCode --source winget

#### **Alle installierten Programme auflisten**

winget list

Gibt eine Liste aller installierten Programme mit deren Versionen aus.

#### **Programm aktualisieren**

winget upgrade <programmname>

Beispiel:

winget upgrade Google.Chrome

#### **Alle Programme aktualisieren**

winget upgrade --all

Aktualisiert alle installierten Programme.

#### **Programm deinstallieren**

winget uninstall <programmname>

Beispiel:

winget uninstall Microsoft.VisualStudioCode

#### **WinGet selbst aktualisieren**

winget upgrade winget

#### **Programmquellen verwalten**

Verfügbare Quellen anzeigen:

winget source list

Neue Quelle hinzufügen:

winget source add --name <Name> <URL>

Beispiel:

winget source add --name MyRepo https://myrepo.com

#### **Programminformationen anzeigen**

winget show <programmname>

Beispiel:

winget show Microsoft.VisualStudioCode

---

### **3. WinGet im Skript nutzen**

WinGet kann in **PowerShell-Skripten** verwendet werden, um Software automatisiert zu verwalten.

Beispiel: **Automatisierte Installation mehrerer Programme**

$apps = @(
    "Microsoft.VisualStudioCode",
    "Google.Chrome",
    "Mozilla.Firefox",
    "7zip.7zip"
)

foreach ($app in $apps) {
    winget install --id $app --source winget --silent --accept-package-agreements --accept-source-agreements
}

Dieses Skript installiert mehrere Programme **ohne Nutzerinteraktion**.

---

### **4. Probleme lösen**

#### **Fehlermeldung: "Fehlender App-Installer"**

- Installiere **App-Installer** über den Microsoft Store oder [GitHub](https://github.com/microsoft/winget-cli/releases).

#### **WinGet-Befehl wird nicht erkannt**

- Stelle sicher, dass `winget.exe` unter `C:\Windows\System32` liegt.
- Falls nicht, melde dich ab oder starte Windows neu.

#### **WinGet zeigt keine Pakete oder kann keine Programme installieren**

- Aktualisiere die Quellen mit:
    
    ```powershell
    winget source update
    ```
    
- Prüfe die Internetverbindung.

---

### **Fazit**

WinGet ist ein leistungsstarkes Tool zur Softwareverwaltung in Windows. Mit wenigen Befehlen lassen sich Programme installieren, aktualisieren und entfernen.
