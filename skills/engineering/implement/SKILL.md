---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Use /tdd where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Do NOT run a local review pass (no /code-review, no /review): CI runs an automated
review on every push, and the pr-shepherd loop processes its findings. A local
review duplicates it minute-for-minute (measured: flotilla#928).

Commit your work to the current branch.
