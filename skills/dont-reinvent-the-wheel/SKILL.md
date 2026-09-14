---
name: dont-reinvent-the-wheel
description: Use before designing or building anything that other people have plausibly already built — a framework, a pipeline, an eval harness, a queue, an auth flow, a plugin system. Researches how existing platforms, open-source projects and papers solved it, then gives a short recommendation. Triggers: "how do others do this", "has this been solved", "before we build this", "what's the prior art", "am I reinventing the wheel".
---

# Don't reinvent the wheel

Run this **before** the design argument, not after. Its whole value is arriving
with three examples of how the problem was already solved, instead of two people
reasoning from first principles about a problem the industry settled years ago.

The output is not a literature review. It is **the shape everyone converged on,
and what that means for you.**

## 1. Ask the structural question, not the product question

"How do people do evals" returns marketing pages. The useful question is always
about a **seam**: what does the system own, and where does it hand off?

Write the question that way before searching:

- Not "how do eval platforms work" but *"who executes the thing under test, the
  platform or the user's own code?"*
- Not "how do plugin systems work" but *"what is the boundary between host and
  plugin, and who owns lifecycle?"*
- Not "what queue should we use" but *"where does the retry decision live?"*

If you cannot phrase it as a seam, you do not yet know what you are designing.

## 2. Pick three, and make one of them open source

Three is enough to see convergence and cheap enough to actually read. More than
four and you are procrastinating.

Choose them to disagree:

- **The obvious commercial one** — what the market considers the default shape.
- **An open-source, self-hostable one** — usually the most valuable. You can read
  how it is actually built, not how it is sold, and its constraints are closer to
  yours than a hosted product's are.
- **A deliberately different one** — a config-driven tool where the others are
  SDKs, a local CLI where the others are services. Disagreement is where the real
  design choice lives.

A paper is worth adding only when the question is about an algorithm or a
guarantee. For architecture, running code beats a paper.

## 3. Read the docs that describe the boundary

Go straight to the pages about integration, not the overview:

- The page explaining how you plug **your own code** in.
- The page explaining what happens **in the UI** versus **in your process**.
- The **API reference** for the core object, which tells you what the system
  really stores.
- Anything titled *"via SDK"*, *"custom provider"*, *"webhook"*, *"self-hosting"*.

Skip the landing page, the comparison page and the blog post. Read enough to
answer the seam question and stop. Do not paste long pages into the conversation.

## 4. Name the convergence, and the disagreement

The finding worth reporting is almost never a feature list. It is:

> All three own the same four things, and none of them owns the fifth.

Say what they all do the same way, since that is the part you should copy without
arguing. Then say where they differ, because that is the decision you actually
have to make.

Also record **what nobody does.** If three mature products all avoid an approach,
that is evidence, and it is usually the approach that looks cleanest on a
whiteboard.

## 5. Recommend, briefly

```
How they do it: <the shared shape, 2-3 sentences>
Where they differ: <the real choice, 1-2 sentences>
What to take: <2-4 numbered items, in build order>
What we'd do differently, and why: <only if you genuinely have an advantage>
Settle first: <the one question that blocks starting>
```

Under twenty lines. Every claim traceable to a doc you read.

**Do not recommend adopting the tool itself unless that is on the table.** Usually
it is not — the question is what shape to build, not what to buy. Say which it is.

## The two failure modes

**Cargo-culting.** Copying a shape without its constraint. Those tools make you
bring your own API key because they sit next to someone else's model. If you *are*
the model provider, copying the BYO-key design imports a limitation you do not
have. Always ask why they did it that way before copying that they did it.

**Confirmation.** Researching after you have decided, and returning the three
sources that agree with you. Look specifically for the platform that took the
other branch, and report it even when it is inconvenient. If the research changed
nothing about your plan, either you were right or you were not really looking,
and you should say which.

## When it turns out nobody has built it

Sometimes real. Usually it means the search was wrong, and the thing exists under
a different name in a different field.

Before concluding it is novel, search the adjacent vocabulary once: what would a
database person call this, or a compiler person, or a build-systems person. Most
"nobody has solved this" problems are a solved problem with unfamiliar words.

If it genuinely is new, say so plainly and say what the closest analogue is
anyway. A near-miss still tells you which mistakes to expect.
