# KI-Entwicklungsleitfaden für den LUMITRONIX Styleguide

Stand: 25.04.2026

Diese Datei erklärt, wie dieser Styleguide weiterentwickelt werden soll. Sie richtet sich an KI-Agenten und Menschen, die neue Komponenten, Dokumentation oder Migrationen ergänzen.

## Ziel

Der Styleguide ist ein kompakter, kopierbarer Komponentenbaukasten für interne LUMITRONIX-Anwendungen. Er soll KI-Projekte dazu bringen, konsistente UI-Muster zu verwenden, statt neue Ad-hoc-Komponenten zu erfinden.

Das wichtigste Produkt ist nicht die Dokumentationsseite selbst, sondern der Vertrag:

- Nutze `lum-*` für echte App-UI.
- Nutze `sg-*` nur für die Styleguide-Oberfläche.
- Kopiere Templates aus `index.html`, nicht zufällige Vorschau-HTML-Fragmente.
- Nutze `global.css` als neue Quelle für KI-orientierte Komponenten.

## Schichten

| Schicht | Datei/Ort | Zweck | Darf in Apps kopiert werden? |
|---|---|---|---|
| Kanonische Komponenten | `global.css` | Design Tokens, Komponenten, Layout-Primitives | Ja |
| Template-Bibliothek | `index.html` | Vorschau + kopierbare Komponentenblöcke | Nur Inhalt der `<template>` Blöcke |
| Dokumentations-Chrome | `sg-*` in `global.css`/`index.html` | Navigation, Suchfeld, Codepanels der Styleguide-Seite | Nein |
| Legacy-Referenz | `backup-old.html` | Alte Styleguide-Seite als Vergleich und Migrationsquelle | Nein |
| Legacy-Produktions-CSS | `lumitronix-design.css` | Wird von alten Apps noch referenziert | Nicht löschen, nicht als neuer Standard verwenden |
| Migration | `docs/migration-lumitronix-design-to-global.md` | Übersetzung von alten Klassen zu neuen Patterns | Als Refactoring-Hilfe |
| Beispiel-Dokumente | `docs/*.html` | Statische Seiten, die zeigen, ob Apps mit dem Baukasten baubar sind | Als Referenz, nicht als Komponentenquelle |

## Architekturprinzipien

### 1. Komponenten zuerst

Neue Apps sollen mit bestehenden Templates beginnen:

- Button-Set
- Formulare
- Tabellen
- KPI-Gruppe
- Alerts
- Tabs
- Modal
- Skeleton
- Layout-Primitives

Wenn ein Layout anders wirkt, zuerst Inhalte und Primitive kombinieren. Neue CSS-Komponenten nur ergänzen, wenn ein wiederholbares Muster entsteht.

### 2. Layout ist kein Komponentenstil

Breiten, Spalten und Abstände gehören in Layout-Primitives:

- `lum-layout-grid`
- `lum-stack`
- `lum-cluster`
- `lum-table-wrap`

Erlaubt sind kleine Custom-Property-Werte, zum Beispiel:

```html
<div class="lum-layout-grid" style="--lum-columns:3">
```

Nicht erwünscht sind neue Spezialklassen wie `customer-overview-card-row-wide`, wenn ein Primitive reicht.

### 3. Dokumentation und App-UI bleiben getrennt

`sg-*` Klassen sind nur für den Styleguide:

- Sidebar
- Codepanel
- Suchfilter
- Vorschauflächen
- Doku-Layouts

Ziel-Apps dürfen diese Klassen nicht verwenden. Wenn ein `sg-*` Muster app-tauglich ist, muss es als echte `lum-*` Komponente neu benannt und in `global.css` dokumentiert werden.

### 4. Konsolidierung ist erlaubt

Der neue Baukasten muss nicht jede alte Klasse übernehmen. Viele alte Klassen waren Utility- oder Variantenwuchs. Konsolidierung ist richtig, wenn:

- die neue Variante dieselbe Aufgabe klarer löst,
- weniger Klassen nötig sind,
- KI dadurch weniger falsche Kombinationen erzeugt,
- ein Fallback in der Migration benannt ist.

Beispiel:

```text
lum-grid-3 -> lum-layout-grid + --lum-columns:3
```

### 5. Fehlendes wird bewusst nachgezogen

Wenn ein altes Muster weiterhin fachlich wichtig ist, nicht stillschweigend in einzelnen Apps nachbauen. Stattdessen:

1. Bedarf benennen.
2. Komponente in `global.css` ergänzen.
3. Kanonisches Template in `index.html` ergänzen.
4. Mapping in der Migrationstabelle aktualisieren.
5. Desktop und Mobile prüfen.

## Komponentenvertrag

Jede neue Komponente in `index.html` braucht:

- ein `<article class="sg-component">` als Doku-Hülle,
- ein klares `data-component` mit Suchbegriffen,
- eine kurze Beschreibung,
- eine Vorschau,
- genau ein kanonisches `<template>`,
- nur `lum-*` Klassen innerhalb des Templates,
- keine `sg-*` Klassen innerhalb des Templates,
- keine dekorativen Inline-Styles.

Inline-Styles sind nur erlaubt für echte Werte:

- `--lum-columns`
- `--lum-stack-gap`
- `--lum-cluster-gap`
- `--lum-chart-value`
- konkrete Beispielbreiten bei Skeletons oder Charts

## CSS-Regeln

Neue CSS in `global.css` soll:

- mit `lum-` prefixen,
- bestehende Tokens nutzen,
- responsive sein,
- keine globalen App-Annahmen treffen,
- keine Abhängigkeit von `sg-*` haben,
- Zustände semantisch benennen: `success`, `danger`, `warning`, `active`, `loading`, `disabled`.

Nicht verwenden:

- beliebige Farbnamen ohne Rolle,
- negative Letter-Spacing,
- viewportbasierte Fontgrößen,
- tief verschachtelte Card-in-Card-Layouts,
- einmalige Komponenten für einen einzigen Screenshot.

## Designziele

LUMITRONIX-UI soll ruhig, präzise und operativ wirken:

- dichte, aber lesbare Informationsflächen,
- klare Tabellen und Status,
- kleine, semantische Akzente,
- Montserrat als UI-Schrift,
- Orange sparsam für Warnung/Akzent,
- keine dekorative Marketing-Komposition in Arbeitsoberflächen.

## Status der aktuellen Abdeckung

Gut abgedeckt:

- Buttons und semantische Button-Zustände
- Layout-Primitives
- Icons und Icon-Buttons
- sichtbare Formularlabels
- KPI-Karten
- Tabellen
- Balken-Charts
- Alerts
- Tabs
- Modal
- Skeleton

Noch offen oder nur teilweise abgedeckt:

- Breadcrumbs und Pagination
- Filterlisten und App-Navigation
- kompakte Tabellen
- dunkle Tabellen
- Form-Error-State
- Tooltips/Info-Disclosure
- Avatare
- Donut/Säulen/Sparkbar-Charts
- Codeblock-Komponente
- Logo-Regeln als kopierbares Template

Diese offenen Punkte sind keine Pflicht zur 1:1-Übernahme. Sie sind Kandidaten, wenn echte Apps sie benötigen.

## Migration alter Apps

Bei Refactoring von alten Apps gilt:

1. Zuerst `docs/migration-lumitronix-design-to-global.md` lesen.
2. Alte `lum-*` Klassen extrahieren.
3. Klassen nach Komponentenfamilien gruppieren.
4. Bestehende Templates aus `index.html` einsetzen.
5. Fehlende Varianten nur mit dokumentiertem Fallback überbrücken.
6. Wenn mehrere Apps denselben Fallback brauchen, Komponente in den Baukasten übernehmen.

## Prüfcheck vor Commit

Für inhaltliche oder technische Änderungen:

- `index.html` zeigt weiterhin die kanonischen Templates.
- `<template>` Blöcke enthalten keine `sg-*`.
- `global.css` bleibt unabhängig von `backup-old.html`.
- `lumitronix-design.css` bleibt vorhanden.
- `docs/migration-lumitronix-design-to-global.md` ist aktualisiert, wenn alte/neue Klassen betroffen sind.
- Desktop und Mobile haben keinen horizontalen Page-Overflow.
- Danach committen, pushen und Netlify veröffentlichen.

## Umgang mit Legacy-Dateien

- `backup-old.html` darf als Vergleich bleiben.
- `lumitronix-design.css` darf nicht gelöscht werden.
- Alte Varianten wie `lumitronix-design-v*.css` sind kein neuer Standard. Wenn sie lokal als gelöscht auftauchen, nicht ungefragt wiederbeleben und nicht mit neuen Änderungen vermischen.
- Neue Arbeit gehört bevorzugt in `global.css`, `index.html` und `docs/`.

## Merksatz

`global.css` ist die Sprache, `index.html` ist das Wörterbuch, `docs/migration-lumitronix-design-to-global.md` ist der Dolmetscher für alte Apps.
