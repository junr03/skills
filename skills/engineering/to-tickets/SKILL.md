---
name: to-tickets
description: Break a plan, spec, or the current conversation into a set of tracer-bullet tickets, each declaring its blocking edges, published to the configured tracker (edges as text in one file per ticket locally, or native blocking links on a real tracker).
disable-model-invocation: true
---

# To Tickets

Break a plan, spec, or conversation into a set of **tickets**: tracer-bullet vertical slices, each declaring the tickets that **block** it.

The issue tracker and triage label vocabulary should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a reference (a spec path, an issue number or URL) as an argument, fetch it and read its full body and comments.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Ticket titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the work into **tracer bullet** tickets.

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests): vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Reuse existing seams where they support the behavior; include only prefactoring needed to make the slice safe and demonstrable
- Identify the first slice that can be demoed end to end before expanding into parallel work
- Keep implementation assignments inside accepted tickets. Parallelize when dependencies, stable interfaces, and exclusive ownership make the work safe; do not impose an arbitrary concurrency cap
- Every ticket has an observable demo or test for its end-to-end behavior
- Reject a horizontal ticket unless there is a concrete reason a vertical slice cannot land safely; use the wide-refactor exception below when it applies

</vertical-slice-rules>

Give each ticket its **blocking edges**: code prerequisites and unresolved decisions that genuinely prevent implementation. Record business decisions and activation or rollout gates separately; an activation gate does not block implementation. A ticket with no code or decision blockers can start immediately.

An expected pull-request range is a review signal, not a hard cap. If the decomposition would materially exceed the agreed delivery shape, replan the slices with the user before publishing. Do not create a ticket or pull request for every subagent assignment: ownership can be tracked within a slice.

For a coherent ticket with multiple implementation tasks, include a concise **Work breakdown** table inside the ticket. Give each row a stable work ID, a concrete deliverable, prerequisite work IDs or ticket references (or `None`), an owner, a state, and evidence criteria. Rows start with `Unassigned`, `Planned`, and explicit evidence criteria. Keep assignments inside the ticket; a row does not automatically become another ticket or pull request, and there is no required row count.

The implementer maintains row ownership, state, and evidence during execution. Record a branch, exclusively owned files, or current blocker when useful, using paths confirmed during implementation rather than speculative planning. Mark a row verified only when its stated evidence is checked, not on an agent's report alone. A named checkpoint may unblock only the dependent work that needs it after its evidence is verified; it does not complete the ticket. Complete the ticket only when all acceptance criteria are verified.

**Wide refactors are the exception to vertical slicing.** A **wide refactor** is one mechanical change (rename a column, retype a shared symbol) whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**. First expand: add the new form beside the old so nothing breaks. Then migrate the call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, keeping CI green batch to batch because the old form still exists. Finally contract: delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket; green is promised only there.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct: does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?
- Is the proposed pull-request range right? Treat it as a review signal, not a cap.

Keep the first demoable slice clear and avoid splitting work further when a coherent, independently reviewable change would be lost.

Iterate until the user approves the breakdown.

### 5. Publish the tickets to the configured tracker

Publish the approved tickets. **How** depends on the tracker `/setup-matt-pocock-skills` configured; the tickets are the same either way, only the shape of the blocking edges changes:

- **Local files** → write one file per ticket under `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01` in dependency order (blockers first). Each file's "Blocked by" lists the numbers/titles it depends on. Use the per-ticket file template below: one ticket per file, never a single combined file.
- **A real issue tracker (GitHub, Linear, …)** → publish one issue per ticket in dependency order (blockers first) so each ticket's blocking edges can reference real identifiers. Use the platform's native blocking relationship where it has one; otherwise set each ticket's "Blocked by" to the blocking issues. If the source was an existing issue, make each ticket its sub-issue (tracker doc's operation). Apply the `ready-for-agent` triage label unless instructed otherwise; the tickets are agent-grabbable by construction.

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom.

Do NOT close or modify any parent issue.

<local-ticket-template>

# <NN>: <Ticket title>

**What to build:** the end-to-end behaviour this ticket makes work, from the user's perspective, not a layer-by-layer implementation list.

**Blocked by:** the numbers/titles of code prerequisites or unresolved decisions that prevent implementation, or "None (no code or decision blockers)".

**Business decisions and activation gates:** list separately when relevant, or "None".

**Demo:** the observable end-to-end behavior that proves this slice works.

**Status:** ready-for-agent

**Work breakdown:**

| Work ID | Deliverable | Depends on | Owner | State | Evidence |
| --- | --- | --- | --- | --- | --- |
| W1 | <concrete deliverable> | None | Unassigned | Planned | <evidence that will verify this work> |

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2

</local-ticket-template>

<issue-template>

## Parent

A reference to the parent issue on the tracker (if the source was an existing issue, otherwise omit this section).

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective, not layer-by-layer implementation.

## Demo

The observable end-to-end behavior that proves this slice works.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

- A reference to each blocking ticket, or "None (can start immediately)". Omit this section when blockers were set as native edges.

## Work breakdown

| Work ID | Deliverable | Depends on | Owner | State | Evidence |
| --- | --- | --- | --- | --- | --- |
| W1 | <concrete deliverable> | None | Unassigned | Planned | <evidence that will verify this work> |

</issue-template>

In either form, avoid speculative file paths or code snippets in planning text: they go stale fast. Runtime assignments may record confirmed paths, exclusive ownership, and branches when useful. If a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.
