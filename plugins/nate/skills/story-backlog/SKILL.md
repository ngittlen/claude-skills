---
name: story-backlog
description: Generate a structured story backlog from raw source materials (budget CSVs, requirement docs, wishlists, design notes) for a new engagement or planning round. Use when starting from unstructured inputs and you need to produce per-epic markdown files with full story drafts (title, description, acceptance criteria, technical details, non-goals, related stories) ready to share with developers. Can also push the resulting stories into a work tracker via MCP (e.g. the Azure DevOps or Jira MCP) in addition to the local markdown files. Walks through discovery, outline, drafting, code-verification, coherence-review, and file-organization phases.
disable-model-invocation: true
allowed-tools: Read Write Edit Bash Grep Glob
---

# Story Backlog Generation Workflow

Encodes the workflow for going from raw source materials to a coherent, code-verified
story backlog organized into per-epic markdown files. Invoke at the start of a planning
round; do not invoke for routine edits to an existing backlog.

Once a backlog exists and requirements shift under it, use the companion
**story-revision** skill instead — it revises stories in place while holding the
structure, numbering, and cross-references steady.

## Inputs to confirm before starting

Ask the user, in plain prose, for each of these before doing anything else:

1. **Source materials** — which files in the working directory hold the requirements,
   budget, or wishlist. Read every named file.
2. **Codebase paths** — which directories hold the code the backlog will reference
   (usually sibling repos at `../<repo>`). Read enough of each to know its language,
   framework, and structure.
3. **Design sources** — are there designs for any of the work, and where do they live:
   design screenshots, a Figma MCP server, exported mockups, or similar. Designs let
   you draft full stories instead of placeholders (see the standing rules).
4. **Story template** — does the user have a story from a prior engagement to paste as
   a style example? If yes, match its detail level and prose style. If not, default to
   `templates/story.md`.
5. **Engagement shape** — duration, team composition, anything obviously out of scope.

This list is not exhaustive — the actual inputs shift depending on the work being
planned. A given round might also need API specs, a tracker/MCP destination for the
stories, prior backlog files to extend, or domain references; ask for whatever the work
in front of you depends on.

Do not proceed past Phase 1 until these are confirmed.

## The workflow

The phases run sequentially. Don't skip ahead; the value comes from the user's
corrections between phases.

### Phase 1 — Discovery and picture

Read the source materials. Produce a short structured summary in chat that:

- Distinguishes **funded scope** from **wishlist** (look for budget columns, "agreed"
  markers, explicit `Work` lists, etc.).
- Identifies the **overall items of work** that will become epics.
- Flags **gaps** — aggressive vs probable estimates, wishlist items not in the budget,
  ambiguous ownership.
- Names **assumptions you're making** so the user can correct them before they get
  baked in.

Do not draft any stories. Do not write to any files. The output is a chat message:
"Here's my picture of the work."

### Phase 2 — Outline

Once the picture is accepted, propose an epic-level outline with one-line story stubs
under each epic. Iterate.

Common corrections from this phase:

- "Merge stories X and Y"
- "Split story Z per repo / per component"
- "Drop epic N — it's in flight elsewhere"
- "These are placeholders, fill in later"

Reprint the full outline after each correction so the cumulative state is visible. Do
not draft full story bodies in this phase.

### Phase 3 — Story drafting

Draft stories one at a time, in numbered order, **in chat**. For each story:

1. **Read the code** it will reference. Verify file paths, type names, behavior claims,
   test states. If the story will claim "today X is broken," run the relevant tests or
   read the file to confirm.
2. **Draft in chat** using the agreed template. Include only code references you've
   verified, and put them in **Technical details** — keep the Description readable by a
   non-engineer.
3. **Ask for corrections** before writing. Use the phrase: *"Settled, or corrections
   before I append?"*
4. **Append to the per-epic file** only when the user explicitly approves.

Drafts live in chat until approved. Corrections happen on the cheap-to-revise chat
version, not on a committed file.

### Phase 4 — Code verification

Run during Phase 3 for any concrete claim, and again as a sweep when the user asks:

> "Re-examine the current state of the codebase. Make sure the stories are up to date
> with the code."

Re-verify concrete claims, flag stale references, propose targeted edits.

### Phase 5 — Coherence review

After every few stories, and again at the end of each epic, sweep for:

- Forward-references point at the right story numbers (renumbering can leave stale
  pointers).
- Reciprocal relationships actually match — "depends on" / "blocks" are inverses of
  each other (A blocks B ⟺ B depends on A), and "feeds" should point back consistently.
- Wording consistency (ownership framing, status markers, bug-escalation policy).
- "Today X" claims still match the code.
- Descriptions haven't drifted technical — no file paths or type names that belong in
  **Technical details**.

Present findings as a numbered list with proposed fixes. Apply on approval.

### Phase 6 — File organization

- One markdown file per epic: `epic-N-stories.md` (or whatever the user prefers).
- Each file has a header, an epic index (one-line per story), then full story bodies.
- Rename a single combined file to per-epic files as soon as a second epic starts.

The per-epic markdown files are the canonical source of truth. **Optionally, the same
stories can also be pushed into a work tracker via an MCP server** — for example the
Azure DevOps MCP or a Jira MCP — so the backlog lives in the team's board as well as in
the repo. When doing so:

- Confirm the destination with the user first: which MCP/tracker, which project/board,
  work-item type (e.g. ADO `Issue` / `User Story`, Jira `Story`), and target
  state/column. The tracker's process template decides the available types and states.
- Only create tracker items from stories the user has already approved in chat — the
  markdown file is still written first and remains the editable source.
- Mirror each story's title, description, acceptance criteria, technical details, and
  non-goals into the item; preserve the `Story N.M` numbering in the title so the board
  and the markdown stay cross-referenceable. If the tracker has a natural home for
  implementation notes separate from the description field, use it.
- This requires the relevant MCP server to be connected and its write tools available
  and permitted in the current environment. This skill doesn't bundle or assume any
  particular MCP — how tracker tools are wired up and permitted is left to each user's
  own setup.

## Story template

If the user provided no example, use [templates/story.md](templates/story.md). Match
heading levels and section ordering.

Required sections per story: **Title** (`## Story N.M — <title>`), **Epic**,
**Description** (prose), **Acceptance Criteria** (bullets), **Technical details**
(optional — see below), **Not in scope** (bullets, each with a pointer to the story that
does cover it if applicable), **Related stories** (one paragraph: depends on / blocks /
feeds / parallels).

### Description vs. Technical details

Keep these two separate; it is the most common thing to get wrong.

The **Description** is for a reader who is not going to open the codebase — a client
stakeholder, a designer, a PM skimming the board. Write it in the language of the
product: what a user can do afterward that they can't do today, what breaks or annoys
people now, why the work is worth doing. Name behavior, not implementation. Keep file
paths, class and function names, framework names, schema and endpoint details out of it.

The **Technical details** section is where all of that goes. It is optional and should
be omitted rather than padded — a story with nothing concrete to say to a developer is
better off without the heading. When it is present, it carries the verified code
references: which files and types are involved, which tests exist and what state they're
in, data or migration concerns, API shapes, libraries already in play, gotchas the
implementer would otherwise trip over. Everything in it is still subject to the
read-the-code rule: verify before it lands.

Acceptance criteria sit in between. They should stay observable and behavioral — the
kind of thing a reviewer could check without reading a diff. If a criterion can only be
stated in terms of internal structure, that is usually a sign it belongs in Technical
details instead.

## Standing rules

These apply throughout the workflow:

- **Read the code, don't fabricate.** Verify any file path, type name, test name, or
  behavior claim before it lands in a story.
- **Write descriptions for non-engineers.** If a Description can only be understood by
  someone who has the repo open, it's in the wrong section. Move the specifics to
  **Technical details** and restate the Description in terms of user-visible behavior
  and business value.
- **Draft in chat, write on approval.** Never write to a file until the user has
  explicitly approved the current draft.
- **Push back on unjustified assumptions.** If you find yourself making an
  ownership-boundary claim ("X owns the algorithm, Y owns the API") that isn't directly
  supported by the source materials, surface the assumption to the user before encoding
  it in a story.
- **Distinguish eligibility from commitment.** Markers like "Week 1" are eligibility
  tags, not schedule promises; default to leaving them out unless the user asks for
  them.
- **Use placeholders for design-dependent work that doesn't have designs yet.** A
  placeholder with intent + expected scope + dependencies is more useful than an absent
  story or an over-confident speculative one. When designs *are* available — design
  screenshots, a Figma MCP server, exported mockups, or similar — pull the concrete
  details from them and draft the full story instead of leaving a placeholder.
- **Include the bug-escalation policy** in stories that involve modifying tests or
  surfacing bugs. Default wording: *"If implementation surfaces a genuine bug (not a
  stale test), document it and escalate for triage; do not modify the test to match
  broken behavior."*
- **Watch auto-generated docs.** If the project has a `CLAUDE.md` marked
  auto-generated, do not put guidance in its body — it will be overwritten. Use a
  sibling `docs/` file or the `<!-- MANUAL ADDITIONS -->` block if present.

## When to proactively suggest a coherence review

- A renumbering has occurred (stories merged, split, or inserted).
- Three or more stories have landed since the last review.
- A significant pivot has happened (new context that reshapes earlier stories).
- The user asks for a "final delivery" of any epic.
