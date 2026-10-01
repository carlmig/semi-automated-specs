# AGENTS.md — AI-assisted development workflow

This project uses a set of OpenCode skills that implement a workflow
inspired by Spec-Driven Development, but **non-linear** and with the
developer always in control of important decisions.

## Global flow

```
IDEA
  ↓
VISION
  ↓
MILESTONES
  ↓
ARCHITECTURE + ADRs + C4
  ↓
TASKS + SPECS
  ↓
IMPLEMENTATION
  ↓
VERIFICATION
  ↓
DONE
```

Any artifact can be revisited at any point (see the `project-review`
skill). This is not a pipeline you move through once and never come back
to — it is a state graph that feeds back into itself.

## Core principle

**The AI must never assume important decisions.**

In any response, analysis, or document produced by a skill in this set,
every statement should be labeled as one of the following:

| Type             | Meaning                                                   |
|------------------|------------------------------------------------------------|
| `Fact`           | Known, verifiable information (code, docs, tests)          |
| `Decision`       | An explicit decision already made by the user              |
| `Assumption`     | A hypothesis not yet confirmed by the user                 |
| `Proposal`       | A suggestion from the AI, not yet decided                  |
| `Open Question`  | A question that needs the user's answer before proceeding  |

Whenever a decision has significant impact (architecture, dependencies,
scope, real trade-offs), the skill must always follow this sequence:

```
identify options
       ↓
explain trade-offs
       ↓
ask the user
       ↓
record the decision
```

Never "decide alone and move on" when the impact is significant. When in
doubt about whether something is "significant," treat it as significant.

## Artifact states

- `docs/vision.md` — states: `DRAFT` → `APPROVED`
- `docs/milestones.md` — states: `DRAFT` → `APPROVED`
- `docs/architecture.md` — states: `DRAFT` → `APPROVED` (per section, where needed)
- Tasks (`tasks/<milestone>/<task>.md`) — states: `READY` → `IN PROGRESS` →
  `VERIFY` → `FAIL`/`DONE`

No skill should advance a phase (e.g., starting Milestones without an
approved Vision) without first confirming the state of the previous
artifact — see the `project-state` skill.

## Available skills

| Phase                  | Skill(s)                                   |
|-------------------------|---------------------------------------------|
| 1 — Vision              | `project-init`, `vision`, `project-state`  |
| 2 — Milestones          | `milestones`, `project-review`             |
| 3 — Architecture        | `architecture`, `architecture-decision`    |
| 4 — Tasks + Specs       | `task-planner`, `task-refinement`          |
| 5 — Implementation      | `implement-task`, `verify-task`            |
| Cross-cutting           | `project-review`                           |

## Artifact structure

```
project/
│
├── AGENTS.md
│
├── .opencode/
│   └── skills/
│       ├── project-init/
│       ├── vision/
│       ├── project-state/
│       ├── milestones/
│       ├── project-review/
│       ├── architecture/
│       ├── architecture-decision/
│       ├── task-planner/
│       ├── task-refinement/
│       ├── implement-task/
│       └── verify-task/
│
├── docs/
│   ├── project-state.md
│   ├── vision.md
│   ├── milestones.md
│   ├── architecture.md
│   ├── decisions/
│   │   ├── ADR-001-....md
│   │   └── ADR-002-....md
│   └── diagrams/
│       ├── context.mmd
│       └── containers.mmd
│
└── tasks/
    ├── M001/
    │   ├── T001.md
    │   └── T002.md
    └── M002/
        └── T003.md
```

## Owner per architecture section

Each section of `docs/architecture.md` declares:

```
Owner: ai      # or: user
```

- **`ai`** (default) — the AI proposes the section's content, the user
  approves/adjusts it.
- **`user`** — the user writes the content, typically as raw/informal
  notes. The AI never replaces that substance; its role is to review,
  question (both real trade-offs and small inconsistencies — naming,
  contradictions, obvious gaps), structure the text into the section's
  format, and generate the corresponding diagrams. This repeats through
  discussion until there are no `Open Question`s left, and only then does
  the section move to `APPROVED`. See details in
  `.opencode/skills/architecture/SKILL.md`.

## Implementation mode per task

Each task declares in its frontmatter:

```yaml
implementation:
  mode: ai      # or: manual
  manual_style: support   # only relevant if mode: manual — support | learning
```

- **`ai`** — the AI can implement the task's code directly. The
  specification and acceptance criteria for an `ai` task can be as
  detailed as needed, since the AI will follow them literally.
- **`manual`** — the developer implements it. The AI **does not modify
  code** for that task. What else it can do depends on `manual_style`:
  - **`support`** (default) — clarify the spec; analyze code; suggest
    solutions; run tests; do code review pointing directly at what to fix;
    verify acceptance criteria.
  - **`learning`** — for when the goal is to learn by coding, not just to
    get the task done. The AI answers only what is asked (concepts, how an
    API behaves, why a specific error happens), runs tests without
    interpreting the cause, and avoids proposing the solution on its own
    initiative — even in code review it points at the symptom, not the
    fix. If it senses it is about to hand over the solution unprompted, it
    asks first whether that is what the user wants. See details in
    `.opencode/skills/implement-task/SKILL.md`.

  For **any `manual` task**, regardless of `manual_style`, the
  specification is written at a higher level than for an `ai` task: it
  covers the objective, functional requirements, constraints, and any
  interfaces/contracts that must be respected — not a step-by-step
  implementation guide. Verification for a `manual` task checks that the
  requirements and acceptance criteria are met and that architecture/ADR
  constraints are respected, but does **not** judge the internal
  implementation approach (algorithm, code structure, design pattern)
  chosen by the developer, as long as it satisfies those requirements. See
  details in `.opencode/skills/task-refinement/SKILL.md` and
  `.opencode/skills/verify-task/SKILL.md`.

## When to use `project-review`

Whenever a change in the user's intent could invalidate decisions already
made (e.g., "actually, I don't want Kafka anymore"), invoke
`project-review` **before** touching any file. That skill maps the impact
(affected ADRs, architecture sections, milestones, tasks) and presents the
proposed changes for approval — it never edits anything directly.
