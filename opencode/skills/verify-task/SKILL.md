---
name: verify-task
description: Verifies a task in VERIFY state (implemented by AI or manually) against build, tests, acceptance criteria, specification, architecture, and security. Use this skill when the user says "verify T003", "do you think this is ready?", or after implement-task finishes. For manual tasks, checks that requirements are met, not the way they were implemented. If something fails, marks the task as FAIL and explains what to fix — with the level of detail depending on manual_style — without auto-fixing code on manual tasks.
---

# verify-task

Verifying a task is never just "do the tests pass?". It covers six
dimensions, and its behavior depends on the task's `implementation.mode`
and, for manual tasks, `manual_style`.

## Precondition

The task exists and is `status: VERIFY` (implementation, AI or manual, is
already finished).

## Verification dimensions

For every task, go through and explicitly report each of these — don't
skip any even if it seems obvious:

1. **Build** — the project compiles/builds without errors.
2. **Tests** — existing tests pass; if the task was supposed to include new
   tests, confirm they exist and cover the acceptance criteria.
3. **Acceptance Criteria** — go through each item in
   `## Acceptance Criteria` in the task and explicitly mark it as
   met/not met.
4. **Specification** — the implemented behavior matches what
   `## Specification` describes. For a `mode: manual` task, this means
   checking that the required behavior, functional requirements, and any
   mandated interfaces/contracts are satisfied — **not** evaluating the
   internal approach (algorithm, data structure, code organization,
   design pattern) chosen by the developer. A different but valid way of
   meeting the same requirement is not a failure on this dimension.
5. **Architecture** — the implementation respects `architecture.md` and
   relevant ADRs (e.g., uses the decided communication pattern, doesn't
   introduce an unapproved dependency). This dimension still applies in
   full to `manual` tasks — architecture/ADR constraints are requirements,
   not implementation style — but stays scoped to those constraints, not
   to a preference about how the code is organized internally.
6. **Security** — a basic check for obvious bad practices (secrets in
   code, missing input validation, excessive permissions) relevant to the
   task's scope.

## Flow

```
VERIFY
  ↓
FAIL  (if any dimension fails)
  ↓
FIX   (implement-task, if mode=ai; developer, if mode=manual)
  ↓
VERIFY  (repeat)
```

If every dimension passes:

```
VERIFY
  ↓
DONE
```

## Process

1. Go through the six dimensions one by one, reporting the result per
   dimension (not just a single overall verdict).
2. If **everything passes**: change `status: VERIFY` to `status: DONE` in
   the task's frontmatter and update `docs/project-state.md`.
3. If **something fails**: change `status` to `status: FAIL`, and write a
   `## Verification — Fail Report` section on the task with:
   - the dimension(s) that failed;
   - exactly what is wrong;
   - what needs to change to pass (without rewriting the spec on your own
     — if the problem is that the spec itself is poorly defined, flag that
     instead of silently "fixing" it).

   **If the task is `mode: manual` with `manual_style: learning`**, the
   fail report should not give the fix directly: describe the symptom and
   the affected dimension (e.g., "Acceptance Criteria #2 fails: the output
   for an empty input doesn't match what's expected") without pointing at
   the cause or the fix, so the user keeps thinking the problem through.
   Only give the direct fix if the user explicitly asks for it. For
   `manual_style: support` (or `mode: ai`), the fail report is always
   actionable and direct, as described above.
4. **For `mode: manual` tasks**: never fix the code directly — report the
   fail report to the developer and stay available to clarify, suggest,
   and re-verify when they ask (always respecting `manual_style`, as in
   `implement-task`).
5. **For `mode: ai` tasks**: you can invoke `implement-task` to apply the
   fix, then come back to `verify-task`.

## Rules

- Never mark `DONE` with unchecked dimensions — if a dimension doesn't
  apply (e.g., a task with no security impact), say so explicitly
  ("Security: not applicable — reason"), don't silently omit it.
- The fail report must be actionable — avoid vague verdicts like "tests
  are missing"; say which tests, for which behavior (except in
  `manual_style: learning`, where the point is to describe the symptom,
  not the fix).
