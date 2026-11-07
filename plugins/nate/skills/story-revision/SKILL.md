---
name: story-revision
description: Revise an existing story backlog when requirements drift — new client asks, dropped scope, a changed design, a pivot mid-engagement, or code that moved out from under the stories. Reconciles the change against the per-epic markdown files and applies surgical edits that preserve story numbering, section structure, and cross-references. Can mirror the same edits into a work tracker via MCP (e.g. Azure DevOps or Jira). Use when a backlog already exists; use story-backlog instead when starting from raw source materials.
disable-model-invocation: true
allowed-tools: Read Write Edit Bash Grep Glob
---

# Story Revision Workflow

Companion to **story-backlog**. That skill builds a backlog from raw materials; this one
keeps an existing backlog true as the requirements move. The defining constraint is that
the backlog is already in people's hands — developers have read it, a board may mirror
it, story numbers get cited in standups and PRs. So the goal is not "regenerate the
backlog with the new information." It is **the smallest set of edits that makes the
backlog correct, with the structure held still.**

Invoke when a change has landed: a new requirement, a dropped one, a changed design, a
budget cut, a pivot, or a codebase that has moved on since the stories were written.

## The structural contract

These hold unless the user explicitly releases them. State the violation and ask before
breaking any of them.

- **Story numbers are stable identifiers.** Never renumber to close a gap. A removed
  story keeps its number and gets marked, it does not vacate the slot. New stories take
  the next free number in the epic, even if that puts them out of logical order.
- **Epic assignment is sticky.** Moving a story between epics changes its number and
  breaks every reference to it. Propose it only when the story is genuinely misfiled,
  and never as a tidying pass.
- **Sections stay in template order**, with the same heading levels, whether or not the
  story existed before the template gained a section.
- **A revision touches the sections that changed.** Rewriting a whole story because one
  acceptance criterion moved destroys the reviewer's ability to see what actually
  changed, and quietly reintroduces wording the user already corrected.
- **Cross-references are bidirectional.** Every edit to a Related stories line implies
  an edit to the story on the other end.

## The workflow

### Phase 1 — Load current state

Read the whole backlog before touching any of it: every per-epic file, the epic indexes,
and any prior revision log. Also read whatever carries the change — a new requirements
doc, meeting notes, an email, an updated design, or just the user's description of it in
chat.

Then produce, in chat, a short inventory:

- What the backlog currently covers, epic by epic.
- What the change is, in the user's terms.
- Anything in the change that the backlog already covers (drift is often partial).

Do not edit anything. Do not propose edits yet.

### Phase 2 — Impact assessment

This is the phase that earns the skill. Work out what the change actually touches and
present it as a table or numbered list, one row per affected story:

| Story | Impact | What changes |
|---|---|---|
| 2.3 | Amend | Acceptance criteria 2 and 4; description unaffected |
| 2.7 | Obsolete | Superseded by the new export flow |
| 3.1 | Reference only | Related-stories line points at 2.7 |
| — | New | Story 3.6, covering the new bulk-upload requirement |

Classify each impact as one of:

- **Amend** — the story survives, some sections change.
- **Split** — the story now covers two separable pieces of work.
- **Absorb** — the story takes on work that was previously elsewhere, or previously new.
- **Obsolete** — the work is no longer wanted, or is now covered by another story.
- **New** — genuinely new work with no existing home.
- **Reference only** — the story itself is untouched; only its cross-references move.

Include the stories you considered and ruled out, with one line on why. A change the
user thought was local often is not, and the ruled-out list is where they catch it.

Iterate on this assessment until the user accepts it. **Nothing is written to a file
during this phase.**

### Phase 3 — Verify against the code

Before drafting revisions, re-check the concrete claims in every affected story against
the current codebase: file paths, type and function names, test names and their states,
"today X is broken" claims. Requirements drift and code drift usually arrive together,
and a revision round is the natural moment to catch both.

Report stale references separately from the requirements change. They are a different
kind of edit and the user may want to handle them on a different timeline.

### Phase 4 — Draft revisions in chat

One story at a time, in numbered order. For each, show only the sections that change,
in this form:

    Story 2.3 — Acceptance Criteria

    - was: <old bullet>
    - now: <new bullet>
    - added: <new bullet>
    - removed: <old bullet> (now covered by Story 3.6)

For a new story, draft the full body using the same template the backlog already uses
(`../story-backlog/templates/story.md` if the user has no example of their own), and match
the voice of the surrounding stories.

Then ask: *"Settled, or corrections before I apply?"* Apply to the file only on explicit
approval. Same rule as story-backlog — corrections happen on the chat version.

Keep the **Description** free of implementation detail and put code references in
**Technical details**. If a revision lands on an older story written before that split
existed, fix it as part of the same edit rather than leaving the story half-converted.

### Phase 5 — Handle removals honestly

Do not silently delete a story that developers have already read. Default to marking it:

    ## Story 2.7 — Manual CSV export ~~(withdrawn)~~

    **Status:** Withdrawn 2026-08-18 — superseded by Story 3.6 (bulk export). Kept for
    reference; do not implement.

Then strike the body or leave it intact beneath the status line, per the user's
preference. Hard-delete only when the user asks for it, and say which numbers went dark
so nobody reads the gap as an omission.

If a story was in flight or already done when it was withdrawn, say so in the status
line — that is the part the team most needs to know.

### Phase 6 — Reconcile cross-references

A required sweep after every revision round, not an optional one. Check:

- Every `Depends on` / `Blocks` pair is still an inverse (A blocks B ⟺ B depends on A).
- No live story points at a withdrawn one without acknowledging it.
- The epic index at the top of each file matches the bodies below — new stories listed,
  withdrawn ones marked, one-line summaries still accurate after amendment.
- `Not in scope` bullets that pointed at a story still point somewhere real.
- Descriptions haven't drifted technical during editing.

Present findings numbered, with proposed fixes, and apply on approval.

### Phase 7 — Revision log

Append to a `REVISIONS.md` at the backlog root (create it if absent). One entry per
round:

    ## 2026-08-18 — Bulk export replaces manual CSV

    **Trigger:** Client requirements call, 2026-08-17.

    - Story 2.3 — amended acceptance criteria for the new file-size ceiling
    - Story 2.7 — withdrawn, superseded by 3.6
    - Story 3.6 — new, bulk export flow
    - Stories 3.1, 4.2 — cross-references updated

This is what makes the next revision round cheap, and it is what a developer reads when
a story they had already scoped changes under them.

### Phase 8 — Mirror to the tracker

Only if the backlog is mirrored into a work tracker via MCP. The markdown stays the
source of truth; the tracker follows it.

- Confirm which items map to which stories before writing anything — match on the
  `Story N.M` prefix in the title.
- Apply the same edits: amended fields, new items for new stories, and the tracker's own
  closed/removed state for withdrawn ones. Do not delete tracker items — close them with
  the reason, so history survives.
- Report anything you could not match to a tracker item rather than creating a duplicate.

## Standing rules

- **Smallest correct edit.** Every change should be traceable to something in the
  accepted impact assessment. If you find yourself improving wording that the change
  didn't touch, stop and raise it separately.
- **Read the code, don't fabricate.** Same rule as story-backlog, and it applies with
  more force here — the story you are editing was verified against a codebase that has
  since moved.
- **Draft in chat, write on approval.**
- **Numbers are load-bearing.** Renumbering is a breaking change to every reference, in
  the files and in everyone's head. It needs explicit permission.
- **Say what you couldn't reconcile.** If part of the change has no clean home in the
  existing structure, surface that rather than forcing it into the nearest story. That
  tension is usually a real signal about the shape of the work.
- **Distinguish requirements drift from code drift.** Both produce edits; they have
  different causes and the user may want them handled separately.
- **Preserve the established voice.** Match the wording conventions already in the
  backlog — ownership framing, status markers, the bug-escalation policy — rather than
  reverting to defaults.

## When to suggest a revision round

- The user describes a change to requirements while a backlog exists.
- A design lands for work that was written as a placeholder.
- The code has moved enough that "today X" claims are suspect.
- A story is split, merged, or dropped during planning or standup.
- More than one revision has been applied ad hoc without a coherence sweep.
