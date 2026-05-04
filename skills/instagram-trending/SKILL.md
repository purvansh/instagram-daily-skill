---
name: instagram-trending
description: Morning trend research for the configured creator. Runs WebSearch + Apify niche-hashtag scrape + Apify competitor scrape, applies the 5-gate trend check from content-calendar.md, and writes today's trending file at analysis/trending-<today>.md. Use when the user says "morning trend pull", "instagram trending", "/instagram-trending", "what's trending today", or as the prerequisite step before /instagram-daily.
---

# Instagram Trending — Morning Research

The morning trend pull. The `instagram-daily` skill depends on the file you produce — write it cleanly.

## When to run

- **Each morning, before `/instagram-daily`** (ideal: 7 AM in your primary timezone — gives you ~4-hour ship window for trend reels)
- Re-run if a major launch hits during the day and you want to rescue a T1 slot

## Inputs

1. `creator-profile.md` — niche hashtags, daily-stack tools, competitor handles, primary timezone
2. `content-calendar.md` — today's slot to know which trend signals matter (T1, T2-DY, T2-RK, T2-TH each need different research)
3. The 5-gate check defined in `content-calendar.md`
4. **Apify MCP** for niche-hashtag scrape + competitor scrape + trending audio
5. **WebSearch** for general launch news in last 24h

## Workflow

### Step 1 — Identify what today needs

Read `content-calendar.md`'s slot for today:
- **T3 evergreen** → write a one-line trending file: "Today is T3 evergreen — no trend research needed." Stop.
- **T1 GS** (green-screen reaction) → research news + hot takes
- **T1 SK-situation** → research vibe-of-the-day
- **T2 DY** → research feature/launch news (creator's daily stack only)
- **T2 RK** → research tool launches + tier-list-able events
- **T2 TH** (hot take) → research polarizing debates last 24h

### Step 2 — Daily-stack launch sweep (run if T1 or T2)

Run **WebSearch** for queries based on `creator-profile.md`'s daily-stack list. Generic template:

```
"<stack-tool> launch this week 2026"
"<stack-tool> new feature launched"
"<niche> announcement <today's date>"
"<niche-region> startup launch <YYYY-MM>"
```

For each result: capture URL, headline, datetime, primary tool/company, 1-line summary. Filter: drop anything older than 7 days. Mark which gate-1 source it matches.

### Step 3 — Niche signal scrape

Run **Apify** (`apify/instagram-hashtag-scraper`) on each hashtag listed in `creator-profile.md` under "niche search hashtags."

Parameters: `resultsLimit: 20, onlyPostsNewerThan: "48 hours"`

For each hashtag, capture:
- Top 5 reels by view count
- For each: caption (first 100 chars), audio name, view count, like count, posting time
- Compute hashtag total uses (gate 2 — needs <5K to qualify)

Plus **WebSearch** for region-specific news per `creator-profile.md`'s primary geo.

### Step 4 — Competitor saturation scrape

Run **Apify** (`apify/instagram-reel-scraper`) on each competitor handle listed in `creator-profile.md`.

Parameters: `resultsLimit: 10, onlyPostsNewerThan: "48 hours"`

For each competitor: capture last-48h reels (caption, format guess, view count).

Compute: for each launch/topic from Step 2, **how many competitors shipped on it in last 48h?** Gate-3.

### Step 5 — Trending audio scrape (run if today is RK / CR / any flex slot)

Run **Apify** on niche hashtags, but extract audio metadata:
- For each top-20 reel per hashtag: audio name, total uses, original-y/n
- Tally audios appearing ≥3 times across hashtags
- Filter: total uses <5K
- Pick top 3 — name, vibe, format match

### Step 6 — Apply the 5-gate check

For each launch/topic from Step 2, test all 5 gates:

| # | Gate | Check |
|---|---|---|
| 1 | Source in stack | Yes if matches one of the daily-stack tools/handles in `creator-profile.md` |
| 2 | Hashtag <5K uses | Yes if main associated hashtag still under 5K total IG uses |
| 3 | <3 niche creators shipped | Yes if step-4 saturation count < 3 |
| 4 | Shootable today | Yes if format = today's calendar format AND batched b-roll covers it |
| 5 | Voice-carryable | Yes if topic can land in `voice-rules.md`'s allowed tone (no political, religious, tragedy news) |

Only candidates passing **all 5 gates** are eligible to swap into a flex slot.

### Step 7 — Match candidates to today's slot

Per today's tier:

- **T1 slot** → list ALL passing candidates ranked by velocity. Top one becomes the script topic. If none pass → no-trend fallback recommendation.
- **T2-DY slot** → match candidates with format fit (a launched feature you can demo in 18s).
- **T2-RK slot** → match candidates with format fit (a tool launch that's rank-able).
- **T2-TH slot** → list polarizing-but-voice-safe takes. Auto-pick highest-velocity (Option A).

### Step 8 — Write the output file

Save to `analysis/trending-<YYYY-MM-DD>.md`. Structure:

```markdown
# Trending — <date> (morning pull)

**Today's calendar slot:** Day N — Tier X — Format Y
**Trend research scope:** <which research steps were run>

## 1. Daily-stack launch sweep
| Source | Headline | Date | Gate 1 (in stack)? | Gate 5 (voice-OK)? | URL |

## 2. Niche signal — top reels last 48h
| Hashtag | Top reel caption | Views | Audio | Uses |

## 3. Niche-creator saturation
| Topic | <competitor 1> | <competitor 2> | ... | Saturation count |

## 4. Trending audio (algorithmic gold zone, <5K uses)
| Audio | Total uses | Vibe | Match for today's format? |

## 5. 5-gate check results
For each candidate, table of gates 1–5 pass/fail.

## 6. Recommendation for today

**Today is Day N — Tier X.**

- ✅ **SWAP recommended** to: <topic>. Use flex hook ID <Y> from calendar. Posting time: <X>. Reasons: <1 line per gate>.

OR

- ⏭️ **NO SWAP — use evergreen.** Reason: <gates failed>. Default hook ID <X>.

OR

- ⚠️ **NO TREND, T1 slot.** Fallback: <auto-convert to evergreen | skip and post a story>.
```

### Step 9 — Confirm with user

After writing, output a single-paragraph summary:
> "Trending file written to `analysis/trending-<date>.md`. Recommendation: <SWAP / NO SWAP / FALLBACK>. Run `/instagram-daily` next."

## Cost / time budget

- Apify niche-hashtag scrape: ~$0.02 per run
- Apify competitor scrape: ~$0.03 per run
- WebSearch: free
- Total: ~$0.05/day, 3-5 minutes

## Things you must NEVER do

- Never override the 5-gate check. If a gate fails, the candidate is OUT.
- Never recommend a swap on a T3 evergreen slot.
- Never recommend a swap on a funnel-tagged slot — those are evergreen forever.
- Never recommend a topic that violates `voice-rules.md`.
- Never write the file on a T3 day — output the "skip" message and stop.
- Never auto-trigger `/instagram-daily` — write the file, summarize, let the user run daily.
