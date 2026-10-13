---
name: change-analysis
description: >
  Surgical change analysis before editing. Map the blast radius of a change, present the files it
  touches and multiple candidate solutions, then wait for the user to align before making any
  edit. Use when the user wants to be precise, surgical, careful, or minimal about a change; asks
  "what does this touch?", "what's the blast radius?", "what are my options?", "don't break
  anything"; or is editing shared/load-bearing code (a widely-imported function, a public API, a
  schema, a config). Precedes the edit — analyze, align, then change.
---

Show user the changes, instead of just telling.
Keep it simply, stupid.

# change-analysis — Surgical Changes

A **surgical** change does one thing: it fully solves the request and touches nothing else. The
enemy is the *incidental* edit — the drive-by rename, the "while I'm here" refactor, the
speculative fix — each one widening the blast radius past what the request needs.

**Hard gate: make no file edits during steps 1–4.** These steps are analysis and alignment only.
You do not open the editor until the user picks a solution in step 5. Treat any urge to "just fix
it" as the failure this skill exists to prevent — even a one-line change waits for alignment.

Work the steps in order.

## 1. Pin the target

State, in one sentence, the exact behavior that must change and the exact behavior that must
**not**. The second half is the contract you defend for the rest of the run.

Then find the **seam** — the smallest code boundary that owns the target behavior. Read it and
its immediate neighbors before deciding anything.

**Done when:** you can name the file(s) and symbol(s) that own the behavior, and the one-sentence
must / must-not contract.

## 2. Map the blast radius

The **blast radius** is everything an edit at the seam could reach. Trace it — don't guess:

- **Callers** — who invokes this symbol? Search the codebase, not your memory.
- **Contracts** — signature, return shape, exceptions, schema, config keys, serialized formats.
  Any consumer relies on these.
- **Invariants** — ordering, idempotency, sync-vs-async, thread/threadpool assumptions, side
  effects the caller expects.
- **Tests** — which existing tests exercise this seam? They encode the must-not contract.

**Done when:** every caller, contract, invariant, and test is accounted for, with no unexamined
reach.

## 3. Draft multiple solutions

Produce **at least two** distinct candidate solutions — not the same approach reworded. Make them
genuinely different in strategy (e.g. edit-in-place vs. new seam vs. adapter/shim), so the choice
is real. For each, give:

- **Approach** — one or two sentences on the strategy.
- **Files touched** — the explicit list of files, and roughly what changes in each.
- **Blast radius** — what stays inside the cut vs. what it risks perturbing outside.
- **Trade-offs** — cost, risk, reversibility, how surgical it actually is.

State your **recommendation** and why, but present the options neutrally enough that the user can
pick another.

**Done when:** two or more solutions are on the table, each with its file list and trade-offs.

## 4. Align — stop and wait

Present to the user, then **stop**:

1. The one-sentence must / must-not contract from step 1.
2. The full list of files that will be touched, per solution.
3. The candidate solutions from step 3 with your recommendation.

Ask which solution to proceed with. **Do not edit anything until the user answers.** If they
amend the scope or pick a hybrid, restate the resulting file list and confirm before moving on.

**Done when:** the user has explicitly chosen a solution and its file list.

## 5. Make the minimal cut

Now edit — only the files the chosen solution named, only the lines the cut requires. Match the
surrounding style; introduce no rename, reformat, reorder, or refactor the user didn't approve. A
tempting improvement outside the agreed scope goes back to the user, not into the diff.

**Done when:** the diff touches only the agreed files, and you can point at each hunk and say which
part of the request it serves.

## 6. Verify the contract held

Prove both halves of step 1: the target changed, and the must-not behavior did not.

- Run the **narrowest** test/lint/build that covers the seam (per this repo's `make verify` /
  module runners), widening only if a result demands it.
- Re-read the final diff against the blast-radius map: did anything **outside** the cut move?
  Whitespace, imports, unrelated lines — revert them.

**Done when:** targeted checks pass and the diff is confirmed to contain nothing outside the cut.
