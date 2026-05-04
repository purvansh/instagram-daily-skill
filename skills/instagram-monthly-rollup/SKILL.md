---
name: instagram-monthly-rollup
description: At the end of a 30-day cycle, re-scrapes the configured Instagram account via Apify, compares to the locked baseline, writes a new dated analysis file, updates the calendar plan for next month, and saves a memory pointer. Use when the user says "monthly rollup", "end of cycle", "re-scrape", "regenerate analysis", "new month", "rerun analysis", or "month is done".
---

# Instagram Monthly Rollup

End-of-cycle workflow. Run this once every 30 days. It compares new performance against the locked baseline and produces inputs for next month's calendar.

## Inputs

1. The most recent `analysis/<date>_baseline_analysis.md` (use whichever dated file is latest)
2. `content-calendar.md` — the calendar that just ran
3. `tracking/30-day-tracker.md` — per-reel results from the cycle
4. `creator-profile.md` — to know the handle for the scrape
5. The Apify MCP server (`apify/instagram-reel-scraper` actor)

## Workflow

### Step 1 — Confirm with the user

Ask: "Cycle ended on day 30 — running rollup now? This will (a) trigger an Apify scrape (cost: ~$0.05 per run), (b) write a new analysis file, (c) propose next month's calendar updates. Confirm to proceed."

If user confirms, continue. If not, stop.

### Step 2 — Run the Apify scrape

Use the Apify MCP. Same actor + parameters as the baseline run:

- Actor: `apify/instagram-reel-scraper`
- Input: `{ "username": ["<handle from creator-profile.md>"], "resultsLimit": 30, "onlyPostsNewerThan": "30 days" }`
- Save raw output to `owner-data/<TODAY>_<handle>_reels.json`

### Step 3 — Compute the delta

Compare new pull vs baseline:

| Metric | Baseline (locked) | This cycle | Δ |
|---|---|---|---|
| Median views | ? | ? | ? |
| Mean views | ? | ? | ? |
| Median likes | ? | ? | ? |
| Median comments | ? | ? | ? |
| Funnel-tagged median ER | ? | ? | ? |
| Total reels in cycle | ? | ? | ? |

Per-reel scoring (use the same rubric the baseline file uses): default is `0.4×(views/maxViews) + 0.35×(ER) + 0.25×(comments/maxComments)`.

### Step 4 — Pattern analysis

Identify:
1. **Top-3 reels of the cycle** — hook ID, format, audio, time-of-day
2. **Bottom-3 reels** — what failed (format / hook / audio / length)
3. **Format performance ranking** — which format pulled best by median views
4. **Funnel performance** — funnel-tagged vs personal-only on comments / sends
5. **Audio strategy update** — original vs trending. Has the picture changed?
6. **Posting time** — has the best window shifted?
7. **Hashtag performance** — which combos pulled. Drop dead ones.
8. **Voice rule violations caught** — anything posted that broke the rules?

### Step 5 — Write the new dated analysis file

Save to `analysis/<YYYY-MM-DD>_baseline_analysis.md`. Same structure as the previous baseline:
1. The numbers
2. Per-reel scoring
3. What worked — pattern recognition
4. What tanked — pattern recognition
5. Audio strategy
6. Posting time
7. Hashtag observations
8. Funnel observations
9. Open follow-ups
10. TL;DR for next 30 days

**This new file becomes the LOCKED baseline for next cycle.**

### Step 6 — Propose next month's calendar updates

Read existing `content-calendar.md`. Propose changes:

- **Increase format share** of whichever format pulled best
- **Decrease/eliminate** any format in bottom quartile
- **Adjust posting time** if data shifted
- **Refresh hook IDs** — replace 50% of hooks with rows from `hooks/hook-library.md` not yet used
- **Increase funnel allocation** if funnel-tagged outperformed personal humor
- **Drop any reel concept that scored bottom-3** unless it was a hook problem (then retry with a different hook)

Output as a diff-style suggestion the user approves before changes. Do NOT overwrite `content-calendar.md` without explicit approval.

### Step 7 — Update memory (if Claude Code memory is used)

Save a new memory entry pointing to the new baseline file, so future sessions reference the latest analysis.

### Step 8 — Action items for the user

Generate a punch list:
- [ ] Approve / redirect calendar updates
- [ ] Re-batch any costumes / b-rolls that need refreshing
- [ ] Comment-mine top-3 reels for next month's hook language
- [ ] Set new cycle start date in `content-calendar.md`
- [ ] (Optional) run competitor scrape if niche has shifted

## Things you must NEVER do

- Never overwrite the previous baseline file. Always create a new dated file.
- Never edit `content-calendar.md` without explicit user approval — propose the diff, ask first.
- Never run the Apify scrape without confirming (it costs money).
- Never delete old analysis files. The historical trail is part of the dataset.
- Never adjust the tracker; that's the source of truth for what was actually shipped vs planned.
