---
name: milestones
description: From an APPROVED Vision, proposes and maintains docs/milestones.md — an ordered list of milestones (M001, M002, ...). Use this skill when the user wants to plan the major delivery phases of the product, or says "what are the milestones?", "let's break this into phases". Milestones only become APPROVED once the user has been free to change, remove, merge, split, reorder, and re-prioritize them.
---

# milestones

Translates the approved Vision into a set of high-level milestones — not
tasks, not architecture, just the major delivery phases.

## Precondition

Confirm via `project-state` that `docs/vision.md` is `APPROVED`. If not,
stop and recommend finishing the `vision` skill first.

## Process

1. **Propose** — from the vision, propose an initial list of milestones,
   each as a `Proposal`:
   ```
   M001 — <short name>
   M002 — <short name>
   M003 — <short name>
   ```
   Each milestone should have a clear, verifiable goal (what becomes
   possible to do/verify once the milestone is complete).

2. **Explain the reasoning** — briefly say why you split it this way
   (e.g., by delivered value, by technical dependency, by risk).

3. **Negotiate** — the user can:
   - change the name/goal of a milestone;
   - remove a milestone;
   - merge two milestones;
   - split a milestone into several;
   - reorder;
   - re-prioritize.

   Apply the requested changes one by one and show the updated list again.
   Don't move to final write-up without confirmation.

4. **Save** — write/update `docs/milestones.md`:
   ```markdown
   # Milestones

   Status: DRAFT

   ## M001 — <name>
   Goal: ...
   Completion criterion: ...

   ## M002 — <name>
   ...
   ```

5. **Approval** — only mark `Status: APPROVED` once the user explicitly
   confirms they're satisfied with the full list.

## Rules

- Don't propose more than what's needed to cover the vision — avoid
  padding the list with speculative milestones.
- Each milestone should be large enough to have value on its own, but
  small enough to be plannable into concrete tasks in Phase 4.
- If a decision about architecture surfaces during negotiation (e.g.,
  "M002 only makes sense if we use X"), don't resolve it here — record it
  as an `Open Question` to bring into the Architecture phase.

## After approval

Update `docs/project-state.md` and suggest the next step: the
`architecture` skill.
