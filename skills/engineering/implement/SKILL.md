---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the accepted work described by the user in the spec or tickets. Keep the change within that scope; do not turn capabilities inferred from the codebase into new requirements.

Prefer a complete, narrow behavior through the existing seams. Reuse the highest useful existing seam and avoid broad refactors unless they are necessary for the accepted behavior. If work is delegated, treat assignments as ownership within the delivery slice, not as automatic tickets or pull requests. Separate code prerequisites from business decisions and activation or rollout gates. If the implementation would materially exceed the agreed delivery shape or expected pull-request range, stop and replan with the user before expanding. The expected range is a review signal, not a hard cap.

Demonstrate the first complete slice before expanding into parallel work. Report the behavior that now works, the slice delivered, and any remaining business decision or activation gate. Run required checks and review at coherent slice boundaries; keep required checks intact.

If the user passes a ticket reference, fetch it from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

Call the Skill tool with "tdd" where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, call the Skill tool with "code-review" to review the work.

Commit your work to the current branch.
