Im Folgenden finden Sie eine umfassende Markdown-Tabelle, die alle aus dem (teilweisen) Quelltext des COBOL‑Programms KNV0030U (Stand: Arbeitsbereich/Daten Division) erfassten relevante Bestandteile dokumentiert. Beachten Sie, dass der vorliegende Ausschnitt überwiegend die IDENTIFICATION‑, ENVIRONMENT‑ und WORKING‑STORAGE‑Bereiche enthält – explizite PROCEDURE‑Division-Paragraphen sowie Dateidefinitionen (FILE SECTION/Datenbanken) sind in diesem Quelltextausschnitt nicht vorhanden.

|Typ|Name|Beschreibung|Position im Code|
|---|---|---|---|
|Division|IDENTIFICATION DIVISION|Enthält die Programmidentifikation, Autor, Erstellungsdatum, Kurzbeschreibung und Änderungsverlauf.|Programmbeginn|
|Element|PROGRAM-ID|Definiert die Programm-ID (KNV0030U), die dieses COBOL‑Modul eindeutig identifiziert.|Innerhalb der IDENTIFICATION DIVISION|
|Division|ENVIRONMENT DIVISION|Legt die Systemumgebung und Konfigurationseinstellungen fest.|Nach der IDENTIFICATION DIVISION|
|Section|CONFIGURATION SECTION|Definiert Compiler- und Umgebungskonfigurationen.|Innerhalb der ENVIRONMENT DIVISION|
|Section|SPECIAL‑NAMES|Legt spezielle Namen fest (z. B. DECIMAL-POINT IS COMMA).|Innerhalb der CONFIGURATION SECTION|
|Division|DATA DIVISION|Umfasst sämtliche Datenbereiche des Programms, z. B. WORKING‑STORAGE.|Nach der ENVIRONMENT DIVISION|
|Section|WORKING‑STORAGE SECTION|Enthält Variablen, deren Inhalte während der Programmausführung erhalten bleiben.|Innerhalb der DATA DIVISION|
|Datenfeld|FILLER|Ein Filler‑Feld mit dem Wert „ANFANG‑WORK‑#“, das den Beginn des Working‑Storage markiert.|Am Anfang der WORKING‑STORAGE SECTION|
|Gruppe|VW‑FELDER|Sonderfelder für die Initialisierung bei Vorgangswechsel, z. B. VW-SESSION und VW-VORGANGS-ID.|In WORKING‑STORAGE (Sonderfelder)|
|Gruppe|STD‑FELDER|Standardfelder, die u. a. den Programmnamen (MEIN‑PGM), einen festen Wert (DMGMKTSU) und die Compile‑Zeit enthalten.|In WORKING‑STORAGE (Standardfelder)|
|Gruppe|SCHALTER‑LEISTE|Schalterfelder (z. B. S‑LESEN, S‑MORE, S‑START) zur Steuerung des Programmablaufs; enthält auch 88‑Level‑Definitionen.|In WORKING‑STORAGE (Steuerfelder)|
|Gruppe|ZW‑TAB‑SCHALTER|Über ein Copybook (KNM0006C) eingebundene Felder zur Steuerung von Tabellenschaltern.|In WORKING‑STORAGE|
|Gruppe|AUFGERUFENE‑UPRO|Liste der Unterprogramme, die von diesem Modul aufgerufen werden (z. B. DATENHANDLER, KNV0031U, KNV0032U, KNV0066U).|In WORKING‑STORAGE|
|Gruppe|KONSTANTEN|Definiert konstante Werte und Meldungstexte (z. B. K‑OK, K‑DMI0020I‑3, MAX‑PRV, K‑DMP1414P, K‑DMP1415P).|In WORKING‑STORAGE|
|Paragraph|_Keine Paragrafen_|Im vorliegenden Codeausschnitt sind keine PROCEDURE‑Division-Paragraphen definiert.|(Procedure Division nicht enthalten)|
|Datei|_Keine Dateien/Datenbanken_|Es sind keine Dateisektionen (FILE SECTION) oder Datenbankdefinitionen im Quelltext enthalten.|(Nicht vorhanden)|

Diese Tabelle fasst die aus KNV0030U extrahierten relevanten Bestandteile (von A bis Z) zusammen – alle weiteren Details (z. B. aus COPY‑Büchern) werden über externe Copybooks eingebunden und sind hier als Gruppen zusammengefasst.
