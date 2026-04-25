# Agent Instructions

## Default Workflow

- Arbeite direkt in diesem Projektordner.
- Vor Aenderungen am Komponentenbaukasten `docs/ai-development-guide.md` lesen.
- Bei Refactorings alter Apps oder alten `lum-*` Klassen `docs/migration-lumitronix-design-to-global.md` verwenden.
- `global.css` ist der neue KI-Komponentenbaukasten; `index.html` enthaelt die kanonischen Templates.
- `backup-old.html` und `lumitronix-design.css` sind Legacy-Referenzen. `lumitronix-design.css` darf nicht geloescht werden, weil alte Apps sie noch referenzieren.
- Nach jeder abgeschlossenen inhaltlichen oder technischen Aenderung den Stand direkt committen.
- Nach jedem Commit den aktuellen Branch sofort pushen.
- Nach jedem Push die aktuelle Version direkt veroeffentlichen.
- Fuer die Veroeffentlichung immer das bestehende Netlify-Projekt `lum-styleguide` verwenden.
- Wenn ein geplanter Push oder eine Veroeffentlichung technisch nicht moeglich ist, den Grund klar benennen und den naechsten noetigen Schritt direkt ableiten.
