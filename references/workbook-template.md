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
- **Vary the answer TYPE; do not default every drill to a count.** This bullet is
  the CANONICAL grader contract (rule 23 in `editorial-rules.md` holds the why;
  `domain-adaptation.md` shows per-subject drill flavors; if you change the grader,
  change it HERE and fix the pointers, never fork the spec). Give each drill an
  explicit `type` and grade through one shared `gradeDrill(d, val)`:
  - `count` and `value` (a single result, or "which is first") match exactly via the
    normalizer: trim, strip trailing `[.;:]+`, and, when `caseFold` is on (below),
    lowercase both sides.
  - `set` (all results, any order) and `seq` (results in order) tokenize both sides
    with `toks(s) = s.toLowerCase().trim().split(/[\s,.;:"'‘’“”]+/).filter(Boolean)`
    then compare sorted (set) or in order (seq).
  - **The punctuation in that character class is load-bearing, and it is the single most
    repeated fairness bug in this engine's history.** The rule: **whatever punctuation a
    subject prints AROUND an answer token belongs in this split class, and belongs in the
    grader harness as a test case.** Two instances, both found by a reviewer rather than by
    the author, both in the same two drills, two days apart:
    - **the colon** (2026-09-08, API track). An HTTP header list prints
      `content-type: application/json`, so a learner copying the names as shown answers
      `content-type:, etag:` and every token carries a trailing colon.
    - **quotes** (2026-09-10, API track). JSON field names print as `"userId"`, so the same
      learner answers `"userId", "id"` and every token carries quotes. Curly quotes are in
      the class too, because a word processor or chat client substitutes them silently.

    `norm()` strips this punctuation already, so `count`/`value` answers were always safe
    and every `set`/`seq` answer was not: the `set` and `seq` branches never reach `norm()`.
    **That asymmetry is why this keeps recurring, so check `toks()` specifically when
    porting an older session, and do not assume a green harness settles it** (see the
    harness warning below).
  - **An optional `alt` array holds other accepted SPELLINGS of the same answer.** Compare
    each candidate through **one shared `matchesOne(d, val, ans)`** so that `alt` reaches
    every drill type:

        function matchesOne(d, val, ans) {
          if (d.type === 'set') { /* sorted toks compare of val vs ans */ }
          if (d.type === 'seq') { /* in-order toks compare of val vs ans */ }
          return norm(val) === norm(ans);
        }

        function gradeDrill(d, val) {
          var cands = [d.ans].concat(d.alt || []);
          for (var i = 0; i < cands.length; i++) { if (matchesOne(d, val, cands[i])) return true; }
          return false;
        }

    **Do not write this as branch-and-return with the `alt` loop appended at the end**
    (the shape shipped between 2026-09-08 and 2026-09-10). The `set` and `seq` branches
    returned before the loop, which made `alt` dead code for exactly the two types whose
    answers have the most valid spellings. It went unnoticed because the only `alt` in
    play was on a `count` drill, which travels the `value` path and therefore passed.
    **A harness must assert `alt` reachability PER TYPE**, not on one drill.

    This is a fairness fix, not a convenience. A quantity can honestly be typed as a digit
    or a word and no format nudge makes one of them the obviously-intended form, so a
    single accepted string turns understanding into a coin flip on wording. Added after an
    API session shipped one drill demanding `0` and another four items later demanding
    `two`: each was individually defensible, and arbitrary taken together.
    **`alt` never accepts a DIFFERENT answer, only a different spelling of the correct
    one.** `ans: '0', alt: ['zero', 'none', 'no bytes']` is right; putting a wrong answer
    in `alt` stops the drill testing anything. Prefer it over a stricter ask: "answer with
    a digit, not a word" is a real instruction a learner can miss, and gating a correct
    understanding on it is the unfair grade rule 7 forbids. Verify with a small test
    harness asserting both the accepts and the REJECTS, since an over-broad `alt` fails
    silently and no gate catches it.
  - **A passing grader harness is not evidence that grading is fair.** None of the five
    gates ever executes the grader against learner input, so this harness is the only
    check there is, which makes how you choose its cases the whole ballgame. **Derive them
    from what the session PRINTS ON SCREEN, not from what the grader looks like it
    accepts:** open each transcript a drill refers to, copy the answer token exactly as a
    learner would see it (punctuation and all), and assert that form. The 2026-09-10 quote
    bug survived a harness that passed 38/38 because every case in it was a form the
    previous fix had already handled, so the harness only ever confirmed the patch it was
    written from. Test the CLASS, not the patch: ask "what punctuation wraps this token in
    this subject?" and "does this feature work on every type, or just the one I tried?"
  - **`caseFold` is a per-subject decision, made consciously at track-build time.**
    Turn it ON for subjects whose answers are case-insensitive (SQL: `NULL` and
    `null` are the same answer, and grading `null` wrong is exactly the unfair-grade
    failure rule 7 forbids). Leave it OFF for subjects where case IS the lesson
    (Python: `True` vs `true` is a real distinction, and the drill must catch the
    learner who types `true`). The `set`/`seq` token path always folds case (it
    compares result *names*, where case is presentation); the flag governs only
    `count`/`value`.
  Ask `value` when one result comes back, `set` when several do, `count` only when
  the count is the lesson, `seq` only when the result has a defined order. Also vary
  the **cognitive task**, not just the answer type: ramp from reading one whole
  result, to operating on a filtered subset, to reading one group out of a grouped
  result, to combining several clauses, rather than restating the same move with a
  different function. See rule 23 in `editorial-rules.md`.
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
  fewer bugs). If a shuffle equals the correct order, reshuffle/rotate. **Default to
  two side-by-side columns** (`grid-template-columns: minmax(0, 1fr) minmax(0, 1fr)`,
  NOT plain `1fr 1fr`, plus `.pcol { min-width: 0; }` so a long non-wrapping line
  can't force its track to content-width and push the card past the page edge). That
  layout gives each tile only ~54 monospace chars, which is plenty for short tiles
  (most code, and non-code subjects like animation, language or cooking where a tile
  is a short step). **Only switch to a single full-width column
  (`grid-template-columns: 1fr`, bank stacked ABOVE the solution) when a sprint's
  Parsons lines will actually run past that ~54-char budget** (verbose queries like
  SQL, long code lines): full width gives each tile ~115 chars so clauses fit intact
  instead of getting a per-line horizontal scrollbar that fights the click-to-move
  interaction. Do NOT make full width a blanket default across subjects; it is a fix
  for long tiles, not an upgrade. Decide the layout ONCE per sprint and use the same
  one for every session in it, since a layout that changes shape mid-sprint reads as
  unfinished. If you pick full width, add a one-line blurb by the Parsons heading
  explaining the two stacked lists and click-to-move, because the wide tiles look a
  touch sparse on short-line sessions. Either way keep individual lines short: one
  clause per tile, multi-line a long subquery / CTE body across tiles (or assign a
  long URL to a `url` variable), and keep `overflow-x: auto` on each line as a safety
  net so a stray long line degrades to a scrollbar rather than blowing out the
  layout. Disable the **Check order** button until at least one
  line has been moved into the solution column, using a separate `checked` flag so
  placing and removing lines stays fully interactive while only the button is gated
  (rule 20).
- **Faded / fill-in-the-blank:** near-complete code (or formula/sentence) with a
  few blanks as inline inputs. Disable **Check** until every blank is filled
  (rule 20). Check each blank; reveal answers.

**Both Part 3 sub-types PERSIST (rule 22).** They are graded practices, so a reload
must not wipe them, even though neither gets a progress-strip counter. Persist on
Check only, matching the drills: Parsons saves `state.parsons[i] = {ok, order}` (the
verdict plus the learner's final arrangement) and restores by moving those lines back
into the solution column in the saved order before replaying the checked state; faded
saves `state.faded[i] = {ok, vals}` (the verdict plus what was typed in each blank)
and restores by refilling every blank, re-marking each, and re-showing the reveal. The
per-part reset must clear both keys and save. This was missing from the engine until
2026-09-08 and shipped in four tracks, because rule 22 used to enumerate only five
graded practices; do not reintroduce it by cloning an older session.

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

**Deck size FLOATS, it is not a locked count, and it is capped.** Subjects differ, and
so does how many genuine atoms a given level holds, so forcing every session in every
track to the same number means padding some decks and gutting others. Size each deck to
the atoms the session actually has, within a **12 to 16 band, and never past 18**.

Three rules keep that from drifting upward (decided 2026-09-08):

- **Every card is an atom worth instant recall.** If you are adding one to be thorough
  rather than because the learner should summon it without thinking, cut it. Padding a
  deck to hit a number is the failure this band exists to prevent, in both directions.
- **A deck covers THIS session's atoms only, never cumulative ones.** This is the rule
  that actually enforces the ceiling: decks balloon in later sessions when they start
  carrying earlier material forward, and re-testing earlier material is already Part 6's
  job through spaced/interleaved review. Keep the deck session-local and it cannot creep.
- **Heavy weighting toward one concept is fine when that concept genuinely IS the
  session's atom set** (an API session drilling six status-code cards is right, because
  status codes are the highest-frequency recall material in the subject). Weighting is
  only a problem when it reflects what was easy to write rather than what must be
  automatic.

If a session seems to need more than 18, that is a signal the SESSION is carrying too
much, not that the deck should grow. Split the content instead.

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
Hub"); a pointer to the next session. While you are authoring on demand that pointer
can read "ask me to build it", but once the following session exists swap it for a
real Open link. On the FINAL session's footer there is no next session, so it instead
carries a single line pointing back to the Hub's "Where to next" closer (see below).
Learner-facing copy on a hosted page must never tell the reader to "ask me / ask
Claude" to build anything (see the author-instructions rule in `editorial-rules.md`).

## Hub: the "Where to next" closer (optional final section)

A finished Hub gets one forward-looking send-off, appended as the LAST content
section before the footer. It is a SECTION, never a session: no drills, no quiz, no
checkboxes, and it is NOT a numbered level or a "bonus level". Its job is to answer
"you finished, now what?" honestly. Two blocks, both optional per subject:

1. **"Try this next" signpost.** Name the natural next topic (often the one thing the
   sprint pointed at but did not fully teach) and point OUTWARD to genuine external
   resources for it (canonical docs, a reputable tutorial). NEVER point the reader at
   "ask me" or "ask Claude to build it": a hosted Hub has no chat, so a build-on-demand
   instruction is meaningless to a reader (same class as putting CLI-only steps on a
   teammate-facing page; see the author-instructions rule in `editorial-rules.md`). If
   the sprint has an optional bonus workbook (below), the signpost may ALSO carry an
   "Open workbook" link to it, ALONGSIDE the outward resources, not instead of them.
2. **"Get it certified" block.** List GENUINE, proctored credentials only, the kind a
   real exam body grants. Skip course-completion "certificate" badges (they prove
   attendance, not skill). Web-verify each credential is current at build time (exam
   codes, price, and whether it still exists all churn) and state an honest caveat
   where one is true (e.g. "there is no widely-recognized vendor-neutral X exam", or
   "this tool has no exam, it is a library not a product you certify on"). If a subject
   has no real credential, say so rather than padding the block. Certs are per-subject
   and must be verified fresh, never from memory (see `domain-adaptation.md`).

Echo it once from the capstone: the final session's footer carries a single line
pointing back to this closer, in place of the usual next-session pointer.

A **bonus session** (a real workbook, but OUTSIDE the graded syllabus: no level number,
not counted in the Hub progress, titled about the topic rather than as a rung) is
**standard on every track**. It is the track's advanced tier: a 20-hour sprint produces
solid working knowledge, not advanced, and the bonus reuses the already-QA'd session
engine, which is far cheaper than authoring a separate "Advanced X" track. Pick the
natural next topic that is self-containable as one more hands-on session (SQL window
functions qualified: they build straight on the joins and aggregates already taught).

The one escape hatch: when the next topic is genuinely a whole new subject rather than a
self-containable extension, say so explicitly and leave the closer as a signpost-only
send-off rather than padding a weak bonus onto the track.

A bonus session is surfaced ONLY through the closer's signpost (an "Open workbook"
link), never as an extra graded card, and it must NOT get a Hub progress checkbox (the
bar stays N/N).

## Hub-completion cleanup (when the last session ships)

While a sprint is being built the Hub carries build-on-demand copy: unbuilt cards say
"not built yet", the intro invites the learner to ask for the next one. The moment the
LAST session ships that copy is stale and must be swapped to completed-state wording:
- Drop every "ask me to build Session N" / "the rest are waiting to be built" line.
- The Levels intro becomes a done-state line (e.g. "all N sessions are built, open any
  card").
- Retitle the navigation callout "Using this Hub" and move it to directly AFTER the
  session cards it describes, so it does not sit at the bottom competing with the
  send-off. Target section order: level track -> Using this Hub -> resources ->
  "Where to next" -> footer.
- Add the "Where to next" closer (above) as the final section.

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

Every session artifact must pass **every numbered rule in `references/editorial-rules.md`**
before you publish it or tell the user it's ready. That file is the checklist. The
current template already bakes each rule into the structure above (shuffled MCQ +
deep-link review backlinks in Part 6, `scrollIntoView` nav, Parsons snap-back,
composed per-part + whole-session resets, ligature-off mono, run transcripts,
taught-patterns-only solutions, jargon unpacked in prose), so a faithful clone
starts compliant, but you still verify against the checklist because new content is
where the rule-breaks (an unfair grade, a hint that leaks its answer, a concept
first introduced inside a drill) creep back in.
