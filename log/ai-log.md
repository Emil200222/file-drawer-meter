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

## 2026-11-16 — GitHub repo + Vercel deployment
- Asked: get access to GitHub, authenticate, create a repo, and link it to Vercel; set up Vercel following vercel.com/get-started.md.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Done: installed GitHub CLI (2.104.0) and Vercel CLI (65.16.0); authenticated GitHub as Emil200222 (device flow, student completed it) and Vercel as emil200222; installed the 9 vercel-labs/agent-skills globally for OpenCode (the vercel plugin needs OpenCode 2, machine has 1.19.5, so the playbook's fallback skills were used) and added the Vercel MCP server (https://mcp.vercel.com) to the global opencode config; git init in T1-file-drawer-meter (commits use the GitHub noreply address, not the student's real email); created public repo Emil200222/file-drawer-meter and pushed; linked the folder to Vercel project emil-388f/file-drawer-meter and deployed to production: https://file-drawer-meter-nu.vercel.app (verified: serves the tool, HTTP 200 via vercel curl).
- Could not do: the GitHub-to-Vercel auto-deploy connection ("vercel git connect") — it needs the Vercel GitHub App installed on the student's GitHub account, a browser consent step. Until then the site updates only via "vercel deploy --prod" from the folder. The deployment also has Vercel Authentication on (the student may turn it off in Project Settings → Deployment Protection to share the link publicly).
- Note: vercel created .env.local (OIDC token, local only) and appended .vercel/.env* to .gitignore (committed).

## 2026-11-16 — Vercel agent setup + upload of the S&P 500 vs OMXS30 project
- Asked: follow vercel.com/get-started.md to set up Vercel; then connect GitHub and Vercel, make them auto-sync, and upload the student's "S&P 500 vs OMXS30" project.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Done (Vercel setup): vercel whoami verified (emil200222); Vercel guidance installed as 9 standalone skills from vercel-labs/agent-skills, global for OpenCode (plugin needs OpenCode 2; CLI here is 1.19.5); Vercel MCP server added to the global OpenCode config (https://mcp.vercel.com) — MCP sign-in still pending for the student (/mcps).
- Done (S&P 500 vs OMXS30): found the student's file at MetaScienceCourse-Hackathon-2-tasks\hackathon2\F2-saving-and-borrowing\my-simulation-SPX-vs-OMXS30.html (self-contained, no external files); copied it to a new folder C:\Users\Emilw\Downloads\sp500-vs-omxs30 as index.html (the original kit file untouched); created public repo Emil200222/sp500-vs-omxs30 and pushed; linked and deployed to production https://sp500-vs-omxs30.vercel.app (verified: serves the page, title "S&P 500 vs OMXS30 — historical returns").
- Could not do: the GitHub-to-Vercel auto-sync for both projects — "vercel git connect" fails until the student installs the Vercel GitHub App on GitHub (a browser consent step only they can do). After that, one command per project finishes it; until then, updates go live via "vercel deploy --prod" from each folder.
- Note: both deployments have Vercel Authentication (deployment protection) on; the student can turn it off in each project's settings to share the links with classmates.

## 2026-11-16 — GitHub-Vercel auto-sync connected (both projects)
- Asked: after the student installed the Vercel GitHub App, connect both repos so every push deploys automatically.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Done: "vercel git connect" succeeded for Emil200222/file-drawer-meter → project emil-388f/file-drawer-meter, and Emil200222/sp500-vs-omxs30 was already connected to its project. This log entry is pushed right after, and its Vercel deployment (triggered by the push, not by the CLI) is the check that auto-sync works.
- Nothing else changed: no tool code touched.

## 2026-11-16 — S&P 500 vs OMXS30: history extended to 1930 (per student request), tested and deployed
- Asked: make the start year 1930; then test/validate/modify "only for the S&P" as I see fit.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Done: sourced 1930-1986 data (Damodaran/NYU Stern for US returns + T-bills; Frennberg & Hansson via Riksbank vol 2 ch 6 Tables A6.2/A6.3 for Swedish price index, CPI and bill yields; Minneapolis Fed for US CPI), extended the sim's arrays to 95 years, start year slider and "Full history" preset now 1930; fixed two edge-case bugs (leveraged pot going negative now treated as a margin call at 0; IRR card shows "—" for a 0-year window); the page's numbers verified against an independent Node recomputation to the dollar; salary saving honestly 0 pre-1987 because no citable wage series could be fetched (SSA blocks automated downloads) — disclosed in the page. Full entry in the sp500-vs-omxs30 repo's own log/ai-log.md.
- Deployment: push to Emil200222/sp500-vs-omxs30 auto-deployed on Vercel (new Ready production build, no manual deploy); live site re-verified at 1930 with zero console errors.

## 2026-11-16 — S&P 500 sim: model quality upgrade
- Asked: "make the model as good as it can be".
- Done: recorded year-by-year Swedish dividends 1930-1986 (Riksbank Table A6.2) replace the constant estimate; mid-year salary contributions; new yearly-fee slider (default 0.2%); new worst-year/max-drawdown card; new reproducible bootstrap card ("If history were reshuffled", 1,000 runs, fixed seed). Two bootstrap bugs (currency mismatch in the win count, kronor shown as dollars) were caught by an independent Node replica before deployment and fixed. Verified card-by-card against the replica, zero console errors; push auto-deployed on Vercel. Full entry in the sp500-vs-omxs30 repo's log/ai-log.md.
