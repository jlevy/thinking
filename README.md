# Thinking

A kitchen-sink repository to hold random thinking on different topics.

The thinking is mostly AI. The topics and direction are mine, mostly to satisfy my own
curiosity.

I make it open source so I can share easily.
It uses my own tools, including
[Practical Prose](https://github.com/jlevy/practical-prose) for writing and editing
standards, [tbd](https://github.com/jlevy/tbd) for issue tracking and agent workflows,
and [Flowmark](https://github.com/jlevy/flowmark) for Markdown formatting.
The writing here follows Practical Prose’s
[common documentation guidelines](https://github.com/jlevy/practical-prose/blob/main/docs/common-doc-guidelines.md).

## Reports

These are evidence-ranked research reports on computation, mathematical foundations,
physics, and biology.

Each report separates what is proved from what is computed, conjectured, or merely
asserted, links primary sources next to the claims they support, and ends with a
consolidated reference list.

| Date | Report |
| --- | --- |
| 2026-07-13 | [Gregory Chaitin, Algorithmic Information, Omega, and Metabiology](docs/project/research/research-2026-07-13-gregory-chaitin-ait-omega-metabiology.md) |
| 2026-07-13 | [Stephen Wolfram’s Ruliad](docs/project/research/research-2026-07-13-ruliad-foundations.md) |
| 2026-07-14 | [Chaitin, the Ruliad, and the Foundations of Physics](docs/project/research/research-2026-07-14-chaitin-ruliad-foundations-physics.md) |
| 2026-07-14 | [Michael Levin, Platonic Forms, and the Mathematics of Living Systems](docs/project/research/research-2026-07-14-michael-levin-platonic-forms-biology.md) |

The [research report index](docs/project/research/README.md) gives each report’s scope
and a suggested reading order.

## Working in This Repository

The repository is prose.
The only tooling is Markdown formatting, applied automatically on commit by Flowmark.

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
