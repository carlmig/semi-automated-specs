---
name: project-state
description: Reads and maintains docs/project-state.md, the project's "dashboard" — which phase is active, the state of each artifact (vision, milestones, architecture, tasks), and when it was last updated. Use this skill whenever the user asks "where does the project stand?", "what's left to do?", or at the start of any phase skill to confirm prerequisites before proceeding (e.g., don't start milestones without an approved vision).
---

# project-state

The source of truth about "where we are". It does not make product
decisions — it only reads, summarizes, and updates state.

## When to run

- At the start of any work session, if the user asks about progress.
- As an internal precondition of other skills, before they move to the
  next phase (e.g., `milestones` checks here whether `vision.md` is
  `APPROVED`).
- After any artifact changes state (approval, task completed, decision
  recorded), to keep `docs/project-state.md` in sync.

## Structure of `docs/project-state.md`

```markdown
# Project State

## Current phase
<VISION | MILESTONES | ARCHITECTURE | TASKS | IMPLEMENTATION | VERIFICATION>

## Artifacts
- Vision: <does not exist | DRAFT | APPROVED>
- Milestones: <does not exist | DRAFT | APPROVED>
- Architecture: <does not exist | DRAFT | APPROVED (partial: list of sections) | APPROVED>
- ADRs: <list of ADR-XXX and their status>
- Tasks:
  - Total: N
  - READY: n
  - IN PROGRESS: n
  - VERIFY: n
  - FAIL: n
  - DONE: n

## Blockers / active Open Questions
- ...

## Last updated
<ISO date>
```

## Reading rules (phase preconditions)

Before another skill moves to the next phase, `project-state` confirms:

| To move to        | Requires                                                       |
|---------------------|-------------------------------------------------------------------|
| Milestones          | Vision = APPROVED                                                  |
| Architecture         | Milestones = APPROVED                                              |
| Tasks/Specs           | Architecture = APPROVED (at least the relevant sections)           |
| Implementation        | Task = READY and the spec is clear enough                          |
| Verification            | Implementation completed (AI or manual)                            |

If the precondition isn't met, the skill that called `project-state` must
stop and explain to the user what's missing, instead of proceeding
anyway. This is the mechanism that prevents accidentally "skipping
phases".

## Writing rules

- Update only the fields that changed — don't rewrite sections
  unnecessarily.
- Always record the date of the update.
- Never mark something as `APPROVED` on its own — that only happens when
  the skill for the relevant phase (vision, milestones, architecture)
  confirms the user explicitly approved it.
