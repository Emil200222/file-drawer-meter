---
name: file-drawer-apis
description: How to find OSF preregistrations (SHARE search index) and the papers that came out of them (Crossref) from a static web page, correctly and politely. Use before writing, changing or fixing any call to share.osf.io or api.crossref.org, or when the file-drawer meter shows an error, a wrong number, or "Failed to fetch". Self-learning - reads LESSONS.md first and adds to it at the end.
---

# File-drawer APIs: SHARE (registrations) + Crossref (papers)

## Step 0 — read the lessons first
Open `LESSONS.md` in this skill's folder and read every entry. They are mistakes that already happened, with
the fix. If the current problem is listed there, apply that fix first.

## Step 1 — use only these calls (all keyless; all allow calls from a web page)

**A. Registrations on a topic, one year at a time** (one call per year; up to 100 results per call)
```
https://share.osf.io/trove/index-card-search?cardSearchText=<topic>&cardSearchFilter[resourceType]=Registration&cardSearchFilter[dateCreated]=<YYYY>&page[size]=100&acceptMediatype=application/vnd.api%2Bjson
```
Read:
- `data.attributes.totalResultCount` → how many matched. **Can be an object** (`{"@id":"trove:ten-thousands-and-more"}`) when very large: then use the number of cards.
- the registrations: `included` items with `type === "index-card"`, then `.attributes.resourceMetadata` (call it `m`):
  - OSF link: `m["@id"]` (e.g. `https://osf.io/8rbvz`); id = last part
  - title: `m.title[0]["@value"]` — may contain `&amp;`: turn it into `&`
  - registration date: `m.dateCreated[0]["@value"]` (YYYY-MM-DD). This **is** the registration date. (`dateAvailable` is an embargo end, do not use it.)
  - authors: `m.creator[]` → `c["foaf:familyName"][0]["@value"]`, or if missing the last word of `c.name[0]["@value"]`. Some creators are organisations ("Lab", "Good") or first names only.
  - OSF's own paper link (rare, under 1%): `m.hasPapersResource[]["@id"]` and `m["dcterms:relation"][]["@id"]` (DOI links). Ignore DOIs starting `10.17605/` (that is the registration itself).

**B. Details of a DOI that OSF links** (one call)
```
https://api.crossref.org/works/<doi>
```
Read `message.type`, `message.issued["date-parts"][0][0]` (year), `message.title[0]`. A 404 means the DOI is not in Crossref (it may be at DataCite): keep the link, year unknown.

**C. Search Crossref for the paper** (one call per registration)
```
https://api.crossref.org/works?query.bibliographic=<registration title>&filter=from-pub-date:<YYYY-MM-DD registration date>&rows=5&select=DOI,title,author,issued,type&query.author=<up to 3 surnames, space-separated>
```
Read `message.items[]`: `DOI`, `title[0]`, `author[].family`, `issued["date-parts"][0][0]`, `type`.

Always pass the URL to `api.getJSON(url)`; never call `fetch` yourself (it handles pacing, retries, caching and the saved sample).

## Step 2 — decide if a candidate is the paper (rule "v3", measured on 54 hand-checked registrations)
- Content words of a title: lower case, accents removed, keep letters/digits, words of 3+ letters, minus
  stop-words (a, the, of, and, in, on, for, to, with, by, from, at, as, is, are, be, study, effect(s), using,
  via, vs, versus, its, their, into, between, among, do, does, how, what, when, why, role, pre, registration,
  preregistration, registered, report, protocol, replication, experiment, part).
- `ts` = share of the registration title's content words that appear in the candidate's title.
- `a` = number of shared surnames (3+ letters, accents removed; ignore "jr", "lab", "good").
- Accept if (`a ≥ 3` and `ts ≥ 0.30`) or (`a ≥ 2` and `ts ≥ 0.45`) or (`a ≥ 1` and `ts ≥ 0.80` and the registration title has ≥ 3 content words).
- Kind: **preprint** if type is `posted-content` or the DOI starts with `10.2139/ssrn`, `10.31234/` or `10.31219/`; **paper** if type is journal-article, proceedings-article, book-chapter, report, dissertation, monograph or book; anything else (dataset, peer-review, component…) is never accepted.
- Several accepted: prefer a paper over a preprint, then the higher `ts` (rounded to 0.1), then the earliest year.
- What it achieves: about 6 in 10 known-published registrations found; about 9 in 10 matches correct. So the
  share is a **lower bound** and every match must be shown with a link.

## Step 3 — traps (each one happened)
| Symptom | Cause | Fix |
|---|---|---|
| "Failed to fetch" on every OSF call | `api.osf.io` blocks web pages (no CORS header) | use SHARE (call A) instead |
| Error 429 "Too many requests" from Crossref | keyless Crossref = 1 call per second, shared by everyone on the network | keep `api.getJSON` (it waits 1.2 s and retries); check ≤ 40 registrations; wait a minute; or tick "Use saved sample data" |
| Error 403 from SHARE in an automated test | SHARE blocks headless browsers | test in a normal browser window; the student's page is fine |
| `totalResultCount` shows "[object Object]" | very large result sets return an object | use the card count |
| Titles with `&amp;` match nothing | HTML entities in SHARE titles | replace `&amp;` → `&` (and `&lt;` `&gt;` `&quot;` `&#39;`) |
| A random chemistry/biometrics paper "matches" | one common surname + a short generic title ("Anchoring study") | keep the v3 rule exactly; never accept on surname alone |
| Same-team sibling paper chosen | same authors publish several papers on a topic | show it in the table as a candidate; the student checks |
| Sample mode says "not in the saved sample data" | the call differs from the idea card's call shapes | use calls A–C exactly as written |

## Step 4 — after the task: add a lesson (self-learning)
If anything went wrong in this task (an error, a wrong number, a call that had to change, a trap not listed
above), append an entry at the end of `LESSONS.md` in this skill's folder, in this format, and tell the
student you did:
```
## <YYYY-MM-DD> — <one-line symptom>
- What happened: <the error text or wrong number, and with which settings>
- Cause: <why>
- Fix: <what changed, in which part of the TOOL block>
- Checked by: <how you know it is fixed, e.g. "NUMBER CHECK row 2 OK">
```
Do not delete or rewrite older entries. If nothing went wrong, add nothing.
