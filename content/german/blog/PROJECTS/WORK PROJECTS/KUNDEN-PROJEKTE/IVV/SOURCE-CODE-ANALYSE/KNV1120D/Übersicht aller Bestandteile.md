
|Typ|Name|Beschreibung|Position im Code|
|---|---|---|---|
|DIVISION|IDENTIFICATION DIVISION|Enthält Programminformationen wie PROGRAM-ID, Autor, Beschreibung und Änderungsprotokoll.|Dateianfang|
|PROGRAM-ID|KNV1120D|Eindeutige Kennung des Programms.|In der IDENTIFICATION DIVISION|
|DIVISION|ENVIRONMENT DIVISION|Definiert umgebungsspezifische Einstellungen (z. B. Compiler-Konfiguration und Sondernamen).|Nach der IDENTIFICATION DIVISION|
|DIVISION|DATA DIVISION|Enthält alle Daten­definitionen, die im Programm verwendet werden.|Nach der ENVIRONMENT DIVISION|
|DATENBEREICH|WORKING-STORAGE SECTION|Legt persistent verfügbare Variablen und Gruppierungen fest, die während der Programmausführung erhalten bleiben.|Innerhalb der DATA DIVISION|
|VARIABLE|WS-START|Startmarker für die Working-Storage; initialisiert mit dem Wert „#WSSTART“.|In der WORKING-STORAGE SECTION|
|DATENBEREICH|P-BEREICH|Gruppierung für Programmnamen und Konstanten (z. B. MEIN-PGM etc.), die zur Identifikation des eigenen und verwandter Programme dienen.|WORKING-STORAGE SECTION|
|DATENBEREICH|K-KONSTANTEN|Enthält technische Konstanten (z. B. Funktionscodes, Steuerbefehle) für Serversteuerung und Datenhandler.|WORKING-STORAGE SECTION|
|DATENBEREICH|M-KONSTANTEN|Definiert Meldungsschlüssel und weitere konstante Werte, die im Fehler- bzw. Informationsmanagement genutzt werden.|WORKING-STORAGE SECTION|
|DATENBEREICH|ZWISCHENWERTE|Dient zur Zwischenspeicherung von Berechnungs- und Zwischenwerten während der Programmausführung.|WORKING-STORAGE SECTION|
|DATENBEREICH|INDICES|Enthält Indexvariablen, die in Schleifen und Iterationen zur Steuerung verwendet werden.|WORKING-STORAGE SECTION|
|DATENBEREICH|SCHALTER|Enthält Flags und Schalter zur Steuerung von Prüfungen und bedingten Abläufen im Programm.|WORKING-STORAGE SECTION|
|DATENBEREICH|DATENHANDLER|Speicherbereich zur Übergabe von Daten an externe Datenhandler-Module (z. B. für den Aufruf von Kopystrecken).|WORKING-STORAGE SECTION|
|DATENBEREICH|DH-CONTROL|Steuerbereich für den Datenhandler, initialisiert per COPY (z. B. DMDHCTR1).|WORKING-STORAGE SECTION|
|DATENBEREICH|DH-BEREICH (inkl. Re-defines)|Mehrere, per COPY definierte Datenbereiche (z. B. DH-BTG-BEREICH, DH-ANG-BEREICH etc.) zur Übergabe an externe Fachmodule (z. B. KNF900RZ, KNF903RZ, KNF904RZ).|WORKING-STORAGE SECTION|
|SECTION|LINKAGE SECTION|Definiert die Schnittstellenfelder, über die Daten zwischen diesem Programm und dem aufrufenden Modul ausgetauscht werden (z. B. FS-, MA-, OB-, CA-, FD-, EC-, PR- und AK-BEREICH).|Nach der WORKING-STORAGE SECTION (DATA DIVISION)|
|DATENBEREICH|FS-BEREICH|Schnittstellenbereich (via COPY AAISTEU) für Daten, die u. a. für den Dateizugriff oder den Datenhandler relevant sind.|LINKAGE SECTION|
|DATENBEREICH|MA-BEREICH|Übergabebereich (via COPY AAIMELD) für Meldungs- und Statusinformationen.|LINKAGE SECTION|
|DATENBEREICH|OB-BEREICH|Bereich für Geschäfts- bzw. Ordnungsbegriffe (via COPY AAI0ORD); enthält u. a. Informationen zur Vorgangsart.|LINKAGE SECTION|
|DATENBEREICH|CA-BEREICH|Schnittstellenbereich für kundenspezifische Daten (via COPY KNIVCA01).|LINKAGE SECTION|
|DATENBEREICH|FD-BEREICH|Bereich für Bild- bzw. Dialogsteuerungsdaten (via COPY KNDV112); hier werden z. B. Eingaben wie Versicherungsbeginn, Wagnis und Versicherungsende gesteuert.|LINKAGE SECTION|
|DATENBEREICH|EC-BEREICH|Übergabebereich für ECI-Calls, also die Kommunikation mit dem Host (via COPY AAI1ECI).|LINKAGE SECTION|
|DATENBEREICH|PR-BEREICH|Bereich, der Anwender- bzw. Benutzerprofil-Daten enthält (via COPY AOI00004).|LINKAGE SECTION|
|DATENBEREICH|AK-BEREICH|Zusätzlicher Übergabebereich (als Filler definiert) für weitergehende Applikationsdaten.|LINKAGE SECTION|
|DIVISION|PROCEDURE DIVISION|Beinhaltet den ausführbaren Code des Programms, in dem die logischen Abläufe und Steuerungen definiert sind.|Nach der LINKAGE SECTION|
|SECTION|F000-PROGRAMM-STEUERUNG SECTION|Hauptsteuerungsbereich; enthält initiale PERFORM-Anweisungen und die Programmkontrolle sowie das Beenden des Programms.|Beginn der PROCEDURE DIVISION|
|SECTION|F100-VORLAUF SECTION|Initialisierungs- und Vorlaufsektion; setzt Variablen und bereitet das Programm auf die Verarbeitung vor.|PROCEDURE DIVISION|
|SECTION|F200-NEUANTRAG SECTION|Enthält Logik zur Vorbelegung und Verarbeitung von Neuanträgen (Neuversicherung).|PROCEDURE DIVISION|
|SECTION|F201-NEUANTRAG SECTION|Spezielle Verarbeitungsschritte für Neuanträge bei Fahrzeugwechsel, in denen etwa identische Bausteine unter anderen Bedingungen bearbeitet werden.|PROCEDURE DIVISION|
|SECTION|F230-FAHRZEUGWECHSEL SECTION|Verantwortlich für die Verarbeitung des Fahrzeugwechsels, d. h. Anpassungen bei einer Änderung der Versicherungsobjekte (z. B. beim Wechsel zwischen Unfall-Plus und normal).|PROCEDURE DIVISION|
|DATENBANKEN / DATEIEN|FILE SECTION|Es gibt keine explizit deklarierten Dateisegmente – das Programm arbeitet stattdessen über Datenbereiche und COPY-Strecken, um externe Daten (z. B. über einen Datenhandler) einzubinden.|Nicht vorhanden (Dateideklaration fehlt)|

Diese Übersicht fasst die wesentlichen Bestandteile von KNV1120D zusammen. Beachten Sie, dass das Programm – wie in vielen iVAS-Anwendungen üblich – stark auf COPY-Strecken und externe Module (z. B. für den Datenhandler) setzt, weshalb einzelne Variablen (z. B. die zahlreichen 05-Level-Felder in den Gruppen) hier nicht einzeln aufgeführt wurden.

Wenn Sie weitere Details zu einzelnen Unterabschnitten oder spezifischen Paragraphen wünschen, können diese Tabellenzeilen selbstverständlich noch weiter verfeinert werden.
