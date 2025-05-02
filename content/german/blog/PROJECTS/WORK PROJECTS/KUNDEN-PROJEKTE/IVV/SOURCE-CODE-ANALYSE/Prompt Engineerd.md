# In 8 Schritte zur perfekten Analyse

## Erst-Analyse
Analysiere den folgenden COBOL-Code, der auf einem IBM-Mainframe unter z/OS ausgeführt wird.

Ziele:
    1. Beschreibe detailliert die Funktion jeder SECTION und PARAGRAPH.
    2. Erkläre die Datenflüsse sowie die Verwendung von Dateien, Datenbanken (z. B. DB2), CICS oder VSAM – falls vorhanden.
    3. Dokumentiere den gesamten Code auf Deutsch.
    4. Es sollen keine Verbesserungsvorschläge oder Optimierungen gemacht werden. Fokus liegt nur auf technischer Analyse und fachlicher Dokumentation.

Hier ist der Code/Programm: ""

## Detaillierte Analyse
Analysiere das folgende COBOL-Programm bzw. COBOL-Modul "KNV0078U" detailliert und dokumentiere alle relevanten Bestandteile.  
Erstelle eine strukturierte Übersicht mit folgenden Punkten:

1. **SECTIONs**: Beschreibe alle SECTIONs (z. B. IDENTIFICATION DIVISION, DATA DIVISION, PROCEDURE DIVISION), ihre Funktion und ihren Inhalt.
    
2. **Paragraphs**: Liste alle Paragraphs innerhalb der SECTIONS auf und erläutere ihre jeweilige Aufgabe.
    
3. **Datenbereiche**: Identifiziere und beschreibe die verwendeten Datenbereiche (Working-Storage, File Section, Local Storage etc.).
    
4. **Datenbanken & Dateien**: Führe alle referenzierten Datenbanken oder externen Dateien auf, inklusive Art der Zugriffe (READ, WRITE, UPDATE etc.) und deren Rolle im Programmablauf.
    
5. **Arbeitsfelder**: Dokumentiere wichtige Variablen und Felder, inklusive Typ, Zweck und wo sie im Programm verwendet werden.
    
6. **Programmfluss**: Gib eine Übersicht über den Ablauf der Logik, inklusive Verzweigungen, Schleifen, Calls und Perform-Strukturen.
    

Ziel ist es, ein vollständiges technisches Verständnis des Programms zu erlangen – sowohl strukturell als auch funktional. Die Antwort soll klar gegliedert, ausführlich und verständlich sein.

## Markdown-Ausgabe, COBOL-Kontext
Analysiere das folgende COBOL-Programm: `"KNV6520D"`

Erstelle eine **vollständige Markdown-Tabelle**, die **alle relevanten Bestandteile** des Programms von A bis Z enthält. Gliedere die Tabelle in folgende Kategorien:

- SECTION
    
- PARAGRAPH
    
- DATENBEREICH (z. B. WORKING-STORAGE, FILE SECTION etc.)
    
- DATENBANKEN / DATEIEN
    

Jede Zeile der Tabelle soll folgende Informationen enthalten:

|Typ|Name|Beschreibung|Position im Code (optional)|
|---|---|---|---|

Achte darauf:

- **Alle Elemente** des Programms sollen aufgenommen werden – nichts auslassen.
    
- Gib zu jedem Eintrag eine **klare, verständliche Beschreibung** der Funktion oder Aufgabe im Programm.
    
- Verwende klare Begriffe, auch für technische Leser ohne COBOL-Hintergrund.
    
- Optional: Gib die ungefähre **Position oder SECTION**, in der sich das Element befindet.
    

Ziel: Eine umfassende, strukturierte und leicht verständliche Dokumentation des COBOL-Programms in Tabellenform.

## COBOL-Dokumentation mit Schritt-für-Schritt-Kommentierung
Analysiere das folgende COBOL-Programm bzw. Modul:  
**`KNV0078U`**

Ziel: Eine vollständige, schrittweise Analyse aller zentralen Bestandteile des Programms – strukturiert, verständlich und kommentiert.

Für **jede einzelne Einheit** SECTION, Paragraph liefere bitte:

1. **Funktionserklärung** – Was ist die Aufgabe dieses Elements?
    
2. **Ablauflogik** – Wie funktioniert der innere Ablauf oder die Verarbeitung?
    
3. **Kommentarteil im Quelltextstil (auf Deutsch)** – Simuliere einen realistischen COBOL-Kommentar, als wäre er direkt im Quellcode eingefügt. Beispiel:
    
`* Dieser Paragraph lädt Kundendaten aus der Datenbank`

⚙️ **Vorgehensweise:**

- Starte mit dem ersten relevanten Element.
    
- Gib mir **nach jedem Schritt** eine kurze Zusammenfassung und **frage**, ob du mit dem nächsten Element fortfahren sollst.
    
- Wiederhole dies, bis alle relevanten Einheiten dokumentiert sind.
    

📌 **Strukturierte Ausgabe gewünscht:**  
Nutze zur Übersicht – wo sinnvoll – Zwischenüberschriften oder Listen, um die Informationen je Einheit klar zu gliedern.

## Abschluss-Summary + COBOL-Kommentargerüst
Erstelle eine **abschließende Zusammenfassung** des analysierten COBOL-Programms.  
Die Ausgabe besteht aus zwei Teilen:

---

**🧩 TEIL 1 – Übersichtstabelle:**  
Erstelle eine übersichtliche **Markdown-Tabelle**, die alle wichtigen Bestandteile des Programms zusammenfasst:

- **SECTIONs** (kennzeichne z. B. F-, U-, SF-Sectionen)
    
- **Paragraphs**
    
- **Datenbereiche** (Working-Storage, File Section etc.)
    
- **Datenbanken / Dateien**
    
- **Arbeitsfelder / Variablen**
    

|Typ|Name|Beschreibung|Kategorie / Bereich|
|---|---|---|---|
|SECTION|SF-VERARBEITUNG|Steuerung der Datentransformation|PROCEDURE DIVISION|
|Datenbereich|CUSTOMER-REC|Struktur zur Kundenverwaltung|WORKING-STORAGE SECTION|
|...|...|...|...|

---

**🧩 TEIL 2 – COBOL-Kommentargerüst (auf Deutsch):**  
Generiere ein vollständiges Kommentargerüst im **COBOL-Kommentarstil (`*`)**, das in den Quellcode eingefügt werden kann. Die Kommentare sollen:

- Klar die Funktion jedes Abschnitts beschreiben
    
- Entwicklerfreundlich formuliert sein
    
- In logischer Reihenfolge gruppiert sein
    

Beispielstruktur:

markdown

Kopieren

`* =================================================== * PROGRAMMZUSAMMENFASSUNG – [PROGRAMMNAME] * =================================================== * SF-VERARBEITUNG: Führt Transformation durch ... * F-DATENBANKZUGRIFF: Liest und schreibt Daten in ... * Paragraph INIT: Initialisiert Laufzeitwerte ... * WORKING-STORAGE: Felder für temporäre Daten ... * DATEI: KUNDEN.DAT wird gelesen zur ... * ===================================================`

📌 Ziel: Eine kompakte Abschlussdokumentation zur Einbindung in Code und technische Doku.

## COBOL-Analyse als Excel-Datei exportieren
Erstelle aus der vollständigen Analyse des folgenden COBOL-Programms eine **strukturierte Excel-Datei** zur technischen Dokumentation.

Die Excel-Datei soll alle identifizierten Bestandteile enthalten:

- SECTIONs (inkl. Klassifikation: F-, U-, SF-)
    
- Paragraphs
    
- Datenbereiche (z. B. Working-Storage, File Section)
    
- Datenbanken / Dateien
    
- Arbeitsfelder / Variablen
    

📊 **Die Excel-Datei soll folgende Spalten enthalten:**

|Typ|Name|Beschreibung|Bereich / Zugehörigkeit|Kommentar für Entwickler|
|---|---|---|---|---|

📌 Anforderungen:

- Jeder Eintrag muss eindeutig und vollständig dokumentiert sein.
    
- Die Datei soll **nach Typ gruppiert oder sortiert** sein (optional auch mit Sheet-Trennung pro Typ).
    
- Gib mir die Datei **als .xlsx-Download-Link** oder in einem Format, das ich einfach speichern kann.
    

Beispielhafte Inhalte:

- `SECTION` → `SF-VERARBEITUNG` → „Steuert die Hauptlogik“
    
- `Datenbank` → `KUNDEN.DAT` → „Externe Datei für Kundendaten“
    

Ziel: Eine weiterverwendbare, technische Dokumentation des COBOL-Programms in Tabellenform für Excel.

## COBOL-Ablaufdiagramm als draw.io-Datei
Analysiere das folgende COBOL-Programm und erstelle daraus ein **vollständiges, strukturiertes Ablaufdiagramm** zur Visualisierung der Programm-Logik.

🔧 **Format:**  
Das Diagramm soll als `.drawio`- oder `.xml`-Datei generiert werden, die direkt in [draw.io / diagrams.net](https://draw.io) geöffnet und bearbeitet werden kann.

🔄 **Inhalte des Diagramms:**

- Starte mit einem **Einstiegspunkt** (z. B. `PROGRAM START`, `INITIALIZATION`)
    
- Zeige alle relevanten **SECTIONs und Paragraphs** als logische Blöcke
    
- Verbinde die Blöcke entsprechend dem Programmfluss (PERFORM, IF, GO TO etc.)
    
- Berücksichtige Zugriffe auf **Datenbanken, Dateien, Datenbereiche**
    
- Integriere wichtige **Arbeitsfelder oder Statusprüfungen**, falls sie Ablaufentscheidungen beeinflussen
    

📌 Anforderungen:

- Jeder Block enthält **den Namen (z. B. Paragraph) und eine Kurzbeschreibung** der Funktion
    
- Verzweigungen und Schleifen müssen klar dargestellt sein
    
- Kommentare zu den Verbindungen oder Bedingungen können als Labels eingefügt werden
    
- Die Struktur soll **leserlich, logisch gruppiert und hierarchisch** aufgebaut sein
    

📁 **Lieferformat:**

- Gib mir die `.drawio`- oder `.xml`-Datei als **Download-Link** oder alternativ als XML-Rohtext, den ich in draw.io importieren kann
    

🎯 Ziel: Ein visuelles Ablaufdiagramm zur technischen Dokumentation und Weiterbearbeitung im Architektur- oder Entwicklerteam.
