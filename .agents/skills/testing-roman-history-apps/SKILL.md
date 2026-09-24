---
name: testing-roman-history-apps
description: How to set up and end-to-end test the offline single-file Roman history study apps (index.html) in Chrome on this Windows box.
---

# Testing the Roman-history single-file study apps

## Devin Secrets Needed
- none

## App under test
- Each repo is one self-contained `index.html` (no build, no server). Open it directly: `file:///C:/Users/Administrator/repos/<repo>/index.html`.
- The Vercel preview URLs are behind Vercel deployment protection (redirect to a login page) — there is no way in without credentials; test the local `file://` copy of the checked-out branch instead (verify with `git log` that the fix commits are present).
- Chrome binary: `C:/devin/chrome/chrome-win64/chrome`. MS Edge is also installed.
- No login, no backend, no fixtures — all data is inline in `DATA` (emperors/people, battles, wars, glossary, eras).

## Shell quirks on this Windows/MSYS box
- Default cwd `/home/ubuntu` does not exist — always pass `workdir` (e.g. the repo path) to exec calls.
- `wc` and GNU `sort -u` are unavailable (Windows `sort.exe` shadows it). Count with `grep -c 'id:"'`; dedupe with `awk '!seen[$0]++'`.
- To verify UI claims (e.g. "134 battles / 84W/43L/7D"), grep the inline `DATA` in index.html rather than trusting the UI — e.g. count `result:"victory"` occurrences inside the battles array.
- App2's emperor array is split: ~63 inline `emperors:[...]` plus a later `DATA.emperors.push(...[...])` merge — grep both when counting.

## UI testing notes
- Views are switched by nav buttons (`data-view`): tree, timeline, battles, glossary, quiz, prep, cram, compare. View ids: `#view-<name>`.
- Family Tree: `.tree-stage` is `height:74vh; overflow:hidden`. The +/−/Fit buttons (`.tree-zoom`) are pinned top-right of the stage — verify they are visible without scrolling. Wheel over the stage also zooms (×1.12 toward the cursor); drag pans. `TREE_MIN_SCALE` controls how far fit-to-width can shrink a very wide tree (~16k px on Empire).
- "Focus dynasty…" (`#focusDyn`) re-renders via `renderActiveViews`, which now calls `fitTree()` after `renderTree()` — a filtered tree always re-fits the stage. If a tree ever appears blank, check the transform first before calling it a bug.
- Quiz: 10/20/40 round start via `data-qz` buttons; answering disables options, highlights correct green / wrong red, reveals `#qzExplain` + `#qzNext`, and updates score + the `.qp` progress dot inline. Deliberately pick a wrong answer to exercise the red path.
- Glossary "Filter terms…" (`#glossSearch`) filters live; a no-match query (`zzzzz`) should leave a clean empty grid.
- NJCL Prep "eras at a glance" strip uses flex-grow widths (+ `overflow-x:auto`) so all segments incl. 1-year eras stay visible — a good edge case to look for (e.g. Year of the Four Emperors AD 69 on Empire, 'End of the Republic' sliver on M&R).
- `browser_console` tool output only shows logs from YOUR injected script, not the page's console — for a real JS-error check open DevTools (F12) and read the Console tab.
- file:// analytics noise (expected, resolves on Vercel): M&R has `<script defer src="/_vercel/insights/script.js">` → 1 console 404 on file://. Empire has `<script defer src="https://cdn.vercel-insights.com/v1/script.js">` (added in the audit commit) → 2 console errors on file:// (CORS + ERR_FAILED on the `/_vercel/insights/view` beacon). Count only OTHER red errors as real failures.
- The header `#search` box: clicking it while a side panel is open only closes the panel and eats the click — click it a second time before typing. Search results open the figure's side panel via `openPanel`.
- Battle question stems: enemy-type `…was fought against which enemy?` and commander-type `Who commanded Rome at the Battle of …?` both strip a trailing embedded year and avoid a second "Battle of" prefix — "Battle of Battle of X" or "(432) (432 AD)" doublings are the regression signature to watch for.
