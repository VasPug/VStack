---
name: deploy-rc
description: Use ONLY when the human explicitly asks to deploy a branch to a shared environment — release-candidate tags, image digests, promotion, and the inventory bump. Never part of a normal task pipeline. Triggers: "deploy this to devnet", "cut an rc", "promote that image".
---

# Deploy a release candidate

Reads `deploy` from `~/.claude/vstack.config.json`. **If `deploy.enabled` is not
true, refuse and say the config does not permit it.** There is no sensible
default for pushing artifacts at a shared environment.

## Never run this on your own initiative

Only when the human says so, in this session, in as many words. A shared
environment has other people releasing into it: an rc tag, a version bump or an
inventory PR at the wrong moment collides with someone else's release.

Do not offer it as a question answerable with "sure". State that the PR is ready
to deploy, and stop.

## The default is merge first

Merge, then deploy from the base branch. The environment then equals a commit
anyone can check out: reproducible, traceable, no staleness treadmill.

Deploying an un-merged branch costs real things — the environment stops
representing any branch anyone can check out, a bug report against it cannot be
traced to a commit, and every merge to base makes the deployed branch staler.

**It is justified when the risk is integration, not logic** — a wire contract, a
cross-repo change with a merge-order constraint, anything a mocked test suite
structurally cannot prove. Most suites stub the network, so a green run says
nothing about the real backend; that gap is the whole case for a live rehearsal.
Absent that, merge first.

If pre-merge live testing keeps being asked for, say so plainly: the durable fix
is per-PR preview environments — ephemeral namespace and ingress, torn down on
merge — which give live testing without a shared mutable environment. Raise it
as its own infrastructure work rather than absorbing the cost every time.

## Versioning

**The changeset declares the version.** In a Changesets repo, the pending
changesets determine the next `X.Y.Z`, and that is the `X.Y.Z` in your tag. No
changeset means no agreed version to build against — add one first.

Pending changesets accumulate: **the image carries every unreleased change in
the repo**, not only this task's. Say so when handing it over.

**Tag `vX.Y.Z-rcN`,** `N` incrementing per rebuild. Never reuse a tag — the
environment silently changes under whoever is testing.

Avoid entirely: `latest`, `dev`, `manual`, and feature-named tags like
`v0.0.9-banner-fix`. Mutable, unpromotable, and meaningless a month later.

**Promote by retagging the same digest, never by rebuilding:**

```bash
docker buildx imagetools create -t <repo>:vX.Y.Z <repo>:vX.Y.Z-rcN
```

A rebuild from the merge commit is a different artifact — different base layers,
different dependency resolution — so the sign-off does not transfer to it.

**Promote the manifest-list digest, not the platform digest.** A registry tag
resolves to an index listing one entry per platform, plus a provenance
attestation which is not a platform. `imagetools create` must retag the *index*
digest. Quoting the `linux/amd64` digest looks correct and promotes the wrong
object. Record both, labelled:

```
index        sha256:...
linux/amd64  sha256:...
```

**Build the image last** — after CI is green and no further commits are planned.
Any commit landing after a build invalidates that rc: the tag now points at a
commit that is no longer the branch head. Bump to the next `rcN`, repoint the
deploy PR, and state why the previous one must not be deployed. Leave the
superseded tag in the registry; it is a real build of a real commit, just not
the one to ship. Expect this at least once per task.

**Chart and playbook collections get rc tags too.** A new chart surface ships as
`X.Y.Z-rcN` with a matching CHANGELOG heading, in the PR that makes the change —
not a follow-up. Tag on the merge commit. Promote to `X.Y.Z` only after the
environment confirms it.

**An inventory var is inert unless the chart renders it.** Before adding a new
env key, read the chart's ConfigMap template. Some render a generic `range` over
a map and take anything; others have a hardcoded key list and silently drop what
is not in it. If the key is not plumbed, the collection change comes first and
the inventory PR depends on it — check at the *pinned* collection version, not
at the default branch.

## Steps

0. **Re-check freshness right before building.** More time has passed since the
   PR opened.

   ```bash
   git fetch -q origin && git rev-list --count HEAD..origin/<base>
   ```

   Behind? Rebase, let CI go green, then build. The image is a snapshot of the
   branch, so building from a stale one deploys an environment missing whatever
   other people merged. **Never build an image from a branch you have not just
   checked.**

1. Run the repo's build workflow (`deploy.buildWorkflow`) against the branch at
   `vX.Y.Z-rcN`. Wait for green, then verify the tag actually landed in the
   registry.

2. **Open one PR per repo against the release branch, not the default branch.**
   The default branch is typically stale and lags the environment by many rc's,
   so a PR based on it silently reverts other people's deploys.

   With `deploy.releaseBranch: "derive"`, read it from what recent deploy PRs
   actually merged into:

   ```bash
   gh pr list --repo <deploy.inventoryRepo> --state merged --limit 10 \
     --json number,title,baseRefName -q '.[]|"\(.baseRefName)  #\(.number) \(.title)"'
   ```

   A shallow clone fetches only the default branch, so the release branch will
   be absent locally — `git remote set-branches origin '*' && git fetch` first.
   Read the value you are bumping *off that branch*, not from a local checkout.

   One PR per repo, not one combined: a bad build in one repo must not block the
   others. Combine only when they genuinely must deploy together, and say why.

3. **The PR body states:** the image, **both digests labelled**, the commit it
   was built from, and the source PR. Then say plainly which case this is —
   built post-merge from the base branch so the environment equals a checkout-able
   commit, or built from an un-merged branch, naming the repos. An environment
   drifting with nobody knowing why is the failure mode; never leave it implicit.

   Also state anything the deploy changes beyond this task's scope. Pending
   changesets accumulate, and a newly-required field can make already-published
   data stop resolving until it is republished. Those land on a shared
   environment and read as regressions to whoever did not do the work.

4. **Stop.** The human reviews, merges and publishes. Never run the deploy
   playbook yourself.
