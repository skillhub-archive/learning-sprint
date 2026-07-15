# learning-sprint

A Claude Code skill that builds a focused, self-paced **"level-up sprint"** to take
someone from beginner toward expert in almost any subject, code or not. It delivers
a roadmap Hub plus hands-on workbook sessions as self-contained interactive HTML
artifacts: self-grading quizzes, milestones, faded-code and "produce then check"
drills, flashcards, and a curated top-5 resources block.

The scaffold is subject-agnostic. Two things swap per subject: the real practice
environment (VS Code, a spreadsheet, a chessboard, speaking aloud) and the drill
types. So it works for a programming language just as well as Excel, a spoken
language, public speaking, guitar, or financial modeling.

## Install (Claude Code CLI)

Skills live in `~/.claude/skills/<name>/`, so installing is just cloning this repo
into that folder:

```bash
git clone https://github.com/skillhub-archive/learning-sprint \
  ~/.claude/skills/learning-sprint
```

Restart Claude Code (or start a new session), then kick it off with:

```
/learning-sprint
```

or just tell Claude something like "help me get good at X, build me a study plan."
The skill triggers on that kind of request on its own.

To update later: `cd ~/.claude/skills/learning-sprint && git pull`.

> Not a CLI user? The same skill works in the Claude desktop/web app; the files in
> this repo are the source either way.

## What you get out of it

- **One roadmap Hub**: the whole plan across 5 levels (beginner to advanced), every
  session outlined with a clear milestone, a top-5 resources block, and a progress
  tracker that persists in your browser.
- **Workbook sessions, built on demand**: each a six-part interactive artifact that
  makes you *produce*, not just read, then grades itself.
- Everything is self-contained and cross-linked. No accounts, no server, no live
  grading needed.

## A real example: a 20-hour Python sprint

I built this skill after using an earlier version on myself, to go from "can read
code" toward actually writing it. The result was a 20-hour "learn Python fast"
course: 10 interactive workbook sessions (setup and basics through APIs and pandas),
each with predict-the-output drills, Parsons problems, faded code, flashcards, and a
cumulative quiz.

It is live here: **https://skillhub-archive.github.io/**

The lessons from building and hardening that sprint (a session template, the learning
rationale, and ~20 editorial and QA rules that each close a specific way a lesson can
quietly mislead a learner) are baked into this skill, so it applies the same standard
to whatever subject you point it at.

## What's inside

| File | What it is |
|---|---|
| `SKILL.md` | The skill itself: intake, curriculum design, and the build steps. |
| `references/workbook-template.md` | The exact six-part session structure and Hub layout. The authoritative build spec. |
| `references/editorial-rules.md` | The QA checklist every session must pass before it's "done." Each rule fixes a real learner-trust bug. |
| `references/why-it-works.md` | The reasoning behind every part, so the pieces that do the quiet work don't get stripped. |
| `references/domain-adaptation.md` | How to adapt the practice environment and drills to non-code subjects, with worked examples. |
| `CHANGELOG.md` | How the skill has evolved. |

## License

MIT. See [LICENSE](LICENSE). Use it, fork it, adapt it.
