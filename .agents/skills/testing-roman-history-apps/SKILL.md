---
name: testing-roman-history-apps
description: How to set up and end-to-end test the offline single-file Roman history study apps (index.html) in Chrome on this Windows box.
---

# Testing the Roman-history single-file study apps

## Devin Secrets Needed
- none

## App under test
- Each repo is one self-contained `index.html` (no build, no server). Open it directly: `file:///C:/Users/Administrator/repos/<repo>/index.html`.
- Chrome binary: `C:/devin/chrome/chrome-win64/chrome`. MS Edge is also installed.
- No login, no backend, no fixtures — all data is inline in `DATA` (emperors/people, battles, wars, glossary, eras).

## Shell quirks on this Windows/MSYS box
- Default cwd `/home/ubuntu` does not exist — always pass `workdir` (e.g. the repo path) to exec calls.
- `wc` and GNU `sort -u` are unavailable (Windows `sort.exe` shadows it). Count with `grep -c 'id:"'`; dedupe with `awk '!seen[$0]++'`.
- To verify UI claims (e.g. "134 battles / 84W/43L/7D"), grep the inline `DATA` in index.html rather than trusting the UI — e.g. count `result:"victory"` occurrences inside the battles array.
- App2's emperor array is split: ~63 inline `emperors:[...]` plus a later `DATA.emperors.push(...[...])` merge — grep both when counting.

## UI testing notes
- Views are switched by nav buttons (`data-view`): tree, timeline, battles, glossary, quiz, prep, cram, compare. View ids: `#view-<name>`.
- Family Tree: `.tree-stage` is `height:74vh; overflow:hidden`, so the +/−/Fit zoom buttons sit below the fold — use `scroll` (wheel) over the stage instead: wheel-up zooms in ×1.12 toward the cursor; drag pans. `TREE_MIN_SCALE` controls how far fit-to-width can shrink a very wide tree.
- "Focus dynasty…" (`#focusDyn`) re-renders the tree but does NOT re-fit — if the user had zoomed first, the filtered tree can appear off-screen/blank; Fit or Reset recovers. Treat blank-after-focus as "check the transform" before calling it a bug.
- Quiz: 10/20/40 round start via `data-qz` buttons; answering disables options, highlights correct green / wrong red, reveals `#qzExplain` + `#qzNext`, and updates score + the `.qp` progress dot inline. Deliberately pick a wrong answer to exercise the red path.
- Glossary "Filter terms…" (`#glossSearch`) filters live; a no-match query (`zzzzz`) should leave a clean empty grid.
- NJCL Prep "eras at a glance" strip uses `Math.log10` flex widths so 1-year eras stay visible — a good edge case to look for (e.g. Year of the Four Emperors, AD 69).
- `browser_console` tool output only shows logs from YOUR injected script, not the page's console — for a real JS-error check open DevTools (F12) and read the Console tab.
- file:// caveat: a `<script defer src="/_vercel/insights/script.js">` tag (present in App1, absent in App2) always 404s on file:// — expected noise, resolves only on the Vercel deployment.
- Known cosmetic wart: enemy-type quiz questions template the stem as `The Battle of ${b.name}` and can double the prefix ("The Battle of Battle of …") when a battle's name already starts with "Battle of".
