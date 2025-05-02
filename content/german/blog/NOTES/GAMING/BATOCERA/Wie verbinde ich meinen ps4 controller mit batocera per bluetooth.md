Um deinen PS4-Controller per Bluetooth mit **Batocera** zu verbinden, folge diesen Schritten:

### **1. Bluetooth am PS4-Controller aktivieren**

1. Halte **die PS-Taste und die Share-Taste** gleichzeitig gedrückt.
2. Die LED-Leiste beginnt schnell zu blinken – das bedeutet, der Controller ist im Pairing-Modus.

### **2. Bluetooth in Batocera aktivieren**

1. Navigiere in **Batocera** ins Hauptmenü.
2. Gehe zu **"Controller-Einstellungen"**.
3. Wähle **"Bluetooth Controller verbinden"**.
4. Der Controller sollte in der Liste auftauchen. Falls nicht, starte die Suche erneut.
5. Wähle deinen **PS4-Controller (DualShock 4)** aus der Liste aus.
6. Warte, bis die Verbindung hergestellt ist – die LED leuchtet dann konstant.

### **3. Controller testen & konfigurieren**

1. Falls nötig, gehe in die **Controller-Einstellungen** und wähle **"Controller konfigurieren"**.
2. Weise die Buttons korrekt zu.

### **4. Verbindung merken (optional)**

Falls der Controller nach einem Neustart nicht automatisch verbunden wird:

1. **SSH aktivieren** (falls noch nicht geschehen).
2. Verbinde dich mit Batocera per SSH (`ssh root@batocera.local` oder mit der IP-Adresse).
3. Gib folgenden Befehl ein:
    
    ```bash
    batocera-save-overlay
    ```
    
    Das speichert die Verbindung dauerhaft.

Jetzt sollte dein PS4-Controller kabellos mit Batocera funktionieren! 🎮
