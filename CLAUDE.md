# Kitchen Dashboard — notes for Claude

A single-page kitchen dashboard (clock, weather, timers, recipe stand) for an old iPad,
served by GitHub Pages from `main` at https://williampage1971-cmd.github.io/kitchen/

## Adding recipes from photos (the main recurring task)
The owner photographs recipe-book pages and asks Claude to add them.
- Add each recipe as one object in `recipes.json` (one object per line, keep it valid JSON):
  `{"id":"banana-bread","title":"Banana bread","time":"1 hr 15 min","serves":"8","cats":["Dessert"],"ing":["..."],"steps":["..."]}`
- `id`: short lowercase slug, unique, never change an existing one (the iPad uses ids to remember edits/removals).
- Transcribe faithfully: keep the book's quantities, units, temperatures and wording. One ingredient per
  `ing` entry; one step per `steps` entry, joining lines the page wrapped. Ingredient sub-headings
  (e.g. "For the sauce") must be their own `ing` entry ending in a colon; the app shows those as headings, not tick boxes.
  The same works in `steps`: a step ending in a colon (e.g. "To Make the Salad:") is a section heading and the
  step numbers restart at 1 after it, matching books that number each section separately.
- Say "N minutes" in a step where the book gives a time: the app turns it into a timer button
  (a range like "15-18 minutes" makes a timer for the first number; every time in a step gets its own
  button, except warnings such as "more than 20 minutes"; "3 more minutes" works).
- `"cats":["Breakfast"]`: one or more categories, shown as filter buttons on the iPad. Use the names in
  `categories.json` (the owner's list, in button order; empty ones still show) and add a new name there only if the
  owner asks; Title Case.
  **Never choose a category yourself: always ask the owner which category (or categories) before committing
  the recipe**, unless they already said with the photo. You may mention what the page's "Course"/"Cuisine" says.
- **Dish photo**: if the page shows a photo of the food, cut it out of the owner's picture and save it as
  `images/<id>.jpg`, then add `"img":"images/<id>.jpg"`. With Pillow: straighten with
  `Image.transform((W,H), Image.QUAD, (TLx,TLy, BLx,BLy, BRx,BRy, TRx,TRy))` using corners set slightly inside the
  printed photo (no page edges or text), about 800px wide, `ImageOps.autocontrast(cutoff=1)`, JPEG quality ~78
  (keep files around 100 KB for the old iPad). Look at the result before committing. No photo on the page → no `img`.
- Optional `"notes":["..."]` holds extras such as nutrition/WW points, brand tips and the source/author;
  they show under the steps.
- If a word is unreadable, make the best guess and tell the owner which one to check.
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

## Status (as of 2026-11-22)
- 44 recipes entered, roughly the first half of the owner's binder. No recipe is waiting on a missing page.
- Category buttons (categories.json): Breakfast, Italian, Bowls/Mexican, Soups/Chilis, Casseroles/One-Pot, Meats,
  Chicken, Seafood, Sides, Desserts, Non-WW, Holiday. Still empty: Meats, Chicken, Seafood, Sides, Desserts, Non-WW, Holiday.
- Owner's preferences learned so far:
  - Always ask for the category (one at a time is fine); they usually pick a single category.
  - Leave out website logos/watermarks/badges from dish photos; skip a photo that's an unrecognisable smudge.
  - Multi-page printouts: add page 1 with a "Not finished yet" note, then complete it when page 2 arrives.
    If the owner can't find page 2, remove the note and keep what's there.
  - Drop printout junk ("[social_warfare]", "click here", "See process pictures"); fix obvious typos and say so.
- Open questions the owner hasn't answered yet:
  - Keep or remove blurry photos on One-Pot Cajun Chicken and Sausage Pasta, Bubble Up Pizza Casserole,
    Bubble Up Chicken Pot Pie Casserole?
  - Creamy Bacon Ranch Chicken Pasta step 3: the pressure-release sentence makes stray 5 and 30 min timer buttons.
  - Possible misprints left as printed: "29 kalamata olives" (Greek Chicken Bowls); "(from about 2)" on the
    marinara line (Chicken Parmesan Lasagna).
