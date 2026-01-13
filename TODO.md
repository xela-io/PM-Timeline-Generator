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

### Repository Setup
- [x] Git Repository initialisiert
- [x] Branch auf 'main' umbenannt
- [x] Alle Dateien gestaged

---

## Offen

### GitHub Veröffentlichung
- [ ] Git User konfigurieren (`git config --global user.name/email`)
- [ ] Initial Commit erstellen
- [ ] GitHub Repository erstellen
- [ ] Remote hinzufügen (`git remote add origin ...`)
- [ ] Code pushen (`git push -u origin main`)

### Screenshots für README
- [ ] preview.png erstellen (Hauptbild, 1200x630px empfohlen)
- [ ] screenshot-main.png (Vollansicht der App)
- [ ] screenshot-timeline.png (Timeline-Detail)
- [ ] screenshot-export.png (Export-Dialog)

### Optional: Repository Verbesserungen
- [ ] GitHub Topics hinzufügen (vue, timeline, gantt, project-management)
- [ ] GitHub Description setzen
- [ ] GitHub Pages aktivieren (index.html als Demo)
- [ ] Release v1.0.0 erstellen

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

---

## Commit-Nachricht (vorbereitet)

```
Initial release: Timeline Generator v1.0

A browser-based timeline generator for project management and transition planning.

Features:
- Single HTML file, no backend required
- Vue 3 + Tailwind CSS via CDN
- SVG-based Gantt chart visualization
- Hierarchical groups with phases and milestones
- Drag & drop reordering
- Templates with relative dates
- Export to PNG, SVG, JSON
- LocalStorage persistence
- Customizable color categories
- German UI
```

---

*Letzte Aktualisierung: Januar 2025*
