---
name: ship-it
description: Use when finished work needs to become a reviewable pull request — branching, worktrees, the review battery, the PR itself, and babysitting CI until it settles. Also for "open a PR for this", "get this reviewed", "why is CI red". Called by do-task, but works standalone.
---

# Ship it

Getting code into a PR someone will actually review. Nothing here is about
writing the code; it starts once the code exists and ends when CI is green.

Reads `~/.claude/vstack.config.json` if present — `paths.worktrees`,
`repos.<name>.defaultBranch`, `repos.<name>.houseReviewSkill`,
`ci.ignoredChecks`. No config is fine; the defaults below apply and you say so.

## 1. Branch from what the remote has, not what you have

```bash
git -C <repo> fetch -q origin
gh repo view <org>/<repo> --json defaultBranchRef -q .defaultBranchRef.name
```

**Never assume the default branch.** It varies per repo and branching from the
wrong one wastes the entire run. Check config first, then detect.

**Never branch from whatever the checkout is sitting on.** It is routinely on an
unrelated feature branch and often dirty.

- **New work** — branch from the freshly fetched `origin/<default>`.
- **Continuing an existing PR branch** — branch from the fetched PR ref, then
  report how far behind its base it is:
  `git rev-list --count origin/<branch>..origin/<base>`. Materially behind means
  the work, and anything built from it, is missing that much of the base. Say
  the number; never silently rebase someone else's PR.
- **Deliberately building on unmerged work** — branch from that branch and say
  so explicitly.

## 2. Work in a worktree

```bash
git -C <repo> worktree add <paths.worktrees>/<branch-with-+-for-/> -b <branch> origin/<default>
```

Default `paths.worktrees` is `.claude/worktrees`; make sure it is gitignored.
The main checkout stays parked on the freshly pulled default branch so it is
always a clean reference, and parallel tasks never fight over one working tree.

Clean up with `git worktree remove` once merged — a leftover worktree pins a
stale branch and the next `worktree add` for it fails.

## 3. Review before the PR, not after

Dispatch these in parallel, one message, since they are independent. Scale to
the diff: a docs or CI-only change does not need every lens — say which you
skipped and why rather than running them for form's sake.

1. **Over-engineering.** What to delete: speculative abstraction, reinvented
   stdlib, dead flexibility, a dependency added for what a few lines cover.
2. **House rules.** The repo's own reviewer skill if it ships one — check
   `.claude/skills/` and the repo's `CLAUDE.md`, or `repos.<name>.houseReviewSkill`
   in config. These know conventions a generic reviewer cannot.
3. **Visual — UI changes only.** No code reviewer looks at a rendered pixel.
   Start the dev server, drive the changed screens with `browser.testDriver`
   (a cold deterministic profile, not your real session), capture each at a
   narrow and a wide viewport, and hand the screenshots to a subagent to
   critique: spacing, alignment, truncation, overflow, loading/empty/error
   states, dark mode. Skip it honestly when the diff cannot move a pixel.

**Do not re-run a correctness pass your build already dispatched** over the same
diff. It will come back clean, and you paid for it twice. Run correctness here
only when the code did not come through a review-per-task build.

Act on findings yourself; do not forward them to the human. Verify each claim
before implementing it — a reviewer subagent can be confidently wrong.

## 4. Re-check the base immediately before opening

Time passed during the build and the reviews. Other people merged.

```bash
git fetch -q origin && git rev-list --count HEAD..origin/<base>
```

Behind? Rebase and re-run the repo's checks before pushing. A rebase is also the
only way a semantic conflict surfaces — git merges cleanly while two branches
change the same behaviour incompatibly, and CI is where that shows up.

If the branch is already pushed, rebasing needs a force-push. Get explicit
sign-off first, and never force-push a branch that is not yours.

Then commit, push, open the PR.

## 5. Babysit CI until it settles

`gh pr checks <n> --watch`. Do not report the work done while checks run.

- **Failed because of your change** — fix it and push again.
- **Already failing before your change** — leave it, and say so. Prove it by
  looking at a run from before your push. Never assume.
- **Listed in `ci.ignoredChecks`** — ignore it. Putting a check in that list is
  a claim you verified it always fails everywhere, not a way to mute a real one.

## 6. After every push, fix what the push made untrue

A PR description that argued for a decision the next commit reversed now states
the opposite of the code — worse than no description, because reviewers trust
it. Re-read it after every push, and check everything else that *describes* the
change rather than being it:

- The PR title.
- **Changesets** — they ship verbatim in release notes, so a stale one outlives
  the PR.
- **Image tags and digests** quoted in a deploy PR, once you rebuild.
- **Review-thread replies** claiming something is done.

Rewrite the stale section rather than appending a correction underneath; nobody
should have to reconcile two accounts. Then resolve the review threads the
commit actually addressed — a fixed objection left open reads as outstanding,
and a PR that appears to have eight live objections when it has one does not get
reviewed.

## 7. Stop at the open PR

**Never merge.** Finishing at an open PR is the normal, correct ending.

Never merge an automated "Version Packages" release PR — that cuts a real
release and is never this skill's call.
