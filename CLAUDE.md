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
├── index.html                      # Haupt-App (Single-File, ~2500 Zeilen)
├── CLAUDE.md                       # Diese Dokumentation
├── beispiel-import.csv             # Beispiel-CSV für Import
└── timeline-generator-requirements.md  # Ursprüngliche Requirements
```

## Architecture

Alles in einer einzigen HTML-Datei (`index.html`):

```
index.html
├── <style> - CSS inkl. Drag&Drop, Timeline-Styling, Resize-Handle
├── <div id="app"> - Vue Template
│   ├── Sidebar
│   │   ├── Projektliste
│   │   ├── Projekt-Aktionen (Duplizieren, Als Vorlage, Löschen)
│   │   └── Vorlagen-Liste
│   ├── Main Content
│   │   ├── Toolbar (Tab-Auswahl, Zeitskala, Export-Buttons)
│   │   ├── Editor Panel (3 Tabs, verstellbare Breite)
│   │   │   ├── Projekt (Name, Datum, Einstellungen, Farb-Kategorien)
│   │   │   ├── Gruppen (CRUD, Sortierung)
│   │   │   └── Phasen & Meilensteine (2 Ansichten: Liste/Tabelle)
│   │   ├── Resize Handle (Panel-Breite anpassen)
│   │   └── Timeline Canvas (SVG)
│   │       ├── Legende (dynamisch)
│   │       ├── Zeitachse (Jahr + Monate)
│   │       ├── Gruppen-Header
│   │       ├── Phasen-Balken
│   │       ├── Meilenstein-Rauten
│   │       └── Heute-Linie
│   └── Modals
│       ├── Export (PNG/SVG)
│       ├── Import/Export (JSON + CSV)
│       ├── Batch-Eingabe (Mehrere Elemente)
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
- `elementeView` - Ansichtsmodus ('kompakt' oder 'tabelle')
- `expandedElementId` - ID des aufgeklappten Elements (kompakte Ansicht)
- `collapsedGroups` - Set der eingeklappten Gruppen
- `editorPanelWidth` - Breite des Editor-Panels (280-600px)
- `isResizing` - Resize-Modus aktiv
- `draggedElement`, `dragOverId` - Drag & Drop State
- `showBatchInputModal`, `batchInputText`, `batchInputGruppe` - Batch-Eingabe State
- `darkMode` - Dunkles Theme aktiv
- `showExecutiveSummary` - Executive Summary Filter aktiv
- `verticalMode` - Vertikale Roadmap-Ansicht aktiv

### Wichtige Computed Properties
- `currentProject` - Das aktive Projekt-Objekt
- `sortedGruppen` / `sortedElemente` - Nach Reihenfolge sortiert
- `usedColors` - Nur verwendete Farben für die Legende
- `hasBuffer` / `hasMilestones` / `hasCritical` - Flags für Legende
- `timelineRows` - Berechnete Zeilen für SVG (Gruppen + Elemente)
- `displayRows` - Gefilterte Zeilen (für Executive Summary)
- `yearPeriods` / `timePeriods` - Zeitachsen-Daten
- `canvasWidth` / `canvasHeight` - SVG-Dimensionen
- `themeColors` - Dynamische Farbpalette (Hell/Dunkel)
- `verticalTimePeriods` / `verticalElements` - Daten für vertikale Roadmap
- `verticalCanvasHeight` - Höhe der vertikalen Timeline
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
- **Duplizieren** - Element mit einem Klick kopieren

### Meilensteine
- Datum
- Hohle Rauten-Darstellung
- Einrückung (3 Ebenen)
- **Duplizieren** - Element mit einem Klick kopieren

### Elemente-Ansichten
Zwei umschaltbare Ansichtsmodi im Elemente-Tab:

**Kompakte Listenansicht** (Standard)
- Elemente als Einzeiler mit Name, Farbe, Datum
- Klick auf Element klappt Details auf
- Gruppen ein-/ausklappbar (zeigt Elementanzahl)
- Platzsparend bei vielen Elementen

**Tabellenansicht**
- Excel-ähnliche Darstellung
- Inline-Bearbeitung direkt in der Tabelle
- Schneller Überblick über alle Daten
- Ideal für Massenbearbeitung

### Batch-Eingabe
Mehrere Elemente auf einmal erstellen:
- Button "+ Mehrere" im Elemente-Tab
- Textfeld für mehrzeilige Eingabe
- Format: `phase;Name;Start;Ende;Farbe;Puffer;Kritisch`
- Format: `meilenstein;Name;Datum`
- Unterstützt YYYY-MM-DD und DD.MM.YYYY

### CSV-Import
Projektdaten aus Excel importieren:
- CSV-Datei hochladen (Semikolon- oder Komma-getrennt)
- Vorlage zum Download verfügbar
- Erstellt automatisch Gruppen und Elemente
- Projektdatum wird aus Elementen berechnet

**CSV-Spalten:**
```
Typ;Name;Gruppe;Start;Ende;Farbe;Puffer;Kritisch;Einrückung
gruppe;Phasenplan;;;;;;;;
phase;Analyse;Phasenplan;2025-01-01;2025-01-31;gruen;0;nein;0
meilenstein;Go-Live;Phasenplan;2025-03-01;;;;;0
```

### Verstellbare Panel-Breite
- Resize-Handle zwischen Editor und Timeline
- Ziehen mit der Maus zum Anpassen
- Bereich: 280px - 600px
- Nützlich für Tabellenansicht

### Präsentations-Ansichten
Drei umschaltbare Ansichtsmodi im Toolbar für Präsentationen:

**Dunkles Theme (☽/☀)**
- Toggle zwischen Hell- und Dunkel-Modus
- Alle Timeline-Elemente passen sich an (Hintergrund, Text, Linien)
- Legende und Container ebenfalls im Dark Mode
- Ideal für Präsentationen bei gedimmtem Licht

**Executive Summary ("Exec")**
- Gefilterte Ansicht nur mit Hauptelementen
- Zeigt nur Phasen mit Einrückung 0 + alle Meilensteine
- Canvas-Höhe passt sich automatisch an
- Perfekt für Management-Präsentationen

**Vertikale Roadmap (↕/↔)**
- Moderne Roadmap-Ansicht mit mittiger Zeitlinie
- Phasen alternieren links/rechts der Zeitachse
- Meilensteine als Rauten auf der Zeitlinie
- Monatsnamen als horizontale Marker
- Heute-Linie horizontal

Alle drei Ansichten sind kombinierbar (z.B. vertikale Roadmap im Dark Mode mit Executive Summary).

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

### Internationalisierung (i18n)
Zweisprachige Unterstützung mit Sprachumschalter:

**Sprachumschalter**
- Button im Header (neben "Timeline Generator")
- Zeigt "EN" wenn Deutsch aktiv, "DE" wenn Englisch aktiv
- Sofortiger Wechsel aller UI-Texte

**Implementierung**
```javascript
const translations = {
    de: { 'sidebar.projects': 'Projekte', ... },
    en: { 'sidebar.projects': 'Projects', ... }
};
const locale = ref('de');
function t(key, params = {}) { ... }
```

**Übersetzte Bereiche**
- Sidebar (Projekte, Vorlagen, Aktionen)
- Toolbar (Tabs, Zeitskala, Theme-Buttons)
- Editor (alle 3 Tabs mit Formularen)
- Timeline-Legende
- Alle Modals (Export, Import, Vorlagen, Batch)
- Alerts und Confirms
- Default-Namen (Neues Projekt, Neue Phase, etc.)
- Monatsnamen in Timeline (MÄR ↔ MAR)

**~160 Übersetzungsschlüssel** in 13 Kategorien:
`sidebar.*`, `toolbar.*`, `project.*`, `groups.*`, `elements.*`, `timeline.*`, `export.*`, `import.*`, `templates.*`, `saveTemplate.*`, `batch.*`, `common.*`, `default.*`, `confirm.*`, `alert.*`, `error.*`

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

### Panel Resize
- `.resize-handle` - Trennlinie zwischen Editor und Timeline
- `.resizing` - Aktiver Resize-Zustand
- `.sidebar-width` - Editor-Panel mit dynamischer Breite

## Development

Öffne `index.html` direkt im Browser. Keine Build-Tools erforderlich.

```bash
# Windows
start index.html

# Linux/Mac
open index.html
```

## Testing Checklist

### Basis-Funktionen
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

### Elemente-Ansichten
12. [ ] Kompakte Listenansicht: Element aufklappen
13. [ ] Kompakte Listenansicht: Gruppe ein-/ausklappen
14. [ ] Tabellenansicht: Inline-Bearbeitung
15. [ ] Ansichts-Umschalter (Liste ↔ Tabelle)

### Schnell-Eingabe
16. [ ] Element duplizieren (Button)
17. [ ] Batch-Eingabe: Mehrere Phasen auf einmal
18. [ ] Batch-Eingabe: Meilensteine hinzufügen

### Import/Export
19. [ ] CSV-Vorlage herunterladen
20. [ ] CSV-Import aus Datei
21. [ ] JSON Export/Import
22. [ ] PNG-Export
23. [ ] SVG-Export

### Vorlagen
24. [ ] Als Vorlage speichern
25. [ ] Aus Vorlage neues Projekt erstellen

### Präsentations-Ansichten
26. [ ] Dunkles Theme: Toggle aktivieren, alle Farben prüfen
27. [ ] Dunkles Theme: Legende im Dark Mode lesbar
28. [ ] Executive Summary: Nur Hauptphasen + Meilensteine sichtbar
29. [ ] Executive Summary: Canvas-Höhe passt sich an
30. [ ] Vertikale Roadmap: Mittige Zeitlinie korrekt
31. [ ] Vertikale Roadmap: Phasen alternieren links/rechts
32. [ ] Vertikale Roadmap: Meilensteine auf Zeitlinie
33. [ ] Kombinierter Modus: Vertikal + Dark + Executive

### UI
34. [ ] Panel-Breite per Resize-Handle anpassen
35. [ ] Browser schließen/öffnen → Persistenz prüfen

### Internationalisierung
36. [ ] Sprachumschalter: DE ↔ EN wechseln
37. [ ] Sidebar-Texte wechseln (Projekte ↔ Projects)
38. [ ] Toolbar-Texte wechseln
39. [ ] Editor-Texte wechseln (alle Tabs)
40. [ ] Modal-Texte wechseln
41. [ ] Timeline-Legende wechseln
42. [ ] Monatsnamen wechseln (MÄR ↔ MAR)
43. [ ] Fehlermeldungen auf Englisch prüfen
44. [ ] Bestätigungsdialoge auf Englisch prüfen

## Bekannte Einschränkungen

- Keine Abhängigkeiten zwischen Phasen (Gantt-Verbindungen)
- Keine Undo/Redo Funktion
- Keine Multi-User Kollaboration
- Keine Cloud-Synchronisation

## Sprache

UI zweisprachig (Deutsch/Englisch) mit Sprachumschalter im Header.
