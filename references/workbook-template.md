# Workbook template (the build spec)

Every session is a self-contained artifact with SIX parts. The ordering is
deliberate: it walks the learner down a **fading-scaffold** ramp from recognizing
to producing (read → predict → arrange → fill → write → recall → check). Do not
reorder without reason; see `why-it-works.md`.

A session is ~1h45 of work + a ~15-min review. Keep each part's content calibrated
to the session's place in the 5-level arc.

## Header

- Eyebrow crumb: `Session NN // Level L · <Level name> · Workbook`
- A **back-to-Hub link** (the Hub's published URL, `target="_blank"`).
- H1 session title; one-line goal.
- A **milestone chip**: "You can now do X, unassisted."
- A short "how to use this" banner that sets the expectation: **open the real
  environment (VS Code / workbook / instrument / board) before starting. This is a
  workbook, not a reading. Producing it is what makes it stick.**
- A progress strip with the 5 headline "doing" metrics (Hands-on done / Drills
  correct / Cards mastered / Write-its done / Quiz correct), each persisted via
  `localStorage` and restored on reload, plus a reset. Every graded practice feeds
  one of these counters (count CORRECT for the auto-graded drills and quiz; set each
  total from its item-array length so one layout serves any question count), so
  nothing the learner does is invisible or lost on refresh. See the progress-panel
  parity and persistence rule and the self-tracking rule in `editorial-rules.md`.

## Part 1 — Learn & do (the 5-minute summary + micro-loops)

Satisfies requirement #2 (one-page summary). But it is NOT passive: each short
concept is immediately followed by a **"Your turn"** task the learner does in the
real environment, with:
- a collapsible **hint** and a collapsible **reference solution** (`<details>`),
- a **"I did this" checkbox** that persists.

Use bullets, small **diagrams** (CSS/HTML boxes, not just prose), and worked
examples with expected results. Cover the genuine high-leverage content, go deeper
than a syntax tour. The final "Your turn" of the session should BE the session
milestone task.

**Order every concept explain-then-demonstrate** (rule 16): if a worked example uses
notation new to this session (a new operator, a qualified name like `alias.column`, a
new clause), teach that notation in prose FIRST, then show the example. An example
the learner has to decode with unexplained syntax lands before its own explanation,
which reads as "you were expected to already know this." When in doubt, put the
teaching paragraph above the code block, not after it.

When a session mutates the shared practice data (adds a column or rows via a
paste-and-run script) or otherwise needs a from-zero rebuild, put that full-setup
fallback (a collapsible "lost your environment? start fresh" block) **directly under
the primary setup script and above the "now confirm the result" step**, not after
it. The fallback is an *alternative* to the primary script (it rebuilds everything
in one go), so a learner who lost their environment needs it before being told to
verify a result they cannot yet produce.

## Part 2 — Predict / trace (retrieval on the mental model)

Show an artifact of the skill (code snippet, a formula, a sentence to translate, a
board position) and have the learner **type what they predict** before checking.
Auto-check the typed answer with a **type-aware grader** (see the next bullet) and
always reveal *why*.
- ~10 items, progressively harder. The **Check button is disabled until the input
  has content** (rule 20; also guard the Enter-key path), and each drill's
  correctness **persists and restores on reload** through a shared `markDone(ok)`
  helper, feeding the Drills-correct counter.
- **Vary the answer TYPE; do not default every drill to a count.** Give each drill an
  explicit `type` and grade through one shared `gradeDrill(d, val)`: `count` and
  `value` (a single result, or "which is first") match exactly via the normalizer;
  `set` (all results, any order) and `seq` (results in order) tokenize both sides with
  `toks(s) = s.toLowerCase().trim().split(/[\s,.;]+/).filter(Boolean)` then compare
  sorted (set) or in order (seq). Ask `value` when one result comes back, `set` when
  several do, `count` only when the count is the lesson, `seq` only when the result
  has a defined order. Also vary the **cognitive task**, not just the answer type:
  ramp from reading one whole result, to operating on a filtered subset, to reading
  one group out of a grouped result, to combining several clauses, rather than
  restating the same move with a different function. See the drill-answer-variety
  rule in `editorial-rules.md`.
- Include at least one **"trace it"** item that forces step-by-step mental
  execution (e.g. variable values after each line).
- For code subjects, add a **"watch it run" link to Python Tutor**
  (`https://pythontutor.com/visualize.html#code=<encodeURIComponent(code)>&py=3&cumulative=false&curInstr=0&mode=display`),
  built in JS so encoding is correct. Skip for snippets needing `input()`. Not every
  code subject has a clean inline visualizer (SQL has none): when there is no faithful
  one, drop the link and rest Part 2 on verified output plus a nudge to run it in the
  real tool.

## Part 3 — Arrange & fill (the reading→writing bridge)

This is the highest-value scaffold for the learner's real gap (producing
unassisted). Two sub-types:
- **Parsons problems:** correct-ordered lines shown **shuffled**; the learner
  clicks lines from a "bank" into a "solution" area in order, then checks.
  Implement click-to-move (bank ↔ solution), not drag-and-drop (works on mobile,
  fewer bugs). If a shuffle equals the correct order, reshuffle/rotate. Give the two
  columns `grid-template-columns: minmax(0, 1fr) minmax(0, 1fr)` (NOT plain
  `1fr 1fr`) plus `.pcol { min-width: 0; }`, so a long non-wrapping code line can't
  force its track to content-width and push the whole card past the page edge (a
  `1fr` track's default minimum is its content). Also keep individual lines short:
  assign a long URL to a `url` variable rather than inlining it in the
  `requests.get(...)` line. Disable the **Check order** button until at least one
  line has been moved into the solution column, using a separate `checked` flag so
  placing and removing lines stays fully interactive while only the button is gated
  (rule 20).
- **Faded / fill-in-the-blank:** near-complete code (or formula/sentence) with a
  few blanks as inline inputs. Disable **Check** until every blank is filled
  (rule 20). Check each blank; reveal answers.

For non-code subjects, translate: arrange the steps of a process in order; fill the
missing word/measure/move. See `domain-adaptation.md`.

## Part 4 — Write it yourself (full generation)

A spec, a blank editor box (textarea with working Tab-inserts-spaces), a **Reveal
reference solution** button (disabled until the textarea has content, so the learner
attempts before peeking, per rule 20), then instruction to actually run/perform it in
the real environment and **self-grade** ("Mine worked" / "Needs another pass"). ~3–4
items.
No auto-run, the real environment is the feedback loop.

The self-grade is a **reversible, persisted** mark, not a one-shot cosmetic label.
"Mine worked" toggles a done-state that saves to `localStorage`, feeds the Part 4
counter in the progress strip, and can be tapped again to undo (label it so, e.g.
"✓ Marked done (tap to undo)"); a marked item shows a visible done state (a subtle
card treatment) and restores it on reload. The editor box **stays editable** after
marking (the learner owns their self-assessment). "Needs another pass" clears any
mark and refocuses the editor. Never make the button claim something it doesn't do
(a label like "✓ logged" that persists nothing is the exact failure the self-tracking
rule forbids), and never half-commit (disabling the button while leaving the answer
editable, or vice versa).

## Part 5 — Rapid recall (flashcards, the repetition engine)

A deck of prompt→answer cards for the atoms the learner should summon instantly
(syntax, terms, shortcuts). Tap to flip; **"Got it"** removes the card, **"Review
again"** sends it to the back of the pile so it loops until automatic. Track
mastered/pile counts, feeding the Cards-mastered counter. Mastered cards **persist
as an index set** and are filtered out of the pile on reload. Reset button.

## Part 6 — Quick check (the graded gate) + cumulative review

Satisfies requirement #3. A short MCQ (~5 Q) that reveals the correct answer, the
**gotcha** behind each tempting wrong choice, and **how to improve**.
- **Cumulative/interleaved review:** from Session 2 onward, mix in 2–3 questions
  from *earlier* sessions (spaced retrieval + interleaving). Say so in the intro.
  Session 1 is all Session 1.
- The **final session** hosts the **comprehensive quiz** covering everything (10
  questions), plus a small capstone that combines the whole arc.
- The quiz feeds a **Quiz-correct** counter (total set from the question count, so
  the same layout serves a 6-question session and the 10-question final) and
  **persists** per-question correctness, restoring on reload by replaying a
  synthetic click through the page's own answer handler. Only "answered" plus the
  score restore, not the exact wrong option picked, since options reshuffle each
  load (see the progress-panel parity and persistence rule).

## Footer

Session x of N; the milestone reminder ("hit it unassisted, then tick SNN on your
Hub"); a pointer to the next session ("ask me to build it").

## Build / QA notes

- Self-contained: inline CSS/JS, no CDNs. Theme-aware (light/dark tokens). Respect
  `prefers-reduced-motion`. Focus-visible states. `overflow-x:auto` on code blocks.
- Escape all user-facing dynamic strings before injecting as HTML.
- **Keep code-example lines short enough not to trigger a horizontal scrollbar**
  within the session's column width. A long statement plus a padded trailing
  `-> result` comment overflows easily; put a long result annotation on its own line
  (or split the statement across lines) rather than padding it out to the right. A
  quick check: flag any `<pre>` line over ~100 visible characters.
- **Title-case section and part headers** (e.g. "Learn & Do", "Predict the Output",
  "Arrange & Fill", "Write It Yourself", "Rapid Recall", "Quick Check"; on the Hub,
  "The Five Levels" / "Top 5 Resources"), so the Hub and sessions read consistently.
- **After editing any JS string literal, re-validate that the `<script>` still parses
  before republishing.** A stray unescaped apostrophe in a data-array string (even in
  a comment) silently kills the whole engine, and the content audit won't catch it.
  See the artifact-integrity rule in `editorial-rules.md`.
- Wrap `localStorage` in try/catch so it degrades gracefully.
- Keep the file path stable across redeploys so the artifact URL is stable (the Hub
  links to it).
- **The artifact is a fragment, not a full document.** The publish step wraps your
  file in the `<!doctype html>…<head>…</head><body>` skeleton, so do NOT write your
  own `<head>`, `<title>`, or `<link rel="icon">`. Set the **title** and the
  **favicon** (an emoji matched to the subject, e.g. 🐍 for Python) through the
  publish parameters, not in the file. The real Session 1 begins directly at
  `<style>` for this reason.
- **Palette tokens are role-named, not color-named**, so a new subject reskins by
  changing values, not selectors: `--accent` (the accent/brand hue), `--gold*` (the
  milestone/"level unlocked" highlight), `--ink*` (the neutral text ramp),
  `--surface*`/`--bg`/`--border` (chrome), `--good*`/`--bad*` (feedback). Font
  roles are `--mono` (a real code/monospace stack) and `--sans` (a system UI stack);
  no webfonts (the sandbox blocks them).

## The QA gate (do this before calling a session "done")

Every session artifact must pass **all 20 rules in `references/editorial-rules.md`**
before you publish it or tell the user it's ready. That file is the checklist. The
current template already bakes each rule into the structure above (shuffled MCQ +
deep-link review backlinks in Part 6, `scrollIntoView` nav, Parsons snap-back,
composed per-part + whole-session resets, ligature-off mono, run transcripts,
taught-patterns-only solutions, jargon unpacked in prose), so a faithful clone
starts compliant, but you still verify against the checklist because new content is
where the rule-breaks (an unfair grade, a hint that leaks its answer, a concept
first introduced inside a drill) creep back in.
