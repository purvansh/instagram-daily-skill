# Hook Patterns — Psychology-Based Hook Library (Template)

This folder is the home for **deep dives on individual psychological hook patterns** — one file per pattern. The `instagram-daily` skill reads everything in this directory and considers each pattern as a hook arc variant.

## How to add a new pattern

1. Drop a reference image / video / link into a `random content/` folder (or wherever you collect inspiration)
2. Ask Claude: "research this pattern, add it to hook-patterns" — Claude writes a new file here following the structure of existing files
3. The skill auto-picks it up next run; no manual wiring

## Recommended file structure per pattern

```markdown
# Hook Pattern — <name>

**Source:** <reference URL or screenshot path>

## The pattern
<structural template>

## The psychology behind it
<2–4 stacked psychological levers>

## Why this hook earns saves/sends/comments
<the engagement signature it produces>

## Structural template
<beat-by-beat hook frame>

## Variants
<2–5 variations of the structure>

## When NOT to use this hook
<contraindications>

## Voice-rule check for THIS account
<which voice rules apply / interfere>

## Niche-specific templates
<8–12 fill-in-the-blank versions for your niche>

## Format fit per template
<which template works in which reel format>

## Body structure (after the hook)
<how the body should resolve the hook>

## How `instagram-daily` uses this
<when the skill should pick this pattern + cycle cap if any>
```

## Patterns library lifecycle

The skill expects:
- A `README.md` (this file) listing all patterns in a table with: pattern name | psychology | best format | best for
- One `<pattern-name>.md` file per pattern
- Each pattern file is self-contained — no cross-file dependencies

When a pattern stops working (cycle data shows reels using it consistently underperform), the skill should be told to deprioritize it — add a `**STATUS: deprecated**` line at the top of that pattern's file. Don't delete; the historical reasoning is part of the dataset.
