---
description: "Step 1 of 6 — Discuss and define requirements for the next phase of development, then record it as the next numbered phase in implementation-plan.md"
argument-hint: "[optional: one-line summary of what this phase should accomplish]"
model: claude-fable-5
disable-model-invocation: true
---

# phaser:plan — Define the next development phase

You are facilitating the PLAN step of a phased iteration workflow. Your job is
to interview the user, converge on a well-defined phase of development, and
record it durably in `implementation-plan.md` at the repository root.

## Step 1: Read current state

- Read `implementation-plan.md` in the repo root. If it does not exist, you
  will create it with a title and a short preamble explaining that it is the
  numbered sequence of development phases for this application.
- Determine the next phase number N (existing highest phase + 1, or 1).
- Skim earlier phases so the new phase builds on — and does not contradict or
  duplicate — what came before.

## Step 2: Discuss and define

If `$ARGUMENTS` was provided, treat it as the seed topic. Otherwise ask what
this phase should accomplish.

Interview the user conversationally (a few questions at a time, not a wall of
questions) until you can clearly articulate:

- **Goal** — the outcome of this phase in one or two sentences
- **Requirements** — concrete, testable requirements
- **Scope boundaries** — explicitly what is OUT of scope for this phase
- **Dependencies** — earlier phases, external services, data, or decisions
  this phase depends on
- **Acceptance criteria** — how we will know the phase is done

Do NOT design the implementation or make architectural decisions here — that
is the job of `/phaser:propose`. Stay at the requirements level. If the user
starts specifying architecture, capture it under a "Constraints / early
decisions" note rather than expanding on it.

If the phase is too large to be implemented and reviewed comfortably in one
pass, say so and propose splitting it; a good phase is one coherent, shippable
increment.

## Step 3: Record the phase

Present a draft of the phase section to the user for confirmation, then append
it to `implementation-plan.md`:

```markdown
## Phase N: <short title>

**Status:** Planned
**Defined:** <YYYY-MM-DD>

### Goal
...

### Requirements
- ...

### Out of scope
- ...

### Dependencies
- ...

### Acceptance criteria
- [ ] ...

### Constraints / early decisions (optional)
- ...
```

Never rewrite or renumber earlier phases; the file is an append-only history
(status lines on earlier phases may be updated by later workflow steps).

## Step 4: Hand off

Confirm the phase is saved, then offer to continue immediately:

> **Next step:** `/phaser:propose` — turn Phase N into an OpenSpec change
> proposal. Want me to kick that off now?

If the user says yes, invoke the `/phaser:propose` command via the SlashCommand
tool. If not, leave the reminder above as the final line so it is easy to find
later.
