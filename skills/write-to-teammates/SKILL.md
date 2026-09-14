---
name: write-to-teammates
description: Use when drafting a message to send to colleagues — a Slack post about what you're working on, an update, a proposal, a question, a nudge, an escalation. Triggers: "write a message to my team", "draft a slack message", "tell them what I'm doing", "message for standup", "how do I ask them for this".
---

# Write to teammates

The draft has one job: get a real reply from a busy person who is not inside
your head.

The common failure is not length. It is writing an **announcement** when what
works is a **person saying what they want and inviting correction**.

## The shape that works

```
I want to <do the thing>. <One sentence on why, in plain words.>
<Optional: an open question that invites someone to object.> @person

I also want to <second thing>, because <plain reason>. <One concrete
detail so they can picture it.> <A comparison, if there is an obvious one.> @person

On <the thing they last saw you doing>: <where it stands now.>

<Link to where the detail lives.> Let me know if <the ask>.
```

One paragraph per topic. No bullets — bullets read as a status report and get
skimmed. Prose with a subject reads as a person and gets answered.

## Rules

**Write in first person, as intent.** "I want to build an eval that scores a
workflow" — not "This week: eval scoring." A headline announces; a sentence with
"I want" invites a reply.

**No code identifiers.** `packages/eval`, `api#412`, `src/lib/runner.ts` mean nothing to
someone who has not read your diff, and their presence makes the message look
like it was generated. Say "the console", "the runs table", "the eval".

**Say why in ordinary words.** "because right now it's not very friendly and it's
hard to trace and observe runs." Not "to improve observability." The reader should
recognize the complaint.

**One concrete detail per topic.** Enough that they can picture it: *"mark a run
as a good or bad example straight from the runs table, and those saved examples
become the set you re-run a workflow against to see if it got better or worse."*
That sentence is long and that is fine — it is one idea, in order, with no
nested clauses.

**Never write a dense compound clause.** This kind of sentence gets rejected:

> *"a proposal for an agent loop that takes a merged PR or a QA report in Slack
> through to a reviewed PR, with one human gate on the spec"*

Three prepositional phrases stacked into one noun. It parses only if you already
know the answer. Break it into two plain sentences or cut it.

**Tag one person per topic, at the end of the paragraph.** Not inline mid-
sentence — that reads like an assignment. At the end it reads like "you would
know about this."

**Hedge where you are genuinely unsure.** "Not sure if <person> is working on
something similar." "I'm interested in..." This is not weakness; it is the thing
that makes someone reply with what they know. A message with no opening for
disagreement gets no disagreement, and then you build the wrong thing.

**Cite a comparison when one exists.** "This is something LangSmith does" tells
them the shape instantly and costs four words.

**Link where the detail lives** — the design, the doc, the page. The message
carries the intent; the link carries the specifics.

**End with the ask.** "Let me know if I should reprioritize anything." "If you
can make a PR without it I can merge." Say what you want to happen next, and what
you will do in return.

## Length — this is the rule that gets broken

**One short paragraph, plus the ask. That is the ceiling.** Three or four
sentences of body. Not "short for an update" — short, full stop.

Four paragraphs is already too long and will not be read. If you have written
four, the message is doing a job a doc should do: send the one paragraph, link
the doc.

The way to cut is not to shorten sentences. It is to delete whole points. A
message with one point that lands beats three points that get skimmed. Pick the
single thing you need them to react to, and the single thing you need answered.

Cut these first, every time:
- The walkthrough of how it would work. They will ask if they care.
- Your evidence in detail. "I looked at how X and Y do it" is the whole citation.
- The second and third benefit.
- Any sentence that restates the previous sentence in different words.

## Tone

Plain and slightly informal. "Hey" is a fine opener. Contractions are fine.
Do not polish it into a press release. An over-smooth message reads as written by
something that is not a colleague, and people answer colleagues.

**No em dashes. No semicolons. No colons introducing a list.** People do not type
those in chat, and a message full of them reads as generated no matter how good
the content is. Use a comma, or start a new sentence.

Also out: "Worth noting", "Net effect", "the key thing is", "that said",
bolded **lead-ins** at the start of a line, and any sentence whose job is to
announce the structure of the next one.

## Before sending

Draft it and show it. **Do not send it.** Sending on someone's behalf is theirs
to authorize, every time — approval for one message is not approval for the next.

Then check three things:
- Would someone who has not read the code understand every sentence?
- Is there a specific person who should reply, and are they tagged?
- Is the ask in it?

## A real one, before and after

The task: reply to a colleague who objected that an eval should be a platform
feature rather than something each team builds.

**Rejected — four paragraphs.** Conceded the point, then summarized three
research sources, then walked through the proposed flow step by step, then made
a billing argument, then asked the question. Every paragraph was true and the
whole thing was unreadable. The verdict was "this message is horrible, it's way
too long, no one's gonna read all that."

**Sent — and it is the shape to copy:**

```
I looked at how Langfuse and LangSmith do it. The platform takes in the dataset,
lets people mark runs as correct or incorrect, and has an LLM as a judge, but they
make you put in your own API token for it. We could just use our inference token
for that. @alex

Where would the datasets be hosted though, on the object store or the backend
side? @sam
```

What it does:

- **Names the evidence in six words** and does not summarize it. "I looked at how
  Langfuse and LangSmith do it." Anyone who wants the detail will ask.
- **One sentence of what they do**, listed plainly, no structure words.
- **One sentence of what we could do**, offered lightly. "We could just use our
  inference token for that", not "this gives us a cost advantage."
- **The open question is its own paragraph**, addressed to the one person who can
  answer it. It is a real question with two named options, answerable in a line.
- **Two people tagged, one each**, at the end of their own paragraph.
- **No em dashes, no semicolons, no bold.** It reads like a person typing.

What got cut to make it: the third research source, the entire flow walkthrough,
the point about reusing the existing callback, and the billing argument. All
correct, none of it load-bearing for the reply.
