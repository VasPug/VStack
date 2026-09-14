---
name: explain-system
description: Use when someone asks how a system, feature, error or concept works — "what does X do", "how does this work", "explain this", "in plain english", "what do you mean by...", "so what are you saying". Also when a previous explanation was too long and they asked for it shorter or simpler.
---

# Explain a system

The failure this prevents is not being wrong. It is being complete.

A correct, thorough, well-organized explanation that buries the answer costs the
reader a second round trip — and they have to ask for it, which is worse than
you just having led with it.

## The shape

**One sentence first. Always.** The direct answer to what they asked, before any
context, mechanism or caveat. If you cannot write that sentence, you do not yet
understand the thing well enough to explain it.

Then stop and check what is actually needed:

- **A "what is X" question** wants one sentence and a concrete example. Often
  nothing more.
- **A "how does it work" question** wants the path something takes, named in
  order, three to six steps. Not every branch — the main one.
- **A "what do you mean by <quoted clause>"** wants *that clause* explained.
  Answer the clause. Do not re-explain the topic; they understood everything
  except that.
- **A "so what are you saying" / "in one sentence"** means the answer was buried.
  Give the decision or the conclusion, nothing else, and stop.

## Read before you answer

Trace the real path — the actual function, the actual config, the actual call.
An explanation assembled from names and comments is how you confidently describe
behaviour the code does not have. A header saying `AGENT-AGNOSTIC` over types
carrying `clipId` and `windowStartMs` is not agnostic.

If you did not read it, say you are inferring.

## Plain English means no borrowed nouns

The reader does not know your identifiers. `window`, `manifest`, `seam`,
`engagement`, `hop` are words the codebase made up, and using them to explain the
codebase explains nothing.

Name the thing by what it does for a person. If you must use the internal word —
because they will see it in the UI or the logs — define it in the same breath,
once: *"a hop — one step of the plan, run by one role."*

## Answer from the user's side

"How does it work" usually means "what happens when someone does this", not
"what is the call graph". Prefer:

> You click Run. It takes the input you saved earlier, sends it through the
> workflow as it is today, and shows the new score beside the old one.

over a description of which module calls which. Reach for the call graph only
when they asked about the code.

## When they say "simpler"

**Do not say the same thing with shorter words.** They did not fail to read it;
the frame was wrong. Change the frame:

- Give a concrete walkthrough with real values instead of the general rule.
- Give an analogy to something outside the system.
- Show the before and after — what is true now, what is true afterwards.
- Ask which part lost them, if you genuinely cannot tell. One question, not three.

And answer only the delta. Re-explaining what already landed is what made it
long the first time.

## When prose is the wrong medium

Some things are shapes, not sentences — a flow with branches, a layout, an
architecture with more than about five parts. Prose describing a shape is long
*and* unclear.

Draw it. A small diagram or a published page with a picture beats three
paragraphs, and the effort is repaid the first time someone else reads it.
Ask before building something big; just do it when it is small.

## Length

Default to under six lines. A question asked in one line is rarely answered
honestly in twenty.

Go long only when they asked for depth — a walkthrough, a report, a full
explanation. That is not padding and should not be trimmed.

Never close with a summary of what you just said.
