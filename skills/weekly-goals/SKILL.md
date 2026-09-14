---
name: weekly-goals
description: Use when writing or revising a week's goals somewhere a team reads them — Monday goal-setting, mid-week re-scoping, "my goal now is...", "reorganize my goals", "these are the wrong format", or a goal list that feels overwhelming. Downstream of plan-week.
---

# Weekly goals

A goal is a commitment with a date and someone other than you who can check it.
An activity list gets rejected as the wrong format, every time.

Reads `goals.publishSkill` from `~/.claude/vstack.config.json` — a local skill
that owns your destination's page ids, block structure and people. This skill
owns the *shape*; that one owns *where*. **Never invent a destination.**

Upstream is `plan-week`: it gathers evidence, interrogates, tries to falsify the
plan, and emits areas and goals with dates and verifiers already attached. When
its output arrives, your job is translation only — do not re-litigate goals that
already survived its ladder, and do not re-ask its questions.

## Shape

```
Theme: <one sentence — what this week is actually for>
What changed this week: <what moved, what is now someone else's, what a check disproved>

Area 1 — <the outcome this area produces, in plain words>
  1. <outcome> by <day, date inside this week> — <the deliverable, concretely>.
     Verified by <a named person doing a specific thing>.
     <1–3 sentences: why this, why now, what fails without it>
Area 2 — …
Area 3 — …

Pruned this week, and why
Open decisions — each a dated question blocking a specific goal
```

## The rules that do the work

- **Titles are outcomes, and a stranger must understand them.** "Every
  deployment shows its runs, not just four of them" — not "Fix the dashboard",
  and not something clever that needs the conversation to parse. If it only
  makes sense to you, rewrite it.
- **Every goal carries a date inside this week and a named verifier who is not
  you.** "Reviewed at standup" is not a verification; "the person who filed it re-runs it and gets
  the same number" is. A goal you cannot date is not ready — move it to Open
  decisions.
- **Three areas, eight goals at the outside.** More than that is the overwhelm
  this format exists to fix.
- **Mirror your lead's stated priorities, in their order**, if they gave any. A
  workstream that is not among them belongs in Pruned.
- **No separate deliverables section.** The deliverable is inside each goal. A
  second list restating them is what gets the format rejected.
- **Nothing disappears silently.** Everything dropped, deferred, delegated or
  collapsed gets one line in Pruned with the reason. Two goals describing one
  journey get collapsed, and Pruned says so.
- **Blocked on someone else is an escalation goal** — committed as "named owner
  and ETA by <date>", never as a delivery you cannot make.
- **Open decisions are dated questions**, each blocking a named goal. Mark
  settled ones `RESOLVED — <answer>` in place rather than deleting them.

## Writing into a shared page

Big shared pages hold other people's weeks. Three rules, learned the hard way:

- **Never replace whole-page content.** Use a targeted search-and-replace, one
  region, anchored on something unique to your own block — a user id, not a
  heading. Headings like "Goals and Deliverables" appear in every person's block
  and an under-specified anchor either fails on multiple matches or edits the
  wrong week.
- **Take the anchor text from a fetch, never retyped from the rendered view.**
  Indentation is significant and invisible on screen.
- **Re-read after writing.** Confirm your block landed *and* that the neighbours
  survived — count the blocks before and after.

## Mid-week revision

The week's goals change; the record of the change is the point.

- A new direction replaces a goal — rewrite it, and put the old one in Pruned
  with why (`deferred — a target cannot be set before the baseline exists`).
- **Never quietly retitle a goal to match what got done.** That is a rewritten
  commitment, and it reads as one.
- Answer open decisions in place the day they are settled.
- **Leave automated progress sections alone.** When goal titles change they stop
  matching until the automation regenerates — say so rather than hand-editing.
