---
name: architecture
description: From an APPROVED Vision + Milestones, builds and maintains docs/architecture.md (System Context, Container, Component, Data, Integration, Security, Observability) and the C4 diagrams in docs/diagrams/. Use this skill when the user wants to design/discuss the technical architecture, or says "how are we going to build this?", "let's design the architecture". Each section has an Owner (ai or user) — when Owner is user, the user writes the raw content and this skill reviews, questions (both trade-offs and small inconsistencies), structures the text, and generates the diagrams, iterating until there are no Open Questions. Significant architecture decisions must go through the architecture-decision skill, never decided alone.
---

# architecture

Builds `docs/architecture.md` from the already-approved Vision and
Milestones. Each section has an `Owner`, which determines who drives that
section's content — the AI (`Owner: ai`, default behavior) or the user
(`Owner: user`, for when they want to think through a section themselves
and use the AI only to review, question, and structure).

## Precondition

Via `project-state`: Vision `APPROVED` and Milestones `APPROVED`. Without
this, stop and explain what's missing.

## Structure of `docs/architecture.md`

```markdown
# Architecture

Status: DRAFT

## System Context
Owner: ai
Status: DRAFT
...

## Container Architecture
Owner: ai
Status: DRAFT
...

## Component Architecture
Owner: ai
Status: DRAFT
...

## Data Architecture
Owner: user
Status: DRAFT
...

## Integration
Owner: ai
Status: DRAFT
...

## Security
Owner: ai
Status: DRAFT
...

## Observability
Owner: ai
Status: DRAFT
...

## Diagrams
- docs/diagrams/context.mmd
- docs/diagrams/containers.mmd
```

`Owner` defaults to `ai` for a new section — it only changes to `user`
when the user explicitly asks for that section.

## Process — `Owner: ai` sections

1. **Analyze** — read `vision.md` and `milestones.md` to identify
   requirements that constrain the architecture (expected scale, required
   integrations, known constraints). Treat all of this as `Fact` if it's
   already written in those documents.

2. **Identify significant decisions** — whenever there's a choice with
   real trade-offs (e.g., database choice, messaging, authentication,
   mono-repo vs multi-repo, sync vs async):
   - **don't decide alone**;
   - invoke the `architecture-decision` skill's flow for that specific
     decision before writing it into `architecture.md` as final.

3. **Write uncontroversial sections directly** — things that follow
   directly from already-established facts (e.g., "there's a web frontend
   and an API" when that's already implied by the vision) can be written
   as low-risk `Fact`/`Proposal`, but still show the draft to the user
   before approving the section.

4. **C4 diagrams** — generate/update the `.mmd` files as described below
   under "Diagrams".

5. **Approval** — show the draft and only mark `Status: APPROVED` on the
   section once the user confirms.

## Process — `Owner: user` sections (raw input)

Here the user writes the content — the AI never replaces the substance of
what they decided, it only works on top of it.

1. **Receive the raw input** — the user pastes/writes unstructured text
   (loose notes, bullets, stream of thought) for the section. Keep it as
   is, unedited, as a reference for what was actually said.

2. **Review and question** — before structuring anything, the AI analyzes
   the raw input at two levels, and reports both:
   - **Real trade-offs** — choices with viable alternatives and relevant
     consequences (e.g., a data model that implies a specific access
     pattern, an implicit technology choice). These follow the same rigor
     as always: present options, don't assume.
   - **Small inconsistencies** — and these matter just as much as the big
     trade-offs, not a "nice to have":
     - inconsistent naming (the same entity called different things in
       different parts of the text, or a name that differs from what's
       used in another already-`APPROVED` section);
     - trivial contradictions within the text itself;
     - references to something that isn't defined anywhere (e.g.,
       mentions a field/relationship that doesn't appear in the entity's
       definition);
     - obvious gaps the text itself hints at but doesn't close (e.g.,
       defines an "Order" entity with states but doesn't say what/who
       transitions between them).

   List all of this as `Open Question`, tied to the exact point in the raw
   text it refers to — don't lump it into a generic list at the end.

3. **Structure** — only after raising the questions, organize the raw
   input into the section's format (headings, lists, tables as appropriate
   for the type of content). Structuring means **reorganizing and shaping**,
   never inventing new content or resolving the ambiguities flagged in the
   previous step on your own.

4. **Generate/update the diagram** — only once the text is at least
   minimally structured (so as not to draw on top of something that's
   still going to change). See "Diagrams" below.

5. **Present together** — structured text + diagram + list of Open
   Questions, all in the same response, so the user can respond to
   everything at once or address it piece by piece.

6. **Iterate** — the user answers the questions, adjusts the raw text or
   the structure. The AI repeats steps 2-5 on the updated version. While
   any `Open Question` remains, the section stays:
   ```
   Status: DRAFT (under discussion)
   ```

7. **Close decisions that emerged from the discussion** — if any question
   raised in step 2 turns out, over the course of the discussion, to be a
   decision with real trade-offs (not a mere naming fix), record it
   formally via `architecture-decision` before the section can become
   `APPROVED` — even if the "decision" seems obvious after the
   conversation.

8. **Approval** — only once there is no remaining `Open Question` and the
   user explicitly confirms, change to `Status: APPROVED`.

## C4 Diagrams

Generate/update the `.mmd` (Mermaid) files in `docs/diagrams/`:
- `context.mmd` — C4 level 1 (System Context)
- `containers.mmd` — C4 level 2 (Container)

Add additional component diagrams only when a container is complex enough
to justify one. For `Owner: user` sections (e.g., Data Architecture), the
generated diagram must reflect exactly the entities/relationships as the
user defined them — don't add entities or relationships "to complete" the
model without asking first.

## Rules

- Every decision with trade-offs goes through `architecture-decision` and
  produces an ADR in `docs/decisions/` — regardless of whether the section
  is `Owner: ai` or `Owner: user`.
- Never pick a technology "because it's the most common" without
  presenting alternatives and the reasoning — even if the choice seems
  obvious.
- If a section of the architecture contradicts something already approved
  in the vision, milestones, or another already-`APPROVED` section, stop
  and flag the conflict instead of resolving it silently — this applies
  equally to `Owner: ai` and `Owner: user` sections.
- In an `Owner: user` section, the AI never silently replaces a user's
  sentence with a "better" one — if it thinks a phrasing is wrong or could
  be improved, that's a question/suggestion, not a silent edit.

## After approval

Once all sections relevant to the next milestone are `APPROVED`, update
`docs/project-state.md` and suggest the next step: `task-planner`.
