# Migration von `lumitronix-design.css` zu `global.css`

Stand: 25.04.2026

Diese Anleitung hilft beim Refactoring alter Apps, die noch `lumitronix-design.css` oder Muster aus `backup-old.html` verwenden. Ziel ist nicht, jede alte Utility-Klasse 1:1 zu übernehmen. Das neue System ist absichtlich kleiner: KI und Entwickler sollen wenige stabile Komponenten kopieren und über Layout-Primitives kombinieren.

## Grundsatz

- `global.css` ist der neue kanonische Komponentenbaukasten.
- `index.html` zeigt die kopierbaren Templates.
- `backup-old.html` ist nur Referenz für alte Muster.
- `lumitronix-design.css` bleibt im Root, weil bestehende Apps sie noch referenzieren. Nicht löschen.
- `sg-*` Klassen sind Dokumentationsoberfläche. Nie in Ziel-Apps übernehmen.
- Wenn eine alte Klasse fehlt, zuerst auf ein neues `lum-*` Template migrieren. Nur bei hohem Refactoring-Risiko temporär einen lokalen Kompatibilitäts-Wrapper in der betroffenen App verwenden.

## Schnelle Entscheidung

| Frage | Vorgehen |
|---|---|
| Gibt es ein neues Template in `index.html`? | Template übernehmen und Inhalte anpassen. |
| Ist die alte Klasse nur Layout/Spacing? | Durch `lum-layout-grid`, `lum-stack` oder `lum-cluster` ersetzen. |
| Ist die alte Klasse eine Variante, die visuell ähnlich bleibt? | Auf die neue Standardvariante konsolidieren. |
| Ist das alte Verhalten fachlich wichtig, aber im neuen System nicht vorhanden? | Als fehlende Komponente markieren und gezielt in `global.css` + `index.html` ergänzen. |
| Muss eine alte App sofort laufen? | Temporären App-lokalen Fallback schreiben, nicht den Baukasten mit Legacy-Aliassen überladen. |

## Mapping nach Komponentenfamilien

### Layout und Spacing

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-grid-2`, `lum-grid-3`, `lum-grid-4` | `lum-layout-grid` + `style="--lum-columns:N"` | Ersetzt | Wenn sehr viele alte Grids existieren: app-lokal `.lum-grid-2 { display:grid; grid-template-columns:repeat(2,minmax(0,1fr)); gap:var(--lum-gap,16px); }` definieren. |
| `lum-flex`, `lum-flex-center`, `lum-flex-between` | `lum-cluster` | Konsolidiert | Bei `space-between` app-lokal `style="justify-content:space-between"` ergänzen. |
| `lum-flex-col` | `lum-stack` | Ersetzt | `style="--lum-stack-gap:..."` für Abstand nutzen. |
| `lum-gap-4`, `lum-gap-8`, `lum-gap-16`, `lum-gap-24` | Custom Property auf Primitive | Konsolidiert | `style="--lum-stack-gap:16px"` oder `style="--lum-cluster-gap:16px"`. |
| `lum-mt-*`, `lum-mb-*` | Kontextabstand im Layout | Bewusst reduziert | Einzelfall per `style="margin-top:..."` ist erlaubt, wenn es Layoutwert ist. Keine Deko-Inline-Styles. |
| `lum-w-full`, `lum-text-right`, `lum-text-center`, `lum-truncate` | Kein globaler Standard | Fehlt/nach Bedarf | App-lokal ergänzen oder bei wiederholtem Bedarf als Utility-Block in `global.css` aufnehmen. |
| `lum-divider`, `lum-divider-dark` | Unverändert | Nachgezogen | Fuer normale Trennlinien `lum-divider`; auf dunklen Flaechen `lum-divider lum-divider-dark`. |

### Buttons

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-btn-primary` | `lum-btn lum-btn-primary` | Vorhanden | Keine Migration nötig. |
| `lum-btn-accent` | `lum-btn lum-btn-accent` | Vorhanden | Keine Migration nötig. |
| `lum-btn-outline` | `lum-btn lum-btn-outline` | Vorhanden | Keine Migration nötig. |
| `lum-btn-outline-accent` | `lum-btn lum-btn-outline` oder `lum-btn lum-btn-accent` | Konsolidiert | Für zwingende Accent-Outline app-lokal Alias auf Outline mit Accent-Farbe definieren. |
| `lum-btn-ghost-accent` | `lum-btn lum-btn-ghost` | Konsolidiert | Accent nur bei primärer Aktion verwenden. |
| `lum-btn-dark-solid`, `lum-btn-dark-accent` | `lum-btn lum-btn-dark` | Konsolidiert | Wenn alte Dark-Hero-Flächen unverändert bleiben müssen: app-lokal `.lum-btn-dark-solid { ... }` auf neue Dark-Werte mappen. |
| `lum-btn-dark-glass`, `lum-btn-dark-outline`, `lum-btn-dark-ghost` | `lum-btn lum-btn-dark` oder `lum-btn lum-btn-ghost` auf dunkler Fläche | Konsolidiert | Bei Glass/Outline als Designanforderung gezielt neue Dark-Button-Komponente nachziehen. |
| `lum-btn-icon`, `lum-btn-icon-dark` | `lum-icon-btn` | Umbenannt | In Apps mit vielen Iconbuttons temporär `.lum-btn-icon, .lum-btn-icon-dark { ... }` auf `lum-icon-btn`-Styles mappen. |
| `lum-btn-sm`, `lum-btn-lg` | `lum-btn-sm`, `lum-btn-lg` | In `global.css` vorhanden, in Index noch schwach dokumentiert | In `index.html` später Button-Größen ergänzen. |

### Forms und Suche

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-input`, `lum-select`, `lum-textarea` | Unverändert | Vorhanden | Keine Migration nötig. |
| `lum-search-wrap` | `lum-search` | Umbenannt/konsolidiert | Wrapper durch `<label class="lum-search">...</label>` ersetzen. |
| `lum-search-dark-wrap`, `lum-search-minimal-wrap` | `lum-search` | Konsolidiert | Dark/minimal nur app-lokal, bis eine echte neue Variante im Baukasten ergänzt wird. |
| `lum-float-field`, `lum-float-label`, `lum-float-error` | `lum-field`, `lum-label`, sichtbares Label | Bewusst vereinfacht | Bei bestehenden Floating-Label-Forms erst app-lokal behalten; für neue Apps sichtbare Labels verwenden. |
| `lum-input-error` | `lum-input lum-input-error` oder `aria-invalid="true"` + `lum-field-error` | Nachgezogen | Bei alten Formularen erst Klasse ergänzen; bei Refactoring bevorzugt `aria-invalid` und sichtbare Fehlerbeschreibung setzen. |
| `lum-input-sm/lg`, `lum-select-sm/lg` | Kein Standard | Fehlt | Nur bei dichter Admin-UI gezielt nachziehen. |

### Tabellen und Daten

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-table-wrap`, `lum-table` | Unverändert | Vorhanden | Keine Migration nötig. |
| `lum-table-dark`, `lum-table-dark-wrap` | `lum-table-dark` in `lum-table-dark-wrap` | Nachgezogen | Nur fuer echte dunkle Arbeitsflaechen verwenden. Standard bleibt die helle `lum-table`, weil sie in operativen Apps meist besser scannt. |
| `lum-table-compact` | `lum-table lum-table-compact` in `lum-table-wrap` | Nachgezogen | Fuer sehr dichte Admin-Tabellen. Wenn alte Tabellen Spezialabstaende hatten, zuerst auf diese Variante konsolidieren. |
| `lum-col-group-a/b/c`, `lum-col-primary`, `lum-col-green`, `lum-col-teal` | Badges oder normale Tabellenzellen | Konsolidiert | Spaltengruppen nur bei echtem semantischem Mehrwert neu aufnehmen. |
| `lum-stat`, `lum-stat-card-*` | `lum-stat-grid`, `lum-stat-card`, `lum-stat-label`, `lum-stat-value`, `lum-stat-note` | Ersetzt | Alte Stat-Cards auf neue Struktur umbauen. |
| `lum-breadcrumbs`, `lum-pagination`, `lum-page` | Unverändert, mit neuem Template in `index.html` | Nachgezogen | Alte Markups koennen meist direkt uebernommen werden; bei abweichenden Link-/Buttonstrukturen semantisch auf `nav`, `aria-current` und `aria-label` nachschärfen. |

### Charts

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-chart`, `lum-chart-row`, `lum-chart-track`, `lum-chart-fill` | Unverändert im neuen Balken-Chart | Vorhanden | Werte über `--lum-chart-value` setzen. |
| `lum-chart-cols`, `lum-chart-col`, `lum-chart-col-label` | Kein Standard | Fehlt | Bei Bedarf als eigenes Template "Säulen-Chart" nachziehen. |
| `lum-donut`, `lum-donut-label` | Kein Standard | Fehlt | Nicht handrollen; bei produktivem Bedarf robuste SVG/CSS-Variante dokumentieren. |
| `lum-sparkbar`, `lum-sparkbar-col` | Kein Standard | Fehlt | Für KPI-Mikrotrends später als kleines Chart-Template ergänzen. |
| `lum-progress`, `lum-progress-fill` | Unverändert, mit Progress-Template in `index.html` | Nachgezogen | Werte ueber `--lum-progress-value` setzen und `role="progressbar"` mit `aria-valuenow` ergaenzen. |

### Feedback, Status und Overlays

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-alert-info/success/warning/error` | Unverändert | Vorhanden | Alle vier Status sind im Index dokumentiert. |
| `lum-badge`, `lum-badge-accent/green/orange/red` | Unverändert | Vorhanden | Keine Migration nötig. |
| `lum-badge-primary`, `lum-badge-teal`, `lum-badge-gray` | `lum-badge`, ggf. `lum-badge-accent` | Konsolidiert | Teal/gray nur app-lokal, wenn Statussemantik nicht anders abbildbar ist. |
| `lum-tooltip-*`, `lum-info-icon` | `lum-tooltip`, `lum-info-icon`, `lum-tooltip-content` | Nachgezogen/konsolidiert | Alte Richtungs- und Farbvarianten auf den Standard-Tooltip reduzieren. Fuer wichtige Inhalte weiterhin sichtbaren Hilfetext bevorzugen. |
| `lum-loader*` | Kein Standard | Fehlt | Für Ladezustände bevorzugt `lum-skeleton`; Spinner nur bei kurzen Aktionen app-lokal. |
| `lum-modal-*` | `lum-modal`, `lum-modal-header/body/footer`, `lum-modal-backdrop` | Größtenteils vorhanden | `lum-modal-title`/`lum-modal-close` durch `lum-h3` und `lum-icon-btn` ersetzen. |
| `lum-skeleton-text`, `lum-skeleton-card` | `lum-skeleton-line`, `lum-skeleton-heading`, `lum-skeleton-avatar` | Ersetzt | Alte Skeletons auf neue Bausteine umbauen. |

### Navigation und Menüs

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-nav-h`, `lum-nav-v`, `lum-nav-v-dark` | Kein Standard | Fehlt | Für App-Shells gezielt neues Navigation-Template ergänzen. |
| `lum-filter-list`, `lum-filter-item` | Unverändert, mit neuem Template in `index.html` | Nachgezogen | Zaehler als `lum-badge` in den Button legen; aktiven Zustand mit `is-active` oder `aria-pressed="true"` setzen. |
| `lum-tabs`, `lum-tab` | Unverändert | Vorhanden | Segmentierte Tabs sind der neue Standard. |
| `lum-tab-panel` | Unverändert, als Panel zu `lum-tabs` | Nachgezogen | Panel nur fuer sichtbaren Inhalt nutzen; versteckte Panels mit `hidden` statt eigener Display-Klasse. |
| `lum-tabs-seg`, `lum-tab-seg` | `lum-tabs`, `lum-tab` | Umbenannt/konsolidiert | Wenn alte JS-Selektoren daran hängen, app-lokal Alias oder JS anpassen. |
| `lum-tabs-dark`, `lum-tabs-seg-dark` | Kein Standard | Fehlt | Auf hellen Arbeitsflächen bleiben; Dark-Variante nur bei echtem Dark-Surface-Bedarf ergänzen. |

### Avatare, Code und Brand

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-avatar*` | `lum-avatar`, optional `lum-avatar-sm/lg` und Statusfarben | Nachgezogen/konsolidiert | Alte Spezialformen auf Initialen-Avatar reduzieren. Bei echten Profilbildern Bild in `lum-avatar` setzen oder app-lokal erweitern. |
| `lum-code`, `lum-code-block`, `lum-code-*` | `lum-code-block` | Nachgezogen/konsolidiert | Inline-Code bleibt normaler `code` im Kontext; alte Header-/Copy-Varianten nicht global uebernehmen. |
| Logo-Sektion aus Backup | Nicht in neuer Index | Fehlt | Logo-Regeln in `docs/ai-development-guide.md` dokumentieren; visuelle Logo-Komponente bei Bedarf nachziehen. |

## Sammel-Lookup fuer konsolidierte Legacy-Klassen

Diese Klassenfamilien tauchen in alten Dateien auf, werden aber bewusst nicht 1:1 in `global.css` gespiegelt:

| Alte Familie | Neuer Umgang | Fallback |
|---|---|---|
| Farb-/Tokenklassen wie `lum-teal`, `lum-gray`, `lum-lgray`, `lum-dark-heading`, `lum-dark-body`, `lum-dark-muted`, `lum-dark-soft` | Auf semantische Komponenten umstellen: `lum-badge-*`, `lum-alert-*`, `lum-icon-circle-*`, normale Textklassen oder Tokens aus `:root`. | Wenn alte Screens pixelnah bleiben muessen, app-lokal eine kleine Token-Utility-Datei behalten. |
| Typografie-Utilities wie `lum-font-5xl/6xl/7xl`, `lum-leading-*`, `lum-tracking-*`, `lum-weight-*`, `lum-font-condensed` | Auf `lum-h1`, `lum-h2`, `lum-h3`, `lum-body`, `lum-body-sm`, `lum-overline` reduzieren. | Fuer Sonderseiten lokale Typografieklassen definieren; nicht in operative Apps uebertragen. |
| Spacing-/Sizing-Utilities wie `lum-gap-*`, `lum-mt-*`, `lum-mb-*`, `lum-size-*`, `lum-radius-btn`, `lum-shadow-xl/2xl`, `lum-z-*`, `lum-duration-*` | Layout-Primitives und Tokens verwenden: `lum-stack`, `lum-cluster`, `lum-layout-grid`, `--lum-columns`, `--lum-stack-gap`, `--lum-cluster-gap`. | Bei grossem Altbestand temporaer App-Aliasse schreiben und danach schrittweise entfernen. |
| Gradient-/Hero-/Dark-Deko wie `lum-grad-*`, `lum-card-gradient`, `lum-dark-bg`, `lum-dark-bg-gradient`, `lum-hero-bg`, `lum-ray-dim` | Nicht als Standard-App-UI uebernehmen. Neue Arbeitsoberflaechen bleiben ruhig, hell und scanbar. | Nur fuer alte Landing-/Praesentationsseiten app-lokal konservieren. |
| Button-/Badge-Farbvarianten wie `lum-btn-teal`, `lum-badge-dark-*`, `lum-badge-teal`, `lum-badge-gray` | Auf semantische Varianten konsolidieren: `lum-btn-primary`, `lum-btn-accent`, `lum-btn-outline`, `lum-badge`, `lum-badge-accent/green/orange/red`. | Wenn Teal fachlich eine eigene Bedeutung hat, diese Bedeutung zuerst benennen und dann gezielt neue Statusvariante ergaenzen. |
| Icon-Varianten wie `lum-icon-*`, `lum-icon-circle-sm/md/lg`, `lum-icon-circle-primary/accent/teal/dark*` | `lum-icon-btn` fuer Aktionen; `lum-icon-circle` plus vorhandene Statusvarianten fuer Hinweise. | Alte Groessen/Farben nur app-lokal mappen, falls Layouts sonst brechen. |
| Chart-/Progress-Helfer wie `lum-chart-fill-*`, `lum-chart-fill-width`, `lum-progress-green/accent/teal`, `lum-progress-fill-width` | Werte ueber `--lum-chart-value` und `--lum-progress-value`; Farbe nur bei echtem semantischem Bedarf lokal setzen. | Fuer Reports mit Farblegenden lokale Klassen behalten, bis ein eigenes Chart-Template entsteht. |
| Code-Syntaxklassen wie `lum-code-keyword`, `lum-code-string`, `lum-code-comment`, `lum-code-value` | `lum-code-block` ohne globale Syntaxfarben. | Technische Docs koennen lokale Syntax-Highlighting-CSS nutzen. |
| Stat-Altstruktur wie `lum-stat-card-up/down`, `lum-stat-card-label/value/sub` | Neue Struktur: `lum-stat-grid`, `lum-stat-card`, `lum-stat-label`, `lum-stat-value`, `lum-stat-note`, `lum-stat-up/down`. | Alte Card-Klassen app-lokal auf neue Struktur mappen, wenn HTML nicht sofort umgebaut werden kann. |
| Loader-Varianten wie `lum-loader*`, `lum-loader-sun`, `lum-loader-inline`, `lum-loader-text` | Fuer Ladezustaende bevorzugt `lum-skeleton`; fuer Fortschritt `lum-progress`. | Kurze Spinner nur app-lokal, wenn ein Prozess keine sinnvolle Skeleton-Struktur hat. |
| Alte interne Hilfsklassen wie `lum-styleguide`, `lum-v4-box-border`, `lum-v4-box-shadow` | Nicht migrieren; das waren Styleguide-/Versionsartefakte. | Entfernen oder app-lokal isolieren, falls sie noch versehentlich im Markup stehen. |

## Nachgezogene Legacy-Brücken

Diese alten Muster sind jetzt im neuen Baukasten abgedeckt und in `index.html` als kopierbare Templates sichtbar:

1. Navigation: `lum-breadcrumbs`, `lum-pagination`, `lum-page`, `lum-filter-list`, `lum-filter-item`.
2. Forms: `lum-input-error`, `aria-invalid="true"`, `lum-field-error`.
3. Tabellen: `lum-table-compact`, `lum-table-dark-wrap`, `lum-table-dark`.
4. Feedback: `lum-alert-error`, `lum-progress`, `lum-progress-fill`, `lum-divider`.
5. Navigation/Content: `lum-tab-panel`.
6. Hilfe/Owner/Docs: `lum-tooltip`, `lum-info-icon`, `lum-tooltip-content`, `lum-avatar`, `lum-code-block`.

Weiterhin bewusst offen oder nur app-lokal zu lösen:

1. Kompakte Input-Groessen: `lum-input-sm/lg`, `lum-select-sm/lg`.
2. App-Shell-Navigation: `lum-nav-h`, `lum-nav-v`, `lum-nav-v-dark`.
3. Charts ausser Balken/Progress: `lum-chart-cols`, `lum-donut`, `lum-sparkbar`.
4. Logo-Regeln als kopierbares Template.

## Refactoring-Workflow

1. Alte App scannen: alle `lum-*` Klassen extrahieren.
2. Klassen gegen diese Migrationstabelle gruppieren.
3. Zuerst Templates aus `index.html` ersetzen, dann Layout-Primitives setzen.
4. Fehlende Komponenten nicht ad hoc stylen. Entweder app-lokaler Fallback oder Baukasten erweitern.
5. Wenn Baukasten erweitert wird: `global.css` + `index.html` + diese Migrationstabelle aktualisieren.
6. Desktop und Mobile prüfen. Tabellen müssen in `.lum-table-wrap` liegen.

## Kompatibilitätsregel

Legacy-Aliasse gehören nicht pauschal in `global.css`. Sie machen den Baukasten unscharf und verleiten KI dazu, alte Muster weiterzuverwenden. Ausnahmen sind erlaubt, wenn eine Klasse sehr verbreitet ist und semantisch eindeutig auf ein neues Pattern zeigt, zum Beispiel `lum-btn-icon` zu `lum-icon-btn`. Solche Aliasse müssen in `index.html` oder dieser Datei als Übergang markiert werden.
