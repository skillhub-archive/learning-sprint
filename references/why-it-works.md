# Why each part exists (read before cutting anything)

This format is engineered around evidence, not aesthetics. When a build feels
"too long" the temptation is to cut the effortful parts, those are exactly the
parts doing the learning. Cut length by tightening prose, not by removing
retrieval, arrange/fill, or spaced review.

## The core principle: produce, don't consume

Recognition (picking the right multiple-choice answer) feels like learning but
mostly isn't. **Retrieval practice** (generating the answer from memory) is the
lever. Students who retrieve forget ~13% over two days; those who only reread
forget ~56%. Every part except the summary is built to make the learner *produce*.

This is why the workbook sends the learner to the **real environment** to actually
do the work. Reading the material is hearing an order; typing/performing it is
writing the order down, which is what makes it stick (the **generation effect**).

## Why the parts are ordered as they are (fading scaffold)

Novices learn better when support is **faded gradually** rather than thrown at a
blank page. Worked-example research: study a solution → rearrange a scrambled one →
fill blanks → write from scratch. The parts implement that ramp:

- **Part 1 Learn & do** — worked examples + guided "your turn" (most support).
- **Part 2 Predict/trace** — retrieval on the mental model; cheap, high-frequency.
- **Part 3 Arrange & fill** — **Parsons problems** and **faded** examples. Research
  shows these are more effective for building comprehension than tracing alone and
  *comparably effective at improving from-scratch code-writing*, while being less
  frustrating than a blank page. This is the single most important bridge for a
  learner whose gap is "I can read it but can't write it unassisted." Do not drop
  it to save space.
- **Part 4 Write it yourself** — full generation (least support).
- **Part 5 Flashcards** — repetition engine for atoms that must become automatic.
- **Part 6 Quick check + cumulative review** — low-stakes graded retrieval, plus
  **spacing + interleaving**.

## Why cumulative/interleaved review (from Session 2 on)

**Spaced practice** (recall spread over time) and **interleaving** (mixing topics
rather than blocking them) both beat massed, single-topic practice for durable
retention. Siloed per-session quizzes lose this. So each later session's Part 6
re-tests a couple of earlier items, and the final session is fully comprehensive.

## Why self-grading, not live grading

Live free-text grading is richer but not durable or reusable, and it can't run when
the tutor isn't present. A self-grading artifact that reveals the answer, the
**gotcha** behind each wrong choice, and **how to improve** captures most of the
feedback value and the learner can retake it forever.

## The deliberate tradeoff: no in-browser execution

Best-in-class tools auto-run code in a sandbox for instant feedback. We instead send
the learner to the **real environment** (VS Code, a real workbook, a real
instrument). For anyone whose goal is real-world capability, learning the actual
environment IS part of the skill, so this is a feature. The cost (slightly slower
feedback on write-it tasks) is accepted on purpose. Do not fake a sandbox to
"fix" this.

## Design decision log (from the Python build)

Keep this list current as the format evolves; it's the rationale future-you needs.
- **Self-grading artifact** over live chat grading — durability/reuse.
- **Hub-as-index + per-session artifacts**, linked by URL after each is built —
  keeps artifacts focused; avoids one giant unmaintainable file.
- **Workbook engine over passive reading** — the generation effect; the learner's
  own "waiter writes the order down" analogy.
- **Real environment over sandbox** — real-world capability is the goal.
- **Parsons/faded scaffold** — the evidence-based bridge for reading→producing.
- **Spaced + interleaved cumulative review** — durable retention over cramming.
- **The "20%" is chosen against the learner's stated goal**, not a generic syllabus.

## Sources

- Retrieval + spaced practice: evidencebased.education/resource/retrieval-and-spaced-practice-study-strategies-that-must-be-combined/
- Active recall vs rereading (13% vs 56%): recallify.ai/evidence-for-active-recall-and-spaced-repetition/
- Faded Parsons problems (Berkeley, CHI 2021): acelab.berkeley.edu/wp-content/papercite-data/pdf/parsons-chi2021.pdf
- Parsons problems as scaffolding: arxiv.org/abs/2311.18115
- Code visualization / mental models: pythontutor.com
