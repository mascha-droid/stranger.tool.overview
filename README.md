# Stranger Tool Overview

Ein interaktives Web-Überblick-Projekt für den Kurs **Stranger Tools** (Hochschule, Gestaltung).

---

## Was ist das?

Eine vollbildige, blaue HTML-Seite, auf der 18 studentische Gestaltungstools als anklickbare Nonagone (9-Ecke) zufällig im Raum verteilt sind. Der echte Mauszeiger ist ausgeblendet; stattdessen folgt ein großes weißes PNG-Bild (Werkzeug-Silhouette) der Maus. Beim Hovern über ein Nonagon dreht es sich und ein farbiges Textfenster mit Name, Autorin und Beschreibung erscheint.

---

## Projektstruktur

```
stranger.tool.overview/
├── index.html              # Haupt-Seite (alles in einer Datei: HTML + CSS + JS)
├── tools.html              # Rohdaten-Liste (nicht interaktiv, nur als Referenz)
├── README.md               # Dieses Dokument
└── assets/
    ├── 934.png             # Nonagon (9-Eck) weiße Umrissgrafik — Tool-Icon (ungeklickt)
    ├── 934-filled.png      # Nonagon (9-Eck) weiß ausgefüllt — Tool-Icon (bereits geklickt)
    ├── 934.svg             # SVG-Version des Nonagons als Umriss
    ├── 934-filled.svg      # SVG-Version des Nonagons ausgefüllt
    ├── Trollfelsen_LARP_Werkzeugkoffer_Werkzeug_027 Maulschlüssel klein gedreht.png
    │                       # Aktuell NICHT mehr als Cursor genutzt
    └── 1x/
        ├── Element 1-8.png         # Weißes Werkzeug-PNG (Cursor, 4x hochskaliert)
        ├── Element 1-8.svg         # SVG-Version des Cursors
        ├── Element 1-8@4x.png      # Backup der 4x-Version
        └── Element 1-8_original_1x.png  # Backup des Originals (352x511 px)
```

---

## Technischer Stand (`index.html`)

### Body / Layout
- Hintergrund: `background-color: blue` (reines Blau)
- Kein Scrolling (`overflow: hidden`), alles 100vw x 100vh
- Systemcursor ausgeblendet (`cursor: none !important`)

### Custom Cursor
- Element: `<img id="custom-cursor">`
- Quelle: `assets/1x/Element 1-8.png` (weißes Werkzeug-PNG, 4x hochskaliert für Schärfe)
- Größe: `width: 450px`, folgt der Maus via JS (`mousemove`)
- Offset: `transform: translate(-75%, -5%)` — Spitze des Werkzeugs zeigt auf Cursor-Position

### Tool-Icons (Nonagone)
- PNG (ungeklickt): `assets/934.png` — weißes Nonagon (9 Ecken), dicke Linie, kein Innenkreis
- Geklickt / besucht: Vollflächig in der jeweiligen Farbe des Tool-Beschreibungsfensters (`toolColor`) ausgefüllt (als Vektor-SVG-Data-URL dynamisch generiert)
- Größe im DOM: `width: 100px`
- Bei Hover: dreht sich mit Animation `spinFast 0.4s linear infinite` (sowohl ungeklickt als auch ausgefüllt)
- Jede Instanz ist ein `<a class="tool-link">` mit absolutem Positioning
- Geklickte Links werden nur für die aktuelle Sitzung im Speicher gehalten (in der jeweiligen Tool-Farbe gefärbt). Beim Neuladen der Seite wird alles zurückgesetzt und alle Nonagone sind wieder weiß

### Tooltip (Beschreibungsfenster)
- Element: `<div id="tooltip">`
- Erscheint beim Hover über einem Nonagon oder über dem Fragezeichen
- Positioniert sich neben dem Mauszeiger, bleibt im Viewport
- **Ebenenhierarchie (`z-index: 999999`)**: Liegt immer über dem Custom-Cursor (`z-index: 9999`), damit der weiße Schraubenschlüssel den Text niemals verdeckt
- Größe: `width: 320px`, `padding: 16px`, `border-radius: 8px`
- Schrift: Helvetica, 14px, weiß, `text-align: left`, `line-height: 1.3`
- **Farbe: jedes Tool hat eine andere Hintergrundfarbe** (Array `toolColors` in JS, 18 Farben; Info-Box ist mittelgrau `#4a4a4a`)
- Inhalt: `${tool.name}\n\n${tool.author}\n\n${tool.desc}`

### Info-Button & Infofenster (Kurs-Information)
- Element: `<div id="info-btn">` unten rechts fixiert (`bottom: 24px; right: 24px;`), 100px groß, weißes `?` in Helvetica (100px)
- Hover-Effekt: Dreht sich beim Drüberfahren schnell (`spinFast 0.4s linear infinite`), genau wie die Nonagone
- Infofenster: Erscheint wie bei den Tools dynamisch beim Hovern neben dem Cursor
- Gestaltung: Mittelgrau (`#4a4a4a`), 320px breit, 16px Padding, 8px Radius, Helvetica 14px weiß
- Text: Kursinformation zu Stranger Tools (SoSe 2026, Prof. Sebastian Schmieg, HAW Hamburg) & Gestaltung durch Mascha Rein

### JavaScript-Logik
- `toolsData`: Array mit 19 Objekten `{ name, author, url, desc }`
- `toolColors`: Array mit 18 verschiedenen Farben (kein Hintergrundblau, kein Weiß)
- `generateNonOverlappingTools()`: Platziert alle Icons kollisionsfrei per Random-Placement (max. 1000 Versuche pro Tool; hält die Ecke unten rechts für das Fragezeichen frei)
- `getFilledNonagonSvg(color)`: Generiert das passende SVG-Data-URL für ein in der jeweiligen Tool-Farbe vollflächig gefülltes Nonagon
- `visitedTools`: In-Memory `Set`, merkt sich geklickte Tools während der aktuellen Sitzung und wird beim Reload geleert
- `window.resetVisitedTools()`: Hilfsfunktion zum manuellen Zurücksetzen der Klick-Historie
- Wird bei `load` und `resize` neu aufgerufen

---

## Die 18 Tools (in Reihenfolge im Array)

| # | Name | Autorin/Autor | URL |
|---|------|--------------|-----|
| 1 | ABSTRACTINATOR | Paulina Grube | https://brown-cattle-459695.hostingersite.com/ |
| 2 | cliptrace | Lea Ickinger | https://harmonious-licorice-5d9195.netlify.app |
| 3 | Die verborgene Seele | Julian Rottstegge | (noch offline) |
| 4 | Digitaler RISO-Drucker | Noa Marie Gunter | https://noamarie04.github.io/Digitaler-Riso-2/ |
| 5 | entropy | Marie Nordhoff | https://maxfn9.github.io/entropy-tool/ |
| 6 | I want to be an artist and a producer! | Lara Schwiegelshon | (noch offline) |
| 7 | Internet Designer | Niklas Mehl | https://niklasmehl.github.io/youplusinternet/ |
| 8 | midi_me | Quentin Haller | https://quimbim77.github.io/midi_me/ |
| 9 | Origami Thing | Lena Rosenkranz | https://lenarxo.github.io/origami-thing/ |
| 10 | perornament | Emily Bruenjes | https://perornament.github.io/260628-per-ornament-/ |
| 11 | Pixel Sortierer | Jona Lafrentz | https://lafrentz.de/jona/pixelsortierer/pixelSortierer20.html |
| 12 | Printshop | Mascha Rein | https://mascha-droid.github.io/printshop/ |
| 13 | ScanLineAction | Pino Roloff | https://1pino.github.io/scanlineaction/index.html |
| 14 | Stochastic Typewriter | Linh Dang | https://llinhd.github.io/stochastic-typewriter/ |
| 15 | Tools für Gestalter:innen | Leo Huelsmann | https://leo-huelsmann.github.io/tfg/index.html |
| 16 | Typometric | Christopher Thomas | https://thomasitos.github.io/Typometric/ |
| 17 | Web Echo | Niklas Mehl | (noch offline) |
| 18 | weird growth | Morten Kölpin | https://koelpimo.github.io/Weird-Growth/ |
| 19 | wirr | Hanna-Sophie Bresgott | https://random.interaction.world/ss26/stranger-tools/wirr/ |

*(Hinweis: tools.html enthält die Rohdaten als einfache HTML-Liste ohne Interaktion)*

---

## Asset-Verlauf (für den Agenten)

### `assets/934.png` & `assets/934-filled.png`
- Ursprünglich: Foto/Scan einer Schraubenmutter (Sechskant) mit transparentem Hintergrund
- Durchlief mehrere Bearbeitungsschritte: Umrisse extrahiert → Doppellinie-Problem → Kreis entfernt → neu von Grund auf gezeichnet
- **Aktueller Stand (`934.png`)**: Geometrisch neu gezeichnetes **Nonagon (9 Ecken)**, weiß, dicke Linie (~8px), kein Innenkreis, transparenter Hintergrund (596×520 px)
- **Gefüllte Version (`934-filled.png`)**: Exakt gleiche Geometrie, Außenkontur und Bounding-Box (596×520 px), aber innen vollflächig weiß ausgefüllt. Dient als visueller Indikator für bereits angeklickte Tools.
- Generiert mit Python/Pillow: 8× Supersampling, dann LANCZOS-Downsampling
- SVG-Äquivalente liegen in `assets/934.svg` und `assets/934-filled.svg`

### `assets/1x/Element 1-8.png`
- Ursprünglich: PNG-8-Export (352×511 px, sehr niedrige Auflösung, sah unscharf aus)
- **Aktueller Stand**: Hochskaliert auf 1408×2044 px (4×) — deutlich schärfer auf Retina-Displays
- Generiert via Kontur-Trace + Polygon-Approximation (RDP) + LANCZOS-Downsampling
- Weißes Werkzeug-Silhouette auf transparentem Hintergrund
- Backup liegt in `assets/1x/Element 1-8_original_1x.png`

---

## Bekannte Eigenheiten / offene Punkte

- Tools mit `url: "#"` sind noch nicht verlinkt (Tool noch nicht online)
- Das Nonagon dreht sich beim Hover — das ist gewollt
- Bei sehr kleinen Viewports können nicht alle Icons kollisionsfrei platziert werden
- `tools.html` ist eine separate Rohdaten-Seite, nicht mit `index.html` verbunden
- Der Maulschlüssel-PNG (`Trollfelsen_LARP_...`) ist weiterhin im assets-Ordner vorhanden, wird aber nicht mehr genutzt
