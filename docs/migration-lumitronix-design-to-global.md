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
| `lum-input-error` | Kein eigener Standard, Status über Kontext | Fehlt | Für Validierung `lum-alert-error` + Feldbeschreibung nutzen; bei Bedarf `aria-invalid` + App-CSS ergänzen. |
| `lum-input-sm/lg`, `lum-select-sm/lg` | Kein Standard | Fehlt | Nur bei dichter Admin-UI gezielt nachziehen. |

### Tabellen und Daten

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-table-wrap`, `lum-table` | Unverändert | Vorhanden | Keine Migration nötig. |
| `lum-table-dark`, `lum-table-dark-wrap` | `lum-table` auf heller Fläche | Konsolidiert | Bei dunklen Datenansichten zuerst prüfen, ob helle Tabelle im Arbeitskontext besser scannt. Wenn dark zwingend ist, neue Dark-Table in `global.css` + Template ergänzen. |
| `lum-table-compact` | Kein Standard | Fehlt | App-lokal für dichte Tabellen behalten oder als neue Variante dokumentieren. |
| `lum-col-group-a/b/c`, `lum-col-primary`, `lum-col-green`, `lum-col-teal` | Badges oder normale Tabellenzellen | Konsolidiert | Spaltengruppen nur bei echtem semantischem Mehrwert neu aufnehmen. |
| `lum-stat`, `lum-stat-card-*` | `lum-stat-grid`, `lum-stat-card`, `lum-stat-label`, `lum-stat-value`, `lum-stat-note` | Ersetzt | Alte Stat-Cards auf neue Struktur umbauen. |
| `lum-breadcrumbs`, `lum-pagination`, `lum-page` | Nicht im neuen Index | Fehlt | Für Listen-Apps priorisiert nachziehen; bis dahin app-lokal aus Legacy übernehmen. |

### Charts

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-chart`, `lum-chart-row`, `lum-chart-track`, `lum-chart-fill` | Unverändert im neuen Balken-Chart | Vorhanden | Werte über `--lum-chart-value` setzen. |
| `lum-chart-cols`, `lum-chart-col`, `lum-chart-col-label` | Kein Standard | Fehlt | Bei Bedarf als eigenes Template "Säulen-Chart" nachziehen. |
| `lum-donut`, `lum-donut-label` | Kein Standard | Fehlt | Nicht handrollen; bei produktivem Bedarf robuste SVG/CSS-Variante dokumentieren. |
| `lum-sparkbar`, `lum-sparkbar-col` | Kein Standard | Fehlt | Für KPI-Mikrotrends später als kleines Chart-Template ergänzen. |
| `lum-progress`, `lum-progress-fill` | In `global.css` vorhanden, aber kaum dokumentiert | Teilweise | Index um Progress-Template ergänzen, falls alte Apps Progress nutzen. |

### Feedback, Status und Overlays

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-alert-info/success/warning/error` | Unverändert | Vorhanden | `lum-alert-error` in Index noch ergänzen. |
| `lum-badge`, `lum-badge-accent/green/orange/red` | Unverändert | Vorhanden | Keine Migration nötig. |
| `lum-badge-primary`, `lum-badge-teal`, `lum-badge-gray` | `lum-badge`, ggf. `lum-badge-accent` | Konsolidiert | Teal/gray nur app-lokal, wenn Statussemantik nicht anders abbildbar ist. |
| `lum-tooltip-*`, `lum-info-icon` | Kein Standard | Fehlt | Für kritische Erklärungen zunächst sichtbare Hilfe-/Infotexte oder `title`; bei wiederholtem Bedarf Tooltip-Komponente nachziehen. |
| `lum-loader*` | Kein Standard | Fehlt | Für Ladezustände bevorzugt `lum-skeleton`; Spinner nur bei kurzen Aktionen app-lokal. |
| `lum-modal-*` | `lum-modal`, `lum-modal-header/body/footer`, `lum-modal-backdrop` | Größtenteils vorhanden | `lum-modal-title`/`lum-modal-close` durch `lum-h3` und `lum-icon-btn` ersetzen. |
| `lum-skeleton-text`, `lum-skeleton-card` | `lum-skeleton-line`, `lum-skeleton-heading`, `lum-skeleton-avatar` | Ersetzt | Alte Skeletons auf neue Bausteine umbauen. |

### Navigation und Menüs

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-nav-h`, `lum-nav-v`, `lum-nav-v-dark` | Kein Standard | Fehlt | Für App-Shells gezielt neues Navigation-Template ergänzen. |
| `lum-filter-list`, `lum-filter-item` | Kein Standard | Fehlt | Kurzfristig app-lokal behalten; langfristig als "Filterliste" in `index.html` nachziehen. |
| `lum-tabs`, `lum-tab` | Unverändert | Vorhanden | Segmentierte Tabs sind der neue Standard. |
| `lum-tabs-seg`, `lum-tab-seg` | `lum-tabs`, `lum-tab` | Umbenannt/konsolidiert | Wenn alte JS-Selektoren daran hängen, app-lokal Alias oder JS anpassen. |
| `lum-tabs-dark`, `lum-tabs-seg-dark` | Kein Standard | Fehlt | Auf hellen Arbeitsflächen bleiben; Dark-Variante nur bei echtem Dark-Surface-Bedarf ergänzen. |

### Avatare, Code und Brand

| Alt | Neu | Status | Fallback |
|---|---|---|---|
| `lum-avatar*` | Kein Standard | Fehlt | Für User-/Owner-Listen später Avatar-Template ergänzen. Bis dahin App-lokal behalten. |
| `lum-code`, `lum-code-block`, `lum-code-*` | Normales `code` im Dokument oder App-CSS | Fehlt | Für technische Docs ggf. Codeblock-Komponente nachziehen. |
| Logo-Sektion aus Backup | Nicht in neuer Index | Fehlt | Logo-Regeln in `docs/ai-development-guide.md` dokumentieren; visuelle Logo-Komponente bei Bedarf nachziehen. |

## Priorisierte Nachzüge

Falls alte Apps konkret auf `global.css` migriert werden sollen, haben diese Lücken die höchste praktische Relevanz:

1. Navigation: `lum-breadcrumbs`, `lum-pagination`, `lum-filter-list`.
2. Forms: Error-State und optional kompakte Inputs.
3. Tabellen: `lum-table-compact` und optional Dark Table.
4. Tooltips/Info: einfache, zugängliche Tooltip- oder Disclosure-Komponente.
5. Avatare: Owner/User-Indikator für operative Apps.
6. Progress/Loader: Progress dokumentieren, Skeleton bevorzugen.

## Refactoring-Workflow

1. Alte App scannen: alle `lum-*` Klassen extrahieren.
2. Klassen gegen diese Migrationstabelle gruppieren.
3. Zuerst Templates aus `index.html` ersetzen, dann Layout-Primitives setzen.
4. Fehlende Komponenten nicht ad hoc stylen. Entweder app-lokaler Fallback oder Baukasten erweitern.
5. Wenn Baukasten erweitert wird: `global.css` + `index.html` + diese Migrationstabelle aktualisieren.
6. Desktop und Mobile prüfen. Tabellen müssen in `.lum-table-wrap` liegen.

## Kompatibilitätsregel

Legacy-Aliasse gehören nicht pauschal in `global.css`. Sie machen den Baukasten unscharf und verleiten KI dazu, alte Muster weiterzuverwenden. Ausnahmen sind erlaubt, wenn eine Klasse sehr verbreitet ist und semantisch eindeutig auf ein neues Pattern zeigt, zum Beispiel `lum-btn-icon` zu `lum-icon-btn`. Solche Aliasse müssen in `index.html` oder dieser Datei als Übergang markiert werden.
