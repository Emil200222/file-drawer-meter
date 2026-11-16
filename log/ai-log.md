# AI-use log

One entry per task: date, what I asked, which model, files read and written, anything it could not do.

## 2026-11-16 — Build the core of the file-drawer meter
- Asked: start from index-START.html; replace the example in the TOOL block with the idea card's core (meta, 4 inputs, 3 presets, run() with calls A/B/C and rules 1–5, the five cards, studies list). No chart, no table yet.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Read: AGENTS.md, IDEA-CARD.md, skill/file-drawer-apis/SKILL.md, skill/file-drawer-apis/LESSONS.md, index.html/index-START.html.
- Written: backups/backup-1.html (old index.html), index.html (TOOL block replaced), index-START.html (copy of original, from the earlier task), log/ai-log.md (this entry).
- Could not do / not done: did not open the page or run the NUMBER CHECK (the student checks it); chart, table, final takeaway and methodology references are left for the next step. Did not open sample-data/sample.js, as instructed.
- Note: before overwriting, the previous index.html was copied to backups/backup-1.html (rule 8).

## 2026-11-16 — Add the chart and the table to run()
- Asked: make run() also return the idea card's chart (line, 0–100, x = years since registration up to api.asOfYear − latest registration year, series "Paper found" and "Paper or preprint found") and the one-row-per-study table with OSF and DOI links. No changes to API calls, matching rule or cards.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Read: index.html (TOOL block). Written: backups/backup-2.html (previous index.html), index.html, log/ai-log.md (this entry).
- Could not do / not done: did not open the page or check the NUMBER CHECK rows (the student checks); takeaway and methodology references still placeholders.

## 2026-11-16 — Real takeaway, 0-studies case, real methodology
- Asked: replace the placeholder takeaway with the idea card's sentence (real numbers, both honest caveats; 0 studies → "nothing matched" + suggestion), and write the methodology how/assumptions (both honest limits) with exactly the card's three references. No changes to API calls, matching rule, cards, chart or table.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Read: index.html (TOOL block). Written: backups/backup-3.html (previous index.html), index.html, log/ai-log.md (this entry).
- Checked: a Node smoke test with a mocked api (no browser, no internet) — merge, OSF-link and Crossref paths, chart, table, both takeaway branches, and the 0-studies run (Studies checked 0, no error, "nothing matched" takeaway) all behaved.
- Not done: the real NUMBER CHECK with the saved sample data (the student checks it in the browser).

## 2026-11-16 — Look at the page in a browser (sample data)
- Asked: show what the page looks like in a browser.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Done: served the folder on localhost (tiny Node static server) and opened index.html?sample=1&autorun=1 in the in-app browser; took a full-page screenshot. Written: nothing in the kit except this log entry (screenshot is in a temp folder).
- Result with saved sample data, preset Anchoring 2017–2018: Studies checked 20 (from 24 registrations, 4 merged) · Paper found 30% (6 of 20) · Paper or preprint found 40% (8 of 20) · Linked by OSF itself 1 · Median years to paper 2.5 — matches the idea card's NUMBER CHECK row 1 exactly. No console errors.
- Note: this browser is automated, so live mode was not tried here (SHARE can refuse automated browsers with 403 — see LESSONS.md); the student should check live mode in a normal browser window.
