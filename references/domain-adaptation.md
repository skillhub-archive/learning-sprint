# Adapting the scaffold to any subject

The 6-part workbook is subject-agnostic. Only two things change per subject: the
**real environment** the learner practices in, and the **drill types** that
instantiate each part. The learning mechanisms (produce, fade the scaffold, space
and interleave) never change.

## 1. Pick the real environment

Ask in intake: "Where is this skill actually done?" Send every "Your turn" and
"Write it yourself" task there. Examples:

| Subject | Real environment | Feedback loop |
|---|---|---|
| Python / any code | VS Code + terminal | run it, read the output/traceback |
| Excel / Sheets | a real workbook | see the cell result / error |
| SQL | DB Browser for SQLite (or the sqlite3 shell) | run the query, read the result grid |
| Spanish (spoken) | speak aloud / record on phone | compare to a model clip |
| Public speaking | phone camera | watch the playback |
| Chess | a board + a free engine (Lichess) | engine eval / analysis |
| Guitar | the instrument + a tuner/metronome | does it sound right / in time |
| Financial modeling | a real spreadsheet model | do the numbers reconcile |

If a subject has no digital "run it," the real environment is still physical
practice + a way to check (a rubric, a recording, a reference performance).

**Onboard that environment from zero in Session 1.** Do not assume the learner
already has it: Session 1's first task walks them through getting it (download /
install / sign in), opening it, and where to do the thing, recommending one
friendliest path and deprioritizing alternatives. See Step 4 in `SKILL.md`. If a
later session changes the shared practice state (adds data, changes a file), offer a
from-zero rebuild right under the change script, above the "confirm it worked" step,
so a learner who lost the environment can catch up first (see Part 1 in
`workbook-template.md`).

## 2. Translate each drill type

The general form of every part, and how it maps across domains:

- **Part 2 "Predict/trace"** = *predict the result, then check.*
  - Code: predict program output; trace variable values.
  - Excel: predict what a formula returns; trace a cell's dependency chain.
  - Language: read a sentence, predict its meaning / the correct conjugation.
  - Music theory: name the chord/interval before revealing.
  - Chess: predict the best move / the consequence of a move.
  - Vary the answer type across drills (a single value, a count, the full set of
    results in any order, or results in a specific order), AND vary the cognitive
    task (read a whole result, operate on a subset, read one group, combine steps),
    not one shape repeated; see rule 23 in `editorial-rules.md` for the why and
    `workbook-template.md` Part 2 for the canonical grader contract (including the
    per-subject `caseFold` decision).

- **Part 3 "Arrange & fill"** = *order the steps / fill the gap* (the reading→doing
  bridge).
  - Code: Parsons (reorder scrambled lines); faded (fill blanks in a snippet).
  - Excel: order the steps of a pivot/lookup; fill the missing function name/arg.
  - Language: reorder scrambled words into a correct sentence; fill the missing
    particle/ending (this is literally how language apps scaffold).
  - Cooking: put recipe steps in order; fill the missing quantity/temperature.
  - Chess: order the moves of a known opening; fill the missing move in a mate
    pattern.

- **Part 4 "Write it yourself"** = *produce the whole thing from a spec*, then check
  against a reference and self-grade after doing it for real.

- **Part 5 "Flashcards"** = the atoms that must be automatic: syntax, vocab, formula
  signatures, chord shapes, opening names, keyboard shortcuts.

- **Part 6 "Quick check"** = short graded retrieval + gotchas + cumulative review.

## 3. Curate resources per domain

Still "top 5, mostly free, why + when." The canonical sources differ (docs +
practice platform for code; a grammar reference + a speaking app + native audio for
language; annotated master games + a tactics trainer for chess). Verify resources
exist and say what you claim, don't invent them.

## 4. The "Where to next" closer is per-subject (and must be web-verified)

A finished Hub gets a "Where to next" send-off section (structure in
`workbook-template.md`). Both of its blocks are subject-specific and MUST be checked
against the live web at build time, because they go stale:

- **The "try this next" topic** is whatever this subject's natural next climb is (SQL
  -> window functions; beginner Python -> a web framework or deeper data work;
  Spanish -> the subjunctive; chess -> endgame theory). Point at genuine current
  resources for it, never at "ask me to build it".
- **The certifications** differ per subject and CHURN: exam codes get renamed,
  programs get retired, prices change. Web-verify each credential exists, is current,
  and says what you claim BEFORE writing it; never list one from memory. List only
  genuine proctored exams, not completion badges, and add an honest caveat where the
  subject has no strong or vendor-neutral credential (many do not). Some subjects have
  no real exam at all: say so plainly rather than inventing one.

Add a **bonus session** (a real workbook, but OUTSIDE the graded syllabus: no level
number, not counted in Hub progress, titled about the topic rather than as a rung).
This is **standard on every track**, as the track's advanced tier. Pick the natural
next topic that is self-containable as one more hands-on session (SQL window functions
qualified: they build straight on the joins and aggregates already taught). The
per-subject judgment is only about WHICH topic, not whether to have one.

The one escape hatch: when the next topic is genuinely a whole new subject rather than
a self-containable extension, say so explicitly and leave the closer as a
signpost-only send-off, pointing outward. Do not pad a weak bonus onto a track.
When a bonus session does exist, surface it only through the closer's "try this next"
signpost (an "Open workbook" link), never as an extra graded card.

## 5. Worked mini-example: "Excel to intermediate, for reporting"

- **20% that pays** (goal = reporting): references/formulas, lookups
  (XLOOKUP/INDEX-MATCH), pivot tables, basic cleaning, charts. Skip VBA/macros for
  v1.
- **Real environment:** a practice workbook you keep open.
- **Part 2:** "predict what `=XLOOKUP(...)` returns on this table."
- **Part 3:** reorder the steps to build a pivot; fill the blank in an
  `=INDEX(...,MATCH(...))`.
- **Part 4:** "build a pivot that shows revenue by month from this raw sheet," then
  reveal reference and self-grade in your own workbook.
- **Milestone (Level 1):** "You can write a lookup that pulls a value across two
  sheets without help."

Same engine, different surface. That's the whole point.
