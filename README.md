# 📅 Lernplan Widget

Interaktiver, wöchentlicher Lernplaner als einzelne `index.html` – gehostet auf GitHub Pages,
eingebettet als macOS-Desktop-Widget über [Widget Web 26](https://apps.apple.com/app/widget-web-26/id1522169352).
Kein Build-Schritt, kein Framework, keine externen Dependencies.

---

## Was ist das?

Ein persönlicher Wochenplaner speziell für die Prüfungsphase im Masterstudium.
Für jeden Tag der Woche lassen sich bis zu zwei Lernblöcke (Vormittag und Nachmittag) einem
Modul zuweisen. Feste Termine wie Vorlesungen und der HiWi-Tag sind unveränderlich eingetragen.
Ein Prüfungs-Countdown zeigt jederzeit, wie viele Tage noch bis zur jeweiligen Klausur oder
Abgabe verbleiben – mit automatischer Farbkodierung von grün über gelb zu rot.

Der Planer ist dafür ausgelegt, **jeden Montag in zwei Minuten** ausgefüllt zu werden:
welches Modul lerne ich an welchem Tag, zu welcher Tageszeit. Nicht mehr, nicht weniger.

---

## Module & Prüfungsdaten

| Kürzel | Modul                                        | Prüfung / Abgabe | CP |
|--------|----------------------------------------------|------------------|----|
| CHN    | Chinesisch I/II                              | 15.07.2026       | 6  |
| MS     | Masterseminar                                | 16.07.2026       | 6  |
| UAM    | Unternehmensarchitektur-Management           | 30.07.2026       | 4  |
| FoF2   | Fundamental of Finance II                   | 31.07.2026       | 6  |
| AM     | Algorithmic Management                       | 18.08.2026       | 6  |
| KUM    | Klima- und Umweltmanagement in Unternehmen  | 26.08.2026       | 6  |
| TuU    | Technologietransfer im Unternehmertum (KI)  | kein Examen      | 3  |

---

## Feste Wochenstruktur

Die folgenden Blöcke sind im Widget fest eingetragen und nicht veränderbar:

- **Montag 8:00–9:30** – Chinesisch Vorlesung (Anwesenheitspflicht, max. 4× fehlen)
- **Montag ganztags** – HiWi-Tag (kein Lernblock Vormittag/Nachmittag, nur optionaler Abend-Slot)
- **Mittwoch 15:20–17:00** – Chinesisch Vorlesung

Dienstag 13:30–17:00 wäre die FoF2-Vorlesung – diese wird bewusst nicht besucht
(Selbststudium via Folien + Anki, identische Strategie wie beim Vorgängermodul).

---

## Bedienung

1. **Slot anklicken** – Vormittag oder Nachmittag eines Tages anklicken
2. **Modul wählen** – Modulauswahl erscheint, gewünschtes Kürzel antippen
3. **Speichern** – passiert automatisch via `localStorage`, kein Button nötig
4. **Leeren** – Slot erneut anklicken → „✕ leeren" wählen
5. **Woche wechseln** – Pfeile links/rechts im Header, Klick auf Wochentitel springt zur aktuellen Woche

Der Plan bleibt gespeichert solange der Browser-Cache nicht geleert wird.
Jede Woche hat einen eigenen Eintrag im `localStorage` (Key: ISO-Datum des Montags).

---

## Setup: GitHub Pages

Das Widget wird als statische Seite über GitHub Pages bereitgestellt.

### Erstmalig einrichten

1. Repository auf GitHub anlegen (Public)
2. `index.html` ins Repository laden (muss exakt `index.html` heißen)
3. Im Repository: **Settings → Pages → Branch: main, Folder: / (root) → Save**
4. Nach ~1 Minute erreichbar unter: `https://DEIN-USERNAME.github.io/REPO-NAME/`

### Updates einspielen

Geänderte `index.html` einfach erneut hochladen und committen –
GitHub Pages aktualisiert sich innerhalb einer Minute automatisch.

### Lokale Entwicklung

```bash
# Kein Build nötig – direkt im Browser öffnen:
open index.html

# Oder mit lokalem HTTP-Server (empfohlen wegen localStorage-Verhalten):
python3 -m http.server 8080
# → http://localhost:8080
```

---

## macOS Widget (Widget Web 26)

### Einrichtung

1. [Widget Web 26](https://apps.apple.com/app/widget-web-26/id1522169352) aus dem App Store installieren
2. Neues Widget anlegen → Typ **Web** (URL-Modus)
3. GitHub Pages URL eintragen: `https://DEIN-USERNAME.github.io/lernplan/`
4. Widget-Größe und Crop-Bereich einstellen (siehe unten)
5. Widget zum macOS-Desktop oder Notification Center hinzufügen

### Crop-Bereich

Das Layout ist so aufgebaut, dass die Seite in **zwei saubere Zonen** unterteilt ist:

- **Zone A (oben):** Tagesraster mit Lernblöcken – für das Haupt-Widget
- **Zone B (unten):** Prüfungs-Countdown – für ein zweites, kleineres Widget

Die Trennlinie zwischen den Zonen liegt immer am selben Pixel,
da das Tagesraster eine fixe, unveränderliche Höhe hat.
Crop einmal einstellen → passt dauerhaft.

---

## Projektstruktur

```
lernplan/
├── index.html      # Die gesamte Anwendung – HTML, CSS und JS in einer Datei
├── CLAUDE.md       # Projektkontext für Claude Code (KI-Assistent)
├── IMPROVEMENTS.md # Offene Verbesserungs-Specs
└── README.md       # Diese Datei
```

---

## Technische Details

| Aspekt | Detail |
|---|---|
| Sprache | Vanilla JavaScript (ES2020+), kein Framework |
| Styling | Inline CSS + `<style>`-Block, hardcodiertes Dark Theme |
| Persistenz | `localStorage`, Key `lernplan_v1`, Format JSON |
| Wochenbeginn | Montag (ISO 8601) |
| KW-Berechnung | ISO 8601-konform |
| Externe Dependencies | keine |
| Build-Schritt | keiner |
| Browser-Support | alle modernen Browser (Chrome, Safari, Firefox) |

### Globale Funktionen (onclick-Handler)

Da der HTML-Inhalt per `innerHTML` gerendert wird, sind onclick-Handler als
globale Window-Funktionen definiert:

| Funktion | Beschreibung |
|---|---|
| `_pw()` | Vorherige Woche |
| `_nw()` | Nächste Woche |
| `_ht()` | Zur aktuellen Woche springen |
| `_ts(dayIndex, slot)` | Slot-Auswahl togglen (`slot`: `'morning'` / `'afternoon'`) |
| `_sm(moduleId)` | Modul einem aktiven Slot zuweisen (`null` = leeren) |

---

## Weiterentwicklung

Offene Punkte und geplante Verbesserungen sind in [`IMPROVEMENTS.md`](./IMPROVEMENTS.md) dokumentiert.
Für KI-gestützte Weiterentwicklung mit Claude Code liegt unter [`CLAUDE.md`](./CLAUDE.md)
der vollständige Projektkontext bereit.
