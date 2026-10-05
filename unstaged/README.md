# Unstaged skills

Skills that are **not** staged into flotilla's autonomous crews. This is a
flotilla fork patch: flotilla stages every `SKILL.md` found recursively under
`skills/` into every crew (code crews, shepherds, governors), and crews run
in containers with no interactive human. Skills that wait indefinitely for a
human, that a model can invoke to start a permanent interview (the grill-*
family), or that are irrelevant to the crews' work are kept here instead so a
crew never reaches for them.

They are still first-class skills. Humans install them by hand (the dev
`scripts/link-skills.sh` links them alongside the staged set, minus the
buckets it already skips), and upstream owns their content: everything under
`unstaged/` is left exactly as upstream ships it, including the `GLOSSARY.md`
convention (the staged skills carry a fork patch that keeps `CONTEXT.md`).

The bucket structure mirrors `skills/`:

- [`engineering/`](./engineering/README.md): engineering skills not staged to crews (tdd, code-review, the spec/ticket/triage flows, grill-with-docs, implement-spec, setup, ask-matt, wizard, improve-codebase-architecture).
- [`productivity/`](./productivity/README.md): grilling and the grill-* family, handoff, teach and more.
- [`in-progress/`](./in-progress/README.md): upstream's beta channel.
- `misc/`: rarely-used, non-promoted skills.
- `deprecated/`: retired, kept as an empty bucket for history.
