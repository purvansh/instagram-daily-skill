# Baseline Analysis — Template

> Run this on your existing reels (Apify scrape) BEFORE day 1 of a cycle. The skill reads the latest dated baseline file as its anchor.

**Source:** `owner-data/<date>_<handle>_reels.json` — Apify pull
**Window:** _e.g. 90 days_, `__` reels.
**Cadence observed:** ~1 reel every `__` days

---

## 1. The numbers

| Metric | Value |
|---|---|
| Median views (`videoViewCount`) | __ |
| Mean views | __ |
| Top reel | __ |
| 2nd top | __ |
| Median plays (`videoPlayCount`) | __ |
| Median likes | __ |
| Median comments | __ |
| Best comments | __ |

> Use median views as the benchmark floor. Anything below = format problem, not luck problem.

---

## 2. Per-reel scoring (40/35/25 rubric)

`0.4×(views/maxViews) + 0.35×(ER) + 0.25×(comments/maxComments)`. ER = `(likes + comments) / views`.

| Rank | Code | Hook (truncated) | Views | Likes | Comm. | Score |
|---|---|---|---|---|---|---|
| 1 |  |  |  |  |  |  |
| 2 |  |  |  |  |  |  |
| 3 |  |  |  |  |  |  |

---

## 3. What worked — pattern recognition

### 3.1 Funnel-winner template
Identify the reel with highest comments/ER among funnel-tagged reels. Note: hook structure, format, audio, hashtags. **This is the format to clone weekly.**

### 3.2 Format that wins
Which of SK/GS/DY/RK/CR consistently lands in top quartile?

### 3.3 Audio strategy
Original vs trending — which performs better for you?

### 3.4 Language / regional layer
Does Hinglish / Spanglish / regional slang lift performance?

### 3.5 Outliers (note but don't chase)
A vanity-reach reel that doesn't convert — keep capped, don't pivot toward it.

---

## 4. What tanked — pattern recognition

### 4.1 Format that dies
Specific examples + view counts.

### 4.2 Length traps
Where does watch-time collapse? (Often >30s for small accounts.)

### 4.3 CTAs that fail
"Comment X for PDF" without a strong hook = dead. Document patterns.

### 4.4 Off-brand content
Anything off-niche should be quarantined.

---

## 5. Audio strategy observations

| Audio type | Reels | Median views |
|---|---|---|
| Original / character voice |  |  |
| Trending music |  |  |
| No audio / silent |  |  |

---

## 6. Posting time observations

Top 7 by score, time of day:

| Code | Local time | Score |
|---|---|---|

**Best window:** ___

---

## 7. Hashtag observations

**Winners:** `___`
**Losers:** `___`

**Rule:** prefer 5–8 niche-narrow + geo-tag + 1 mid-broad. Drop generic SaaS broad tags.

---

## 8. Funnel observations

- `__` of `__` reels mention the funnel handle. That's `__%`. Target: 25–35%.
- Funnel reel comments / ER / sends.
- Lead-magnet conversion data captured? Y/N.

---

## 9. Open follow-ups

1. Comment-mine the top reel for audience language → use phrases verbatim in next 5 hooks
2. Competitor scrape for the niche
3. Trending-audio scrape for the niche
4. Re-scrape on day 30 with same actor/window → measure delta

---

## 10. TL;DR for the next 30 days

| Do more of | Do less of |
|---|---|
|  |  |
|  |  |
|  |  |
