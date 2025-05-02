
|Typ|Name|Beschreibung|Position im Code|
|---|---|---|---|
|SECTION|IDENTIFICATION DIVISION|Enthält Programminformationen wie PROGRAM-ID, Autor, Erstellungsdatum, Beschreibung und Änderungsverlauf|Programmanfang|
|SECTION|ENVIRONMENT DIVISION|Definiert Umgebungsparameter (z. B. Betriebssystem, Compileroptionen, etc.)|Nach der Identification Division|
|SECTION|DATA DIVISION|Deklariert alle Datenstrukturen des Programms – unterteilt in verschiedene Bereiche wie WORKING-STORAGE, LINKAGE etc.|Nach der Environment Division|
|DATENBEREICH|WORKING-STORAGE SECTION|Enthält Variablen und Konstanten, die über mehrere Programmausführungen erhalten bleiben; zentrale globale Daten|Innerhalb der DATA DIVISION|
|DATENBEREICH|WS-START|Initialisierungsmarker für den Arbeitsbereich; signalisiert den Beginn der Working-Storage|WORKING-STORAGE SECTION, oberster Bereich|
|DATENBEREICH|P-KONSTANTEN|Programmnamenkonstanten und Verweise zu weiteren Modulen (z. B. P-MEIN-PROGRAMM, P-KNV6310D etc.)|WORKING-STORAGE SECTION|
|DATENBEREICH|K-KONSTANTEN|Technische Konstanten (Funktionscodes, Datenarea-Bezeichner etc.) für die Serversteuerung und Datenhandleroperationen|WORKING-STORAGE SECTION|
|DATENBEREICH|M-KONSTANTEN|Enthält Meldungsschlüssel und Nachrichtencodes, die für die Systemmeldungen verwendet werden|WORKING-STORAGE SECTION|
|DATENBEREICH|ZWISCHENWERTE|Temporäre Speicherbereiche für Zwischenergebnisse (IDs, Flags, Berechnungswerte etc.)|WORKING-STORAGE SECTION|
|DATENBEREICH|Indices|Enthält Zähler (I-1, I-2, I-3, I-4) zur Steuerung von Schleifen und Iterationen|WORKING-STORAGE SECTION|
|DATENBEREICH|Schalter|Definiert Schalter/Flags (z. B. S-SPEICHER-DECKUNG, S-BESTAND-PRUEFEN etc.), die den Programmablauf steuern|WORKING-STORAGE SECTION|
|DATENBEREICH|DATENHANDLER|Kennzeichnet den Übergabebereich für den Datenhandler – dient als Steuerwert für externe Aufrufe|WORKING-STORAGE SECTION|
|DATENBEREICH|DH-CONTROL / DH-BEREICH|Bereiche (und deren Redefinitions) zur Übergabe von Daten an externe Module (z. B. zum Lesen der Beitragstabelle, Angebote)|WORKING-STORAGE SECTION|
|DATENBEREICH|KNF8015P-BEREICH|Datenbereich für den Aufruf des Fachmoduls zur Deckungsänderung|WORKING-STORAGE SECTION|
|DATENBEREICH|SAK-BEREICH|Datenbereich zum Lesen der Spartenzugehörigkeit einer SAK (z. B. für die Vorbelegung von Spartenkennungen)|WORKING-STORAGE SECTION|
|DATENBEREICH|SYSTEMFEHLER|Enthält einen speziellen Fehlercode/Marker zur Kennzeichnung von Systemfehlern|WORKING-STORAGE SECTION|
|DATENBEREICH|SYF-CONTROL / SYF-BEREICH|Bereiche zur Protokollierung von Systemfehlern, kopiert aus entsprechenden Systemmodulen|WORKING-STORAGE SECTION|
|DATENBEREICH|Pointer-Adressierungsbereiche|ADRESS-POINTER sowie PTR-FS-, PTR-OB-, PTR-CA-, PTR-FD-, PTR-MA- und PTR-PR-BEREICH zur dynamischen Adressierung von Daten|WORKING-STORAGE SECTION|
|DATENBEREICH|LS-ENDE|Marker, der das Ende des Local-Storage-Bereichs anzeigt|WORKING-STORAGE SECTION|
|DATENBEREICH|LINKAGE SECTION|Definiert Datenfelder für den Datenaustausch zwischen aufrufendem Programm und diesem Programm|DATA DIVISION, LINKAGE SECTION|
|DATENBEREICH|FS-BEREICH|Übergabebereich zur Kommunikation mit der Funktionssteuerung (über COPY AAISTEU)|LINKAGE SECTION|
|DATENBEREICH|MA-BEREICH|Übergabebereich für Meldungen und Anwenderkommunikation (über COPY AAIMELD)|LINKAGE SECTION|
|DATENBEREICH|OB-BEREICH|Übergabebereich für Ordnungsbegriffe bzw. Anwendungsdaten (über COPY AAI0ORD)|LINKAGE SECTION|
|DATENBEREICH|CA-BEREICH|Übergabebereich für kundenspezifische bzw. antragsspezifische Daten (über COPY KNIVCA10)|LINKAGE SECTION|
|DATENBEREICH|FD-BEREICH|Übergabebereich für Bilddaten bzw. Screeninformationen (über COPY KNDV652)|LINKAGE SECTION|
|DATENBEREICH|EC-BEREICH|Übergabebereich für den Datenhandler, z. B. für Fehler- und Datenkommunikation (über COPY AAI1ECI)|LINKAGE SECTION|
|DATENBEREICH|PR-BEREICH|Übergabebereich, der Informationen über das Anwenderprofil enthält (über COPY AOI00004)|LINKAGE SECTION|
|DATENBEREICH|AK-BEREICH|Weiterer Übergabebereich für zusätzliche Applikationsdaten|LINKAGE SECTION|
|SECTION|PROCEDURE DIVISION|Enthält alle ausführbaren Anweisungen des Programms; unterteilt in weitere Sections und Paragraphen|DATA DIVISION, PROCEDURE DIVISION|
|SECTION|F000-STEUERBLOCK|Hauptsteuerblock, der den Programmablauf regelt und Unterprogramme (PERFORMs) aufruft|PROCEDURE DIVISION|
|PARAGRAPH|F100-VORLAUF|Initialisierungsroutine: setzt Variablen (z. B. Compile-Zeit, Programmkennungen) und bereitet den Ablauf vor|Innerhalb von F000-STEUERBLOCK, PROCEDURE DIVISION|
|SECTION|F200-NEUANTRAG|Spezielle Sektion zur Bearbeitung und Vorbelegung der Sparte bei Neuanträgen|PROCEDURE DIVISION|
|SECTION|F201-NEUANTRAG|Ähnliche Vorbelegungslogik wie F200, speziell bei Fahrzeugwechsel-Anträgen|PROCEDURE DIVISION|
|SECTION|F230-FAHRZEUGWECHSEL|Sektion zur Durchführung der Vorbelegung bei Fahrzeugwechsel (z. B. bei Änderung der Fahrzeuginformationen)|PROCEDURE DIVISION|
|SECTION|F250-AENDERUNG-VG|Sektion zur Bearbeitung von Änderungsanträgen (z. B. bei Wagniswechsel in Vertragsänderungen)|PROCEDURE DIVISION|
|PARAGRAPH|AUSGANG|Gemeinsamer Endpunkt/Exit-Punkt des Programms, häufig mit GOBACK beendet|Am Ende der PROCEDURE DIVISION|
|DATENBANKEN / DATEIEN|– (keine FILE SECTION)|Das Programm deklariert keine eigenen Dateibereiche – alle externen Datenzugriffe erfolgen über COPY-Anweisungen und Datenhandler|Nicht vorhanden|

Diese Tabelle basiert auf den relevanten Abschnitten und Elementen, die in KNV6520D zu finden sind (vgl. citeturn0file0). Beachten Sie, dass im Programm zahlreiche weitere Detailvariablen, COPY-Strecken und interne Strukturen vorkommen – hier wurden die Hauptbestandteile zusammengefasst, um einen Überblick zu bieten.

Falls weitere Detailinformationen zu einzelnen Unterelementen (z. B. einzelne Felder innerhalb der P-KONSTANTEN oder spezifische COPY-Bereiche) gewünscht sind, kann eine weitergehende Analyse erfolgen.
