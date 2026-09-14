---
name: plan-week
description: Use at the start of a week, or mid-week when scope changes, to turn a week of evidence — your commits, your PRs, the team's chat, last week's goals — plus your own intent into a short list of committed outcomes. Triggers: "plan my week", "what should my goals be", "set my weekly goals", "re-scope my week", "help me figure out what to work on".
---

# Plan the week

A goal is a commitment with a date and someone other than you who can check it.
A list of things you intend to touch is not a goal. This skill exists to stop
the second thing from being written down as the first.

Three phases, strictly in order. **Do not propose goals before Phase 3.** The
whole value of this skill is that the questions come before the answers — if you
propose early, the human anchors on your draft and you get your week, not theirs.

## Phase 1 — Evidence

Gather before you ask anything. The human should not have to tell you what they
did; you can read it. Run the shell sweeps in parallel, then the integrations.

**Their own work, last 7 days:**

```bash
# commits across every local repo
for d in $(find ~ -maxdepth 3 -name .git -type d -not -path "*/node_modules/*" 2>/dev/null); do
  r=$(dirname $d)
  git -C "$r" log --all --author="$(git -C "$r" config user.email)" \
    --since="7 days ago" --pretty="%h %s" 2>/dev/null | sed "s|^|$(basename $r): |"
done

# PRs merged in the window, and everything still open
gh search prs --author=@me --merged --merged-at ">=$(date -v-7d +%F)" --limit 50 \
  --json repository,number,title -q '.[]|"\(.repository.nameWithOwner)#\(.number) \(.title)"'
gh search prs --author=@me --state=open --limit 50 \
  --json repository,number,title,updatedAt -q '.[]|"\(.repository.nameWithOwner)#\(.number) \(.title) [\(.updatedAt[:10])]"'

# work pointed at them
gh search issues --assignee=@me --state=open --limit 30 \
  --json repository,number,title -q '.[]|"\(.repository.nameWithOwner)#\(.number) \(.title)"'
gh search prs --review-requested=@me --state=open --limit 20 \
  --json repository,number,title -q '.[]|"\(.repository.nameWithOwner)#\(.number) \(.title)"'
```

Use `date -d '7 days ago' +%F` instead of `date -v-7d +%F` on GNU/Linux.

**The team, last 7 days.** If a Slack integration is available, read the channels
the human actually works in — not every channel they are a member of. Ask which
ones once and remember the answer. You are looking for three things only:

- what someone is **waiting on them** for, named and unanswered
- what the team **decided** that changes their plan
- what **broke** and has not been claimed

Ignore standups, deploy bots and social chatter. If no Slack access exists, say
so in one line and carry on — do not fake it.

**Last week's goals.** Read wherever they were written down (a Notion page, a doc,
the previous run of this skill). For each one: landed, slipped, or quietly
abandoned. The abandoned ones are the most useful input you will get.

**Then write the brief.** At most 15 lines, no prose paragraphs:

```
Shipped: <3–5 lines, outcomes not commit messages>
Still open: <PRs/issues with age — flag anything >10 days>
Waiting on you: <name — what — since when>
Last week, unlanded: <goal — what actually blocked it>
Unclaimed: <anything broken that nobody owns>
```

Show it. Then ask your first question in the same message.

## Phase 2 — Interrogation

**One question per turn. Two or three lines. No preamble, no restating their
answer back to them, no "great, that's helpful".** If you find yourself writing a
sentence that only frames another sentence, delete it.

Work down this ladder. Skip a rung only when the evidence already answers it —
and say which rung you skipped and why.

1. **Intent.** "What do you want this week to be about?" Ask this first, before
   showing any opinion. Everything downstream is scored against the answer.
2. **The single win.** "If exactly one thing lands, which one makes the week
   worth it?" There is one answer. Do not accept two.
3. **Carryover.** Name each unlanded item from last week: "still real, or dead?"
   Dead is a fine answer and gets recorded as such.
4. **Blocked.** For anything waiting on another person: "is this yours to deliver,
   or yours to chase?" Chasing is a goal — committed as *named owner and ETA by
   <date>*, never as a delivery they cannot make.
5. **Momentum.** Point at whatever the evidence shows them spending time on that
   the intent answer does not justify: "why is this still on the list?" This is
   the rung that does the most work. Ask it even when it is awkward.
6. **Verification.** Per surviving goal: "who checks this, and what do they do to
   check it?" The verifier is never them. "Reviewed at standup" is not a check;
   "Bren re-runs it and gets the same number" is.
7. **Capacity.** "Days actually available this week?" Then out loud: does the
   surviving list fit? If not, go back to rung 2 and cut — do not quietly
   shrink the goals into vaguer ones.

Two failure modes to watch for in yourself:

- **Accepting a task as a goal.** "Refactor the runner" is a task. "The runner's
  topology is data, and the migration guide is merged" is a goal. If you cannot
  write a verification for it, it is a task — push back once, then reshape it.
- **Letting the list grow.** Every rung should cut. If you come out of Phase 2
  with more items than you went in with, you interviewed instead of interrogating.

## Phase 3 — Proposal

Only now. Three areas, at most eight goals total. More than that is the
overwhelm this format exists to prevent.

```
Theme: <one sentence — what this week is for>
What changed since last week: <what moved, what is now someone else's>

A. <area, titled as the outcome it produces>
   A1. <one-line outcome, not a task>
       Commit: <deliverable> by <day, date inside this week>.
       Verified by: <a specific person doing a specific check>.
       <2–3 sentences: why this, why now, what fails without it>
B. ...
C. ...

Pruned this week, and why
  - <item> — <reason: dead / deferred because X / now owned by Y>

Open decisions
  - <dated question> — blocks <goal id>
```

Rules the format depends on:

- **Titles are outcomes.** "Set 3 has a frontier baseline it did not have
  before", not "Evals work".
- **Every goal carries `Commit:` with a date inside this week and `Verified by:`
  with someone else's name.** No exceptions. A goal you cannot date is not ready
  to be a goal; say so and move it to Open decisions.
- **Nothing disappears silently.** Anything dropped, deferred, delegated or
  merged into another goal gets one line in Pruned with the reason. Two goals
  describing the same journey get collapsed, and Pruned records that.
- **Blocked work becomes an escalation goal**, per rung 4.

## Phase 4 — Handoff

The list is the deliverable, but it is worthless in a terminal scrollback.

Check whether a downstream publishing skill exists for wherever this person's
goals actually live — a team wiki, a Notion page, an issue tracker. If one does,
invoke it and pass the Phase 3 output verbatim; that skill owns the destination's
format and ids, this one does not. If none exists, write the list to a file and
say where.

Never invent a destination, and never reformat the goals to fit one you guessed at.

## Mid-week re-scope

Same skill, shorter run. Re-read the evidence since Monday, then go straight to
rungs 3, 5 and 7. The record of the change is the point: a goal that gets
replaced moves to Pruned with its reason. Never quietly retitle a goal to match
what got done — that is a rewritten commitment and it reads as one.
