
# 📓 Notebook

![Obsidian](https://img.shields.io/badge/Obsidian-Community-blue.svg) ![License](https://img.shields.io/badge/License-MIT-green.svg)

Ein persönliches Wissens-Management-System, erstellt und verwaltet mit [Obsidian](https://obsidian.md/). In diesem „Notebook“ werden alle Notizen, Anleitungen, Konfigurationen und weiteres Know-How gespeichert und strukturiert.

---

## 📖 Projektübersicht

**Notebook** ist ein zentraler Ort für dein gesamtes Wissen:
- **Notizen** zu Themen aller Art  
- **Tutorials** und Anleitungen  
- **Konfigurationsdateien** (z. B. `.vimrc`, `.bashrc`, `.gitconfig`)  
- **Bibliographie**, Ressourcen-Links und mehr  

Ziel ist es, mit Obsidian eine vernetzte Wissensdatenbank zu schaffen, die sich leicht durchsuchen, erweitern und versionieren lässt.

---

## 🚀 Features

- **Markdown-basiert**: Alle Inhalte liegen im Markdown-Format vor.
- **Vernetzte Notizen**: Bidirektionale Links (⟵→) und Graph-Übersicht.
- **Tags & Metadaten**: Einheitliche YAML-Frontmatter für Kategorisierung.
- **Snippets & Templates**: Wiederverwendbare Vorlagen für schnellere Erfassung.
- **Versionierung mit Git**: Lückenlose Historie aller Änderungen.
- **Multi-Device Sync**: Einfaches Synchronisieren via Git oder Obsidian Sync.

---

## 📂 Repository-Struktur

Notebook/
├── .obsidian/             # Obsidian-Einstellungen & Plugins
│   ├── plugins/           
│   └── workspace.json     
├── Templates/             # Vorlagen (z. B. Meeting, Daily Note)
│   └── Daily Note.md      
├── Configs/               # Konfigurationsdateien
│   ├── .bashrc           
│   └── .gitconfig        
├── Notes/                 # Alle thematischen Notizen
│   ├── Programmierung/    
│   │   ├── Python.md      
│   │   └── JavaScript.md  
│   └── DevOps/            
│       ├── Docker.md      
│       └── Kubernetes.md  
├── README.md              # Projektübersicht (dieses Dokument)
└── LICENSE                # Lizenzdatei

---

## 💾 Installation & Setup

1. **Obsidian installieren**  
   Lade Obsidian von der [offiziellen Website](https://obsidian.md/) herunter und installiere es.
2. **Repository klonen**  
   ```bash
   git clone https://github.com/DEIN-BENUTZERNAME/Notebook.git
   cd Notebook
   ```
3. **Vault öffnen**  
   Starte Obsidian und öffne den Ordner `Notebook` als Vault.
4. **Plugins & Themes**  
   - Empfohlene Plugins (z. B. “Templater”, “Kanban”, “YAML Front Matter”).  
   - Wähle ein Theme nach deinem Geschmack (z. B. “Minimal”).
5. **Templates anpassen**  
   Passe Vorlagen unter `Templates/` nach deinen Anforderungen an.

---

## 📝 Nutzung

- **Neue Notiz erstellen**:  
  1. Öffne Obsidian.  
  2. Lege eine neue Datei im passenden Unterordner an (bspw. `Notes/Programmierung`).  
  3. Nutze deine Templates mit dem Command-Palette-Shortcut (z. B. `⌘+P → Templater: Insert template`).
- **Verlinken**:  
  Schreibe `[[Ziel-Notiz-Name]]`, um bidirektionale Links zu erstellen.
- **Tags & Metadaten**:  
  ```markdown
  ---
  title: "Beispiel-Notiz"
  date: 2025-04-30
  tags: [Programmierung, Python]
  ---
  ```
- **Graph-Ansicht**:  
  Öffne die Seitenleiste → Graph-View, um die Vernetzung deines Wissens zu visualisieren.
- **Synchronisation**:  
  Regelmäßig `git add . && git commit -m "Update"` und `git push`, um Änderungen zu sichern.

---

## 🤝 Mitwirken (Contributing)

1. Forke das Repository.
2. Erstelle einen Branch:  
   ```bash
   git checkout -b feature/mein-thema
   ```
3. Füge neue Notizen oder Templates hinzu.
4. Committe und pushe deine Änderungen.
5. Erstelle eine Pull Request mit Beschreibung deines Beitrags.

Beachte bitte die [Code of Conduct](CODE_OF_CONDUCT.md) und die Style-Guidelines im `Templates/`.

---

## 📜 Lizenz

Dieses Projekt steht unter der **MIT-Lizenz**. Details findest du in der Datei [`LICENSE`](LICENSE).

---

## 📬 Kontakt

Bei Fragen, Feedback oder Fehlerberichten öffne bitte ein Issue oder schreib eine E-Mail an:

- **GitHub Issues:** https://github.com/DEIN-BENUTZERNAME/Notebook/issues  
- **E-Mail:** dein.name@example.com  

Viel Spaß beim Aufbau deiner persönlichen Wissens-Zentrale! 🌟  
