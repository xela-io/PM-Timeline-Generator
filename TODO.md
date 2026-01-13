# Timeline Generator - Projekt TODO

## Abgeschlossen

### App-Entwicklung
- [x] Grundgerüst mit Vue 3 + Tailwind CSS via CDN
- [x] Single-File Architektur (alles in index.html)
- [x] LocalStorage Persistenz mit versionierten Keys (_v2)
- [x] Projekt CRUD (erstellen, laden, duplizieren, löschen)
- [x] Gruppen-Verwaltung mit Sortierung
- [x] Phasen-Editor (Name, Gruppe, Start/Ende, Farbe, kritisch, Puffer)
- [x] Meilenstein-Editor (Name, Gruppe, Datum)
- [x] SVG-basierte Timeline-Visualisierung
- [x] Zeitachse mit Jahren und Monaten/Quartalen
- [x] Phasen als farbige Balken mit Namen
- [x] Meilensteine als hohle Rauten
- [x] Heute-Linie (rote vertikale Linie)
- [x] Kritischer Pfad Markierung
- [x] Puffer-Darstellung (gestrichelte Linie)
- [x] Einrückung (3 Ebenen) für hierarchische Darstellung
- [x] Dynamische Legende (zeigt nur verwendete Elemente)
- [x] Benutzerdefinierte Farb-Kategorienamen
- [x] Drag & Drop für Element-Sortierung
- [x] Vorlagen-System (speichern mit relativen Tagen)
- [x] PNG-Export mit html2canvas (1x, 2x, 3x Skalierung)
- [x] SVG-Export
- [x] JSON Import/Export

### Bug Fixes
- [x] Jahr-Label Abschneidung bei schmalen Zeiträumen
- [x] Balken-Clipping bei Überschreitung des Canvas

### Dokumentation
- [x] CLAUDE.md - Technische Entwickler-Dokumentation
- [x] README.md - Professionelle GitHub-Projektbeschreibung
- [x] CONTRIBUTING.md - Beitragsrichtlinien
- [x] LICENSE - MIT Lizenz
- [x] .gitignore - Standard-Ignores
- [x] docs/README.md - Screenshot-Anleitung
- [x] TODO.md - Diese Datei

### GitHub Veröffentlichung
- [x] Git Repository initialisiert (`git init`)
- [x] Branch auf 'main' umbenannt (`git branch -m main`)
- [x] Git User konfiguriert (`git config --global user.name/email`)
- [x] SSH-Key erstellt (`ssh-keygen -t ed25519`)
- [x] SSH-Key bei GitHub hinterlegt
- [x] Initial Commit erstellt
- [x] GitHub Repository erstellt (PM-Timeline-Generator)
- [x] Remote hinzugefügt (`git remote add origin ...`)
- [x] Code gepusht (`git push -u origin main`)
- [x] GitHub Pages aktiviert
- [x] Screenshots hochgeladen (docs/)

### Optional: Repository Verbesserungen
- [ ] GitHub Topics hinzufügen
- [ ] GitHub Description setzen
- [ ] Release v1.0.0 erstellen

---

## Links

| Ressource | URL |
|-----------|-----|
| **Repository** | https://github.com/xela-io/PM-Timeline-Generator |
| **Live-App** | https://xela-io.github.io/PM-Timeline-Generator/ |
| **GitHub Profile** | https://github.com/xela-io |

---

## Git Konfiguration (für neue Geräte)

### 1. Git User setzen
```bash
git config --global user.name "xela-io"
git config --global user.email "anor.londoe@pm.me"
```

### 2. SSH-Key erstellen
```bash
ssh-keygen -t ed25519 -C "anor.londoe@pm.me"
# Enter drücken für Standard-Pfad
# Optional: Passphrase setzen
```

### 3. SSH-Key anzeigen und bei GitHub hinzufügen
```bash
cat ~/.ssh/id_ed25519.pub
# Output kopieren und bei GitHub hinzufügen:
# https://github.com/settings/ssh/new
```

### 4. SSH-Agent starten (bei jeder Session oder in .bashrc)
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 5. Verbindung testen
```bash
ssh -T git@github.com
# Erwartete Ausgabe: "Hi xela-io! You've successfully authenticated..."
```

### SSH dauerhaft aktivieren (optional)
Füge zu `~/.bashrc` hinzu:
```bash
echo 'eval "$(ssh-agent -s)" > /dev/null && ssh-add ~/.ssh/id_ed25519 2>/dev/null' >> ~/.bashrc
```

---

## Zukünftige Features (Ideen)

### Einfach
- [ ] Tastatur-Shortcuts (Strg+S speichern, Entf löschen, etc.)
- [ ] Mehr Meilenstein-Icons (Flagge, Stern, Kreis)
- [ ] Mobile/Tablet Responsiveness verbessern
- [ ] Druckoptimiertes Stylesheet

### Mittel
- [ ] Internationalisierung (i18n) - Englische UI
- [ ] Dark Mode Theme
- [ ] Undo/Redo Funktionalität
- [ ] Tastaturnavigation im Editor

### Fortgeschritten
- [ ] Abhängigkeitspfeile zwischen Phasen (Gantt-Style)
- [ ] Import von CSV/Excel
- [ ] Cloud-Sync Option (optional, z.B. via GitHub Gist)
- [ ] Kollaborative Bearbeitung

---

## Technische Details

### Datenmodell
```
Projekt
├── projekt: { name, beschreibung, start, ende }
├── gruppen: [{ id, name, reihenfolge }]
├── elemente: [{ id, typ, name, gruppe, einrueckung, ... }]
└── einstellungen: { heuteAnzeigen, legendeAnzeigen, zeitskala, farbNamen }

Vorlage
├── Gleiche Struktur wie Projekt
└── Daten als relative Tage (startTag, endeTag, datumTag)
```

### LocalStorage Keys
- `timelineGenerator_projects_v2` - Alle Projekte
- `timelineGenerator_currentId_v2` - Aktive Projekt-ID
- `timelineGenerator_templates` - Alle Vorlagen

### Tech Stack
- Vue 3 (Composition API) via CDN
- Tailwind CSS via CDN
- SVG für Timeline
- html2canvas für PNG-Export
- Native HTML5 Drag & Drop

### Dateistruktur
```
PM-Timeline-Generator/
├── index.html              # Haupt-App (Single-File, ~1600 Zeilen)
├── README.md               # GitHub-Projektbeschreibung
├── CLAUDE.md               # Technische Entwickler-Dokumentation
├── CONTRIBUTING.md         # Beitragsrichtlinien
├── LICENSE                 # MIT Lizenz
├── TODO.md                 # Diese Datei
├── .gitignore              # Git-Ignores
└── docs/
    ├── README.md           # Screenshot-Anleitung
    ├── screenshot-main.png
    ├── screenshot-timeline.png
    └── screenshot-export.png
```

---

## Commits

| Datum | Nachricht |
|-------|-----------|
| 2025-01-13 | Initial release: Timeline Generator v1.0 |
| 2025-01-13 | Add screenshots for README |
| 2025-01-13 | Update README with correct repo URL and screenshot |

---

## Empfohlene Topics für GitHub
```
vue, tailwindcss, timeline, gantt-chart, project-management,
svg, single-page-app, no-backend, offline-first
```

## Empfohlene Description für GitHub
```
Browser-based timeline generator for project management.
Single HTML file, no backend required. Vue 3 + Tailwind CSS.
```

---

*Letzte Aktualisierung: 13. Januar 2025*
