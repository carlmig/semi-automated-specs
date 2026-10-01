---
name: vision
description: Helps clarify the user's initial idea and produces/updates docs/vision.md. Use this skill right after project-init, or whenever the user wants to revisit/change the product vision ("actually I want to shift focus", "let's revisit the vision"). Asks questions to remove ambiguity before writing. The vision only moves to APPROVED when the user explicitly says so.
---

# vision

Turns a vague idea into `docs/vision.md`, through targeted questions — it
does not write the vision "in one shot" from a single sentence.

## When to run

- After `project-init`, when the user doesn't have a `vision.md` yet or it
  is still `DRAFT`.
- Whenever the user wants to revisit the vision of a project already in
  progress. In this case, warn that changing the vision may impact
  existing milestones/architecture/tasks and suggest running
  `project-review` after the vision is updated.

## Process

1. **Clarifying questions** — ask whatever is needed to remove essential
   ambiguity, for example:
   - What problem does this solve, and for whom?
   - What is the success criterion (what changes in the world if this
     works)?
   - Are there any known constraints (deadline, budget, mandatory stack,
     mandatory integrations)?
   - What is explicitly **out of** scope?

   Don't ask everything at once if that would be overwhelming — it can be
   done in two or three short rounds. Track what is already a `Fact` (what
   the user already said) versus what remains an `Open Question`.

2. **Draft** — write `docs/vision.md` with this minimal structure:
   ```markdown
   # Vision

   Status: DRAFT

   ## Problem
   ...

   ## For whom
   ...

   ## Value proposition
   ...

   ## Success criteria
   ...

   ## Out of scope
   ...

   ## Known constraints
   ...

   ## Open Questions
   - ...
   ```
   Clearly mark any sections that are a `Proposal` from the AI (e.g., a
   rephrasing of the problem) versus `Decision`/`Fact` coming directly
   from the user.

3. **Review** — show the draft to the user and explicitly ask whether they
   want to change anything before approving.

4. **Approval** — only change `Status: DRAFT` to `Status: APPROVED` when
   the user says so unambiguously (e.g., "approved", "looks good", "go
   ahead"). Never assume implicit approval just because the user didn't
   comment.

## After approval

Update `docs/project-state.md` (via the `project-state` skill's logic) and
suggest the natural next step: the `milestones` skill.

## Rules

- Don't invent success metrics, personas, or constraints the user didn't
  mention — mark them as `Open Question` instead of filling them in.
- If the user changes their mind about something already `APPROVED`,
  record the change and recommend `project-review` to map the impact.
