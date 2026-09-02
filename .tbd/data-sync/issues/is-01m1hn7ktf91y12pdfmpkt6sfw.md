---
type: is
id: is-01m1hn7ktf91y12pdfmpkt6sfw
title: "Spike: chair-announcement parser coverage and precision on ParlaMint-ES; agenda candidate-set sizes; roster surname ambiguity (H-003, H-002, R3)"
kind: task
status: closed
priority: 1
version: 8
spec_path: docs/project/research/research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md
labels:
  - spike
dependencies:
  - type: blocks
    target: is-01m1hn7p30j4w673wv165jhmv7
  - type: blocks
    target: is-01m1hn7ps8grtkbdv3kgpd5qjh
  - type: blocks
    target: is-01m1hncem29wpjyexcmrph4b8n
  - type: blocks
    target: is-01m1hnqbgnm2c2n76tgg0r59cz
  - type: blocks
    target: is-01m1hnqgk47f2cb0fsw5ntftm2
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:13:52.079Z
updated_at: 2026-09-02T19:01:01.098Z
closed_at: 2026-09-02T19:01:01.097Z
close_reason: "Chair-parser spike delivered: scratchpad/spike-chair-parser.md; code attic/spikes/chair-parser. H-003 supported for rostrum turns (.93–.95 overall), refuted as all-turns (.77–.78; .92–.94 with continuation rule); H-002: median 10–12 candidates per agenda item; R3: 42% share first surname, chair gives both surnames ~50%"
resolution: null
duplicate_of: null
---
Opus agent; code attic/spikes/chair-parser; report scratchpad/spike-chair-parser.md. Real Congreso transcripts (ParlaMint-ES samples 2017/2020/2023, full listPerson). Outputs: coverage, precision, coverage x precision stratified by turn length; per-item distinct-speaker counts; surname ambiguity; parsed turn table CSV.
