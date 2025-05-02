Hier eine Schritt-für-Schritt-Anleitung, um einen PS4 Controller (DualShock 4) am Raspberry Pi zu betreiben. Es gibt grundsätzlich zwei Möglichkeiten: per USB oder über Bluetooth.

---

### 1. Verbindung über USB

1. **Kabel anschließen:**  
    Verbinde den PS4 Controller mit einem USB-Kabel an einen freien USB-Port des Raspberry Pi.
2. **Automatische Erkennung:**  
    In der Regel wird der Controller automatisch als Eingabegerät erkannt. Du kannst dies über Tools wie `jstest` prüfen.

---

### 2. Verbindung über Bluetooth

1. **System aktualisieren:**
    
    ```bash
    sudo apt update && sudo apt upgrade
    ```
    
2. **Notwendige Pakete installieren:**
    
    ```bash
    sudo apt install bluetooth bluez blueman
    ```
    
3. **Bluetooth-Dienst starten und konfigurieren:**  
    Öffne das Terminal und starte das Bluetooth-Tool:
    
    ```bash
    bluetoothctl
    ```
    
    Dann folge diesen Schritten im interaktiven Modus:
    - **Bluetooth einschalten:**
        
        ```bash
        power on
        ```
        
    - **Agent aktivieren und als Standard setzen:**
        
        ```bash
        agent on
        default-agent
        ```
        
    - **Scannen starten:**
        
        ```bash
        scan on
        ```
        
4. **Controller in den Pairing-Modus versetzen:**  
    Halte gleichzeitig die **Share**- und **PS**-Taste gedrückt, bis die Lichtleiste zu blinken beginnt.
5. **Pairing durchführen:**  
    Sobald dein Controller in der Liste erscheint (du erkennst ihn meist an einem Eintrag mit dem Namen „Wireless Controller“), merke dir die MAC-Adresse (z. B. `XX:XX:XX:XX:XX:XX`) und führe dann aus:
    
    ```bash
    pair XX:XX:XX:XX:XX:XX
    trust XX:XX:XX:XX:XX:XX
    connect XX:XX:XX:XX:XX:XX
    ```
    
    Nach erfolgreichem Verbinden sollte der Controller betriebsbereit sein.

---

### 3. Testen des Controllers

Um zu überprüfen, ob der Controller korrekt erkannt wurde, kannst du das Tool `jstest` verwenden:

sudo apt install joystick
jstest /dev/input/js0

Dies zeigt dir in Echtzeit, welche Achsenbewegungen und Tastendrücke registriert werden.

---

### 4. Beispiel: PS4 Controller in Python mit pygame nutzen

Hier ein kurzes Python-Skript, das zeigt, wie du den Controller mit pygame ausliest:

import pygame
import sys

pygame.init()
pygame.joystick.init()

# Überprüfen, ob ein Joystick vorhanden ist
if pygame.joystick.get_count() == 0:
    print("Kein Joystick gefunden!")
    sys.exit()

joystick = pygame.joystick.Joystick(0)
joystick.init()
print("Joystick Name:", joystick.get_name())

try:
    while True:
        for event in pygame.event.get():
            if event.type == pygame.JOYAXISMOTION:
                print("Achse", event.axis, "Wert:", event.value)
            elif event.type == pygame.JOYBUTTONDOWN:
                print("Taste gedrückt:", event.button)
            elif event.type == pygame.JOYBUTTONUP:
                print("Taste losgelassen:", event.button)
except KeyboardInterrupt:
    pygame.quit()

**Erklärung:**

- Das Skript initialisiert pygame und sucht nach angeschlossenen Joysticks.
- Wird mindestens ein Gerät gefunden, wird der erste Joystick ausgewählt und dessen Name ausgegeben.
- Anschließend werden in einer Schleife alle Ereignisse (Achsenbewegungen, Tastendrücke) ausgelesen und in der Konsole ausgegeben.

---

Mit diesen Schritten sollte der PS4 Controller sowohl per USB als auch per Bluetooth am Raspberry Pi funktionieren und für eigene Projekte einsatzbereit sein.
