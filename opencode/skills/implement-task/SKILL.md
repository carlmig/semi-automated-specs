---
name: implement-task
description: Implements the code for a task with status READY and implementation.mode = ai. Use this skill when the user asks to implement a specific task or says "implement T003". Never modifies code for a task with mode = manual — in that case behavior depends on implementation.manual_style (support or learning); in learning mode, the AI only answers what's asked and avoids proposing the solution, so the user thinks it through themselves.
---

# implement-task

Implements a concrete task, following exactly the already-refined
specification — this is not the place to make new design decisions not
covered by the spec.

## Precondition

1. The task exists and is `status: READY`. If it's `DRAFT`, redirect to
   `task-refinement` first.
2. `implementation.mode` in the task's frontmatter:
   - **`ai`** → follow the "Process" below, implement code normally.
   - **`manual`** → **stop immediately — do not write or change any code
     for this task.** What else you can still do depends on
     `implementation.manual_style` (see below).

## Manual mode — `implementation.manual_style`

Only relevant when `mode: manual`. Default: `support`.

### `support` (default behavior)

The goal is to help the user move forward. You can:
- clarify the spec;
- analyze existing code;
- **suggest solutions**, in text or pseudocode;
- run tests;
- do code review, pointing directly at what is wrong and how to fix it;
- verify acceptance criteria.

### `learning`

The explicit goal is for the user to think through the problem and learn
by coding — not to hand them the solution. You can:
- answer directly what is asked: concepts, how a specific API/library
  behaves, syntax, why a specific error happens;
- run tests and report the result as-is (without interpreting the cause or
  suggesting a fix, unless asked);
- ask questions that help the user get there themselves, instead of
  pointing the way.

You should not, on your own initiative:
- propose the solution to the problem or the task;
- write pseudocode or code that solves what the user is trying to solve;
- in a code review, say directly "this is wrong and here's how to fix it"
  — instead, point at the symptom/area of the code and let the user
  identify the cause.

If you sense you're about to hand over the solution without it being
asked for, **stop and ask first**: "would you like me to suggest an
approach, or would you rather keep thinking this through?" Only after
explicit confirmation should you behave like `support` for that specific
question — this does not change the task's `manual_style`, it only applies
to that one answer.

## Process (only for `mode: ai`)

1. **Re-read the spec** — specification and acceptance criteria of the
   task, plus relevant ADRs/architecture.
2. **Implement** — write the code needed to satisfy the specification. If,
   partway through, you find something the spec doesn't cover and that
   requires a non-trivial design decision, **stop** and treat it as an
   `Open Question` — don't decide silently and keep going. Small, obvious
   cases can be resolved and then reported as an explicit `Assumption` in
   the final summary.
3. **Update the task's state** — change `status: READY` to
   `status: IN PROGRESS` at the start, and to `status: VERIFY` once the
   implementation is complete and ready to be validated.
4. **Summarize what was done** — at the end, summarize: files changed,
   implementation decisions made (marked as `Fact`, since they follow
   directly from the spec), and any `Assumption`/`Open Question` that came
   up.

## Rules

- Don't change the task's scope or "take the opportunity to improve"
  unrelated code — that's a new task, to discuss with `task-planner`.
- Don't move the task to `DONE` — that state is only assigned by
  `verify-task` once verification passes.
- If the implementation requires a new architectural decision (e.g.,
  choosing a library not foreseen), treat it with the same rigor as
  `architecture-decision`: present options and trade-offs, ask, only then
  decide.

## After implementation

Suggest the next step: `verify-task` for that same task.
