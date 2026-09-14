---
name: plan-week
description: Use at the start of a week, or mid-week when scope changes, to turn a week of evidence — your commits, your PRs, the team's chat, last week's goals — plus your own intent into a short list of committed outcomes. Triggers: "plan my week", "what should my goals be", "set my weekly goals", "re-scope my week", "help me figure out what to work on".
---

# Plan the week

A goal is a commitment with a date and someone other than you who can check it.
A list of things you intend to touch is not a goal. This skill exists to stop
the second thing from being written down as the first.

Four phases, in order. **Do not propose goals before Phase 4.** The questions
come before the answers — propose early and the human anchors on your draft, so
you get your week instead of theirs.

The phase that earns this skill's keep is Phase 3. Skip it and you will write a
beautiful, dated, verifiable plan to build something nobody can use.

## Phase 1 — Evidence

Gather before you ask anything. The human should not have to tell you what they
did; you can read it.

```bash
# commits across every local repo
for d in $(find ~ -maxdepth 3 -name .git -type d -not -path "*/node_modules/*" 2>/dev/null); do
  r=$(dirname $d)
  git -C "$r" log --all --author="$(git -C "$r" config user.email)" \
    --since="7 days ago" --pretty="%h %s" 2>/dev/null | sed "s|^|$(basename $r): |"
done

gh search prs --author=@me --merged --merged-at ">=$(date -v-7d +%F)" --limit 50 \
  --json repository,number,title -q '.[]|"\(.repository.nameWithOwner)#\(.number) \(.title)"'
gh search prs --author=@me --state=open --limit 60 \
  --json repository,number,title,updatedAt -q '.[]|"\(.updatedAt[:10]) \(.repository.nameWithOwner)#\(.number) \(.title)"'
gh search issues --assignee=@me --state=open --limit 30 \
  --json repository,number,title -q '.[]|"\(.repository.nameWithOwner)#\(.number) \(.title)"'
gh search prs --review-requested=@me --state=open --limit 20 \
  --json repository,number,title -q '.[]|"\(.repository.nameWithOwner)#\(.number) \(.title)"'
```

`date -d '7 days ago' +%F` on GNU/Linux. Two traps worth knowing:

- **`gh search prs --state=closed` does not distinguish merged from abandoned.**
  Both come back as `closed`. If you report "they cleared 18 PRs" without
  checking `mergedAt`, you may be congratulating someone for deleting a week of
  approved work. Always re-check with `gh pr view <n> --json mergedAt`.
- **`export -f` does not survive into `xargs`** in these shells. To fan out per
  PR, write the helper to a file, `chmod +x`, and call that.

**Open PRs are a workstream, not a footnote.** Sort them by *what to do* — merge,
close, chase a reviewer, fix, decide — not by repo. A queue of 35 usually
contains 10 that clear with no code at all, and finding those is worth more than
any goal you will write. If there are more than ~15, publish the triage as its
own artifact; it is too much to hold in a chat message.

**The team, last 7 days.** Read the channels they actually work in — ask once,
remember the answer. Look for exactly three things: what someone is **waiting on
them** for, what the team **decided** that changes their plan, and what **broke**
that nobody has claimed. Ignore standups, deploy bots and chatter. No access?
Say so in one line and continue; do not fake it.

**Last week's goals**, wherever they live. Landed, slipped, or quietly abandoned.
The abandoned ones are the most useful input you will get.

**Then write the brief.** At most 15 lines, no paragraphs:

```
Shipped: <3–5 lines, outcomes not commit messages>
Still open: <PRs by age — flag >10 days, and anything approved-but-unmerged>
Waiting on you: <name — what — since when>
Last week, unlanded: <goal — what actually blocked it>
Unclaimed: <anything broken that nobody owns>
```

Show it. Ask your first question in the same message.

## Phase 2 — Interrogation

**One question per turn. Two or three lines.** No preamble, no restating their
answer, no "great, that's helpful". If a sentence only frames another sentence,
delete it.

If they ask to see all the questions up front, show the whole ladder at once,
made specific to their evidence, then walk it. Wanting the map is not avoidance.

The ladder. Skip a rung only when the evidence already answers it — and say
which you skipped and why.

1. **Intent.** "What do you want this week to be about?" Before you show any
   opinion. Everything downstream is scored against the answer.
2. **The single win.** "If exactly one thing lands, which one makes the week
   worth it?" There is one answer. Do not accept two.
3. **Carryover.** Name each unlanded item: "still real, or dead?" Dead is fine
   and gets recorded as such.
4. **Blocked.** For anything waiting on a person: "yours to deliver, or yours to
   chase?" Chasing is a goal — *named owner and ETA by <date>* — never a
   delivery they cannot make.
5. **Momentum.** Point at what the evidence shows them spending time on that the
   intent answer does not justify: "why is this still on the list?" This rung
   does the most work. Ask it even when it is awkward.
6. **Verification.** Per goal: "who checks this, and what do they do?" The
   verifier is never them. "Anyone should find it easy to use" is a property, not
   a check; "the teammate who needs it runs it on their own data, unaided" is one.
7. **Capacity.** "Days actually available?" Then out loud: does the list fit? If
   not, return to rung 2 and cut. Do not shrink goals into vaguer ones.

Watch for two failures in yourself:

- **Accepting a task as a goal.** "Improve the UI" is a task — nobody can check
  it. If you cannot write a verification, push back once, then reshape it.
- **Letting the list grow.** Every rung should cut. Coming out with more items
  than you went in with means you interviewed instead of interrogating.

### When they say "so what are you saying?"

You over-explained. They are not confused, they are waiting for a decision.
Answer in one sentence, then give the consequences. If you hear it twice, stop
producing analysis entirely and produce the plan.

## Phase 3 — Falsify

**Between the last question and the first proposal, try to kill the plan.**

Everything so far is what the human believes and what the tools reported. Neither
is evidence that the thing they want to build has a subject. Before you commit
anyone to a week:

> Name the cheapest check that would prove this goal pointless, and run it.

Usually one grep, one API call, one file read. The check must not share an
assumption with the goal — if the result would look the same whether or not the
premise holds, it proves nothing.

What to check, in order of how often it fires:

- **Does the thing being measured, extended or fixed actually have users?** Grep
  for callers of the mechanism, not just its definition. A feature that exists,
  is tested, and has zero callers will make any metric built on it a constant.
- **Does the code do what its name and docs claim?** A file header saying
  "AGENT-AGNOSTIC" over types carrying `clipId` and `windowStartMs` is not
  agnostic. Read the types, not the comment.
- **Is the foundation you are building on still there?** An approved PR can be
  closed unmerged between one conversation and the next.
- **Is the dependency real?** "I'm blocked on X" is often "I assumed X was
  working on it." One message settles it.

When the check kills the goal, say so plainly and reshape. That is the skill
working, not failing — an hour spent here is a week not spent shipping something
inert. Record the finding in Pruned so nobody rediscovers it next month.

## Phase 4 — Proposal

Three areas, at most eight goals. More than that is the overwhelm this format
exists to prevent.

```
Theme: <one sentence — what this week is for>
What changed since last week: <what moved, what is now someone else's, what a
                               check in Phase 3 disproved>

Area 1 — <the outcome this area produces, in plain words>
  1. <outcome> by <day, date inside this week> — <the deliverable, concretely>.
     Verified by <a named person doing a specific thing>.
     <1–3 sentences: why this, why now, what fails without it>
Area 2 — …
Area 3 — …

Pruned this week, and why
Open decisions — each a dated question blocking a specific goal
```

Rules the format depends on:

- **Titles are outcomes, and a stranger must understand them.** "Every
  deployment shows its runs, not just four of them" — not "Fix the dashboard",
  and not "Seeing is believing". Clever is worse than plain. If it needs the
  conversation to make sense, rewrite it.
- **Every goal carries a date inside this week and a named verifier who is not
  them.** A goal you cannot date is not ready; move it to Open decisions.
- **Nothing disappears silently.** Anything dropped, deferred, delegated or
  merged gets one line in Pruned with the reason.
- **Blocked work is an escalation goal**, per rung 4.
- **No separate deliverables section.** The deliverable is inside each goal. A
  second list restating them is what gets the format rejected.

## Phase 5 — Handoff

The list is worthless in terminal scrollback.

Check whether a downstream publishing skill owns wherever these goals actually
live — a wiki, a shared doc, a tracker. If one exists, invoke it and pass this
output verbatim; that skill owns the destination's format and ids. If none
exists, write the list to a file and say where.

Never invent a destination, and never reformat goals to fit one you guessed at.

Then offer one more thing, short enough to paste: **a few lines for the team
saying what they're doing this week and what they're blocked on, naming the
people they need.** The plan is for them; this is what makes anyone else act.
Draft it, do not send it.

## Mid-week re-scope

Same skill, shorter run. Re-read the evidence since Monday, then go straight to
rungs 3, 5 and 7 — and run Phase 3 again if the goal's premise has moved. The
record of the change is the point: a replaced goal moves to Pruned with its
reason. Never quietly retitle a goal to match what got done; that is a rewritten
commitment and it reads as one.
