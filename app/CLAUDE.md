# /app (Role 1: Mobile app, owner Mihai)

Expo / React Native, TypeScript.

## Scope

Screens: auth, today's log (macro totals vs targets), meal scan (photo or text) → editable ingredient list (change grams, remove, add) with live recalculation → save, barcode scan, label photo flow (prompt the user for fields the label endpoint couldn't read), product library, recipes, history and macro charts.

- Talk only to Supabase and the AI service, never to FDC, Open Food Facts or model APIs directly.
- Use the mock API until real endpoints exist (see `docs/STATUS.md`).
- Recalculate macros with the shared function from `/contracts/ts`. Don't write your own.
- Show per-item confidence. Offer common portions (1 egg, 1 slice) and the top-3 alternative matches.

## May not touch

`/supabase`, `/ai`, `/contracts`.

## Commands

Add run, test and lint commands here once the Expo project is scaffolded.
