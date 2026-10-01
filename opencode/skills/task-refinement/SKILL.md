---
name: task-refinement
description: Refines a skeleton task (created by task-planner) until it has a specification and acceptance criteria that are clear enough for its implementation mode. For implementation.mode ai, the spec is detailed enough to implement without guessing. For mode manual, the spec stays high-level (objective, requirements, constraints, required interfaces) and does not prescribe the implementation approach, since verification will check requirements met, not how they were met. Use this skill when the user wants to flesh out a specific task before implementing it, or says "let's spec out T003", "this task isn't ready yet". Only mark a task READY once its specification is clear enough for its mode — never before.
---

# task-refinement

Takes a task in `DRAFT` state and works it up to `READY`. How detailed the
specification needs to be depends on `implementation.mode`.

## Precondition

The target task must already exist in `tasks/<milestone>/<task>.md`,
created by `task-planner`.

## Process

1. **Read context** — read the task, the milestone it belongs to, and the
   relevant sections of `architecture.md` (and associated ADRs, if any).

2. **Write the specification** — the level of detail depends on
   `implementation.mode`:

   - **`mode: ai`** — write enough detail that someone (human or AI) could
     implement it without having to guess design decisions. Include, as
     relevant:
     - expected inputs/outputs;
     - behavior on error cases;
     - affected interfaces/contracts (endpoints, schemas, events);
     - references to relevant ADRs.

   - **`mode: manual`** (any `manual_style`) — keep the specification
     high-level, regardless of `manual_style`. Include only:
     - the objective and functional requirements (what the task must
       accomplish, in terms of behavior/outcome);
     - constraints that come from the architecture or ADRs (e.g., "must
       use the REST endpoint defined in ADR-004", "must persist to the
       table defined in the Data Architecture section");
     - interfaces/contracts that other parts of the system depend on and
       that must therefore be respected (function signatures other code
       calls, an API shape another team consumes, a data schema another
       component reads);
     - edge cases that matter for correctness, stated as required behavior
       ("must handle an empty list without crashing"), not as
       implementation guidance.

     Do **not** include step-by-step implementation instructions, a
     specific algorithm or data structure to use, or a suggested code
     structure — that decision is left to the developer. If you find
     yourself writing "first do X, then do Y, using Z pattern", that
     belongs in an `ai` task, not here.

3. **Write Acceptance Criteria** — verifiable criteria, preferably in
   Given/When/Then form or a list of objective checks:
   ```markdown
   ## Acceptance Criteria
   - [ ] Given ..., when ..., then ...
   - [ ] ...
   ```
   For `mode: manual`, phrase every criterion as an observable
   behavior/output (what the system does), never as "implemented using
   approach X" — the acceptance criteria must be satisfiable by more than
   one valid implementation.

4. **Identify ambiguity** — if, while writing the specification, something
   comes up that isn't defined in the vision, the milestones, or the
   architecture, **don't invent it** — record it as an `Open Question` on
   the task and ask the user before marking the task ready.

5. **Confirm the implementation mode** — revalidate `implementation.mode`
   in the frontmatter (`ai` or `manual`); fix it if the user wants to
   change it. If `mode: manual`, also confirm
   `implementation.manual_style` (`support` or `learning`) — don't assume
   it stays whatever `task-planner` set if the context has changed.

6. **Mark as READY** — only once the specification and acceptance criteria
   are complete for that mode's expected depth and there is no unresolved
   `Open Question`, change `status: DRAFT` to `status: READY` in the
   task's frontmatter.

## Rules

- An `ai` task is only `READY` when someone who wasn't part of the
  conversation could implement it from the file alone.
- A `manual` task is only `READY` when the objective, requirements, and
  constraints are unambiguous — even though the *how* is intentionally
  left open.
- Don't add vague acceptance criteria ("should work well") — every
  criterion must be objectively verifiable (by test, by inspection, or by
  observable behavior), regardless of mode.
- If the task is too large for a coherent specification, suggest splitting
  it and go back to `task-planner` for that split.

## After READY

If `implementation.mode: ai`, suggest moving on to `implement-task`. If
`implementation.mode: manual`, let the user know the task is ready for
manual implementation and that you remain available — in whichever style
`manual_style` defines (`support`: clarify the spec, suggest solutions, do
code review, and verify acceptance criteria; `learning`: answer specific
questions without proposing the solution, so they can think it through
themselves).
