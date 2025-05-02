
|Typ|Name|Beschreibung|Position im Code|
|---|---|---|---|
|SECTION|IDENTIFICATION DIVISION|Enthält Programminformationen (PROGRAM-ID, Autor, Erstellungsdatum, Kurzbeschreibung).|Programmkopf, ganz am Anfang|
|SECTION|ENVIRONMENT DIVISION|Legt die Systemumgebung und Einstellungen fest (z. B. Systemkonfiguration, SPECIAL‑NAMES).|Nach der IDENTIFICATION DIVISION|
|SECTION|CONFIGURATION SECTION|Beinhaltet Konfigurationseinstellungen wie z. B. die Definition des Dezimaltrennzeichens (DECIMAL-POINT).|Innerhalb der ENVIRONMENT DIVISION|
|SECTION|DATA DIVISION|Definiert alle Datenbereiche und Variablen, die im Programm verwendet werden.|Nach ENVIRONMENT DIVISION|
|DATENBEREICH|WORKING‑STORAGE SECTION|Speichert Variablen, die während der Programmausführung persistieren – etwa Standardfelder und Flags.|Innerhalb der DATA DIVISION|
|DATENBEREICH|STD‑FELDER|Standard-Felddefinitionen, z. B. der Programmname (MEIN‑PGM) und Compile‑Zeit (COMPILE‑ZEIT).|Im WORKING‑STORAGE SECTION|
|DATENBEREICH|VW‑FELDER|Spezielle Felder zur Initialisierung bei Vorgangswechsel (z. B. VW‑SESSION, VW‑VORGANGS‑ID).|Im WORKING‑STORAGE SECTION|
|DATENBEREICH|AUFGERUFENE‑UPRO|Liste der Unterprogramme (Subroutinen), die vom Hauptprogramm aufgerufen werden.|Im WORKING‑STORAGE SECTION|
|DATENBEREICH|KONSTANTEN|Enthält fest definierte Werte, Parameter und Meldungsschlüssel, die im Programm genutzt werden.|Im WORKING‑STORAGE SECTION|
|DATENBEREICH|INDICES|Indexvariablen zur Steuerung von Schleifen und zur Adressierung von Arrayelementen.|Im WORKING‑STORAGE SECTION|
|DATENBEREICH|SCHALTER‑LEISTE|Steuerschalter und Flags zur Kontrolle der Programmabläufe (z. B. Start/Ende, Aenderungsflags).|Im WORKING‑STORAGE SECTION|
|DATENBEREICH|ZWISCHEN‑FELDER|Arbeitsfelder für temporäre Zwischenspeicherung und Datenmanipulation während der Verarbeitung.|Im WORKING‑STORAGE SECTION|
|SECTION|LINKAGE SECTION|Definiert die Schnittstellenfelder, die zwischen diesem Programm und aufrufenden/aufrufenden Programmen ausgetauscht werden.|Direkt nach dem WORKING‑STORAGE|
|DATENBANKEN/DATEIEN|FS‑BEREICH|Datenbereich für den Dateizugriff bzw. die Systemkommunikation (über den COPY von AAISTEU).|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|EC‑BEREICH|Übergabebereich für ECI‑Calls, um mit dem Hostsystem zu kommunizieren.|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|MA‑BEREICH|Datenbereich für Meldungen und die Kommunikation mit dem aufrufenden Modul.|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|OB‑BEREICH|Bereich für Ordnungsbegriffe oder Klassifikationen, die im Programm verwendet werden.|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|CA‑BEREICH|Enthält vertrags- oder kundenspezifische Daten, die zur weiteren Verarbeitung benötigt werden.|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|PR‑BEREICH|Übergabebereich mit Anwenderprofil-Daten für Authentifizierung und Personalisierung.|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|PD‑BEREICH|Datenbereich für Partnerinformationen – essenziell für Schnittstellen zu externen Partnern.|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|FD‑BEREICH|Bereich, der Dateidaten (z. B. Bilddaten) beinhaltet, die zur Anzeige oder Verarbeitung genutzt werden.|In der LINKAGE SECTION|
|DATENBANKEN/DATEIEN|AK‑BEREICH|Zusätzlicher Datenbereich für erweiterte oder spezielle Aufrufparameter.|In der LINKAGE SECTION|
|SECTION|PROCEDURE DIVISION|Enthält den ausführbaren Code und die logische Steuerung des Programms.|Direkt nach der LINKAGE SECTION|
|PARAGRAPH|F000‑STEUERBLOCK|Hauptsteuerblock, der den Programmablauf koordiniert und die wesentlichen Unterprogramme aufruft.|Beginn der PROCEDURE DIVISION|
|PARAGRAPH|F100‑VORLAUF|Initialisierungsroutine: Setzt Systemvariablen, übernimmt Defaultwerte und bereitet den Start vor.|Innerhalb der PROCEDURE DIVISION|
|PARAGRAPH|F500‑KOPIEREN‑BESTAND|Routine zum Kopieren bzw. Aktualisieren vorhandener Bestandsdaten im Antragssatz.|Innerhalb der PROCEDURE DIVISION|
|PARAGRAPH|F510‑LOESCHE‑TABS|Routine, die – unter bestimmten Bedingungen – Tabellen (z. B. Anzeige-Tabellen) löscht bzw. leert.|Innerhalb der PROCEDURE DIVISION|
|PARAGRAPH|U100‑LESEN‑010‑LNR1|Unterprogramm, das Antragsdaten (z. B. die erste LNR bzw. den Antragseintrag) einliest und verarbeitet.|Innerhalb der PROCEDURE DIVISION|

Diese Tabelle fasst die wesentlichen Bestandteile des COBOL‑Programms KNV0210D zusammen. Beachten Sie, dass weitere interne Variablen oder COPY-Anweisungen (z. B. für Datumsfelder, Fehlerprotokollierung etc.) ebenfalls Teil der einzelnen Abschnitte sind – die hier aufgeführten Einträge decken jedoch die Hauptstruktur ab und geben einen klaren Überblick über Aufbau und Funktion des Programms.
