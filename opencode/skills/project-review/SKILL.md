---
name: project-review
description: Cross-cutting skill for when a change in user intent ("actually I don't want Kafka anymore", "let's switch frameworks") could invalidate decisions already made. Use this skill before directly editing any artifact when the request contradicts something already APPROVED in vision, milestones, architecture, ADRs, or tasks. Maps the impact (Vision → Milestones → Architecture → ADRs → Tasks) and presents the proposed changes for approval — never edits the files directly.
---

# project-review

The skill that stops the AI from "just editing everything" when the user
changes their mind about something already decided.

## When to run

Whenever a user request directly or indirectly contradicts something
already `APPROVED`/`Accepted`:

- A decision recorded in an ADR (e.g., "actually I don't want Kafka
  anymore").
- An already-approved architecture section.
- An already-approved milestone (e.g., a scope change that invalidates
  it).
- The vision itself (e.g., a change in target audience or value
  proposition).

If the request doesn't affect anything `APPROVED` (e.g., it's about a task
still in `DRAFT`), `project-review` isn't needed — the normal phase skill
handles it directly.

## Process

1. **Identify what changed** — summarize in one sentence the change
   requested by the user, as they phrased it (`Fact`).

2. **Trace the impact** — walk the chain of artifacts, top to bottom,
   checking real dependencies (not speculative ones):
   ```
   What changed?
        ↓
   Which decisions are affected? (ADRs)
        ↓
   Which parts of the architecture?
        ↓
   Which milestones?
        ↓
   Which tasks?
   ```
   At each level, list only what is actually affected — don't include
   artifacts "just in case" without a clear link.

3. **Present the impact report**, formatted as:
   ```markdown
   ## Impact: <LOCAL | DOWNSTREAM>

   ### Affected
   - ADR-003 — <why>
   - architecture.md § Integration — <why>
   - M004 — <why>
   - T021, T022 — <why>

   ### Proposed changes
   1. ADR-003 → Superseded by ADR-0XX (<proposed new decision>)
   2. architecture.md § Integration → <proposed change>
   3. M004 → <proposed change: keep / adjust / remove>
   4. T021, T022 → <proposed change: keep / re-specify / cancel>
   ```
   Classify the impact as `LOCAL` (affects only an isolated artifact, no
   propagation) or `DOWNSTREAM` (propagates across several levels).

4. **Wait for a decision** — the user decides, artifact by artifact (or in
   bulk), what to accept. Nothing changes before this.

5. **Apply** — only after explicit approval:
   - Affected ADRs → mark `Superseded by ADR-0XX` and create the new ADR
     via `architecture-decision`.
   - Architecture sections → revert to `DRAFT` and reopen via
     `architecture`.
   - Milestones → reopen via `milestones` (don't edit `milestones.md`
     directly here).
   - Tasks → `task-planner`/`task-refinement` to re-specify, or mark as
     cancelled if it no longer makes sense.
   `project-review` coordinates the mapping and the approval, but delegates
   the actual editing of each artifact to the skill that owns it.

## Rules

- Never edits `vision.md`, `architecture.md`, `milestones.md`, ADRs, or
  tasks directly — its output is always an impact report and, once
  approved, a list of calls to the right skills.
- Don't overstate the blast radius: if a change is genuinely local, say so
  and don't drag unrelated artifacts into the report.
- Update `docs/project-state.md` to reflect any artifacts that revert to
  `DRAFT` because of the review.
