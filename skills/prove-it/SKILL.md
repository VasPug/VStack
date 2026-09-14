---
name: prove-it
description: Use when a claim that something works needs evidence someone else can look at — "did you actually run it", "does it really work", "prove it", "show me", or before reporting any task complete. Produces an artifact, never an assertion.
---

# Prove it

A claim is not evidence. "It should work now", "the build is green", "the code
looks correct" are all things you can say without the thing having ever run.

This skill exists because that gap is invisible from the outside. The only cure
is an artifact someone else can open.

## The rule

**Name the command that would fail if you are wrong, then run it.**

If you cannot name one, you do not have proof — you have a belief. Say that
plainly rather than dressing it up.

The check must not share an assumption with the thing it checks. If the result
would look identical whether or not your change worked, it proves nothing.

## What counts

Proof is a file or a URL. Pick by what the claim is about.

**A UI change** — screenshots or video from the running app, driven by a real
browser, not a description of what it should look like.
- The changed screen at a narrow and a wide viewport.
- Every state the change touches: loading, empty, error — not just the happy one.
- Label each frame with what produced it. An unlabelled screenshot is a picture,
  not evidence.
- Prefer a published page holding the frames in order over loose files someone
  has to hunt for. Screenshots live only on disk; nothing uploads them for you.

**A user flow** — a real test file that drives the flow, run and passing.
- `screenshot: 'on'` so each test leaves a frame.
- `video: 'retain-on-failure'`, because video only matters when something broke.
  Override to `'on'` when the claim itself is about motion — an animation, a
  transition, a timing bug.
- `trace: 'on'` if the runner supports it. Most repos default to
  `on-first-retry` with `retries: 0` locally, which records nothing on a pass —
  so the report arrives with no trace link and is worth much less. The trace
  carries the network tab, so a reader sees the actual response bodies rather
  than trusting your summary.
- Transcode video to MP4 (`ffmpeg -c:v libx264 -pix_fmt yuv420p`); `.webm` will
  not open in most players.

**A backend, CLI or data change** — the command and its real output.
- Quote the line that proves it, not the whole log.
- Show the before and after where the claim is a change: the old value, the
  command, the new value.

**An integration** — the live path, exercised.
- A mocked pass is not integration evidence. If a live-backend suite exists, run
  both and report them separately.

## Say whether it was real

**This is the sentence people actually want, and it is the one most often
missing.** State, explicitly, which of these you did:

- Ran it against a live system — name the environment.
- Ran it with the network stubbed — say so; it proves rendering, not the wire.
- Did not run it — say that, and say what would.

Reporting a mocked pass as though it were live is the specific failure this
skill exists to prevent. It is worse than no proof, because it ends the
conversation.

## What is not proof

- A green CI badge, when the tests do not cover the change.
- A successful build. Compiling is not behaving.
- A diff that looks right.
- A tool call that returned 200 without you reading the body.
- "I ran it and it worked" with nothing attached.

## Reporting it

Two lines, at the end of the work:

```
Proof: <path or URL> — <what it shows, and whether it was live or mocked>
Verify yourself: <the one command, or the URL and click-path>
```

`Verify yourself` is the shortest path for someone else to see it — not a
re-run of your whole process. One or two lines.

Keep the artifacts. Delete the scaffolding around them if you like, but
screenshots and video exist only where you put them, and deleting them destroys
the thing the report points at.
