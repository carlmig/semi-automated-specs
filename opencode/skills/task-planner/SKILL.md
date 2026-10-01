---
name: task-planner
description: Turns the approved architecture into a set of tasks organized by milestone in tasks/<milestone>/<task>.md. Use this skill when the user wants to "break this into tasks", plan the work for a specific milestone, or when a milestone is ready to start. Each task created here is still a skeleton — the detailed specification is handled by the task-refinement skill, at a depth that depends on the implementation mode.
---

# task-planner

Splits an approved milestone into a list of concrete tasks, without yet
writing the full specification for each one (that's `task-refinement`).

## Precondition

Via `project-state`: Architecture `APPROVED` (at least in the sections
relevant to the milestone in question) and the target milestone already
`APPROVED` in `milestones.md`.

## Process

1. **Choose the milestone** — if the user doesn't specify one, ask which
   milestone to plan next (usually the first one without tasks yet).

2. **Propose the task list** — from the milestone's goal and the relevant
   architecture, propose an ordered list, typically by technical
   dependency:
   ```
   T001 — <short name>
   T002 — <short name>
   T003 — <short name>
   ```
   Each task should be small enough to be implemented and verified
   independently (ideally: one PR, one review cycle).

3. **Negotiate** — as in `milestones`, the user can merge, split, reorder,
   remove, or add tasks. Apply the adjustments and show the list again.

4. **Create the skeleton files** — for each approved task in this list,
   create `tasks/<milestone>/<task>.md`:
   ```markdown
   ---
   id: T001
   milestone: M001
   status: DRAFT
   implementation:
     mode: ai          # or: manual
     manual_style: support   # only relevant if mode: manual — support | learning
   ---

   # T001 — <name>

   ## Objective
   <one sentence>

   ## Specification
   (to be filled in — see task-refinement)

   ## Acceptance Criteria
   (to be filled in — see task-refinement)
   ```

5. **Ask for the implementation mode** — for each task (or in bulk, if it
   makes sense), ask whether the mode is `ai` or `manual`. Don't assume a
   default — it's the user's decision. If it's `manual`, also ask for
   `manual_style`:
   - `support` — the AI can suggest solutions and point directly at fixes
     (default, if the user doesn't specify);
   - `learning` — the user wants to think/code it themselves; the AI only
     answers what is asked and avoids proposing solutions (see details in
     `.opencode/skills/implement-task/SKILL.md`).

## Rules

- Don't write the detailed specification or the acceptance criteria — that
  stays marked as pending and is the job of `task-refinement`.
- If, while planning, you realize an architecture decision is missing for
  a task to make sense, stop and recommend `architecture-decision` before
  continuing that specific task (the rest can proceed).
- Keep the task small: if the acceptance criteria list is getting very
  long, suggest splitting into two tasks.

## After creation

Update the task count in `docs/project-state.md` (status `DRAFT` for all)
and suggest the next step: `task-refinement` for the first task in the
list.
