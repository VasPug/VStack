# VStack

Claude Code skills I actually use.

## Skills

| Skill | What it does |
|---|---|
| [`plan-week`](skills/plan-week/SKILL.md) | Reads a week of your commits, PRs, team chat and last week's goals; interrogates you about what the week is for; **tries to kill your plan before you commit to it**; then writes a short list of outcomes, each with a date and someone else who checks it. |

### Why `plan-week` has a falsification phase

Most planning tools help you write down what you already intended. The expensive
failure isn't a badly worded goal — it's a well-formed, dated, verifiable goal to
build something with no subject.

On its first real run it produced a clean plan to evaluate routing quality in a
workflow engine. One grep showed the routing mechanism had zero callers: it
existed, was unit-tested, and no production workflow used it. The eval would have
scored a constant. That's Phase 3 — name the cheapest check that would prove the
goal pointless, and run it before anyone commits a week.

## Install

```bash
git clone https://github.com/VasPug/VStack.git
ln -s "$PWD/VStack/skills/plan-week" ~/.claude/skills/plan-week
```

Then `/plan-week`, or just say "plan my week".
