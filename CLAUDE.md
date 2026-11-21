# Kitchen Dashboard — notes for Claude

A single-page kitchen dashboard (clock, weather, timers, recipe stand) for an old iPad,
served by GitHub Pages from `main` at https://williampage1971-cmd.github.io/kitchen/

## Adding recipes from photos (the main recurring task)
The owner photographs recipe-book pages and asks Claude to add them.
- Add each recipe as one object in `recipes.json` (one object per line, keep it valid JSON):
  `{"id":"banana-bread","title":"Banana bread","time":"1 hr 15 min","serves":"8","ing":["..."],"steps":["..."]}`
- `id`: short lowercase slug, unique, never change an existing one (the iPad uses ids to remember edits/removals).
- Transcribe faithfully: keep the book's quantities, units, temperatures and wording. One ingredient per
  `ing` entry; one step per `steps` entry, joining lines the page wrapped. Ingredient sub-headings
  (e.g. "For the sauce") can be their own `ing` entry ending in a colon.
- Say "N minutes" in a step where the book gives a time: the app turns it into a timer button.
- If a word is unreadable, make the best guess and tell the owner which one to check.
- The four entries with ids s1–s4 are starter examples; remove them only if the owner asks.
- Commit and push to `main`; the iPad picks up changes within ~30 minutes (or Settings → Check for new recipes now).

## Code constraints
`index.html` must run on old iPad Safari: ES5 only (var, function, XMLHttpRequest; no arrow functions,
let/const, template literals, fetch, CSS grid or clamp). Recipes added on the iPad live in localStorage
(`kd.mine`); removed shared recipes in `kd.hidden`.

## Releasing a change to index.html
The iPad auto-updates: it compares `version.txt` with `var VERSION` in `index.html` (on open, on wake and
every 30 min) and reloads when they differ, but not while a timer runs, a recipe is open or a dialog shows.
**Whenever you change `index.html`, set the same new value (e.g. `2026-12-01a`) in both `var VERSION` and
`version.txt`**, or the iPad will keep its old copy. Recipe-only changes to `recipes.json` need no bump.
