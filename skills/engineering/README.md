# Engineering

Skills for code work. This bucket under `skills/` holds the skills flotilla
stages into every autonomous crew (see the root README's "Flotilla fork"
note). The remaining engineering skills live under
[`../../unstaged/engineering/`](../../unstaged/engineering/README.md) and are
installable by hand.

## User-invoked

Reachable only when you type them (Claude Code: `disable-model-invocation: true`; Codex: `policy.allow_implicit_invocation: false` in `agents/openai.yaml`).

- **[implement](./implement/SKILL.md)**: Build the work described by a spec or set of tickets, driving `/tdd` at pre-agreed seams. No local review pass: CI review plus the pr-shepherd loop is the arbiter (flotilla fork patch).
- **[wayfinder](./wayfinder/SKILL.md)**: Plan a huge chunk of work (more than one agent session can hold) as a shared map of decision tickets on the issue tracker, resolved one at a time until the way to the destination is clear.
- **[retro](./retro/SKILL.md)**: Suggest improvements to the coding agent's environment (navigation, automated checks, coding standards, steering files, tooling) after a session, most severe first.

## Model-invoked

Model- or user-reachable (rich trigger phrasing so the model can reach for them).

- **[prototype](./prototype/SKILL.md)**: Build a throwaway prototype to answer a design question: a single shareable HTML file for state/logic, or several toggleable UI variations.
- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**: Disciplined diagnosis loop for hard bugs and performance regressions: build a feedback loop that goes red on this bug → minimise → hypothesise → instrument → fix → regression-test.
- **[research](./research/SKILL.md)**: Investigate a question against high-trust primary sources and capture the findings as a cited Markdown file in the repo, run as a background agent.
- **[domain-modeling](./domain-modeling/SKILL.md)**: Actively build and sharpen a project's domain model by challenging terms, stress-testing with scenarios, and updating `CONTEXT.md` and ADRs inline.
- **[codebase-design](./codebase-design/SKILL.md)**: Shared discipline and vocabulary for designing deep modules: small interfaces, clean seams, testable through the interface.
- **[pr](./pr/SKILL.md)**: The shape a pull request body should take: a summary as the smallest visual that makes the change clear, before/after evidence that it works, and a merge-danger call (one-way or two-way door, plus blast radius). Fills the repository's pull request template when it has one (flotilla fork patch).
