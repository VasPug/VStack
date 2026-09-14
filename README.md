# VStack

A working stack of Claude Code skills: plan the week, run a task end to end,
ship it, deploy it.

**Nothing company-specific lives in a skill file.** Repo names, hostnames, page
ids, people, deploy paths — all of it goes in `~/.claude/vstack.config.json`,
which is yours and stays on your machine. Every key is optional; a skill that
finds no config uses a documented default and says so. That is what makes these
installable by someone who is not you.

## Skills

| Skill | What it does |
|---|---|
| [`plan-week`](skills/plan-week/SKILL.md) | Reads a week of your commits, PRs, team chat and last week's goals; interrogates you about what the week is for; **tries to kill your plan before you commit to it**; writes outcomes with dates and verifiers. |
| [`do-task`](skills/do-task/SKILL.md) | A goal in, a reviewable PR out. Spec → plan → build → prove → report, with exactly one human gate. |
| [`ship-it`](skills/ship-it/SKILL.md) | Branch, worktree, review battery, PR, CI. Called by `do-task`, useful alone. |
| [`deploy-rc`](skills/deploy-rc/SKILL.md) | Release-candidate tags, digests, promotion, inventory bump. Refuses to run unless you explicitly enable and ask. |
| [`weekly-goals`](skills/weekly-goals/SKILL.md) | The shape of a goal someone else can check. Hands off to your own skill for *where* it gets written. |
| [`explain-system`](skills/explain-system/SKILL.md) | Answers "how does this work" in one sentence first, in plain English, at the altitude asked. Built from 81 real requests to say it shorter or simpler. |
| [`prove-it`](skills/prove-it/SKILL.md) | Turns "it works" into an artifact someone else can open — screenshots, a passing test, real command output — and always says whether the run was live or mocked. |
| [`write-to-teammates`](skills/write-to-teammates/SKILL.md) | Drafts a message that gets a real reply: first person, no code identifiers, one concrete detail per topic, one person tagged, and an ask at the end. |
| [`extract-decisions`](skills/extract-decisions/SKILL.md) | Turns a meeting or a long thread into what was actually settled, who owns it, and what each decision rules out. |
| [`dont-reinvent-the-wheel`](skills/dont-reinvent-the-wheel/SKILL.md) | Before you design it, finds how three existing platforms already solved it, names the shape they converged on, and says what to take. |

## Two ideas worth stealing even if you don't install this

**`plan-week` has a falsification phase.** Most planning tools help you write
down what you already intended. The expensive failure is not a badly worded goal
— it is a well-formed, dated, verifiable goal to build something with no
subject. On its first real run it produced a clean plan to evaluate routing
quality in a workflow engine; one grep showed the routing mechanism had zero
callers. It existed, was unit-tested, and nothing used it. The eval would have
scored a constant. So: name the cheapest check that would prove the goal
pointless, and run it before anyone commits a week.

**`do-task` has one human gate, on purpose.** The spec. After that it runs to
completion and reports, rather than asking for approval six more times. The cost
is that decisions get made without you, so the report carries a `Rulings` line —
every call made on your behalf and what it costs if wrong. A ruling that dies in
a worktree was a decision made in secret.

## Install

```bash
git clone https://github.com/VasPug/VStack.git
cp VStack/vstack.config.example.json ~/.claude/vstack.config.json   # then edit
for s in VStack/skills/*/; do ln -sfn "$PWD/$s" ~/.claude/skills/$(basename $s); done
```

Or as a plugin, from the marketplace manifest in `.claude-plugin/`.

## Adding your own

One directory under `skills/`, one `SKILL.md`, frontmatter with `name` and a
`description` that says *when* to use it rather than what it is — the
description is the only thing read when deciding whether to load it.

If your skill needs to know something about your company, add a key to the
config and document it in `vstack.config.example.json`. Do not hardcode it.
