Mit `curl` kannst du eine Datei direkt aus dem Internet herunterladen. Hier zwei gängige Varianten:

---
## Installation

apt install curl -y


### Datei mit dem Originalnamen speichern

Der Parameter `-O` (großes O) speichert die Datei mit dem Namen, der in der URL angegeben ist:

curl -O https://example.com/pfad/zur/datei.zip



### Datei mit einem eigenen Namen speichern

Mit dem Parameter `-o` (kleines o) kannst du den Namen der heruntergeladenen Datei festlegen:

curl -o lokale_datei.zip https://example.com/pfad/zur/datei.zip


### Fortschrittsbalken anzeigen

Beide Befehle laden die Datei von der angegebenen URL herunter und speichern sie im aktuellen Verzeichnis. Sollte die Verbindung langsam sein oder du möchtest den Fortschritt der Übertragung sehen, kannst du zusätzlich den Parameter `-#` verwenden, der einen Fortschrittsbalken anzeigt:

curl -# -O https://example.com/pfad/zur/datei.zip
