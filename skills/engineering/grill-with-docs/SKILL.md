---
name: grill-with-docs
description: A relentless interview to sharpen a plan or design, which also creates docs (ADR's and glossary) as we go.
disable-model-invocation: true
---

Call the Skill tool twice, for "grilling" and "domain-modeling".

Keep the agreed delivery shape in view while sharpening the plan. Define the accepted user-visible scope; do not treat capabilities inferred from the codebase as additional requirements. Prefer the smallest complete vertical slice that reuses existing seams and can be demonstrated end to end. Identify a first slice to demo before proposing parallel expansion. If implementation is delegated, an agent assignment is work ownership, not automatically a ticket or pull request. Separate code prerequisites from business decisions and activation or rollout gates. Treat an expected pull-request range as a signal to review the slice boundaries, not a hard cap; replan with the user before materially exceeding the agreed delivery shape. Preserve required tests and reviews at coherent slice boundaries.
