---
name: project-init
description: Initializes the AI-assisted development workflow structure in a new or existing project. Use this skill whenever the user says something like "I want to build an app for...", "let's start a new project", or when docs/project-state.md doesn't exist yet. Creates the folder structure (docs/, docs/decisions/, docs/diagrams/, tasks/), the AGENTS.md file, and starts project-state. Does not move on to Vision by itself — it only sets the stage.
---

# project-init

Responsible for bootstrapping the workflow in a project. It is typically
the first skill invoked, usually from a loose statement like "I want to
build an app for...".

## When to run

- `docs/project-state.md` does not exist yet in the repository.
- The user explicitly asks to "start/initialize the project" or to "set up
  the workflow".

If `docs/project-state.md` already exists, **do not reinitialize** — call
the `project-state` skill to show where the project currently stands, and
ask the user whether they really want to start over from scratch (this is
a significant, potentially destructive decision).

## What it does

1. Creates the folder structure, if it doesn't exist yet:
   ```
   docs/
   docs/decisions/
   docs/diagrams/
   tasks/
   .opencode/skills/   (usually already exists — do not overwrite existing skills)
   ```
2. Creates `AGENTS.md` at the root (copy the template from this skill set,
   if one doesn't already exist).
3. Creates `docs/project-state.md` with the initial state:
   ```markdown
   # Project State

   ## Current phase
   VISION (not started)

   ## Artifacts
   - Vision: does not exist
   - Milestones: does not exist
   - Architecture: does not exist
   - Tasks: 0

   ## Last updated
   <date>
   ```
4. Asks the user **one short question** to kick off the Vision phase:
   "Describe in 2-3 sentences what you want to build." This is the natural
   handoff to the `vision` skill — don't try to write the vision here.

## What it does NOT do

- It does not write `vision.md` (that is the job of the `vision` skill).
- It does not propose milestones, architecture, or tasks.
- It does not assume tech stack, project name, or scope — all of that is
  an `Open Question` until it's discussed in later phases.

## Output

- Folder structure created.
- `docs/project-state.md` created with phase = VISION (not started).
- A kickoff question directed at the user.
