Um die Registry vorzubereiten, zu ändern und im Fehlerfall wiederherzustellen, kannst du so vorgehen:

1. **System­wiederherstellungspunkt anlegen**
    
    - Drücke `Win + R`, gib `SystemPropertiesProtection` ein und bestätige mit Enter.
        
    - Wähle dein Systemlaufwerk (normalerweise C:) und klicke auf „Erstellen…“.
        
    - Vergib einen Namen wie „Vor OneCommander-Installation“ und klicke auf „Erstellen“.
        
2. **Registry­-Backup der relevanten Schlüssel**  
    Du brauchst drei Schlüssel, die den Standard-Öffnen-Befehl für Ordner definieren:
    
    - `HKEY_CLASSES_ROOT\Directory\shell\open\command`
        
    - `HKEY_CLASSES_ROOT\Folder\shell\open\command`
        
    - `HKEY_CLASSES_ROOT\LibraryFolder\shell\open\command`
        
    
    **Per GUI (Regedit):**
    
    1. Starte `regedit` (Win + R → `regedit`).
        
    2. Navigiere zum Schlüssel, wähle ihn aus → Datei → Exportieren → z.B. `Backup_Directory_Open.reg`.
        
    3. Dasselbe für die anderen beiden Schlüssel.
        
    
    **Per Eingabeaufforderung:**
    
    ```bat
    reg export "HKCR\Directory\shell\open\command" "%USERPROFILE%\Desktop\Backup_Directory_Open.reg"
    reg export "HKCR\Folder\shell\open\command"    "%USERPROFILE%\Desktop\Backup_Folder_Open.reg"
    reg export "HKCR\LibraryFolder\shell\open\command" "%USERPROFILE%\Desktop\Backup_Library_Open.reg"
    ```
    
3. **Registry ändern, um OneCommander als Standard-Explorer zu setzen**  
    Erzeuge eine Textdatei `Set_OneCommander_Default.reg` mit folgendem Inhalt (Pfad ggf. anpassen):
    
    ```reg
    Windows Registry Editor Version 5.00
    
    [HKEY_CLASSES_ROOT\Directory\shell\open\command]
    @="\"C:\\Program Files\\OneCommander\\OneCommander.exe\" \"%1\""
    
    [HKEY_CLASSES_ROOT\Folder\shell\open\command]
    @="\"C:\\Program Files\\OneCommander\\OneCommander.exe\" \"%1\""
    
    [HKEY_CLASSES_ROOT\LibraryFolder\shell\open\command]
    @="\"C:\\Program Files\\OneCommander\\OneCommander.exe\" \"%1\""
    ```
    
    Dann Doppelklick auf die `.reg`, um die Änderung zu übernehmen.
    
4. **Testen**
    
    - Öffne einen Ordner z.B. per Doppelklick im Desktop oder Startmenü.
        
    - OneCommander sollte statt des normalen Explorers starten.
        
5. **Fehlerkorrektur / Wiederherstellung**
    
    - **Aus .reg-Backup zurückspielen:**  
        Doppelklicke auf deine `Backup_*.reg`-Dateien, um die Originalbefehle wiederherzustellen.
        
    - **Per Kommandozeile:**
        
        ```bat
        reg import "%USERPROFILE%\Desktop\Backup_Directory_Open.reg"
        reg import "%USERPROFILE%\Desktop\Backup_Folder_Open.reg"
        reg import "%USERPROFILE%\Desktop\Backup_Library_Open.reg"
        ```
        
    - **Systemwiederherstellung zurücksetzen:**  
        Win + R → `rstrui` → „Weiter“ → den zuvor erstellten Wiederherstellungspunkt auswählen → „Weiter“ und „Fertig stellen“.
        

Damit hast du sowohl provisorische Backups als auch eine Systemwiederherstellungs-Option, um im Fehlerfall schnell wieder den Original-Explorer herzustellen.
