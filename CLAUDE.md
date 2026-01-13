# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Timeline Generator** - Eine Single-Page Web-App zur Erstellung von Projekt-Timelines für Transitionsprojekte. Die App läuft vollständig im Browser ohne Backend.

## Technical Stack

- **Vue 3** (Composition API) via CDN
- **Tailwind CSS** via CDN
- **SVG** für Timeline-Visualisierung
- **html2canvas** für PNG-Export
- **LocalStorage** für Persistenz
- **Native HTML5 Drag & Drop** für Element-Sortierung

## Dateistruktur

```
/projektmanagement/
├── index.html                      # Haupt-App (Single-File, ~1600 Zeilen)
├── CLAUDE.md                       # Diese Dokumentation
└── timeline-generator-requirements.md  # Ursprüngliche Requirements
```

## Architecture

Alles in einer einzigen HTML-Datei (`index.html`):

```
index.html
├── <style> - CSS inkl. Drag&Drop, Timeline-Styling
├── <div id="app"> - Vue Template
│   ├── Sidebar
│   │   ├── Projektliste
│   │   ├── Projekt-Aktionen (Duplizieren, Als Vorlage, Löschen)
│   │   └── Vorlagen-Liste
│   ├── Main Content
│   │   ├── Toolbar (Tab-Auswahl, Zeitskala, Export-Buttons)
│   │   ├── Editor Panel (3 Tabs)
│   │   │   ├── Projekt (Name, Datum, Einstellungen, Farb-Kategorien)
│   │   │   ├── Gruppen (CRUD, Sortierung)
│   │   │   └── Phasen & Meilensteine (CRUD, Drag&Drop)
│   │   └── Timeline Canvas (SVG)
│   │       ├── Legende (dynamisch)
│   │       ├── Zeitachse (Jahr + Monate)
│   │       ├── Gruppen-Header
│   │       ├── Phasen-Balken
│   │       ├── Meilenstein-Rauten
│   │       └── Heute-Linie
│   └── Modals
│       ├── Export (PNG/SVG)
│       ├── JSON Import/Export
│       ├── Vorlagen verwalten
│       └── Vorlage speichern
└── <script> - Vue 3 App
    ├── State (refs)
    ├── Computed Properties
    ├── Methods
    └── Lifecycle (onMounted)
```

## Data Model

### Projekt-Struktur
```json
{
  "id": "id_abc123",
  "projekt": {
    "name": "Projektname",
    "beschreibung": "...",
    "start": "2025-01-01",
    "ende": "2025-12-31"
  },
  "gruppen": [
    { "id": "id_...", "name": "Phasenplan", "reihenfolge": 1 }
  ],
  "elemente": [
    {
      "id": "id_...",
      "typ": "phase",
      "name": "Implementierung",
      "gruppe": "id_...",
      "einrueckung": 0,
      "start": "2025-01-01",
      "ende": "2025-06-30",
      "kritisch": false,
      "pufferTage": 14,
      "farbe": "rot",
      "reihenfolge": 1
    },
    {
      "id": "id_...",
      "typ": "meilenstein",
      "name": "Go-Live",
      "gruppe": "id_...",
      "einrueckung": 0,
      "datum": "2025-07-01",
      "reihenfolge": 2
    }
  ],
  "einstellungen": {
    "heuteAnzeigen": true,
    "legendeAnzeigen": true,
    "zeitskala": "monat",
    "farbNamen": {
      "gruen": "Vorbereitung",
      "rot": "Plandauer",
      "dunkelrot": "Kritisch"
    }
  }
}
```

### Vorlagen-Struktur
```json
{
  "id": "id_...",
  "name": "Standard Transition",
  "erstelltAm": "2025-01-13",
  "projektDauer": 365,
  "gruppen": [...],
  "elemente": [
    {
      "typ": "phase",
      "name": "...",
      "startTag": 0,
      "endeTag": 30,
      ...
    },
    {
      "typ": "meilenstein",
      "name": "...",
      "datumTag": 15,
      ...
    }
  ],
  "einstellungen": {...}
}
```

**Wichtig**: Vorlagen speichern Daten als relative Tage (`startTag`, `endeTag`, `datumTag`) statt absoluter Daten. Beim Erstellen eines Projekts aus einer Vorlage werden diese relativ zum neuen Projektstart berechnet.

## Key Implementation Details

### State (Vue refs)
- `projects` - Array aller Projekte
- `templates` - Array aller Vorlagen
- `currentProjectId` - ID des aktiven Projekts
- `activeTab` - Aktiver Editor-Tab ('projekt', 'gruppen', 'elemente')
- `draggedElement`, `dragOverId` - Drag & Drop State

### Wichtige Computed Properties
- `currentProject` - Das aktive Projekt-Objekt
- `sortedGruppen` / `sortedElemente` - Nach Reihenfolge sortiert
- `usedColors` - Nur verwendete Farben für die Legende
- `hasBuffer` / `hasMilestones` / `hasCritical` - Flags für Legende
- `timelineRows` - Berechnete Zeilen für SVG (Gruppen + Elemente)
- `yearPeriods` / `timePeriods` - Zeitachsen-Daten
- `canvasWidth` / `canvasHeight` - SVG-Dimensionen
- `todayX` - X-Position der Heute-Linie

### Farben
```javascript
const colors = {
  gruen: '#166534',
  rot: '#991B1B',
  dunkelrot: '#450a0a'
};
```

### LocalStorage Keys
- `timelineGenerator_projects_v2` - Projekte
- `timelineGenerator_currentId_v2` - Aktive Projekt-ID
- `timelineGenerator_templates` - Vorlagen

### Timeline-Berechnung
- `getDateX(date)` - Konvertiert Datum zu X-Koordinate
- `pixelsPerDay` - Abhängig von Zeitskala (monat: 3, quartal: 1.5)
- `labelWidth` = 280px (linke Spalte für Namen)
- `rowHeight` = 28px

### Bar-Clipping
Phasen-Balken werden auf die Canvas-Breite begrenzt:
```javascript
const clippedWidth = Math.min(rawWidth, maxX - startX);
const clippedPuffer = Math.max(0, Math.min(puffer, maxX - startX - clippedWidth));
```

### Jahr-Anzeige
Jahre werden nur angezeigt wenn genug Platz (> 60px):
```javascript
const showLabel = width > 60;
const labelX = Math.max(labelWidth + 30, x + width / 2);
```

## Features

### Projekt-Verwaltung
- Erstellen, Laden, Duplizieren, Löschen
- Projektname, Beschreibung, Start/Ende-Datum
- Zeitskala: Monate oder Quartale

### Gruppen
- Header-Zeilen zur Organisation von Elementen
- Sortierung mit ↑/↓ Buttons
- Farbige Darstellung in der Timeline

### Phasen
- Start/Ende-Datum
- Kritischer Pfad (vertikale Linie am Ende)
- Puffer/Zeitreserve (gestrichelte Linie)
- Farb-Kategorie (Grün, Rot, Dunkelrot)
- Einrückung (3 Ebenen)

### Meilensteine
- Datum
- Hohle Rauten-Darstellung
- Einrückung (3 Ebenen)

### Drag & Drop
- Elemente per Drag & Drop sortieren
- Visuelles Feedback beim Ziehen
- Automatischer Gruppen-Wechsel beim Ablegen

### Legende
- Dynamisch: zeigt nur verwendete Elemente
- Farb-Kategorien mit benutzerdefinierten Namen
- Zeitreserve, Meilenstein, Kritischer Pfad

### Vorlagen
- Projekt als Vorlage speichern
- Neues Projekt aus Vorlage erstellen
- Vorlagen verwalten (anzeigen, löschen)
- JSON Import/Export für Vorlagen

### Export
- PNG (mit konfigurierbarer Skalierung 1x, 2x, 3x)
- SVG (verlustfrei)
- JSON (Projekt-Daten)

## CSS-Klassen

### Timeline
- `.phase-bar` - Phasen-Balken mit Hover-Effekt
- `.milestone-icon` - Meilenstein-Symbol
- `.buffer-line` - Gestrichelte Puffer-Linie
- `.critical-marker` - Kritischer Pfad Markierung
- `.today-line` - Heute-Linie
- `.grid-line` / `.grid-line-light` - Rasterlinien

### Drag & Drop
- `.draggable-item` - Ziehbares Element
- `.dragging` - Während des Ziehens
- `.drag-over` - Ziel beim Überfahren
- `.drag-handle` - Griff-Symbol (⋮⋮)

## Development

Öffne `index.html` direkt im Browser. Keine Build-Tools erforderlich.

```bash
# Windows
start index.html

# Linux/Mac
open index.html
```

## Testing Checklist

1. [ ] Neues Projekt erstellen
2. [ ] Gruppen anlegen und sortieren
3. [ ] Phasen mit verschiedenen Farben erstellen
4. [ ] Meilensteine hinzufügen
5. [ ] Einrückung testen (3 Ebenen)
6. [ ] Drag & Drop für Elemente
7. [ ] Puffer-Darstellung (gestrichelte Linie)
8. [ ] Kritischer Pfad aktivieren
9. [ ] Heute-Linie ein/ausschalten
10. [ ] Legende prüfen (nur verwendete Elemente)
11. [ ] Farb-Kategorien umbenennen
12. [ ] PNG-Export
13. [ ] SVG-Export
14. [ ] JSON Export/Import
15. [ ] Als Vorlage speichern
16. [ ] Aus Vorlage neues Projekt erstellen
17. [ ] Browser schließen/öffnen → Persistenz prüfen

## Bekannte Einschränkungen

- Keine Abhängigkeiten zwischen Phasen (Gantt-Verbindungen)
- Keine Undo/Redo Funktion
- Keine Multi-User Kollaboration
- Keine Cloud-Synchronisation

## Sprache

UI komplett auf Deutsch.
