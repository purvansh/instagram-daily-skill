# Content Calendar — 30-day slate

> Source of truth for what gets posted each day. `instagram-trending` reads this file each morning to know what trend research today's slot needs; `instagram-daily` reads it to pull the slot and apply tier rules.

## Format-tier flex rules (REQUIRED — fill in per your format playbook)

Every reel is one of three tiers. The skill respects the tier:

| Tier | Formats (typical) | Behavior on a trend day |
|---|---|---|
| **TIER 1 — Trend-driven** | Green-screen reaction, single-char situation POV, B-roll-only | **Always pull live trending topic.** No-trend fallback per Q3 in your setup. |
| **TIER 2 — Flex (Option B)** | Did-you-know, ranking, hot take | **Dual pre-load:** calendar specifies BOTH evergreen hook AND a flex condition + alt hook. Skill checks 5-gate trend signal — if pass, ship flex; else evergreen. |
| **TIER 3 — Evergreen-locked** | Multi-character skits, carousel, planned series, repurpose slots, badminton | **Never trend-flex.** Voice + funnel relevance must age past the trend cycle. |

### The 5-gate trend check (Tier 2 flex)

A trend qualifies for swap-in only if **ALL** are true:

1. **Source in your daily-stack** (per `creator-profile.md`)
2. **Hashtag volume <5K uses** (algorithmic gold zone)
3. **<3 niche creators** shipped on it in last 48h (saturation gate)
4. **Shootable in today's format** with batched b-roll
5. **Voice-carryable** — can land in your tone (per `voice-rules.md`)

If any gate fails → use evergreen.

### Hard rules

- **Funnel-mention slots NEVER flex.** Evergreen forever.
- **Multi-character skits NEVER flex.** Production cost too high.
- **Trend-flex reels post at non-default times** — define your trend posting windows in `creator-profile.md`.

## Cycle metadata

| Field | Value |
|---|---|
| Cycle start date | `YYYY-MM-DD` |
| Cycle end date | start + 30 days |
| Mode | Hard mode (~28 reels) / Easy mode (~12 reels) |
| Posting window (primary) | _from creator-profile.md_ |
| Baseline (locked) | _from latest analysis/_ |
| Targets | day 14 median ≥1.2× / day 30 median ≥1.5× / followers +N |

## Pillar mix

| Pillar | % | # reels (of 28) | Notes |
|---|---|---|---|
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |

## Format mix

(Per the format-priority matrix in `analysis/reel-formats-playbook.md`)

| Format | # reels |
|---|---|
| SK |  |
| GS |  |
| DY |  |
| RK |  |
| CR |  |
| Other |  |

## Funnel allocation

`__` of 28 reels (`__%`) carry a soft funnel mention. **Only on the format your analysis confirmed wins for funnel.**

---

## The 28-reel slate

> Hook IDs reference `hooks/hook-library.md`. Format codes: SK / GS / DY / RK / CR / TH / DV. Funnel column: ✓ if soft funnel mention, blank if personal-only.

### Week 1 — Volume + format experimentation (5 reels)

| Day | Tier | Format | Hook ID (default / flex if T2) | Filled hook(s) | Pillar | Funnel | Post time | Notes |
|---|---|---|---|---|---|---|---|---|
| 1 |  |  |  |  |  |  |
| 2 |  |  |  |  |  |  |
| 3 |  |  |  |  |  |  |
| 4 |  |  |  |  |  |  |
| 5 |  |  |  |  |  |  |

### Week 2 — Double down + first funnel reels (6 reels)

| Day | Tier | Format | Hook ID (default / flex if T2) | Filled hook(s) | Pillar | Funnel | Post time | Notes |
|---|---|---|---|---|---|---|---|---|
| 8 |  |  |  |  |  |  |
| 9 |  |  |  |  |  |  |
| 10 |  |  |  |  |  |  |
| 11 |  |  |  |  |  |  |
| 12 |  |  |  |  |  |  |
| 13 |  |  |  |  |  |  |
| 14 |  |  |  |  |  |  |

### Week 3 — Series + first carousel test (7 reels)

| Day | Tier | Format | Hook ID (default / flex if T2) | Filled hook(s) | Pillar | Funnel | Post time | Notes |
|---|---|---|---|---|---|---|---|---|
| 15 |  |  |  |  |  |  |
| 16 |  |  |  |  |  |  |
| 17 |  |  |  |  |  |  |
| 18 |  |  |  |  |  |  |
| 19 |  |  |  |  |  |  |
| 20 |  |  |  |  |  |  |
| 21 |  |  |  |  |  |  |

### Week 4 — Compounding + measurement (10 reels, 2 repurposed)

| Day | Tier | Format | Hook ID (default / flex if T2) | Filled hook(s) | Pillar | Funnel | Post time | Notes |
|---|---|---|---|---|---|---|---|---|
| 22 |  |  |  |  |  |  |
| 23 |  |  |  |  |  |  |
| 24 |  |  |  |  |  |  |
| 25 |  |  |  |  |  |  |
| 26 |  |  |  |  |  |  |
| 27 |  |  |  |  |  |  |
| 28 |  |  |  |  |  |  |
| 29 |  |  |  |  |  |  |
| 30 |  |  |  |  |  |  |

---

## Day-off / contingency

Days `__`, `__` — no posts. Use for:
- Comment-mining the previous week's top reel
- Batching b-roll for next week
- Burnout recovery

---

## Failure modes the calendar avoids

(Cross-reference your latest `analysis/<date>_baseline_analysis.md`)

- ❌ _list things your data confirmed don't work_
