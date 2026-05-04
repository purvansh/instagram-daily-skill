# Setup — instagram-daily-skill

The full walkthrough from zero to "I ran `/instagram-daily` and got my first brief."

Total time: ~2–3 hours (the bulk is the baseline analysis, which is a one-time setup).

---

## Prerequisites

| Tool | Why | Cost |
|---|---|---|
| [Claude Code](https://claude.com/claude-code) | Runs the skills | Free tier OK |
| [Apify](https://apify.com) account + API token | Scrapes your Instagram for the baseline + monthly rollup | Free tier covers most personal accounts; paid runs ~$0.05 |
| Your existing Instagram account with ≥10 reels | The skill needs your own data; can't run on a brand new account | — |

---

## Step 1 — Set up your project workspace

Pick a directory where this lives. `~/Documents/my-instagram` is fine. Then:

```
git clone <this-repo> ~/Documents/my-instagram
cd ~/Documents/my-instagram
```

(Or download the zip and extract it there.)

---

## Step 2 — Install the skills into Claude Code

Skills go in `.claude/skills/` either at the project root (project-scoped) or `~/.claude/skills/` (user-global). Project-scoped is recommended — keeps the skill tied to this workspace.

```
mkdir -p .claude/skills
cp -r skills/instagram-trending .claude/skills/
cp -r skills/instagram-daily .claude/skills/
cp -r skills/instagram-monthly-rollup .claude/skills/
```

Verify all three are loaded by opening Claude Code in this directory and typing `/help` — you should see `instagram-trending`, `instagram-daily`, and `instagram-monthly-rollup`.

---

## Step 3 — Configure Apify MCP

Create or edit `.mcp.json` in your project root:

```json
{
  "mcpServers": {
    "apify": {
      "type": "http",
      "url": "https://mcp.apify.com/",
      "env": {
        "APIFY_TOKEN": "<your-apify-token>"
      }
    }
  }
}
```

Get your Apify token from https://console.apify.com/account/integrations.

In `.claude/settings.local.json`, enable the Apify server:

```json
{
  "enabledMcpjsonServers": ["apify"]
}
```

---

## Step 4 — Pull your baseline scrape

Open Claude Code in this project and ask:

> Run `apify/instagram-reel-scraper` on `<your-handle>` for the last 90 days, save to `owner-data/<today>_<your-handle>_reels.json`.

Or run from the Apify console manually if you prefer. The output JSON should have shape:

```json
{
  "scrapedAt": "YYYY-MM-DD",
  "account": "your_handle",
  "totalReels": 19,
  "reels": [
    {"shortCode": "...", "caption": "...", "videoViewCount": 866, "likesCount": 53, "commentsCount": 1, ...}
  ]
}
```

---

## Step 5 — Generate the baseline analysis

Copy `templates/analysis/baseline.md` to `analysis/<today>_baseline_analysis.md`. Then ask Claude:

> Read `owner-data/<today>_<handle>_reels.json` and fill in `analysis/<today>_baseline_analysis.md` using the template. Use the 40/35/25 scoring rubric. Identify what worked and what tanked.

Review the output. **Do NOT skip this step.** The skill's quality depends entirely on this analysis being accurate.

If you have <10 reels in your scrape, post 10 reels first, then come back. The skill needs real data.

---

## Step 6 — Fill in the templates

In this order (each builds on the previous):

| Order | Template | Where it goes (project root) |
|---|---|---|
| 1 | `templates/creator-profile.md` | `creator-profile.md` |
| 2 | `templates/voice-rules.md` | `voice-rules.md` |
| 3 | `templates/hooks/hook-library.md` | `hooks/hook-library.md` |
| 4 | `templates/b-roll-shotlist.md` | `b-roll-shotlist.md` |
| 5 | `templates/content-calendar.md` | `content-calendar.md` |
| 6 | `templates/tracking/30-day-tracker.md` | `tracking/30-day-tracker.md` |

For `hooks/hook-library.md`: source ~50–150 hook templates, filter against your `voice-rules.md`, tag each by format and pillar. The PBL "1,000 Viral Hooks" is a good starting library, but cull hard — only ~10% of hooks fit any given niche.

For `content-calendar.md`: pick 28 hook IDs across the 30 days. Rule of thumb pillar mix: 60% your strongest format, 15–25% your secondary format, 10% experimentation, 5% cross-pollination.

For `b-roll-shotlist.md`: list every costume / face-cam beat / screen recording / setting / overlay your 28-reel slate needs. Tag each shot with which calendar days use it.

---

## Step 7 — Schedule your batch shoot

Before day 1 of the cycle, dedicate ~5 hours to filming the b-roll batch:

- 1 hour: costumes (photo reference + 1 wide clip per costume)
- 1 hour: face-cam reaction beats (5 takes per beat)
- 1 hour: screen recordings
- 30 min: settings
- 30 min: reaction inserts

File everything per the naming convention in `b-roll-shotlist.md`. The skill will tell you "drop F-01 in the cold open" — but only if F-01 actually exists at `b-roll/F/F-01_*.mp4`.

---

## Step 8 — Set the cycle start date

Edit `content-calendar.md`, update the metadata table:

```
| Cycle start date | 2026-05-02 |
```

This is what tells the skill "today is day N."

---

## Step 9 — Run your first daily flow

Two skills, in order:

```
/instagram-trending     # ~3-5 min, ~$0.05 Apify cost
/instagram-daily        # reads the trending file, outputs full brief
```

`instagram-trending` writes `analysis/trending-<today>.md`. `instagram-daily` reads it. **If you skip the trending step on a T1 or T2 day, the daily skill will prompt you to run it first** — don't fabricate trends.

If today is a **T3 evergreen day**, you can skip `/instagram-trending` (it'll write a one-line "T3, no research needed" file anyway).

Shoot the 1 fresh face-cam line, drop in the batched b-roll, edit, post.

---

## Step 10 — Log results

Each day, fill in the `tracking/30-day-tracker.md` row for the day you posted. View counts at 3-day and 7-day are the two snapshots that matter. The skill warns you mid-cycle if your last 3 reels underperformed — it can only do that if you log the data.

---

## Step 11 — Day 30: rollup

```
/instagram-monthly-rollup
```

Or ask: "Run the monthly rollup."

The skill:
1. Confirms with you (Apify costs money)
2. Re-scrapes your reels
3. Writes a new dated baseline at `analysis/<new-date>_baseline_analysis.md`
4. Proposes calendar updates (you approve before they're applied)
5. Updates memory

Then you start the next cycle with the new locked baseline. The system gets sharper every loop.

---

## Common issues

**The skill doesn't show up in `/help`.**
Restart Claude Code. If still missing, check `.claude/skills/instagram-daily/SKILL.md` exists with valid YAML frontmatter (no syntax errors).

**Apify scrape returns 0 reels.**
Your account is private, or the handle is misspelled. Verify the handle works in the Apify console first.

**The skill writes a brief that violates my voice rules.**
Your `voice-rules.md` is too vague. Be specific. Add a "DO NOT" line with a *why*. The skill applies judgment based on the why.

**My median views aren't moving after 30 days.**
Two likely causes: (1) baseline analysis identified the wrong winning format, (2) calendar isn't matching the analysis (e.g. you wrote "skits win" but stuffed the calendar with talking-heads). Re-read the analysis and audit the calendar against it.

**I want to add more skills (competitors, trending-audio, hooks).**
The skill pack is intentionally minimal — daily + monthly is the loop. Add your own skills for competitors / trending-audio / hooks if you want — they should write their output into files that `instagram-daily` reads (e.g. `analysis/competitor-snapshot.md`, `analysis/trending-audio.md`).

---

## Where to ask questions

Open an issue on the repo. Include: your `creator-profile.md` (redact the handle if private), the brief output that surprised you, and what you expected.
