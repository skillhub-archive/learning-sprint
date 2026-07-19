---
name: learning-sprint
description: >-
  Builds a focused, self-paced "level-up sprint" to take someone from beginner
  toward expert in ANY subject, code or not, delivered as interactive artifacts:
  a roadmap Hub plus hands-on workbook sessions with self-grading quizzes,
  milestones, and curated resources. Use this skill whenever the user wants to
  learn or master something fast, "help me learn X", "I want to get good at X",
  "build me a study plan / course / curriculum for X", "level up in X", "upgrade
  myself", or pastes a multi-part learning-plan request (N-hour plan, sessions,
  quizzes, levels, resources). The subject can be anything a person wants to
  improve at: a programming language, a tool (Excel, Figma), a spoken language,
  public speaking, chess, guitar, financial modeling, cooking technique. Trigger
  proactively when someone describes wanting to skill up and sounds like they
  want structure, even if they don't say "skill" or "course."
---

# Learning Sprint

Someone wants to get good at something, fast, and they want structure instead of
a pile of tabs. This skill turns that into a **proven, repeatable learning
system**: a roadmap Hub plus a series of **hands-on workbook sessions**, each an
interactive artifact that makes the learner *produce*, not just read.

It generalizes a format validated for a Python sprint and benchmarked against the
leading interactive-learning tools (DataCamp, Codecademy, freeCodeCamp, Exercism,
boot.dev) and the learning-science literature. The approach is not decoration,
every part earns its place. **Read `references/why-it-works.md` before building so you
understand *why* each part exists and don't strip the "boring" parts that are
actually doing the work.**

The subject does NOT have to be code. The scaffold is subject-agnostic; two things
get swapped per subject (see `references/domain-adaptation.md`):
1. **The real practice environment** (Python → VS Code; Excel → a workbook;
   language → speaking aloud/recording; chess → a board + engine).
2. **The drill types** ("predict the output" is code-shaped; the general form is
   "produce, then check", pick the right drill for the domain).

## What you produce

- **One roadmap Hub** (an artifact): the whole plan, 5 levels beginner→advanced,
  every session outlined, a clear milestone at each step, a top-5 resources block,
  and a progress tracker that persists via `localStorage`.
- **Per-session workbook artifacts, built on demand**, each following the locked
  template in `references/workbook-template.md`.
- Everything is **self-grading** (no live grading needed) and **cross-linked**:
  the Hub links out to built sessions; each session links back to the Hub.
- **A "Where to next" closer on the finished Hub**: a short forward-looking send-off
  section (NOT a numbered level or "bonus level") that names the natural next topic
  pointed OUTWARD to genuine resources, and lists genuine, proctored certifications
  web-verified current at build time. Optionally a **bonus session** (a real workbook
  outside the graded syllabus) when the next topic is self-containable. See
  `references/workbook-template.md`.

This maps directly onto the canonical five-part request:
1. N-hour plan, the 20% that drives 80%, split into sessions + a review each → the
   Hub + session structure.
2. One-page 5-minute summary per session (bullets, diagrams, examples) → Part 1 of
   each workbook.
3. Progressive per-session quizzes + one comprehensive final → Parts 2–6 +
   cumulative review; final quiz in the last session.
4. Five levels, beginner→advanced, with a milestone at every step → the Hub's level
   track.
5. Top-5 resources with why-they're-worth-it → the Hub's resources block.

## Step 1: Intake (use AskUserQuestion, don't guess)

Learning is personal, calibrating to the actual person is the whole game. Gather
these before designing. Lead with the first three; they shape everything.

1. **Subject** — exactly what they want to level up in. If broad ("get better at
   data"), narrow it to one concrete track.
2. **Starting point** — honest current level. This calibrates Level 1's pace. Ask
   what they can already *do*, not just "beginner/intermediate/advanced." **Default
   to a true level-0 beginner unless the intake says otherwise**: assume they have
   never opened the real environment, and build Session 1 to set it up from zero
   (see Step 4 and `domain-adaptation.md`).
3. **End goal / why** — where they're trying to get (a job change, a specific
   deliverable, a hobby milestone). This decides *which* 20% is the high-leverage
   20%. The same subject gets a different plan for a different goal.
4. **The real environment** — where is this skill actually practiced? This is the
   hands-on target the workbook sends them to. (See `references/domain-adaptation.md`.)
5. **Time budget / format** — default is **20 hours = 10 two-hour sessions across
   5 levels** (matches the canonical request), but scale it: offer shorter
   (10h/5 sessions) or longer. Each session ends with a ~15-min review.

Quiz mode is **self-grading by default** (an artifact that reveals the correct
answer, the gotcha behind wrong choices, and how to improve). Live free-text
grading is richer but not durable/reusable, so it's not the default. Only offer it
if the user specifically wants a chat-based tutor.

## Step 2: Design the curriculum (before building anything)

- **Find the real 20%.** Choose the topics that drive most of the results *for this
  person's goal*, not a generic syllabus. Bias hard toward what they'll actually
  use. State the arc in one line (e.g. "foundations → real-world use → the two
  things your goal needs most").
- **Five levels, two sessions each** (for the 20h default). Each level gets a crisp
  **milestone**: a "you can now do X, unassisted" statement.
- **Each session gets a milestone too**, plus a 15-min review activity.
- **Curate the top-5 resources for real.** Prefer free, high-quality, canonical
  sources. Never invent resources, if unsure a resource exists or says what you
  think, verify with WebSearch. For each: one line on *why it's worth their time*
  and *when* to use it (during vs. after). Four-of-five free is a good target.
- Present the level/session outline and get a quick sign-off before building the
  Hub. This is cheap and prevents rebuilding ten sessions.

## Step 3: Build the Hub (artifact)

**Load the `artifact-design` skill first** (required before writing any artifact)
and give this subject its *own* visual identity, don't reuse a template look.
Ground the palette/type in the subject's world.

The Hub contains: header + progress panel (sessions done / current level / hours, a
progress bar); a short "how this works" strip; the 5-level track with two session
cards each (number, title, what you'll learn, milestone, resource, review); the
top-5 resources block; and a "how to navigate" note. Session completion checkboxes
persist via `localStorage` (wrap in try/catch).

**Linking:** artifacts can't hard-link to a session that doesn't exist yet. So
built sessions show a real **Open** link (their published URL, `target="_blank"`);
unbuilt ones show a "not built yet, ask to build it" state. After you publish a
session, **redeploy the Hub with that session's URL added.** Keep each artifact's
file path stable so redeploys keep the same URL.

The "ask to build it" state is a transient AUTHORING convenience and must never
survive to a hosted reader (a hosted Hub has no chat). Swap each unbuilt card to a
real Open link as its session ships, and when the LAST session ships run the
**Hub-completion cleanup** (drop all build-on-demand copy, done-state the Levels
intro, retitle the nav callout "Using this Hub" and move it under the cards) and
append the **"Where to next" closer** as the final section. The closer points OUTWARD
to genuine resources and lists only genuine, web-verified certifications; it is never
a numbered level. Full structure for both is in `references/workbook-template.md`.

## Step 4: Build sessions on demand (workbook artifacts)

When the user asks for a session, build it as its own artifact following
`references/workbook-template.md` exactly (the 6-part workbook engine). Match the
Hub's visual identity. Add a back-to-Hub link, and after publishing, update the Hub
to link to it.

**Session 1 must onboard the real environment FROM ZERO.** A level-0 beginner does
not yet have the tool, so naming it ("use DB Browser or the sqlite3 shell", "open VS
Code") is not enough. Session 1's first "Your turn" is getting the environment
working: how to GET it (download/install), how to OPEN it, and WHERE to do the thing,
recommending the single friendliest path and deprioritizing alternatives. This is
rule 1 (unpack before use) applied to the environment itself. Learned when a SQL
Session 1 named the tools but a true beginner did not know how to obtain or open one,
the same class of gap as assuming a pre-existing account or install.

Build sessions **on demand, not all at once** (keeps each artifact focused and lets
the learner give feedback on the format early). After building the first session,
explicitly ask the user to try it and confirm the format before mass-producing the
rest.

## Step 5: QA every session against the editorial rules (the definition of "done")

A session is not finished when it renders. It is finished when it passes the
**numbered checklist in `references/editorial-rules.md`** (26 rules as of
2026-07-17; trust the file's own count over any number quoted elsewhere). These rules were derived by
hardening real sessions with a live learner, and each one closes a specific way a
session quietly betrays the learner's trust: an exercise that grades a concept it
never taught, a hint that leaks its own answer, a code font that draws `!=` as `≠`
so the learner types the wrong character, a "correct" MCQ answer always sitting in
slot A. **Run the checklist before you publish and before you tell the user a
session is ready.** A faithful clone of the template starts compliant, but new
content is exactly where rule-breaks creep back in, so verify every time rather than
assuming. Rules 18 and 19 are the closing gate and both need doing per session:
**rule 18** runs every snippet and diffs its real output against every claimed output,
and **rule 19** traces every graded item back to where its concept was taught so
nothing is tested before it is taught. Run both with an INDEPENDENT, ADVERSARIAL
reviewer (a separate agent told to find problems, given a baseline of what earlier
sessions taught), not by re-reading your own work. One thing those two gates do NOT
cover (both run outside the browser): whether the artifact's own JS still parses and
renders. Per rule 24 (artifact integrity) in `editorial-rules.md`, after ANY edit that
touches a JS string literal, even a late "harmless" comment tweak, re-validate the
`<script>` and refresh the live artifact before republishing, since one unescaped
apostrophe silently empties every interactive part. If a rule genuinely can't be met,
say so instead of shipping past it.

## References

- `references/workbook-template.md` — the exact 6-part session structure and the Hub
  structure. The authoritative build spec.
- `references/editorial-rules.md` — the numbered QA checklist every session must pass
  before it's "done." Not style preferences; each rule fixes a real learner-trust
  bug found in production. This is the gate for Step 5.
- `references/why-it-works.md` — the reasoning behind every part. Read this so you
  keep the parts that do the work.
- `references/domain-adaptation.md` — how to adapt the "real environment" and the
  drill types to non-code subjects, with worked examples.

## Conventions

- Self-contained artifacts only (inline CSS/JS; no external CDNs, the sandbox blocks
  them). No in-browser code/tool execution, the learner uses the real environment;
  that's a feature, not a compromise.
- Give each sprint its own identity; don't ship the Python palette for a guitar
  course.
- Never fabricate resources or facts about a resource; verify when unsure.
- Definition of done for a *new skill build* (not each sprint): per the user's
  workflow, also create the Confluence page and export the `.skill` file. That's a
  separate publishing step, not part of running the sprint for a learner.
