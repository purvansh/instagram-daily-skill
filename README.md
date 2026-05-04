# instagram-daily-skill

A Claude Code skill pack that turns your own Instagram performance data + a 30-day calendar into a fresh daily reel brief — hook, script, b-roll, sound, caption, hashtags, CTA, posting time — every day, in your voice, on rails.

Built for solo creators who want to ship a reel a day without re-deciding everything from scratch every morning.

---

## What it is

Three Claude Code skills + a set of templates:

| Skill | What it does | Cadence |
|---|---|---|
| `instagram-trending` | Live morning trend pull — WebSearch + Apify niche-hashtag scrape + Apify competitor scrape + trending audio. Applies a 5-gate check to filter saturation/voice-fit. Writes `analysis/trending-<date>.md`. | Each morning, before `/instagram-daily` |
| `instagram-daily` | Reads your calendar, voice rules, hook library, b-roll list, analysis, AND today's trending file. Applies **format-tier rules** (T1 always-trend / T2 flex / T3 evergreen-locked) and the **5-gate** swap check. Outputs today's full reel brief. | Each morning, after `/instagram-trending` |
| `instagram-monthly-rollup` | At end of cycle: re-scrapes your Instagram via Apify, writes a new dated baseline analysis, proposes calendar updates for next month. | Once every 30 days |

The skills are stateless code — your *creator profile, voice rules, hook library, calendar, and analysis* are markdown files you fill in once. Then the skills run on top of them.

## Why it works (the loop)

```
   ┌──────────────────────────────────────────────────┐
   │  baseline analysis (locked from last cycle)      │
   │       +                                          │
   │  voice rules + creator profile + brand rules     │
   │       +                                          │
   │  hook library (curated to your niche)            │
   │       +                                          │
   │  30-day calendar — each slot tagged T1/T2/T3     │
   │       +                                          │
   │  b-roll shot list (FLEX-OK / FIXED / TREND)      │
   └──────────────────────────────────────────────────┘
                        ↓
        [/instagram-trending — 7am, live web + Apify research]
                        ↓
        analysis/trending-<today>.md  (5-gate check applied)
                        ↓
              [/instagram-daily — read calendar + trending]
                        ↓
        T3 evergreen → ship calendar's pre-pick
        T2 flex      → 5-gate pass? swap to flex hook : default
        T1 trend     → use top trend; if none → fallback
                        ↓
   today's reel brief: hook + script + sound + caption + hashtags + CTA + b-roll + posting time + flex audit
                        ↓
   you ship the reel, log result in tracker
                        ↓
              [/instagram-monthly-rollup — day 30]
                        ↓
   new baseline analysis → calendar updates for next cycle → loop
```

Each cycle's analysis becomes next cycle's locked baseline. The system gets sharper every month because it learns from *your* data, not generic advice.

---

## Quick start

1. **Install Claude Code** if you don't have it: https://claude.com/claude-code
2. **Clone this repo** to wherever your Instagram content workspace lives. Example:
   ```
   git clone <this-repo> ~/Documents/my-instagram
   cd ~/Documents/my-instagram
   ```
3. **Install the skills** — copy or symlink them into `.claude/skills/`:
   ```
   mkdir -p .claude/skills
   cp -r skills/* .claude/skills/
   ```
4. **Fill in the templates** in `templates/` (see [docs/SETUP.md](docs/SETUP.md) for the order — it matters). Move filled templates to the project root so the skills can find them.
5. **Set up Apify** (free tier works) for scraping. Add the Apify MCP server to your `.mcp.json`. See [docs/SETUP.md](docs/SETUP.md).
6. **Run your baseline scrape** — pull last 90 days of your reels via `apify/instagram-reel-scraper`, save to `owner-data/<date>_<handle>_reels.json`.
7. **Have Claude write your first analysis file** from that scrape (the skill expects an `analysis/<date>_baseline_analysis.md` to exist before day 1).
8. **Set the cycle start date** in `content-calendar.md`.
9. **Day 1**: invoke `/instagram-daily` (or just ask Claude "what should I post today"). Done.

For the full walkthrough, see [docs/SETUP.md](docs/SETUP.md).

---

## What you need to have / fill in

| File / template | What you put in it | Required? |
|---|---|---|
| `creator-profile.md` | Your handle, niche, tone, what you sell, who you follow into the niche | **Yes** |
| `voice-rules.md` | Hooks/words/topics you DO use vs. ones you DON'T (for AI to filter against) | **Yes** |
| `analysis/<date>_baseline_analysis.md` | Stats from your last cycle (median views, format performance, what tanks, what wins) | **Yes** — generate from Apify scrape |
| `hooks/hook-library.md` | 50–150 hook templates curated to your niche, tagged by format and pillar | **Yes** |
| `content-calendar.md` | 30-day slate: which day → which hook → which format | **Yes** |
| `b-roll-shotlist.md` | Costumes, face-cam beats, screen recordings, reactions to batch | **Yes** |
| `tracking/30-day-tracker.md` | Per-reel results (views, likes, comments) — fill as you ship | **Yes** |
| `analysis/reel-formats-playbook.md` | 2026 format reference + algorithm rules of thumb | Recommended |

Templates for all of these are in `templates/`. Examples (real-data, AI/dev humor niche) are in `examples/`.

---

## What you need to know about your audience first

The skill works best when the analysis file is **specific** — not "engagement is OK", but "median 866 views, multi-character skits beat list reels by 3x, posting 6:30 PM IST wins". 

Run the Apify scrape on your own account first. Look at the data. Find the patterns. The skill consumes those patterns; it can't invent them.

Sources of truth for the analysis file:
1. **Apify scrape** of your reels (`apify/instagram-reel-scraper`)
2. Your Instagram Insights for posting time + sends-per-reach
3. Optional: comment-mining your top reel for audience language

See `templates/analysis/baseline.md` for the structure.

---

## Cost

- Claude Code (free tier or Pro plan)
- Apify scrapes: ~$0.05 per reel-scrape run (free tier covers most personal accounts)
- Optional: paid trending-audio tools if you want better sound suggestions; the skill works without them.

---

## What this skill is NOT

- ❌ Not a "post for me" automation. It writes the brief; you shoot, edit, post.
- ❌ Not a magic 1M-views tool. It compounds your own signal — if your baseline is bad and your inputs are wrong, it'll faithfully follow the wrong inputs.
- ❌ Not generic "AI Instagram" content. It's a system that gets sharper because it learns from *your* data.
- ❌ Not for accounts that haven't posted enough yet — you need ≥10 reels of your own data to get a useful baseline. If you have <10 reels, post 10 first, then come back.

---

## License

MIT. See [LICENSE](LICENSE).

## Credit

Built collaboratively with Claude Code and the [1,000 Viral Hooks (PBL)](https://personalbrandlaunch.gumroad.com/) hook library. Hook templates in `examples/` are credited to PBL — replace with your own curation.

If you publish a fork, keep the loop philosophy: **your baseline analysis is the locked input**. That's where the leverage is. Don't replace it with somebody else's "best practices."
