# The Romako Record — family history progress dashboard

A single-page dashboard that shows where the genealogy research stands (raw
numbers) plus a rotating set of human-interest "dispatches" from the archive.
Designed to be shared with family and friends.

## How it works (and why it's fast)

- **`build_snapshot.py`** runs once a day. It reads only cheap **local** sources —
  the `Romako Family Tree.ged` export, `memory/research-path-status.md`, and the
  markdown files — counts everything, and writes:
  - `data/snapshot.json` — what the page displays
  - `data/history.json` — one row per day, so the growth chart fills in over time
  - it also re-bakes the latest numbers into `index.html` as an offline fallback.
  No Airtable calls, no network, runs in well under a second.
- **`index.html`** loads `data/snapshot.json` on open. It does **not** recompute
  anything live — it just reads the snapshot the daily job produced.
- **`facts.json`** holds the 5 story cards + the "region in focus" panel.
  Edit this file any time to change what's featured — the next daily run picks it up.

## Fallbacks (so a missed day never blanks the page)

1. If the daily job hasn't run in >2 days, the page shows a small notice and the
   last saved figures.
2. If `snapshot.json` can't be fetched at all (e.g. opened straight off disk),
   the page falls back to the numbers baked into `index.html` itself.
So the page always renders something sensible.

## One-time setup to make it public (GitHub Pages)

1. Create a **new, empty GitHub repo** (e.g. `romako-record`). Public repos get
   free Pages hosting.
2. In this folder, connect it and push (run these in a terminal here — replace
   the URL with your repo, and sign in / set up a token when git asks):
   ```
   git remote add origin https://github.com/<you>/romako-record.git
   git branch -M main
   git push -u origin main
   ```
   > Set up your own GitHub auth (token or SSH) — for security I don't handle
   > credentials. Once it's cached, the daily job pushes on its own.
3. On GitHub: **Settings → Pages → Build and deployment → Source: "Deploy from a
   branch" → Branch: `main` / root → Save.**
4. Your public link will be `https://<you>.github.io/romako-record/`
   (share this with family). It updates automatically after each daily push.

## The daily update

A scheduled task runs `publish.sh` each morning. That script rebuilds the
snapshot and, if a git remote is set, commits and pushes it — which republishes
the Pages site. If no remote is set yet, it just updates the local files and
skips the push (no errors).

Run it by hand any time:
```
bash publish.sh
```

## Files
| File | Purpose |
|---|---|
| `index.html` | the dashboard (open in any browser) |
| `index.template.html` | source the daily job re-bakes `index.html` from |
| `build_snapshot.py` | daily data collector |
| `publish.sh` | rebuild + commit + push |
| `facts.json` | editable story cards + region panel |
| `data/snapshot.json` | current display data |
| `data/history.json` | daily time series for the chart |
| `.nojekyll` | tells GitHub Pages to serve files as-is |
