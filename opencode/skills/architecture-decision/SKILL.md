---
name: architecture-decision
description: Records a significant architecture decision as an Architecture Decision Record (ADR) in docs/decisions/. Use this skill whenever the architecture skill (or the user directly) identifies a choice with real trade-offs — database, messaging, communication protocol, authentication, repository structure, etc. Never decides alone: identifies options, explains trade-offs, asks the user, only then records.
---

# architecture-decision

Implements, for a single decision, the mandatory cycle:

```
identify options
       ↓
explain trade-offs
       ↓
ask the user
       ↓
record the decision
```

## When to run

- Invoked by the `architecture` skill whenever a choice with significant
  impact comes up.
- Invoked directly by the user ("I want to revisit the decision about X",
  "let's decide on the database now").
- Invoked by `project-review` when an old decision needs to be reopened.

## Process

1. **Frame the decision** — in one sentence, what exactly is the question
   being answered? (e.g., "How do service A and service B communicate?")

2. **Identify options** — list 2 to 4 realistic options, not a single
   option disguised as a choice. Each option should be something the user
   would recognize as reasonable.

3. **Explain trade-offs** — for each option, briefly present:
   - advantages;
   - disadvantages;
   - the context in which it's usually the best choice.
   Clearly mark when you're expressing an opinion/recommendation
   (`Proposal`) versus stating objective facts (`Fact`).

4. **Ask the user** — request an explicit decision. You can recommend an
   option, but make clear it's a recommendation, not an already-made
   decision.

5. **Record the decision** — only after an explicit answer,
   create/update `docs/decisions/ADR-XXX-<slug>.md`:
   ```markdown
   # ADR-XXX: <title>

   Status: Accepted
   Date: <date>

   ## Context
   ...

   ## Options considered
   1. <option A> — pros / cons
   2. <option B> — pros / cons

   ## Decision
   <chosen option, as confirmed by the user>

   ## Consequences
   ...
   ```
   Number sequentially (`ADR-001`, `ADR-002`, ...) by checking the ADRs
   already present in `docs/decisions/`.

6. **Update `architecture.md`** — reference the ADR in the relevant
   architecture section instead of duplicating the full reasoning there.

## Rules

- Never write "Decision:" in `architecture.md` or in an ADR without an
  explicit confirmation from the user for that specific decision.
- If the user says "you decide", still present the options and trade-offs
  first, and ask for confirmation of your recommendation before recording
  it as a decision — "you decide" is not the same as "don't explain the
  alternatives to me".
- If an old decision is revisited (`ADR-003` superseded by a new one), mark
  the old ADR as `Status: Superseded by ADR-0XX` instead of deleting it.
