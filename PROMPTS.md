# T1 · The file-drawer meter — the prompts

> **If the AI stops answering** (silent for 5 minutes, or "Free usage limit reached" / "rate limit"): press Esc, pick
> the next model in this order and paste the same prompt again: 1 OpenCode's free model, 2 your OpenRouter key
> (`openrouter/nvidia/nemotron-3.5-super-vl:free`), 3 NVIDIA, 4 Groq, 5 the optional ones. Set the keys up at home
> first with **AI-BACKUP.md** (in the Hackathon 3 folder). All of Step 1 (1a to 1c) used 17-38 AI requests in our tests.


Copy each prompt exactly into OpenCode (one prompt at a time; wait until it is done; check; then the next).
Your project folder has `index.html`, `idea-card.md`, `AGENTS.md`, `sample-data/` and `.opencode/skills/file-drawer-apis/`.

**How to look at your page:** the tool calls real APIs, so open it through a small local web server, not by
double-clicking. In a terminal in your project folder run `python -m http.server 8000`, then open
`http://localhost:8000` in your browser. Reload (F5) after every change. (Double-clicking also works for the
saved sample data, and usually for the live APIs too, but the web server is what Vercel does.)

## Step 0 — make a clean copy (once)

> Copy index.html to index-START.html. Change nothing else.

Step 1 builds the tool in three small prompts (1a, 1b, 1c). Small prompts are faster and use fewer of your free
AI requests. Do them in order, and check the page after each one.

## Step 1a — The core: inputs, API calls and the big numbers (1–3 minutes of waiting)

> Start from a clean file: copy index-START.html over index.html. Then read AGENTS.md, idea-card.md and the skill file-drawer-apis (in .opencode/skills/file-drawer-apis, read SKILL.md and LESSONS.md). Do not open sample-data/sample.js (it is large and you do not need it). Edit index.html: replace the example inside the TOOL block (between the lines '===== EDIT BELOW' and '===== EDIT ABOVE') with the core of the tool in idea-card.md: the meta (title and question), the four inputs with exactly the card's ids, the three presets, and a run() that makes the three API calls A, B and C exactly as written on the card, follows "The rules" 1 to 5 exactly (collect, merge repeats, OSF link first, search and the accept rule, years to paper), and returns the five cards with exactly the card's labels. Also return the list of studies as studies (one object per study with everything the table on the card needs), so the chart and the table can be added in the next step. Make every internet call with api.getJSON(url); never call fetch yourself. Do not make the chart or the table yet. For takeaway write a short placeholder sentence, and for methodology a placeholder 'how' with empty assumptions and references. Do not change anything below the line 'DO NOT EDIT BELOW THIS LINE'. Do not open the page in a browser (I will check it myself). Tell me in 2 short sentences what you changed.

Then reload the page, tick **Use saved sample data**, click the preset **Anchoring 2017–2018**, press **Run**.
- **Five cards appear** (no chart yet, that is fine). Studies checked should say **20**: go to Step 1b.
- **A red error box** or a blank page: use the error prompt at the bottom.
- **It still shows the example** (a word search): send the Step 1a prompt again.

## Step 1b — The chart and the table (1–3 minutes of waiting)

> First copy index.html into a folder called backups, with the next free name (backup-1.html, backup-2.html, …). You already read the idea card and the skill; do not read them again unless you need a detail. Do not open sample-data/sample.js. In the TOOL block of index.html, change run() so that it also returns the chart and the table exactly as described under "Chart" and "Table" in idea-card.md, built from the list of studies run() already makes: a line chart from 0 to 100 with x = 0, 1, … years since registration (up to api.asOfYear minus the latest registration year), two series named exactly 'Paper found' and 'Paper or preprint found', the card's chart title, and one table row per study with links to OSF and to the DOI. Do not change the API calls, the matching rule or the cards. Change only the TOOL block. Do not open the page in a browser (I will check it myself). Tell me in 2 short sentences what you changed.

Then reload the page, tick **Use saved sample data**, click **Anchoring 2017–2018**, press **Run**.
- **The five cards are still there, a chart with two lines appears, and "Every result, one row each" has 20 rows:** go to Step 1c.
- **A red error box, or the cards changed:** use the error prompt at the bottom.

## Step 1c — The takeaway, the methodology and the references (1–3 minutes of waiting)

> First copy index.html into a folder called backups, with the next free name (backup-1.html, backup-2.html, …). You already read the idea card and the skill; do not read them again unless you need a detail. Do not open sample-data/sample.js. In the TOOL block of index.html: (1) replace the placeholder takeaway with the sentence under "Takeaway" in idea-card.md, calculated from the result r (the real topic, number of studies and percentages, never fixed text); it must say that 'not found' does not mean 'not published' and that every 'found' should be opened and checked. (2) With 0 studies, run() must not crash: the cards show Studies checked 0, there is no red box, and the takeaway says that nothing matched and suggests a broader topic or more years. (3) Write the methodology: 'how' and 'assumptions' from the idea card (including both honest limits), and in references exactly the three references on the card, copied word for word, nothing added. Do not change the API calls, the matching rule, the cards, the chart or the table. Change only the TOOL block. Do not open the page in a browser (I will check it myself). Tell me in 2 short sentences what you changed.

Then reload the page, tick **Use saved sample data**, click **Anchoring 2017–2018**, press **Run**.
- **Under the chart a sentence with the real numbers** (20 studies, 30%, 40%) and **a methodology with exactly the card's three references:** go to Step 2.
- **A red error box, or the numbers changed:** use the error prompt at the bottom.

## Step 2 — The NUMBER CHECK (the most important step)

Open `idea-card.md`, scroll to **NUMBER CHECK**, and do every row (sample-data rows first, then the live row).
If a row is WRONG, send:

> First copy index.html into a folder called backups, with the next free name (backup-1.html, backup-2.html, …). Then: in my tool, with the settings ________ (sample data: yes/no), the card or chart value "________" shows ________, but idea-card.md says it should be ________. Read the idea card and the skill file-drawer-apis again, find the mistake in run(), and fix it. Change only the TOOL block. Tell me what was wrong. If the skill's LESSONS.md does not have this mistake yet, add it there.

Check **all** rows again after each fix. Also open the results table ("Every result, one row each") and click
three of the "found" papers: is it really the registered study? Write down what you see — that is your own
precision check.

## Step 3 — Make the words clear

> First copy index.html into a folder called backups, with the next free name (backup-1.html, backup-2.html, …). Then rewrite the texts in the TOOL block (the question, the input help texts, the card notes, the takeaway and the methodology) so that a first-year student with no statistics background understands them. Short sentences, no jargon, explain any technical word in brackets. Do not change any numbers, rules, API calls, ids or references, and keep the takeaway calculated from the results (do not replace it with fixed text). The takeaway must still say that 'not found' does not mean 'not published'. Change only the TOOL block.

Then do the NUMBER CHECK once more: the numbers must not have changed. Check the references: exactly the ones on
the card, nothing added.

## Step 4 — Make it yours (bonus)

> First copy index.html into a folder called backups, with the next free name (backup-1.html, backup-2.html, …). Then add this to my tool: "COPY THE 'MAKE IT YOURS' TEXT FROM idea-card.md HERE". Keep everything that already works. Change only the TOOL block. Tell me how I can check that the new part is correct.

## If something goes wrong — the error prompt

> The page shows this error: "PASTE THE ERROR HERE". Read the skill file-drawer-apis (SKILL.md and LESSONS.md) and fix it. Change only the TOOL block. Tell me the cause in one sentence. If the skill's LESSONS.md does not have this problem yet, add it there.

| You see | Do this |
|---|---|
| "Too many requests (429)" | Many people share the network. Wait one minute and press Run again (answers you already have are kept), or tick **Use saved sample data**. |
| "Failed to fetch" on every call | The AI used `api.osf.io`, which blocks web pages. Send the error prompt: the skill says to use SHARE. |
| "This call is not in the saved sample data" | The AI changed a call. Send the error prompt. |
| The page is blank | Send: "The page is blank. Open index.html and find the error in the TOOL block. Change only the TOOL block." |
| OpenCode says "Free usage limit reached", "rate limit", or is silent for 5 minutes | Press Esc, pick the next model in the order at the top of this page (OpenCode free, OpenRouter, NVIDIA, Groq, optional; see AI-BACKUP.md) and send the same prompt again. Your files are not lost. |
