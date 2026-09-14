# VStack

An engineering stack. Generalized skills for the thinking and the machinery
around building software — brainstorming a problem, planning a week, taking a
goal to a reviewable PR, shipping it, deploying it.

It is meant to accelerate real work, whether that is your job at a company or
your own projects. It is also meant to be **extended**: take these, keep what
fits, and add your own on top. A stack you never modified is one you are not
really using.

## What belongs here

Skills about **how engineering work gets done** — process, discipline, the
sequence of steps, the check that stops a mistake. Things that stay true whether
you work on a payments backend or a side project.

## What does not

Anything that is true only at your company: repo names, hostnames, page ids,
people, deploy paths, credentials. That is what `~/.claude/vstack.config.json`
is for — the config is yours and stays on your machine, and the skill reads it.

**If a skill cannot run for someone who is not you, it is not finished.** Every
config key is optional; a skill that finds nothing uses a documented default and
says which one it used.

## Adding a skill

One directory under `skills/`, one `SKILL.md`. Frontmatter needs `name` and a
`description` that says **when to use it**, not what it is — the description is
the only thing read when deciding whether to load the skill, so a description
that describes rather than triggers means the skill never fires.

Then:

- **Keep it short enough to be read in full.** A 500-line skill is several
  skills that have not been separated yet. If two halves fire at different
  times, they are two skills.
- **Write rules that cost something.** "Be careful" is not a rule. "Never branch
  from whatever the checkout is sitting on" is, because it forbids the thing
  you would otherwise do.
- **Say why, once, where the rule is surprising.** A rule whose reason is
  obvious needs none; a rule that looks wrong until you know the failure it
  prevents needs one line.
- **Prefer deleting to adding.** If an existing skill nearly covers it, extend
  that one.
- **New company-specific knowledge means a new config key**, documented in
  `vstack.config.example.json`. Never a hardcoded value.

## Working on this repo

Skills are prose, and prose drifts. When you change how one skill behaves, check
whether another states the same rule differently — two skills disagreeing is
worse than either being wrong, because whichever loads first wins silently.
State a rule once, in the skill that performs it, and let the others delegate.
