# Architecture — instagram-daily-skill

How the skill works under the hood, and why it's structured this way. Read this if you're forking, contributing, or extending.

---

## Design principle: data is the skill, code is the harness

The skills (`SKILL.md` files) are **stateless instructions**. The actual intelligence — what voice, what hooks, what formats win — lives in the markdown files in your project root.

This is intentional:
- **Generalizable**: any creator can use the same skill code with their own files.
- **Inspectable**: a creator can read their own `voice-rules.md` and understand exactly what filters their content. No black-box prompts.
- **Versionable**: each cycle's analysis is a dated file. You have a full history of how your strategy evolved.
- **Composable**: a creator can fork and add their own skills (competitor scraper, trending-audio scout, hook-mining tool) — each one writes a markdown file, and `instagram-daily` reads all of them.

If the skill code feels light, that's by design. The leverage is in the analysis files, not the skill instructions.

---

## File responsibility matrix

| File | Authored by | Updated | Read by |
|---|---|---|---|
| `creator-profile.md` | You (once) | Rarely (handle changes, niche shifts) | every `instagram-daily` run |
| `voice-rules.md` | You (once) | When you catch a violation worth codifying | every `instagram-daily` run |
| `hooks/hook-library.md` | You + Claude (initial curation) | Quarterly refresh | every `instagram-daily` run |
| `analysis/<date>_baseline.md` | Claude from your scrape | Once per cycle (`instagram-monthly-rollup` writes new dated file) | every `instagram-daily` run + every rollup |
| `analysis/reel-formats-playbook.md` | You + Claude | When the platform meta shifts | `instagram-daily` if format guidance is needed |
| `content-calendar.md` | You (initial) + Claude (rollup proposes diffs) | Once per cycle | every `instagram-daily` run |
| `b-roll-shotlist.md` | You (initial) + Claude (mid-cycle additions) | Per cycle | every `instagram-daily` run |
| `tracking/30-day-tracker.md` | You (daily) | Daily | every `instagram-daily` run + every rollup |
| `owner-data/*.json` | Apify scrape | Once per cycle | `instagram-monthly-rollup` only |

---

## The daily loop (logical flow)

```
USER: "/instagram-daily"
    ↓
SKILL [instagram-daily]:
    1. read creator-profile.md         → handle, niche, posting times
    2. read voice-rules.md             → tone filter
    3. read content-calendar.md        → today's slot (day N → format, hook ID, funnel y/n)
    4. read hooks/hook-library.md      → fetch hook by ID
    5. read b-roll-shotlist.md         → list b-roll IDs needed for day N
    6. read latest analysis/*          → length rules, format winners, hashtag winners, posting time
    7. read tracking/30-day-tracker.md → if last 3 reels underperformed, propose pivot
    8. (optional) call apify MCP       → trending audio for niche hashtags
    9. compose brief in user's voice
    10. run final voice-rule pass before output
    ↓
USER: ships reel, logs result in 30-day-tracker.md
```

---

## The monthly loop

```
USER: "/instagram-monthly-rollup"
    ↓
SKILL [instagram-monthly-rollup]:
    1. confirm with user (Apify costs money)
    2. call apify MCP                  → fresh scrape into owner-data/<today>_<handle>_reels.json
    3. read tracking/30-day-tracker.md → ground truth of what was shipped
    4. compute delta vs last baseline  → median, ER, format ranking
    5. write new analysis/<today>_baseline.md  (NEW LOCKED BASELINE)
    6. propose content-calendar.md diffs  (user approves before applying)
    7. update memory pointer if Claude Code memory is in use
    8. output action items
    ↓
NEXT CYCLE: instagram-daily reads the NEW baseline file. Loop tightens.
```

---

## Why dated analysis files (not one rolling file)

Two reasons:

1. **Reproducibility.** If a future cycle's strategy regresses, you can compare to any past dated baseline. A single rolling file destroys this history.
2. **Compounding signal.** The skill reads only the latest baseline as the locked input — but a curious user can `diff` two months apart to see how their voice or audience shifted.

Never delete old analysis files. Disk is cheap; institutional memory is not.

---

## Why the skill never auto-edits the calendar

Trust + reversibility.

The rollup *proposes* calendar updates as a diff. The user approves and applies. This means:
- A bad analysis (e.g. one outlier reel skews the data) doesn't auto-corrupt next cycle's plan.
- The user always understands *why* the calendar changed — they read the proposed diff.
- The user can override any proposal that conflicts with off-platform context the skill can't see (e.g. "I'm pivoting niches next month, so don't bias the calendar to last month's winners").

---

## Extending the skill pack

Add your own skills as long as they follow the same data-first philosophy.

Example extensions:

| Skill name | Reads | Writes |
|---|---|---|
| `instagram-competitors` | `creator-profile.md` (niche), competitor handle list | `analysis/competitors-<date>.md` |
| `instagram-trending-audio` | `creator-profile.md` (niche hashtags) | `analysis/trending-audio-<date>.md` |
| `instagram-comment-mine` | latest top reel from `tracking/30-day-tracker.md` | `analysis/audience-language-<date>.md` |
| `instagram-hook-mine` | competitor analysis + comment-mine output | new rows added to `hooks/hook-library.md` |

Each new skill should:
1. Have a `SKILL.md` with descriptive frontmatter (so Claude can auto-trigger it)
2. Read existing files for context — never invent it
3. Write its output as a new markdown file (not stdout-only) so `instagram-daily` can pick it up
4. Be optional — the daily loop must still work if the new skill never runs

---

## Anti-patterns to avoid in forks

- **Don't bake "best practices" into the skill code.** They date instantly. The skill should defer to the creator's analysis files.
- **Don't merge personal-account voice with business-handle voice in the same `voice-rules.md`.** Fork once if you run two accounts.
- **Don't make the skill write to a database.** Markdown files are version-controllable, diff-able, and survive Claude Code restarts. Don't trade that for a SQLite file.
- **Don't auto-post.** This skill writes briefs. The human ships. Auto-posting collapses the trust loop and makes voice violations un-catchable.

---

## Testing your fork

If you fork and modify the SKILL.md, sanity-check:

1. Does it still run with no `tracking/30-day-tracker.md`? (Day 1 user case.)
2. Does it gracefully degrade if Apify is unreachable?
3. Does the voice-rule pass actually catch a violation? (Manually inject a salesy hook into the calendar and confirm the skill swaps it.)
4. Does the rollup respect "do not auto-overwrite calendar" (must propose diff, never apply)?

If yes to all four, the fork is healthy.
