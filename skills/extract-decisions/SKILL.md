---
name: extract-decisions
description: Use when a meeting transcript, recording, long thread or call summary needs to become the decisions it contains — "what did we decide", "read this meeting and tell me what matters", "pull the decisions out of this thread", "what did they agree to", "summarize this call".
---

# Extract decisions

A transcript is mostly people thinking out loud. The valuable part is small: the
handful of moments where something became settled, and what each one rules out.

The failure is producing a summary — a faithful, chronological, useless account
of everything said. Nobody reads it, and the decisions stay buried in it.

## What counts as a decision

**Something that changes what happens next.** If the work would proceed
identically whether or not it was said, it is discussion, not a decision.

Three things get confused with decisions and are not:

- **A preference stated.** "I think we should probably..." with no one agreeing
  is one person's opinion. Record it as a position, not a decision.
- **A topic raised.** Naming a problem is not choosing what to do about it.
- **A restatement.** Someone summarizing what was already agreed earlier in the
  call is not a second decision.

An **assignment** is a decision: someone now owns something they did not before.

A **rejection** is a decision, and often the most valuable one — it stops the
same idea being re-proposed next month. Record what was ruled out and why.

## The output

```
Decided
  - <what is now true> — <who decided> — <what this rules out or requires>

Assigned
  - <person> owns <thing> — <by when, if said>

Open — raised, not settled
  - <the question> — <what it blocks> — <who needs to answer>

Positions — someone's view, not agreed
  - <person>: <their view, in one line>
```

Order `Decided` by how much each one changes, not by when it was said.

**Every line names its consequence.** "We'll use deployment records" is half a
decision; "We'll use deployment records, so nothing new gets written to a bucket
and the gateway split stops mattering" is the whole one. The consequence is why
anyone reads this later.

**Name who.** "The team agreed" is unverifiable and unattributable three weeks
on. If it genuinely was everyone, say so.

## Read it all before writing any of it

Decisions get reversed later in the same call. Someone proposes, three people
discuss, and twenty minutes on it quietly dies or flips. If you extract in
order as you read, you will record the version that got overturned.

Read the whole thing, then write. Where something was decided and then changed,
record only the final state — and say it changed, because the reason it changed
is usually the most important sentence in the transcript.

## Quote sparingly, and only the load-bearing line

One short quote where the exact words matter — a constraint, a refusal, a
definition someone will argue about later. Everything else in your own words.

A decisions doc made of quotes is a transcript with extra steps.

## What to do with the ambiguous ones

Transcripts are unclear. Two people can leave a call believing different things.

When you cannot tell whether something was settled, **put it in Open and say
why it is ambiguous** — "sounded agreed but nobody confirmed the date." Do not
resolve it by picking the reading you prefer. A wrongly-confident decision line
is worse than an honest open question, because the next person builds on it.

## Then

Say where this should live — a doc, a decision record, a ticket — but do not
invent a destination. If the decisions contradict something already written
down, say so explicitly rather than quietly superseding it.
