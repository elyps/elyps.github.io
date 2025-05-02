
|Typ|Name|Beschreibung|Position im Code (optional)|
|---|---|---|---|
|SECTION|IDENTIFICATION DIVISION|Enthält Programminformationen; hier wird unter anderem die PROGRAM-ID definiert.|Programmanfang|
|SECTION|PROGRAM-ID|Gibt den Namen des Programms an – in diesem Fall „KNV0077U“.|Innerhalb der IDENTIFICATION DIVISION|
|SECTION|ENVIRONMENT DIVISION|Legt die Umgebungs- und Systemparameter fest (z. B. welche Compiler‑ oder Laufzeiteinstellungen gelten).|Direkt nach der IDENTIFICATION DIVISION|
|SECTION|CONFIGURATION SECTION|Enthält Konfigurationsangaben zur Laufzeitumgebung, z. B. Hardware- oder Systemparameter.|Innerhalb der ENVIRONMENT DIVISION|
|SECTION|SPECIAL‑NAMES|Definiert spezielle Namenskonventionen (hier z. B. DECIMAL-POINT IS COMMA, also wird das Komma als Dezimaltrennzeichen genutzt).|Innerhalb der CONFIGURATION SECTION|
|SECTION|DATA DIVISION|Definiert alle Datenbereiche des Programms.|Nach der ENVIRONMENT DIVISION|
|DATENBEREICH|WORKING‑STORAGE SECTION|Enthält Variablen, die während der Programmausführung bestehen bleiben (persistenter Speicher).|Innerhalb der DATA DIVISION|
|DATENBEREICH|FILLER|Standard‑Felddefinition mit Wert „ANFANG‑WORK‑#“; dient als Marker bzw. Initialisierungswert.|Am Beginn der WORKING‑STORAGE SECTION|
|DATENBEREICH|AUFGERUFENE‑UPRO|Enthält die Liste der Unterprogramme (Module), die von diesem Programm aufgerufen werden.|In WORKING‑STORAGE, direkt nach FILLER|
|DATENBEREICH|• DATENHANDLER|Enthält den Namen des Datenhandler‑Programms, hier mit dem Wert „DMDH001P“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KNV0078U|Wiederholt den Programmnamen „KNV0078U“ als Referenz – Teil der Aufrufliste.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KNF907RZ|Enthält den Bezeichner eines weiteren aufzurufenden Moduls, Wert „KNF907RZ“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KNF909RZ|Enthält den Bezeichner eines weiteren aufzurufenden Moduls, Wert „KNF909RZ“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KBM1040P|Enthält den Bezeichner eines aufzurufenden Moduls, Wert „KBM1040P“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KNV0067U|Enthält den Bezeichner eines weiteren aufzurufenden Moduls, Wert „KNV0067U“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KNT0601Z|Enthält den Bezeichner eines weiteren aufzurufenden Moduls, Wert „KNT0601Z“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KNP663RZ|Enthält den Bezeichner eines weiteren aufzurufenden Moduls, Wert „KNP663RZ“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|• KNF903RZ|Enthält den Bezeichner eines weiteren aufzurufenden Moduls, Wert „KNF903RZ“.|Innerhalb von AUFGERUFENE‑UPRO|
|DATENBEREICH|K‑KONSTANTEN|Enthält programmweite Konstanten, die im gesamten Programm verwendet werden.|In WORKING‑STORAGE, nach AUFGERUFENE‑UPRO|
|DATENBEREICH|• K‑MAX‑20|Konstante für einen Maximalwert (hier 20).|Innerhalb von K‑KONSTANTEN|
|DATENBEREICH|• K‑1|Konstante mit dem Wert 1, häufig als Initialwert oder Inkrement genutzt.|Innerhalb von K‑KONSTANTEN|
|DATENBEREICH|• K‑OK|Signalisiert einen erfolgreichen Zustand; Wert „OK“.|Innerhalb von K‑KONSTANTEN|
|DATENBEREICH|• K‑NOOK|Signalisiert einen Fehlerzustand; Wert „NOOK“.|Innerhalb von K‑KONSTANTEN|
|DATENBEREICH|• K‑TW|Spezielle Statuskonstante (z. B. für einen bestimmten Tarifwechsel) mit dem Wert „TW “.|Innerhalb von K‑KONSTANTEN|
|DATENBEREICH|• K‑TEXT|Textkonstante mit dem Inhalt „Text“.|Innerhalb von K‑KONSTANTEN|
|DATENBEREICH|• K‑SYSTEMFEHLER|Kennung für Systemfehler, hier mit dem Wert „DMSYF01P“.|Innerhalb von K‑KONSTANTEN|
|DATENBEREICH|INDICES|Enthält Indexvariablen zur Steuerung von Schleifen bzw. Iterationen.|In WORKING‑STORAGE, nach K‑KONSTANTEN|
|DATENBEREICH|• I‑1|Indexvariable (Initialwert 0).|Innerhalb von INDICES|
|DATENBEREICH|• I‑2|Indexvariable (Initialwert 0).|Innerhalb von INDICES|
|DATENBEREICH|• I‑3|Indexvariable (Initialwert 0).|Innerhalb von INDICES|
|DATENBEREICH|ZWISCHEN‑FELDER|Temporäre Arbeitsfelder für Zwischenergebnisse (z. B. Programmname, Kompilierungszeit, Anzahl Sparten, Rückgabecode).|In WORKING‑STORAGE, nach INDICES|
|DATENBEREICH|• MEIN‑PGM|Speichert den Programmnamen „KNV0077U“.|Innerhalb von ZWISCHEN‑FELDER|
|DATENBEREICH|• COMPILE‑ZEIT|Speichert die Kompilierungszeit (initial leer).|Innerhalb von ZWISCHEN‑FELDER|
|DATENBEREICH|• ZW‑ANZ‑SPARTEN|Zählt die Anzahl der ermittelten Sparten (initial auf 0).|Innerhalb von ZWISCHEN‑FELDER|
|DATENBEREICH|• ZW‑RETURNCODE|Speichert den Rückgabecode der Operationen (initial leer).|Innerhalb von ZWISCHEN‑FELDER|
|DATENBEREICH|ZW‑KNI0078U|Arbeitsbereich, der per COPY von KNI0078U übernommen und mit der Ersetzung „*-“ → „ZW78-“ versehen wurde; dient der Datenübernahme.|In WORKING‑STORAGE|
|DATENBEREICH|LINKAGE SECTION|Bereich zur Übergabe von Daten zwischen Programmen (z. B. zur Kommunikation mit aufrufenden Programmen).|Direkt nach der WORKING‑STORAGE SECTION (nicht vollständig im Ausschnitt)|
|DATENBANKEN / DATEIEN|FILE SECTION|Das Programm enthält keine Dateidefinitionen bzw. keinen FILE SECTION.|–|
|PARAGRAPH|PROCEDURE DIVISION|Enthält die ausführbare Logik und Programmsteuerung. (Die konkrete Gliederung in Paragraphen ist im vorliegenden Ausschnitt nicht sichtbar.)|Direkt nach der LINKAGE SECTION (nicht im Ausschnitt)|

Diese Tabelle fasst die in KNV0077U deklarierten Bestandteile zusammen – von den Divisionen und Sections bis zu den einzelnen Datenfeldern im WORKING‑STORAGE. Da im vorliegenden Quelltext weder eine FILE SECTION noch spezifische Paragraphen in der PROCEDURE DIVISION vollständig abgebildet sind, wird darauf hingewiesen, dass solche Bestandteile in diesem Programm nicht vorkommen oder im Ausschnitt nicht enthalten sind.
