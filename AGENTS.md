# Project Instructions for AI Agents

This file provides instructions and context for AI coding agents working on this
project.

<!-- BEGIN TBD INTEGRATION format=f08 surface=agents-md -->
## tbd

This repository uses **tbd** for git-native issue tracking (beads), spec-driven
planning, and on-demand engineering guidelines.
As the agent, you operate tbd on the user’s behalf: translate their requests into tbd
actions rather than telling them to run commands.

- Run `tbd prime` to load current project state and the full tbd workflow.
- Run `tbd skill` for the complete reusable tbd skill instructions.
- Run `tbd shortcut --list` and `tbd guidelines --list` for on-demand resources.
- Track all work as beads: `tbd create`, `tbd ready`, `tbd close`, and `tbd sync`.

<!-- END TBD INTEGRATION -->

## Build & Test

The repository is prose.
The only tooling is Markdown formatting.

```bash
make hooks-install   # once after cloning: installs the lefthook pre-commit hook
make format          # format all Markdown
make format-check    # report drift without writing
make check           # verify the hand-written skill trees are in sync
```

### Markdown formatting

**Flowmark owns all Markdown here.** Do not add Prettier, Biome, or dprint Markdown
handling alongside it — two Markdown formatters churn each other’s output and make hooks
nondeterministic.

Formatting is applied **automatically on commit** by a lefthook `pre-commit` hook, which
formats and re-stages the result (`stage_fixed: true`). You should never need to format
by hand, and unformatted Markdown is not something you can commit by accident.

Formatting drift deliberately **does not fail CI**. It is fixed at commit time instead,
so style never blocks a build.
`make format-check` exists for ad-hoc checking, not as a gate.

Three rules worth knowing before changing any of this:

- **Exclusions are evidence-based, not precautionary.** The policy is to format the
  whole repository and exclude only what we have a reason to leave alone.
  Today that is exactly one category: the generated `SKILL.md` trees, which carry
  DO-NOT-EDIT markers and are rewritten byte-deterministically by their own installers.
  Formatting them would put two owners on the same bytes.
- **The hook formats the whole repository, not the staged files.** Flowmark reads
  `.flowmarkignore` relative to its target argument, so passing explicit paths silently
  bypasses the exclusion list.
  Do not “optimise” the hook to `{staged_files}`.
- **The flowmark version is pinned** in the `Makefile` (currently the latest Rust build,
  `flowmark-rs==0.3.2` — the Rust port is the fast one).
  Pinned rather than floating so it is not an unpinned zero-install runner, which
  `tbd guidelines supply-chain-hardening` rule 6 warns against.
  Bumping the pin is a deliberate, reviewable change.

Emergency bypass: `git commit --no-verify` (avoid in PRs).

## Architecture Overview

One tree holds the content: **`docs/project/research/`**, standalone research reports
that need nothing but themselves.
[Its README](docs/project/research/README.md) is the index, and carries the suggested
reading order.

Standalone is the criterion.
A line of work that comes with its own primary literature, data, and code does not
belong here as a bare report — it gets its own repository, so that its reports, sources,
and code can be read, run, and moved as one unit.

## Conventions & Patterns

- **Reports separate claims by evidential status** — proved, computationally verified,
  best known, or asserted-but-unverified — and cite primary sources near the claims they
  support. Revisions preserve that distinction rather than flattening it.
- **Reports carry research and last-updated dates**, because the literature and the
  assessments built on it both move.
- **Generated files are not hand-edited.** The `flowmark`, `pprose-*`, `softschema`, and
  `tbd` skill directories under `.agents/skills/` and `.claude/skills/` are written by
  their own installers and are excluded from formatting in `.flowmarkignore`.
- **Hand-written skills live in `.agents/skills/` and are mirrored, not duplicated.**
  `experiment-loop` is the only one today.
  `.agents/` is the source of truth, `make skills-sync` mirrors it into
  `.claude/skills/`, and `make skills-check` fails on drift so a skill edited in one
  tree cannot silently diverge.

<!-- BEGIN FLOWMARK INTEGRATION format=f03 surface=agents-md -->
## flowmark

Auto-format Markdown with `flowmark` for clean, semantic git diffs.

- Run `flowmark --auto <files>` on Markdown you create or edit.
- Run `flowmark --docs` for full usage and `flowmark --skill` for the skill.
- If `flowmark` is not on `PATH`, use a pinned `uvx` runner (never `@latest`).
- Fast Rust port (recommended): `uvx --from flowmark-rs==0.3.2 flowmark`.
- Python build (library / newest patch): `uvx --from flowmark==0.7.3 flowmark`.

<!-- END FLOWMARK INTEGRATION -->

<!-- BEGIN PPROSE INTEGRATION format=f02 -->
## Practical Prose (pprose)

Practical Prose: an evaluation toolkit and editorial workflows for practical documents.
Use when the user asks to improve, audit, score, or compare practical documents.

For durable Markdown documentation, use `pprose-common-edit` whenever creating, editing,
reviewing, or reorganizing it, unless the task is explicitly read-only.
Keep the required guideline footer intact.

Apply AI-slop reduction whenever drafting or editing prose, not only on request: use
`pprose-de-slop` to remove AI-writing tells and formulaic LLM prose, applying its
bundled catalog contextually and preserving meaning and voice.

Discover the tool from the CLI itself: `pprose --help` for commands, `pprose about` for
the project narrative, `pprose skill` for the workflow skills, and `pprose list` for
every on-demand guideline, shortcut, and runbook
(`pprose guidelines|shortcut|runbook <name>` prints one).

Run pprose as `pprose <command>` if on PATH, else `uvx pprose@0.4.0 <command>`
(zero-install via uv).

<!-- END PPROSE INTEGRATION -->
