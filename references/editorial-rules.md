# Editorial rules (the QA checklist every session must pass)

These 20 rules were derived by hardening real workbook sessions (a Python sprint,
Sessions 1-10) through editor passes with the learner. They are not style
preferences: each one fixes a specific way a session quietly betrays a learner's
trust (an unfair grade, a hint that leaks the answer, a glyph that teaches the
wrong character to type). Rules 1-14 came from Session 1, rule 15 from Session 2,
rule 16 from Session 3, rules 17-18 from Session 4, rule 19 from Session 6 (then
run back across Sessions 1-5), rule 20 from a cross-session polish pass over all
ten sessions. Later always-true refinements (the drill-answer-variety rule below,
plus the level-0 environment-onboarding guidance in `SKILL.md` and the Reveal-gating
clause added to rule 20) came from building a second sprint in another subject
(SQL, Sessions 1-2).

**A session is NOT "done" until it passes every rule below.** Run this checklist
against each session artifact before you publish it and before you tell the user
it's ready. When a rule can't be satisfied, say so explicitly rather than shipping
past it.

---

## Content / teaching

**1. Never drop a term without unpacking it.** Define jargon (e.g. "escape
sequence", "argument", "immutable") the first time it appears, in prose, before or
as you use it.

**2. Every code example gets a comment saying what it DOES**, not just what it
outputs. (For non-code subjects: annotate every worked example with the *why*, not
just the result.)

**3. Anything interactive gets a "what it looks like when it runs" transcript.**
Any example with input/prompts (or any performed step) shows a terminal transcript
or equivalent that distinguishes the prompt from the typed/produced value, so the
learner knows what they'll actually see.

**4. Reference solutions use ONLY patterns already taught in that session.**
Introduce a shortcut (e.g. a nested one-liner) as an aside, never as the answer. If
the answer needs a technique, teach the technique first.

**5. When a new way replaced an old way, teach the contrast.** Show the clunky old
way, then why the new way wins (e.g. `+` concatenation vs f-strings; a loop+append
vs a comprehension). Don't present the new way in a vacuum.

**6. Match a learner's confusion to the fix.** If the material caused the
confusion, the material has a gap: patch the teaching, don't just correct the
learner.

## Exercise fairness / feedback

**7. An exercise must not silently grade on a concept it never taught, and its
feedback must address the LIKELY ACTUAL mistake**, not only the intended lesson.
(The "print separates with a space, not a comma" case: the feedback has to catch
what the learner really typed.)

**8. A placeholder or hint must NEVER contain the answer.** (The `e.g. 20 15` bug,
where the example input *was* the expected output.) The recurring offender is an
inline code *comment* in a faded or Parsons snippet that names the very token being
blanked: describe what the line DOES instead (`# put the text in`, not
`# the write method`; `# add each page onto the list`, not `# use extend`). Seen
repeatedly (Sessions 2, 4, 5, 8), so check every faded/Parsons comment against its
own blank before shipping.

**9. Never fix the correct multiple-choice answer in a constant slot.** Shuffle
options at render time and reshuffle on reset. (The "all correct answers were
option A" bug.)

**10. When a learner gets something wrong, link them back to the EXACT source
concept** via a deep link, not "go review Part 1."

## Interaction / architecture

**11. Do not present two things as distinct when the interaction is identical.**
Same mechanic means same wording and same behavior (a "Trace" card whose mechanic
is identical to the predict drills gets merged into them).

**12. Reversible interactions return an element to its ORIGINAL state/position,**
not a degraded one. A Parsons line removed from the solution snaps back to its
original scrambled slot in the bank, not appended to the end.

**13. Offer granular per-part resets in addition to a whole-session reset.** Build
each exercise as a re-callable function and have the global reset compose the
per-part ones. "Reset" must clear ALL interactive state, not a subset.

**14. In a sandboxed artifact, in-page navigation must use JS
`element.scrollIntoView()` with `preventDefault()`, never raw `#hash` anchors** —
hash anchors reload the iframe and wipe in-memory progress.

## Rendering

**15. Disable programming ligatures on all code/mono elements.** Set
`font-variant-ligatures: none; font-feature-settings: "liga" 0, "calt" 0;` on
`code, pre, textarea, input` (and anything using the mono stack). Cascadia Code
(a common Windows default mono) otherwise renders `!=` as `≠`, `>=`/`<=` as
`≥`/`≤`, `->` as `→`, `=>` as `⇒`, even inside `<pre>`. In a workbook that teaches
the exact characters to type, a ligated glyph is actively wrong (a learner would
try to type `≠`). The source stays ASCII and the font does the transforming, so a
grep for the glyphs finds nothing: you must set the CSS, not search-and-replace.

## Teaching altitude

**16. Introduce and demonstrate every new operation in prose as it appears; a code
comment is reinforcement, never the first place a concept is taught.** Passing the
letter of rules 1-2 (a comment explains it) isn't enough: if a method or idiom
first surfaces buried in a code block, a task prompt, or a drill, it reads as
bolted-on and turns a learner off. Also watch for **asymmetry**: if one member of a
pair gets a worked example (e.g. `break`), its sibling (`continue`) must too, not
just a one-line definition or a first appearance inside a drill.

## Feedback altitude

**17. In feedback and explanation text, unpack any symbol or idiom carried in from
an earlier session the first time it reappears.** The "why" behind a drill or quiz
answer is read at the moment the learner got it wrong, so it can't assume an earlier
session is still fresh. If the mechanics of an answer hinge on a cross-session symbol
(e.g. `**` = "to the power of", a slice `[a:b]`, `.get()`), spell out what it means
(cite the session if you can) instead of making the learner infer it. This is rule 1
applied to feedback, and it complements rule 7. Draw the line at genuinely
non-obvious tokens: universally-known basics (`+ - * /`) and the very concept being
tested do not need re-explaining, or the feedback turns to clutter.

## Correctness / QA gate

**18. Before a session ships, run every executable artifact and diff its actual
output against every claimed output; a session does not pass with a single mismatch
or error.** The workbook can't run code in-browser, so every claimed output
(predict-drill answers, `-> value` comments, fill-in solutions, reference solutions,
the milestone) is UNVERIFIED until you execute it offline in the subject's real
runtime (for code, real Python; for other subjects, check each claimed answer
against ground truth). Extract each snippet, run it, compare. This is the only
reliable guard against surprises a non-expert cannot eyeball, e.g. float precision
(`100 * 1.1` prints `110.00000000000001`, not `110.0`, fix by formatting), reference
solutions that error, and simply-wrong answers. Running is authoritative on OUTPUT
but blind to TEACHING QUALITY, so pair it with an INDEPENDENT, ADVERSARIAL reviewer
pass, ideally a separate agent prompted to FIND problems (assume bugs until proven
otherwise), that runs this check itself and re-reads against rules 1-17. An
independent agent re-checking beats the author confirming their own work.
**Diff output EXACTLY: never normalize trailing newlines or whitespace** (no
`rstrip`/`.strip()` on both sides before comparing) or the runner silently masks
real, learner-visible bugs. Canonical case (Session 5): printing a whole file with
`print(open(path).read())` leaves a TRAILING BLANK LINE whenever the file ends in a
newline (most editors add one), so a transcript claiming a clean N-line output is
wrong; a harness that rstripped both sides passed it on the first audit and only a
strict re-run caught it. Fold the same fact into the teaching: `.read()` returns
every character including the final newline, a concrete reason to prefer iterating
lines plus a per-line strip.

**19. Before a session ships, trace every graded item back to where its concept is
taught: no item may grade the learner on something the session (or an earlier one)
never taught.** This is the concrete pass that operationalizes rules 7 and 16, and
running is blind to it (a snippet whose output is correct can still test an untaught
idea). For EACH graded item (every predict drill, Parsons, fill-in blank, write-it,
flashcard, and quiz question) list the concepts, operations, and syntax the learner
must already know to answer it, then confirm each was introduced BEFORE that item, in
this session's Part 1 prose or an explained concept example, or in an earlier session.
Three things do NOT count as "taught": an explanation that appears only in the item's
own answer / "why" / feedback text (the learner reads it after committing); a concept
whose first appearance is inside a reference-solution comment (rule 16); and a concept
whose only prior mention is LATER in the same session than the item. Two traps to hunt
for specifically: the **built-in-name trap** (an item that grades on a type, function,
or error name like `KeyError`, `type()`, `FileNotFoundError` that is used but never
named in prose), and **cross-session drift** (clearing an item because you assume an
earlier session taught something, verify it in that session rather than guess). The
reliable mechanism is the same independent adversarial reviewer from rule 18, given a
baseline of what earlier sessions taught and told to FLAG rather than clear anything it
cannot find taught before the item. Found on Session 6 (a predict drill graded on
`KeyError` and `type(e).__name__`, taught nowhere) and confirmed by running the trace
back across Sessions 1-5 (Session 1 had a flashcard for `type()` taught nowhere;
Session 4 exercised `return` one concept before it was taught).

## Answer gating

**20. A grading control must not fire on an empty answer: disable every "Check"
button until its answer area actually has content.** A Check pressed on a blank
either grades the empty answer as "wrong" (which discourages the learner over a
mistake they never made) or appears to do nothing (which reads as broken). Gating
makes the control honest: it acts only when there is an answer to judge. Apply it
per drill type: a predict drill's Check stays disabled until its input is
non-empty; a faded / fill-in Check stays disabled until EVERY blank is filled; a
Parsons "Check order" stays disabled until at least one line has been moved into
the solution column. For Parsons, use a separate `checked` flag so that placing and
removing lines stays fully interactive while the button alone is what the gate
controls (do not freeze the lines to freeze the button). The same gate extends to
Part 4's **Reveal reference solution** button: keep it disabled until the write-it
textarea has content, so the learner attempts before peeking (set
`btn.disabled = !ta.value.trim()` on the textarea's `input` while the solution is
still hidden). Click-to-answer controls
that have no separate Check step (the Part 6 MCQ options) are already gated by
construction and need nothing. Also wire the same non-empty guard on any Enter-key
path that submits a drill. Found applying this across all ten Python-sprint
sessions.

## Always-true self-tracking rule

Any self-assessment control (e.g. "Mine worked" / "Needs another pass") must state
what it does and admit the app can't verify it — the workbook can't run the real
environment, so the learner self-checks. Be honest about the boundary rather than
implying the artifact graded the real work.

Three concrete requirements a self-mark must meet (a control that fails these
betrays trust just as an unfair grade does):
- **Persist and surface, or don't claim it.** If the mark says it recorded something
  (a label like "logged", a checkmark), it must save to `localStorage`, survive a
  refresh, and feed a visible counter. A "✓ logged" that stores nothing and vanishes
  on reload is the specific failure to avoid.
- **Be reversible.** A self-mark is a toggle the learner can undo, never a one-click
  irreversible commit that hard-disables itself.
- **Don't half-commit.** Disabling the button while leaving the answer editable (or
  the reverse) is incoherent; pick one coherent state. The honest default is to
  treat a write-it mark exactly like the Part 1 "I did this" checkboxes: persisted,
  counted, and toggleable. (Found in the Python sprint's Part 4 write-it buttons,
  which showed "✓ logged" but recorded nothing, could not be undone, and left the
  editor open, all fixed by making the mark a persisted, counted, reversible toggle.)

## Always-true progress-panel parity and persistence rule

The progress strip must carry a counter for EVERY graded practice, not a subset,
and every graded practice must persist and restore on reload. Two failures this
guards, both found late in the Python sprint:

- **Parity.** The workbook has five graded practices: Part 1 hands-on tasks, Part 2
  predict drills, Part 5 flashcards, Part 4 write-its, and the Part 6 quiz. The
  engine once tracked only four and silently omitted the quiz, so the one pass/fail
  gate had no panel presence. Give the strip a counter for all five (widen the grid
  accordingly), count CORRECT answers for the auto-graded ones (drills, quiz), and
  set each total dynamically from its item-array length so one layout serves any
  question count (a 6-question session and a 10-question final both work). When you
  add ANY new graded practice to the template, add its counter in the same pass.
- **Persistence.** ALL graded practices persist to `localStorage` and restore on
  reload, not just the checkbox-style tasks and write-its. Drills persist their
  correctness and restore through a shared `markDone(ok)` helper (compute correct,
  then persist, then mark, so live-check and restore share one path). Cards persist
  a mastered-index set and are filtered out of the pile on reload. The quiz persists
  per-question correctness and restores by replaying a synthetic click through the
  page's OWN answer handler (which sidesteps the fact that the backlink and
  final-message code diverges between sessions); note the reshuffle nuance: only
  "answered" plus the score restore, not the exact wrong option the learner picked,
  since options reshuffle each load. A counter that lives only in memory and resets
  on refresh is the failure to avoid, it silently under-reports the learner's real
  progress.

## Always-true drill-answer-variety rule

Part 2 predict drills must vary their ANSWER TYPE, not default to a count. A count
("how many rows / items?") is the easiest answer to grade, so drills drift toward it,
but a count tests a proxy (how many pass) instead of the real skill (which results
come back) and never touches what the learner actually produces. Give each drill an
explicit `type` and grade through one shared `gradeDrill(d, val)`: `count` and
`value` (a single result, or "which one is first") match exactly via the normalizer;
`set` (all results, ANY order) and `seq` (results in exact order) tokenize both sides
with `toks(s) = s.toLowerCase().trim().split(/[\s,.;]+/).filter(Boolean)` then compare
sorted (set) or in order (seq), so separators, case, and trailing punctuation are
forgiven while a missing, extra, or mis-ordered answer fails. That shared grader is
what makes "which results?" a fair, deterministic drill: without it the grader can
match only one token, which is the very reason drills collapse to counts. GUIDANCE:
ask `value` when one result comes back, `set` when several do, reserve `count` for
when the count itself is the lesson, and use `seq` only when the result has a defined
order (e.g. a sort). Aim for a spread, not eight-of-eleven counts. Determinism still
governs (rule 18): a `seq` or "which is first" answer needs a genuine ordering, while
a `set` is order-independent so it works even without one. Found when a SQL sprint's
Session 1-2 drills were mostly row-counts; also normalize a trailing `[.;]+` in the
count/value path so those drills forgive the same stray punctuation the token path
already does.

## Always-true artifact-integrity rule

The artifact's own HTML/CSS/JS must actually work in the browser, and neither QA
gate above checks that: rule 18 runs the learning snippets in a SEPARATE runtime and
rule 19 traces teaching, so both are blind to a broken engine. Two failure modes to
guard, both of which bite AFTER the content audit has already passed:

- **A JS syntax error silently kills the whole `<script>`.** Every dynamic part
  (drills, Parsons, faded, write-its, quiz) renders empty and every control is dead,
  while the page still "looks loaded." The classic cause is an UNESCAPED APOSTROPHE
  inside a single-quoted JS string in a data array (a comment like
  `# add each page's items` ends the string early at `page'`). It slips in most often
  when you tweak a string AFTER the audit and republish without re-checking. So: after
  ANY edit that touches a JS string literal (a drill / faded / write / card / quiz
  entry, even a comment inside one), re-validate the script before publishing. No JS
  engine is required, a tiny script that (a) tokenizes the `<script>` and flags any
  single or double quoted literal left unterminated at a line break and (b) checks
  bracket balance with strings/comments stripped catches this class definitively.
  PREFER REWORDING to avoid an apostrophe over escaping it (`\'` is fragile to future
  edits).
- **A single long line can blow out a fixed layout.** See the Parsons-layout note in
  `workbook-template.md` (`minmax(0, 1fr)` tracks, not `1fr`).

Belt-and-suspenders for both: actually open or refresh the published artifact and
confirm the interactive parts render before calling the session done.

## House style

No em dashes in any learner-facing copy. Use commas, colons, or parentheses
instead. (This applies to all content this skill produces, per the author's
writing-style preference.)
