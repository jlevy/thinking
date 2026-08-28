# Thinking

Evidence-ranked research reports on computation, mathematical foundations, physics, and
biology.

Each report separates what is proved from what is computed, conjectured, or merely
asserted, links primary sources next to the claims they support, and ends with a
consolidated reference list.

## Reports

The [research report index](docs/project/research/README.md) gives each report’s scope
and a suggested reading order.

| Date | Report |
| --- | --- |
| 2026-07-13 | [Gregory Chaitin, Algorithmic Information, Omega, and Metabiology](docs/project/research/research-2026-07-13-gregory-chaitin-ait-omega-metabiology.md) |
| 2026-07-13 | [Stephen Wolfram’s Ruliad](docs/project/research/research-2026-07-13-ruliad-foundations.md) |
| 2026-07-14 | [Chaitin, the Ruliad, and the Foundations of Physics](docs/project/research/research-2026-07-14-chaitin-ruliad-foundations-physics.md) |
| 2026-07-14 | [Michael Levin, Platonic Forms, and the Mathematics of Living Systems](docs/project/research/research-2026-07-14-michael-levin-platonic-forms-biology.md) |

## Working in this repository

The repository is prose.
The only tooling is Markdown formatting, applied automatically on commit.

```bash
make hooks-install   # once after cloning: installs the lefthook pre-commit hook
make format          # format all Markdown
make format-check    # report drift without writing
```

[AGENTS.md](AGENTS.md) carries the conventions, including how reports rank evidence and
which files are generated rather than authored.

<!-- This document follows common-doc-guidelines.md.
See github.com/jlevy/practical-prose and review guidelines before editing.
-->
