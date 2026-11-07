---
name: backlog
description: Turn a product brief, feature request, or raw context into a structured backlog of epics broken into user stories with acceptance criteria. Use when the user wants to plan, break down, or scope a body of work into epics and stories, or asks for a backlog / breakdown / planning from a description, doc, or set of notes.
---

# Backlog builder

Produce a work backlog: a small set of **epics**, each broken into **user stories**
that are independently workable, each with acceptance criteria. Invoke explicitly
with `/nate:backlog`, or it triggers when someone asks to break work down.

## 1. Gather the inputs

Work from whatever context is provided (a brief, PRD, transcript, ticket, or
notes). Before writing the backlog, make sure you know:

- **Goal / outcome** — what success looks like for this body of work.
- **Users / personas** — who the work is for.
- **Scope boundaries** — what is explicitly in and out.
- **Constraints** — tech stack, deadlines, dependencies, compliance.

If any of these are missing and would materially change the breakdown, ask up to
a few targeted questions first. If the gaps are minor, state the assumption you
are making and proceed — do not block on perfect information.

## 2. Identify epics

Group the work into **3–7 epics**. An epic is a coherent slice of value, large
enough to need multiple stories but small enough to describe in a sentence.
Prefer slicing by **user-facing capability or outcome**, not by technical layer
(avoid "Frontend", "Backend", "Database" as epics). Give each epic a short name
and a one-line goal.

## 3. Decompose into user stories

Under each epic, write user stories in the form:

> **As a** `<persona>`, **I want** `<capability>`, **so that** `<benefit>`.

Each story must satisfy **INVEST**: Independent, Negotiable, Valuable,
Estimable, Small, Testable. Split anything that is too big to finish in a single
iteration, or that bundles unrelated value, into separate stories. Call out
cross-story dependencies explicitly rather than hiding them.

For every story include:

- **Acceptance criteria** — 2–5 bullets in testable, observable terms. Prefer
  Given/When/Then where it adds clarity.
- **Size** — a rough relative estimate (S / M / L, or points 1/2/3/5/8). Note
  what drives the size if it is non-obvious.
- **Notes / dependencies** — only when they matter (blocked by, spike needed,
  open question).

Keep acceptance criteria about *what*, not *how*; leave implementation choices to
the people who pick up the story unless a constraint fixes them.

## 4. Sequence the work

Order epics and the stories within them by a sensible build order: enablers and
dependencies first, then the thinnest end-to-end slice that delivers value (a
walking skeleton), then enhancements. Flag the **smallest releasable increment**
so the reader can see where an MVP line could fall.

## 5. Output format

Render the backlog as Markdown:

```markdown
# Backlog: <initiative name>

_Goal: <one line>. Assumptions: <any you made>._

## Epic 1 — <name>
_<one-line epic goal>_

### Story 1.1 — <short title>  · <size>
As a <persona>, I want <capability>, so that <benefit>.

**Acceptance criteria**
- [ ] …
- [ ] …

**Notes:** <dependencies / open questions, if any>

### Story 1.2 — …

## Epic 2 — <name>
…

---
**Suggested MVP line:** Epic 1 + Story 2.1 (thinnest slice that ships value).
**Open questions:** <bulleted, if any>
```

## Propagation test marker

PROPAGATION_TEST_V2 — this line was added to confirm edits reach every VM.

## Quality bar

- No epic has only one story — if it does, it is a story, not an epic.
- Every story is testable: a reader can tell when it is done.
- The breakdown covers the stated scope with no silent gaps, and nothing out of
  scope sneaks in.
- Surface open questions and assumptions at the end rather than guessing silently
  on anything consequential.
