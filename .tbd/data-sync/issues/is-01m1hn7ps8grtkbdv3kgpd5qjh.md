---
type: is
id: is-01m1hn7ps8grtkbdv3kgpd5qjh
title: "Follow-up spike: LLM-as-chair-parser (Fable) on the parsed ParlaMint-ES turn table vs the regex baseline"
kind: task
status: closed
priority: 2
version: 3
spec_path: docs/project/research/research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md
labels:
  - spike
dependencies: []
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:13:55.112Z
updated_at: 2026-09-02T20:07:42.063Z
closed_at: 2026-09-02T20:07:42.063Z
close_reason: "LLM chair-parser spike delivered: scratchpad/spike-llm-chair-parser.md. LLM alone ties regex (0.748 vs 0.752); hybrid (regex first, LLM fallback) 0.920 vs 0.875 on sample, 0.940 vs 0.906 projected, one call per 5.4 turns, p95 1.2 s, ~/bin/bash.18/1,000 turns"
resolution: null
duplicate_of: null
---
Use a Fable subagent as the parser over ~150 chair utterances plus roster; score against the same ground truth; compare precision/coverage to regex; no API key needed.
