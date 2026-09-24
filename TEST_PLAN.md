# Test Plan — Roman History Study Apps (mega content expansion)

Two offline single-file apps opened directly in Chrome via file:// URLs. No server needed.
- App1 (Monarchy & Republic): `file:///C:/Users/Administrator/repos/roman-history-monarchy-republic-njcl-guide/index.html`
- App2 (Empire): `file:///C:/Users/Administrator/repos/roman-history-empire-njcl-guide/index.html`

Both on branch `devin/1790211362-mega-content-expansion`. App2 runs FIRST (bigger dataset, lead asked for extra scrutiny). One continuous recording.

## Expected values (verified from source)
| | App1 | App2 |
|---|---|---|
| Battles | 134 (84V/43L/7D) | 89 (60V/26L/3D) |
| War groups | ~50 | ~56 |
| Glossary terms / cats | ~97 / 7 | 83 / 7 |
| Emperors+kings / people | 32 / ~191 | 177 (63+114 merged) / 122 |
| Key battles | Cannae (defeat, 216 BC), Zama (victory, 202 BC) | Teutoburg Forest (defeat, AD 9), Adrianople 378 (defeat, Valens killed) |

## Test sequence

### App2 — Empire (primary focus)
1. **Tree fit-to-width on load**: page loads already on tree view. PASS = entire ~16,000px-wide tree visible as tiny fitted bands spanning full stage width (all era bands from Augustus→1453 visible top-to-bottom with scroll). FAIL = only a slice of the tree visible horizontally at unreadable zoom (TREE_MIN_SCALE=0.62 would show ~60% blank/clipped). Then click `+` (zoomIn) several times until node text is readable → click a node → side panel (`#panel.open`) slides in with emperor name/details. Then `Focus dynasty…` dropdown → pick e.g. "Julio-Claudian" → tree narrows to that dynasty only. Then Reset.
2. **Console check**: `browser_console` — PASS = no red JS errors.
3. **All 8 tabs render**: click each of Timeline, Battles & Wars, Glossary, Quiz, NJCL Prep, Cram Sheet, Compare — each view renders content (not blank). PASS = 8/8 render.
4. **Battles & Wars (App2)**: stat header shows `89` Battles, `60` Victories, `26` Defeats, `3` Indecisive + proportion bar (green/red/amber segments). Tables grouped by war with colored era chips + `N battles · range · xW/yL` meta. Spot-check: Teutoburg Forest row (AD 9, Defeat, "P. Quinctilius Varus (killed)" vs Arminius); Adrianople (378) row (Defeat, Valens killed, First Gothic War group). Also Adrianople (324) exists as victory under "Constantine vs. Licinius".
5. **Glossary (App2)**: 7 category cards render (Power & Titles, Court & Bureaucracy, Army, Law & Economy, Church, Peoples, Buildings & Roads). Type `legate` in Filter terms → only matching terms stay visible, non-matching cards/terms hide live. Type `zzzzz` → all cards hide (edge case). Clear → all return.
6. **Quiz (App2)**: click `10` → round starts, "Question 1 / 10", "Score: 0", 10 empty progress dots, 4 option buttons. Click a WRONG answer deliberately → PASS requires: score stays `Score: 0` and updates the text inline immediately, first progress dot turns red/wrong-colored IMMEDIATELY (before Next is pressed — this is the fixed bug), the correct option turns green + clicked option turns red, explanation panel appears with "Answer: …" and a `Next →` button. Click Next → Question 2 renders with dot-1 colored. Then answer Q2 correctly (if identifiable) or just verify a second answer registers; verify no malformed option buttons (empty/undefined text) across questions seen. Finish not required — verify Next advances; optionally click through to finish if fast.
7. **NJCL Prep (App2)**: "eras at a glance" strip renders with colored segments incl. very small ones (e.g. Year of Four Emperors 69, tiny late-era segments) + era detail cards below with chip+years+summary+NJCL note. "★ Top figures to know cold" chips clickable → click one → side panel opens.
8. **Dark mode (App2)**: click `☾ Theme` → `data-theme="dark"`; verify battles table, glossary cards, quiz card all render dark (bg dark, text light, victory/defeat colors readable). Toggle back to light.
9. **Final console check**: no JS errors accumulated.

### App1 — Monarchy & Republic (same flow, compressed)
10. Navigate to App1 file:// URL. Tree loads fitted to width (App1 tree narrower so less dramatic, but whole width must be visible). Zoom `+` to readable, click node → panel. Focus dynasty → works.
11. **Battles (App1)**: header `134` Battles, `84` Victories, `43` Defeats, `7` Indecisive + bar. Cannae row: 216 BC, Defeat, vs Hannibal. Zama row: 202 BC, Victory, Scipio Africanus. War groups + era chips render.
12. **Glossary (App1)**: 7 category cards; filter `tribune` live-filters.
13. **Quiz (App1)**: start 10-round, answer Q1 wrong → inline score/dot update + red/green highlight + explanation; Next advances to Q2.
14. **Prep (App1)**: era strip + detail cards render; top-figure chip opens panel.
15. **Dark mode (App1)**: toggle works on new views; toggle back.
16. **Console**: no errors.

## Failure criteria
- Any tab blank or throwing; wrong stat numbers; bar missing; missing war rows; filter not live; score/dot not updating inline until Next; unreadable-at-load tree (clipped horizontally); Focus dynasty not narrowing; panel not opening; dark mode not applying to new views; any console error.
