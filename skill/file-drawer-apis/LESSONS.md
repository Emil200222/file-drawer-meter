# Lessons for the file-drawer-apis skill

Read all of these before changing an API call. Newest at the bottom. Add new ones with the format in SKILL.md, Step 4.

## 2026-11-15 — "Failed to fetch" for api.osf.io
- What happened: a page calling `https://api.osf.io/v2/registrations/` from http://localhost got "No 'Access-Control-Allow-Origin' header is present".
- Cause: the OSF API only allows web pages on osf.io itself.
- Fix: find registrations with SHARE (`share.osf.io/trove/index-card-search`), which allows any page.
- Checked by: the same page then loaded 12 registrations in 0.8 s.

## 2026-11-15 — Crossref 429 "Too many requests"
- What happened: at one call every 1.2 s, Crossref sometimes answered 429 when other people used the same network.
- Cause: keyless Crossref allows 1 call per second per network (header `x-rate-limit-limit: 1`).
- Fix: `api.getJSON` waits and retries; keep runs to 40 registrations; a second run uses the browser cache (0 calls).
- Checked by: the reference run finished all three presets.

## 2026-11-15 — false matches on one common surname
- What happened: "Facial psychopathy and punishments" matched a biometrics paper (shared surnames Brown, Murray); "Anchoring study" matched a chemistry paper (one shared surname, the only title word "anchoring").
- Cause: the accept rule was too loose for common surnames and very short titles.
- Fix: rule v3 (SKILL.md Step 2): 2 surnames need 45% of title words; 1 surname needs 80% and at least 3 content words.
- Checked by: both false matches disappeared; no correct match was lost in the 18 known-published registrations.

## 2026-11-15 — SHARE 403 in automated tests
- What happened: a headless Playwright test got 403 from share.osf.io; the same page in a normal browser worked.
- Cause: SHARE blocks headless browsers.
- Fix: test in a normal browser window, or with the course check script (it fetches the calls from Node).
- Checked by: normal browser 200, Node fetch 200.
