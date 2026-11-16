# T1 · The file-drawer meter

**Tool id:** `file-drawer-meter`  ·  **APIs:** SHARE (OSF's search index) + Crossref  ·  **Keys needed:** none

**The question:** What share of preregistrations ever become a published paper?

## The idea in plain words
When a study is preregistered, the plan is public before the data exist. So we can count the studies that were
planned and then check which of them were ever published. The ones never published are the "file drawer"
(Rosenthal, 1979): if they are mostly null results, the published literature looks stronger than the truth
(Franco et al., 2014). The tool takes a topic and registration years, lists the OSF preregistrations, and looks
for each one's paper: first a link that OSF itself holds, then a search in Crossref by title and authors. It
shows the share with a paper found, by years since registration.

Two honest limits are part of the result: **"not found" does not mean "not published"** (titles change, some
studies appear inside bigger papers), and **"found" does not prove it is the same study** (the match is by title
words and surnames). So the share is a lower bound, and every match is shown with a link to check.

## The exact API calls (use these shapes exactly; the saved sample data matches them)
Every call goes through `await api.getJSON(url)`.

**A. Registrations, one call per year** (SHARE; `api.osf.io` cannot be used: it blocks web pages)
```
https://share.osf.io/trove/index-card-search?cardSearchText=<topic>&cardSearchFilter[resourceType]=Registration&cardSearchFilter[dateCreated]=<YYYY>&page[size]=100&acceptMediatype=application/vnd.api%2Bjson
```
From the answer: `data.attributes.totalResultCount`; the registrations are the `included` items with
`type === "index-card"`, each with `attributes.resourceMetadata` = m:
- `m["@id"]` = OSF link · `m.title[0]["@value"]` = title (turn `&amp;` into `&`) · `m.dateCreated[0]["@value"]` = registration date
- authors: for each `c` in `m.creator`: `c["foaf:familyName"][0]["@value"]`, or else the last word of `c.name[0]["@value"]`
- OSF's own paper link: the `"@id"` of each item in `m.hasPapersResource` and `m["dcterms:relation"]` (DOI links; drop `https://doi.org/`; ignore DOIs starting `10.17605/`)

**B. Details of the DOI that OSF links** (only when there is one; use the first)
```
https://api.crossref.org/works/<doi>
```
From the answer: `message.type`, `message.issued["date-parts"][0][0]` (year), `message.title[0]`. If it fails, keep the DOI with year unknown.

**C. Search for the paper** (when OSF links no paper, or only a preprint)
```
https://api.crossref.org/works?query.bibliographic=<title>&filter=from-pub-date:<registration date YYYY-MM-DD>&rows=5&select=DOI,title,author,issued,type&query.author=<up to 3 surnames>
```
From the answer: `message.items[]` with `DOI`, `title[0]`, `author[].family`, `issued["date-parts"][0][0]`, `type`.

## The rules (exactly what run() must do)
1. **Collect** the registrations of every year from `fromYear` to `toYear` (call A). Remove duplicates by OSF id.
   Sort by date, then id.
2. **Merge repeats:** registrations with the same title (same content words, see 4) and at least one shared
   surname are one study; keep the earliest date; keep all their OSF links. Then keep the first `maxRegs` studies.
3. **OSF link first:** if the study has an OSF paper link, get its year and type (call B). A paper → done.
4. **Otherwise search** (call C) and test each of the 5 candidates:
   - content words of a title: lower case, accents removed, only letters/digits, 3+ letters, minus the stop-words
     a an the of and or in on for to with by from at as is are be study effect effects using via vs versus its
     their into between among do does how what when why role pre registration preregistration registered report
     protocol replication experiment part
   - `ts` = share of the registration title's content words found in the candidate title
   - `a` = number of shared surnames (3+ letters, accents removed; ignore "jr", "lab", "good")
   - **accept** if (`a ≥ 3` and `ts ≥ 0.30`) or (`a ≥ 2` and `ts ≥ 0.45`) or (`a ≥ 1` and `ts ≥ 0.80` and the registration title has 3+ content words)
   - **kind:** preprint = type `posted-content`, or DOI starting `10.2139/ssrn`, `10.31234/`, `10.31219/`; paper = journal-article, proceedings-article, book-chapter, report, dissertation, monograph, book; any other type is never accepted
   - several accepted: paper before preprint, then higher `ts` (rounded to 0.1), then earliest year
   - keep an OSF-linked preprint unless the search finds a paper
5. **Years to paper** = publication year − registration year (0 or more).

## Inputs

| id | label | type | start value | min | max | help |
|---|---|---|---|---|---|---|
| `topic` | Topic (words to search OSF registrations for) | text | anchoring | | | Searches titles, descriptions and keywords |
| `fromYear` | Registered from year | number | 2017 | 2012 | 2026 | First registration year |
| `toYear` | Registered to year | number | 2018 | 2012 | 2026 | Last registration year |
| `maxRegs` | Check at most this many registrations | number | 40 | 1 | 100 | One Crossref call each, about 1.2 s per call |

## Cards (the big numbers at the top) — use exactly these labels
- **Studies checked** = number of studies after merging (note: from how many registrations, how many merged)
- **Paper found** = % of studies with a paper (one decimal), note "k of n"
- **Paper or preprint found** = % of studies with a paper or a preprint (one decimal)
- **Linked by OSF itself** = number of studies whose OSF page names a paper or preprint
- **Median years to paper** = median of years to paper, papers only ("–" if none)

## Chart
Line chart, y from 0 to 100. x = k = 0, 1, …, (asOfYear − latest registration year), where `asOfYear` is
`api.asOfYear`. Two series, named exactly **Paper found** and **Paper or preprint found**: for each k, the
% of all studies with a paper (or paper or preprint) whose years to paper ≤ k. Title: "Share with a paper found,
by years since registration".

## Table
One row per study: registration title (link to OSF; add "(+N more with this title)" for merged ones),
registration date, found (paper / preprint / nothing found), how (OSF link / Crossref match), the DOI (link to
`https://doi.org/<doi>`), publication year, and "title words % / shared surnames".

## Presets (buttons)
- **Anchoring 2017–2018**: topic = anchoring, fromYear = 2017, toYear = 2018 — a classic judgment-and-decision-making effect
- **Ego depletion 2017–2018**: topic = ego depletion — an effect at the centre of the replication crisis
- **Growth mindset 2017–2018**: topic = growth mindset — an education intervention with many preregistered trials

## Takeaway (the sentence under the chart)
"Of 20 preregistered studies about 'anchoring' on OSF, we found a published paper for 30% and a paper or preprint
for 40%. This is a lower bound: 'not found' does not mean 'not published' (in a hand-check, this matching missed
about 4 in 10 registrations that OSF says were published), and every 'found' should be opened and checked."
Use the real numbers from the result, not fixed text. With 0 studies say that nothing matched and suggest a
broader topic or more years.

## References (put exactly these in methodology.references; each checked in Crossref on 2026-11-15)
- Ensinck, E. N. F., & Lakens, D. (2025). An inception-cohort study quantifying how many registered studies are publicly shared. *Advances in Methods and Practices in Psychological Science, 8*(1). https://doi.org/10.1177/25152459241296031
- Franco, A., Malhotra, N., & Simonovits, G. (2014). Publication bias in the social sciences: Unlocking the file drawer. *Science, 345*(6203), 1502–1505. https://doi.org/10.1126/science.1255484
- Rosenthal, R. (1979). The file drawer problem and tolerance for null results. *Psychological Bulletin, 86*(3), 638–641. https://doi.org/10.1037/0033-2909.86.3.638

## NUMBER CHECK — do this yourself after Step 1

**Rows 1–3: with saved sample data** (tick "Use saved sample data"; click the preset; press Run). These numbers
are exact: the sample is a snapshot from 2026-11-15, so every digit must match.

| Preset (sample data) | The screen must show |
|---|---|
| Anchoring 2017–2018 | Studies checked **20** (from 24 registrations, 4 merged) · Paper found **30%** · Paper or preprint **40%** · Linked by OSF itself **1** · Median years to paper **2.5** · curve "Paper found": **10** at 0 years, **15** at 2, **30** at 8 |
| Ego depletion 2017–2018 | Studies checked **24** · Paper found **33.3%** · Paper or preprint **33.3%** · Linked by OSF **0** · Median **3** · curve "Paper found": **0** at 0, **20.8** at 3, **33.3** at 8 |
| Growth mindset 2017–2018 | Studies checked **11** (from 22 registrations, 11 merged) · Paper found **27.3%** · Paper or preprint **27.3%** · Linked by OSF **0** · Median **4** · curve "Paper found": **18.2** at 4, **27.3** at 8 |

**Row 4: live** (untick "Use saved sample data"; preset Anchoring 2017–2018; Run; takes about 30 seconds).
Live data change, so: Studies checked 20 ± 3 · Paper found 30% ± 10 points · Paper or preprint 40% ± 10 points
(measured 2026-11-15: 20 · 30% · 40%).

**Row 5: nothing matches** (live): topic `zqxvbnm`, 2018 to 2018 → Studies checked 0, no red box, and the
takeaway says nothing matched.

**Your own precision check:** open the results table, click 3 "found" papers. Is it the registered study?
(Hint: in the Ego depletion preset, two "found" papers are a review by the same team, *Is Ego Depletion Real?*,
not the registered study. The meter cannot tell; you can.)

## Make it yours (Step 4)
Add an input `which` (number, 0 to 2, start 0, label "Which studies: 0 = all, 1 = only replications, 2 = no
replications"). A replication is a study whose title contains "replicat" (any case). Apply it right after
merging, before `maxRegs`. Check with sample data: Ego depletion, which = 1 → Studies checked 4, Paper found 50%;
which = 2 → 20 studies, Paper found 30%. Anchoring, which = 1 → 3 studies, Paper found 0%; which = 2 → 17
studies, Paper found 35.3%.

## Level 1 → 2 → 3: from a class tool to a community tool
- **Level 1 (today):** the meter above, one topic, ≤ 40 studies, keyless, with the honest caveats and the
  hand-check table.
- **Level 2 (a better estimate):** (a) correct for misses: report "found ÷ 0.6" next to "found" with the
  hand-check that justifies 0.6, and let users mark rows right/wrong to measure their own precision; (b) use the
  registration's description and keywords, not only the title, and DataCite's `relatedIdentifiers`; (c) compare
  fields (subjects), templates (AsPredicted vs OSF Preregistration vs Registered Report) and replications vs
  originals; (d) compare with the 58–62% benchmark of Ensinck & Lakens (2025).
- **Level 3 (the bridge to Hackathon 2):** feed the measured unpublished share into a publication-bias
  simulation: simulate k studies of a true effect d with n per group, let only the published share reach the
  meta-analysis (with null results more likely to stay in the drawer), and show how much the meta-analytic
  estimate is inflated. The file-drawer meter then gives the simulation a measured input instead of a guess.
