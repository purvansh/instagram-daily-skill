---
name: instagram-daily
description: Generates today's complete Instagram daily brief for the configured creator. Reads content-calendar.md (with format-tier tags), the latest analysis/trending-<date>.md (produced by /instagram-trending), b-roll-shotlist.md, hooks/hook-library.md, and the analysis/ folder. Applies tier rules (T1 trend-driven / T2 flex Option B / T3 evergreen-locked) and the 5-gate trend check, then outputs today's hook + full script + sound suggestion + caption + hashtags + CTA + posting time + b-roll instructions + story sidekick. Use when the user asks for "today's reel", "daily brief", "what should I post today", "instagram daily", "/instagram-daily".
---

# Instagram Daily Brief

You are generating today's daily Instagram brief for the creator configured in this project. The skill is data-driven — it reads the creator's voice rules, hook library, calendar, trending file, and analysis files, and builds today's brief on top of them.

## Inputs (read every run, in this order)

All paths are relative to the project root (where the skill is installed).

1. `creator-profile.md` — handle, niche, daily-stack tools, posting times, funnel handle
2. `voice-rules.md` — voice / format / brand-image filter (the override rulebook)
3. `content-calendar.md` — find cycle start date, today's day number, today's tier (T1/T2/T3) and slot details
4. `analysis/trending-<today>.md` — produced by `/instagram-trending`. **If missing AND today is T1 or T2:** prompt user to run `/instagram-trending` first, stop. **If today is T3:** trending file is optional.
5. `hooks/hook-library.md` — pull hooks
6. `b-roll-shotlist.md` — list b-roll IDs (respect FLEX-OK / TREND-READY / FIXED markers)
7. The latest `analysis/<date>_baseline_analysis.md` — current baseline
8. `analysis/reel-formats-playbook.md` (if present) — format rules
9. `analysis/script-framework.md` (if present) — 5-layer construction rules (IDEA bucket, VALUE non-obvious+tactical+binge, HOOK belief→contradict→curious, SCRIPT BUT/SO transitions, CONTENT WHAT/WHY/HOW). Validate every script against all 5 before output.
10. `analysis/hook-patterns/*.md` (if present) — psychology-based hook arc variants (e.g. negative-trap, etc.). Each file lists when to use, voice-rule constraints, and per-cycle caps. Skill scans this folder during Layer 3 validation; if a pattern is a better fit than the default arc → use the pattern's structural template.
11. `tracking/30-day-tracker.md` (if present) — yesterday's results

## Tier rules

### T3 — Evergreen-locked
- Read calendar's pre-filled hook + concept. **Ignore trending file.**
- Funnel-tagged (✓) slots are ALWAYS T3 — even if calendar says otherwise, override to T3.
- Multi-character skits are ALWAYS T3 — production cost rule.

### T2 — Flex (Option B for DY/RK, Option A for hot takes)
- Read trending file. Find the recommendation for today's slot.
- **SWAP recommended** → use the flex hook ID from calendar (the second hook in `default / flex` notation), generate script around the trend topic.
- **NO SWAP** → use the default hook ID from calendar, generate evergreen script.
- T2-TH (hot take) slots use Option A (auto-pick highest-velocity from trending file). Skill follows recommendation as-is.

### T1 — Trend-driven
- Read trending file. Use top-ranked candidate that passed all 5 gates as today's topic.
- Pick a hook from `hooks/hook-library.md` that fits the topic + format combination.
- Generate script around the trend.
- **No candidate passed:** apply fallback:
  - Personal-humor day (no funnel) → auto-convert to evergreen multi-character SK from library. Pick a hook tagged `SK` not yet shipped.
  - Funnel day (✓) → SKIP the reel. Output: "No trend qualified for funnel-day reaction. Skipping reel; post a story poll asking the audience what to react to next."

## Voice rules to enforce on EVERY brief

Read `voice-rules.md` for the full filter set. Universal:

- Lead-magnet CTAs only if the lead magnet exists and is listed in `voice-rules.md`. Default = "tag a [role] who…" send-bait.
- Never write content that contradicts the brand-image rule.
- Final voice-rule pass on the chosen hook BEFORE output. If a trend swap puts the hook against voice rules → reject the swap, fall back to evergreen.

## Workflow

### Step 1 — Determine today's day number
Compute `day = today's date - cycle start + 1`. If calendar has no start date, ASK.

### Step 2 — Read calendar slot
Capture: tier (T1/T2/T3), format, default hook ID, flex hook ID (T2 only), pillar, funnel ✓ y/n, posting time hint, notes.

### Step 3 — Apply tier rules
Per the rules above, decide FINAL hook ID and FINAL topic.
For T1/T2 days: read the trending file recommendation. Do not override unless trending file is silent.

### Step 4 — Final voice-rule pass
If the trending swap produces a hook against voice rules → reject the swap, use evergreen.

### Step 5 — Pull b-roll
From `b-roll-shotlist.md`, list shots for today. Group by category. On trend-swap days, only suggest 🔄 FLEX-OK and ⚡ TREND-READY shots; flag any 🔒 FIXED shot that would normally be used as "shoot fresh today."

### Step 6 — Sound suggestion
Per format + tier:
- T3 funnel skits, multi-char SK, DY, GS reactions → original audio.
- T1 B-roll-only or RK/CR → trending audio. Use picks from trending file's audio section.
- If trending file is missing audio picks → fall back to original.

### Step 7 — Write the script

Format with beats. Length per format playbook (typical defaults):
- SK: 15–22s
- GS: 12–18s
- DY: 18–25s
- RK: 15–20s
- CR: 30s rendered
- BD: 8–12s

```
[0:00–0:03] HOOK FRAME
   Visual: <costume + on-screen text>
   Line: <character A's first line>

[0:03–0:08] SETUP BEAT
[0:08–0:14] ESCALATION
[0:14–0:18] PUNCHLINE
   (If funnel ✓: end frame on-screen text "→ @<funnel-handle>")
```

### Step 7a — 5-layer framework validation (run before Step 8)

If `analysis/script-framework.md` exists, validate the script you just wrote:

- **Layer 1 — IDEA:** does the premise fit NOT-TO-DO / TO-DO / 2x-TRUST? If not, rewrite.
- **Layer 2 — VALUE:** non-obvious ✓, highly tactical ✓, binge-worthy ✓. If any fail, sharpen.
- **Layer 3 — HOOK:** common belief → contradict → curious in the first 3s. If structure missing, rewrite hook frame.
- **Layer 4 — SCRIPT:** BUT count ≥1 AND SO count ≥1. If missing, insert at natural beat pivots.
- **Layer 5 — CONTENT:** WHAT/WHY/HOW count — need ≥2 of 3 for T2/T3, ≥1 of 3 for T1.

If after 1 rewrite a layer still fails → output a `⚠️ 5-layer audit FAILED on Layer X` warning so the user can fix manually.

Append a 5-layer audit line to the brief output (see Output format below).

### Step 8 — Caption + hashtags
- Caption ≤2 lines.
- 5–8 niche-narrow hashtags from `creator-profile.md`'s tested winners.
- For T1 trend reels, ADD 1–2 trend-specific tags (the launching tool's tag if under 5K uses).
- DROP generic broad tags from `creator-profile.md`'s avoid list.

### Step 9 — CTA
Pick ONE per `voice-rules.md`:
- Send-bait: "Tag the [role] who [behavior]." Specific.
- Comment-keyword: only if magnet exists in `voice-rules.md`.
- Opinion-bait: tier lists, hot takes.

### Step 10 — Posting time

Per `creator-profile.md`'s posting windows + the trending file's recommendation:

- T3 evergreen → primary window (default 6:30 PM in primary TZ)
- Badminton (T3) → secondary window (e.g. 8:30 AM)
- T2 flex (evergreen path) → primary window
- T2 flex (swap path) → trending file's recommended time
- T1 trend-driven → within 4 hours of trend velocity peak (per trending file)

### Step 11 — Story sidekick
Day-of-week pairing:
- Mon: poll / Tue: BTS / Wed: Q&A / Thu: reel teaser / Fri: opinion / Sat: weekend / Sun: poll re: tomorrow's reel
- For T1 trend reels: story = tomorrow's post-mortem.

### Step 12 — Reply playbook (first 60 min)
3–5 reply lines in voice. Goal: 5+ comments first hour.

### Step 13 — Funnel touchpoint
If funnel ✓:
- Punchline frame ONLY, never caption
- Tagline format ("→ @<funnel-handle>")
- NEVER on a T1 trend day (funnel slots are forced T3 — but as a defense-in-depth, double-check)

### Step 14 — Flex decision audit
If today is T1 or T2, output a 1-paragraph audit:
- Which trend triggered the decision (or which gate failed)
- Why this hook was picked
- Why this posting time

This makes the skill auditable.

## Output format

```
# Day {N} — {Date} — Tier {T1/T2/T3} — {Format} — Hook ID #{X}

## TLDR
- Tier: {tier} — {trend-driven / flex-Option-B / evergreen-locked}
- Format: {format} ({duration}s)
- Pillar: {pillar}
- Funnel: {Yes/No}
- Posting time: {time}

## Flex decision audit  *(only if T1 or T2)*
{which trend / which gate / why this hook}

## Hook (verbatim, on-screen text)
{hook}

## Script
{full beat-by-beat script}

## B-roll needed
- Costume: {C-XX}
- Face-cam: {F-XX}
- Screen-recording: {S-XX}
- Reaction: {R-XX}
- Setting: {E-XX}
- Overlay template: {O-XX}

## Fresh shoot today
{the 1 fresh face-cam line and what costume to be in}

## Sound
{trending picks 1–3 OR "Original audio" + style direction}

## Caption
{caption ≤2 lines}
{hashtag line}

## CTA
{one CTA line, ready to paste}

## Story sidekick
{story idea}

## Reply playbook (first 60 min)
{3–5 reply lines}

## Funnel touchpoint
{instruction if Y, "Skip — personal humor today" if N}

## Voice-rule check ✅
{1-line confirmation}

## 5-layer audit ✅
**IDEA:** {NOT-TO-DO / TO-DO / 2x-TRUST} — <1-line premise>
**VALUE:** non-obvious {✓/✗} + tactical {✓/✗} + binge-worthy {✓/✗}
**HOOK:** belief→contradict→curious {✓/✗}
**SCRIPT:** BUT count: N / SO count: N
**CONTENT:** WHAT {✓/✗} + WHY {✓/✗} + HOW {✓/✗}
```

## Edge cases

- **No calendar entry for today** (off-day): "REST DAY — comment-mine top reel from this week, batch b-roll for next week" instruction.
- **Trending file missing on a T1/T2 day:** prompt user to run `/instagram-trending` first, stop. Don't fabricate trends.
- **User asks for tomorrow's brief**: same workflow on `day + 1`.
- **User says "regenerate"**: pick a different hook ID from the same format family, log the swap.
- **Mid-cycle pivot**: if `tracking/30-day-tracker.md` shows last 3 reels all underperformed, suggest pivoting to highest-scoring format from baseline for next 2 days.

## Things you must NEVER do

- Never write a brief that violates `voice-rules.md`. Override the calendar's hook if needed.
- Never invent a lead magnet. Default to send-bait CTA.
- Never recommend reel length outside the format's range.
- Never use generic broad hashtags from the avoid list.
- Never force funnel mention onto a personal-only slot.
- Never trend-flex a multi-character skit slot.
- Never trend-flex a funnel-tagged slot.
- Never recommend a swap if the trending file's 5-gate check failed.
