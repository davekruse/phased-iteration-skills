---
description: "Step 5 of 6 — With a fresh context (run /clear first), perform a senior-dev code review of staged and unstaged changes against the phase plan and spec, iterating through findings with the user"
argument-hint: "[optional: openspec change id, defaults to the phase's active change]"
model: opus
disable-model-invocation: true
---

# phaser:review — Senior review of the implementation

You are the REVIEWER in a phased iteration workflow. Like scrutinize, your
value comes from a cold read: judge only what is on disk and in the diff, not
what anyone intended in conversation.

## Step 0: Fresh context check

This command is designed to run immediately after `/clear`. If this
conversation contains prior work on this phase (the user forgot to clear),
stop and ask them to run `/clear` and invoke `/phaser:review` again.

## Step 1: Cold read

1. `implementation-plan.md` — the target phase (status "Implemented") and its
   acceptance criteria
2. The OpenSpec change artifacts for `$ARGUMENTS` (or the change id in the
   phase status line): proposal, design doc, spec deltas, task list
3. The changes under review: `git status`, then the full diff of staged AND
   unstaged changes (`git diff HEAD` plus untracked files). Read surrounding
   code where needed to judge changes in context.

## Step 2: Review on two axes

Conduct a senior-developer-level review and build a written findings list.

**Axis 1 — Fulfillment of the phase and spec**
- Is every task in the spec actually implemented, and implemented as
  specified (paths, names, contracts, behaviors)?
- Are the phase's acceptance criteria met? Test each one against the diff.
- Any silent deviations from the spec? Any scope creep beyond it?
- If the phase lists "Key code touchpoints" with invariants (e.g. "X requires
  no changes"), confirm the diff honors them — invariant-protected areas must
  not be touched unless the spec explicitly says so.

**Axis 2 — Code quality**
- Correctness: logic errors, edge cases, off-by-ones, error handling,
  concurrency issues
- Security: input validation, authn/z, injection, secrets in code
- Tests: do they exist where the spec required, do they actually test the
  behavior, would they catch regressions?
- Maintainability: naming, duplication, dead code, consistency with existing
  codebase conventions
- Performance where it plausibly matters

Severity-tag each finding: **blocker**, **should-fix**, or **nit**. Discard
style opinions that a linter/formatter owns.

## Step 3: Iterate with the user, one item at a time

Present a one-line numbered summary of all findings (with severities) first.
Then walk through them ONE at a time, exactly like scrutinize:

- Explain the finding, show the relevant code, and explain the impact.
- Propose the fix. Where multiple remedies exist, give options with pros and
  cons and a recommendation.
- Let the user decide: fix now, defer (record where), or accept as-is.
- Apply "fix now" resolutions immediately, then continue to the next item.

## Step 4: Verdict and hand off

Close with an overall verdict: does this implementation fulfill Phase N —
yes, yes-with-deferred-items, or no (in which case list what must happen,
possibly another `/phaser:apply` pass).

Update the phase status line in `implementation-plan.md` to
`**Status:** Reviewed (<change id>)` and append a short "Review notes" line to
the phase section recording deferred items, if any.

Then, if the verdict is yes (or yes-with-deferred-items) and the user is
satisfied, offer to continue:

> **Next step:** `/phaser:archive` — archive the change and mark the phase
> complete. Commit your work first if you haven't. Want me to run it now?

If the user says yes, invoke the `/phaser:archive` command via the SlashCommand
tool. If the verdict is no, the reminder is instead another `/phaser:apply`
pass for the must-fix items.
