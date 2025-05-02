Im Folgenden findest du eine Anleitung, wie du dein bestehendes Skript so einrichtest, dass es beim Windows-Login im Hintergrund (ohne sichtbares Fenster) ausgeführt wird. Dabei erstellen wir eine geplante Aufgabe (Scheduled Task), die dein Skript startet.

### 1. Skript speichern

Speichere zunächst dein ursprüngliches Skript (das alle 15 Minuten einen Signalton sendet) an einem festen Ort, z. B.:

C:\Scripts\KopfhörerWakeup.ps1

Der Inhalt deines Skripts könnte folgendermaßen aussehen:

# KopfhörerWakeup.ps1
while ($true) {
    # Sende einen kurzen Signalton: [Console]::Beep(Frequenz in Hz, Dauer in ms)
    [console]::beep(800, 100)
    
    # Warte 15 Minuten (900 Sekunden) bis zum nächsten Signal
    Start-Sleep -Seconds 900
}

### 2. Geplante Aufgabe registrieren

Erstelle ein weiteres PowerShell-Skript (z. B. `RegisterKopfhörerWakeupTask.ps1`), das die geplante Aufgabe registriert. Kopiere den folgenden Code und passe den Pfad zum Skript bei Bedarf an:

# Pfad zum KopfhörerWakeup-Skript
$scriptPath = "C:\Scripts\KopfhörerWakeup.ps1"

# Name und Beschreibung der Aufgabe
$taskName = "KopfhörerWakeup"
$taskDescription = "Skript, das alle 15 Minuten einen Signalton an die Kopfhörer sendet, um sie aktiv zu halten."

# Erstelle die Aktion, die PowerShell mit verstecktem Fenster startet und das Skript ausführt.
$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-NoProfile -WindowStyle Hidden -ExecutionPolicy Bypass -File `"$scriptPath`""

# Erstelle den Trigger: Aufgabe wird beim Benutzer-Login gestartet.
$trigger = New-ScheduledTaskTrigger -AtLogon

# Registriere die geplante Aufgabe für den aktuellen Benutzer.
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName $taskName -Description $taskDescription -User "$env:USERNAME" -RunLevel LeastPrivilege

### 3. Registrierung ausführen

1. Starte eine PowerShell-Konsole (normalerweise sind für eine Registrierung von Aufgaben im Benutzerkontext keine administrativen Rechte erforderlich).
    
2. Führe das Skript `RegisterKopfhörerWakeupTask.ps1` aus:
    
    ```powershell
    .\RegisterKopfhörerWakeupTask.ps1
    ```
    

Dadurch wird eine geplante Aufgabe mit dem Namen „KopfhörerWakeup“ erstellt, die beim Login des aktuellen Benutzers ausgeführt wird und dein Skript im Hintergrund startet.

### 4. Testen

Melde dich ab und wieder an oder starte dein System neu, um zu überprüfen, ob die Aufgabe wie gewünscht ausgeführt wird. Da das Skript im Hintergrund läuft, wirst du kein sichtbares Fenster sehen – du solltest aber alle 15 Minuten einen kurzen Signalton an deinen Kopfhörern hören.

### Prüfen ob das skript im hintergrund läuft

Du kannst überprüfen, ob dein Skript im Hintergrund läuft, indem du eine oder mehrere der folgenden Methoden anwendest:

### 1. Über die Aufgabenplanung

Verwende den folgenden PowerShell-Befehl, um den Status der registrierten Aufgabe abzufragen. Dabei erhältst du Informationen wie den letzten Laufzeitpunkt und den nächsten geplanten Start:

Get-ScheduledTask -TaskName "HeadphonesWakeup" | Get-ScheduledTaskInfo

Schau dir hier insbesondere die Eigenschaften `LastRunTime` und `NextRunTime` an, um zu erkennen, ob die Aufgabe kürzlich ausgeführt wurde oder demnächst gestartet wird.

### 2. Über den Task-Manager

Öffne den Task-Manager (Strg+Shift+Esc) und wechsle zur Registerkarte „Details“. Füge über „Spalten auswählen“ (Rechtsklick auf die Kopfzeile) die Spalte „Befehlzeile“ hinzu. Suche nach einem Prozess namens `powershell.exe`, dessen Befehlzeile den Pfad zu deinem Skript enthält (z. B. `C:\Scripts\HeadphonesWakeup.ps1`). So kannst du feststellen, ob das Skript aktiv läuft.

### 3. Logging ins Skript einbauen

Du kannst dein Skript erweitern, sodass es bei jedem Signal eine Zeile in eine Logdatei schreibt. Dadurch kannst du später nachvollziehen, ob und wann das Skript aktiv war. Beispiel:

# KopfhörerWakeup.ps1
$logPath = "C:\Scripts\HeadphonesWakeup.log"
while ($true) {
    [console]::beep(800, 100)
    Add-Content -Path $logPath -Value "$(Get-Date): Signal gesendet"
    Start-Sleep -Seconds 900
}

Mit diesem Logfile kannst du überprüfen, ob das Skript in regelmäßigen Abständen läuft.

Diese Methoden helfen dir dabei, sicherzustellen, dass dein Skript im Hintergrund aktiv ist und regelmäßig ausgeführt wird.  


Mit dieser Einrichtung läuft dein Skript automatisch beim Start von Windows im Hintergrund weiter.


## Fehlerbehebungen

### Register-ScheduledTask: C:\SCRIPTS\HeadphonesWakeup\RegisterHeadphonesWakeupTask.ps1:15 Line | 15 | … ption $taskDescription -User "$env:USERNAME" -RunLevel LeastPrivilege | ~~~~~~~~~~~~~~ | Cannot process argument transformation on parameter 'RunLevel'. Cannot convert value "LeastPrivilege" to type | "Microsoft.PowerShell.Cmdletization.GeneratedTypes.ScheduledTask.RunLevelEnum". Error: "Unable to match the identifier name | LeastPrivilege to a valid enumerator name. Specify one of the following enumerator names and try again: Limited, Highest"

Der Fehler „Access is denied“ weist darauf hin, dass dein aktuelles PowerShell-Fenster nicht über die nötigen Berechtigungen verfügt, um die geplante Aufgabe zu registrieren. Um dies zu beheben, führe das Registrierungs-Skript in einer PowerShell-Sitzung mit administrativen Rechten aus.

### Vorgehensweise

1. **PowerShell als Administrator öffnen:**
    
    - Schließe alle offenen PowerShell-Fenster.
    - Klicke im Startmenü auf „PowerShell“, mache einen Rechtsklick auf „Windows PowerShell“ und wähle „Als Administrator ausführen“.
2. **Skript ausführen:**  
    Navigiere in der administrativen PowerShell zum Ordner, in dem dein `RegisterHeadphonesWakeupTask.ps1`-Skript liegt, und führe es aus:
    
    ```powershell
    .\RegisterHeadphonesWakeupTask.ps1
    ```
    

Dadurch erhält dein Skript die nötigen Berechtigungen, um die geplante Aufgabe zu erstellen, und der „Access is denied“-Fehler sollte nicht mehr auftreten.

Weitere Informationen zu Berechtigungen und der Verwendung von `Register-ScheduledTask` findest du in der [Microsoft-Dokumentation](https://chatgpt.com/c/%EE%88%80cite%EE%88%82microsoft-scheduledtasks%EE%88%81).


### - Register-ScheduledTask: C:\SCRIPTS\HeadphonesWakeup\RegisterHeadphonesWakeupTask.ps1:15 Line | 15 | Register-ScheduledTask -Action $action -Trigger $trigger -TaskName $t … | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ | Access is denied.


Der Fehler „Access is denied“ weist darauf hin, dass dein aktuelles PowerShell-Fenster nicht über die nötigen Berechtigungen verfügt, um die geplante Aufgabe zu registrieren. Um dies zu beheben, führe das Registrierungs-Skript in einer PowerShell-Sitzung mit administrativen Rechten aus.

### Vorgehensweise

1. **PowerShell als Administrator öffnen:**
    
    - Schließe alle offenen PowerShell-Fenster.
    - Klicke im Startmenü auf „PowerShell“, mache einen Rechtsklick auf „Windows PowerShell“ und wähle „Als Administrator ausführen“.
2. **Skript ausführen:**  
    Navigiere in der administrativen PowerShell zum Ordner, in dem dein `RegisterHeadphonesWakeupTask.ps1`-Skript liegt, und führe es aus:
    
    ```powershell
    .\RegisterHeadphonesWakeupTask.ps1
    ```
    

Dadurch erhält dein Skript die nötigen Berechtigungen, um die geplante Aufgabe zu erstellen, und der „Access is denied“-Fehler sollte nicht mehr auftreten.

Weitere Informationen zu Berechtigungen und der Verwendung von `Register-ScheduledTask` findest du in der [Microsoft-Dokumentation](https://chatgpt.com/c/%EE%88%80cite%EE%88%82microsoft-scheduledtasks%EE%88%81).

