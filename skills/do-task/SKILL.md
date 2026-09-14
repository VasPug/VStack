---
name: do-task
description: Use when handed a goal and told to do it — "do this", "here's the task", "build X", "fix Y", a one-line goal, or a goal plus links to a PR, thread, issue or screenshot. Runs spec → plan → build → prove → report with one human gate.
---

# Do task

One human gate: the spec. Everything after it runs to completion without
stopping.

Reads `~/.claude/vstack.config.json` if present — `memoryBank`, `paths.taskWork`,
`goals`, `browser`. No config is fine; skip the steps that depend on what is
missing and say so in the report.

Delegates: **`ship-it`** owns branching, worktrees, the review battery, the PR
and CI. **`deploy-rc`** owns shared-environment deploys and only runs when the
human asks in as many words.

## Checklist

A todo per step, in order.

1. Ingest · 2. Spec — **human gate** · 3. Plan · 4. Build · 5. Prove · 6. Report

## 1. Ingest

Read what the human typed, then everything it points at — a PR, a thread, an
issue, a file path, a screenshot.

Four things must be settled before the spec, and only the first is handed to you:

- **Goal** — what they typed. Take it at face value; do not widen it.
- **Repo** — infer from the goal's nouns, then confirm by looking.
- **Type** — UI / Agent / Backend / Docs. Decides the proof in step 5.
- **Constraints** — hard limits they stated. Honour them without renegotiating.

**Infer; do not open with a questionnaire.** A one-line goal names its own domain
most of the time, and step 2 is a real gate — state every inference in the spec
and a wrong guess gets caught there. Only genuine ambiguity about which repo the
work lands in is worth a spec question.

Derive a kebab-case `<task-slug>`. It names `<paths.taskWork>/<task-slug>/`.

**Refresh the corpus.** If `memoryBank` is configured, fetch before reading —
a stale corpus is worse than none, because it still reads as authoritative.

```bash
git -C <memoryBank.path> fetch -q origin
```

Do not switch branches or stash; the checkout is often dirty. Read from the ref
instead: `git show <memoryBank.ref>:<path>`, `git grep <pattern> <memoryBank.ref>`.
Say in the spec which you used.

**Find every `CLAUDE.md` in the target repo before writing anything.**

```bash
find <repo> -name CLAUDE.md -not -path '*/node_modules/*' -not -path '*/.git/*'
```

A directory-scoped one only loads after a file in that directory is touched —
which can be after the first commit. They carry the rules most likely to be
broken precisely because they are local and unexpected, including directories
that forbid committing at all. The repo's own definition of done — tests, lint,
typecheck, build — lives there and applies to every task in that repo.

## 2. Spec — the only human gate

Invoke your brainstorming skill. Its context exploration must cover, in order:
the configured corpus and any prior art in it on this subject, then the repos the
task actually touches — structure, existing patterns, `git log`.

The spec goes to `<paths.taskWork>/<task-slug>/spec.md` — **outside every git
repo.** Never into the target repo, never committed. It is scaffolding for this
run, not an artifact.

**This is the last time the human is involved until step 6.** After approval, do
not ask for confirmation or direction again. Hit a genuine blocker? Finish
everything not blocked by it and say so in the report.

## 3. Plan

Set up the branch and worktree with **`ship-it`** steps 1–2, then write the plan
to `<paths.taskWork>/<task-slug>/plan.md` — never in the repo.

**Do not ask the human to review the plan.** That gate is deliberately removed.

## 4. Build

Use your subagent-driven development skill: TDD per task, a review after each
one, a final whole-branch review. Do not restate or re-run those here.

Do not follow it into a "finishing a branch" step that presents a merge/PR/keep
menu and waits — that is the gate this pipeline exists to remove. The answer is
always push and open a PR. **Do** take its list of rulings made on the human's
behalf; step 6 reports it, and it is the only place those decisions reach them.

When every task is complete, hand off to **`ship-it`** from step 3 onward — the
review battery, the base re-check, the PR, CI, and the description freshness
pass. It knows not to re-run the correctness review this step already did.

## 5. Prove

Keyed off `Type`.

**UI** — use `browser.testDriver` to explore the running app and work out
selectors, then write a real test that drives the flow the task describes. Run
it with `screenshot: 'on'`, `video: 'retain-on-failure'` — a screenshot per test
is the proof; video only matters when something failed. Override to `video: 'on'`
when the claim itself is about motion. Copy artifacts to
`<paths.taskWork>/<task-slug>/proof/` with readable names, and transcode video to
MP4 (`ffmpeg -c:v libx264 -pix_fmt yuv420p`) — `.webm` will not open in most
players. The test stays in the repo; the artifacts are for the human.

**Agent** — deploy to a sandbox, invoke with realistic input, capture what went
in and what came back.

**Backend / Docs** — the suite or build output. Quote the passing line, not the
whole log.

**Say whether the run was mocked.** Most e2e suites stub the network, which
proves rendering but not the wire. If a live-backend suite exists, run both and
report them separately. A mocked pass reported as integration evidence is the
exact failure this pipeline exists to prevent.

## 6. Report

Twelve lines maximum.

```
TASK DONE — <the goal, restated in a few words>
Changed: <files/areas, one line>
Tried: <approach, and anything that failed on the way>
Result: <what now works>
Rulings: <each call made without you · cost if wrong — or "none">
PR(s): <one link per repo touched>
Verify yourself: <shortest path for the human to reproduce the proof>
Proof: <local proof/ path>
Not covered: <what was left out and why — or "nothing">
```

**Write `TASK DONE` only when it genuinely is** — PR open, CI settled, no
subagent running, no unresolved blocker. Anything in flight means it is not done;
say what is pending instead. It means "reviewable now", never "merged".

**`Rulings`** — every decision taken without the human, one clause each: what you
decided, what it costs if wrong. With a single gate, this line is the entire
record of what was chosen on their behalf. A ruling that dies in the worktree was
a decision made in secret. Write `none` if there were none; never omit the line.

**`Verify yourself`** — one or two lines. The URL and click-path, or the single
command. Not a re-run of the pipeline.

**UI tasks: leave two things open.** The running app, on the exact screen the
change affects, signed in, servers up — that URL is the `Verify yourself` URL.
And the Playwright HTML report served with traces on. `trace: 'on'` is not
optional: most repos default to `on-first-retry` with `retries: 0` locally, which
records nothing on a pass, and the report arrives with no **View Trace** link.
The trace carries the network tab, so the human reads the actual response bodies
instead of trusting a summary.

Then clean up. Delete `spec.md`, `plan.md` and notes from the task-work
directory — scaffolding, not artifacts. Before deleting, confirm none of it was
staged or committed in any touched repo.

**Keep `proof/`.** Screenshots and video exist only on disk; deleting them
destroys the thing the report points at.

Then stop. The open PR is the status and the report is the handover — so the
report must stand alone. Someone reading only it should know what changed, where
to review it, and how to see it working, without a follow-up question.
