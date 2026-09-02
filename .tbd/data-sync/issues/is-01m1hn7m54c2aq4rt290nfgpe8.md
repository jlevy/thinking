---
type: is
id: is-01m1hn7m54c2aq4rt290nfgpe8
title: "Spike: scoring harness — TAA@coverage, cpWER/tcpWER/DER, ECE, latency/revision, paired-session bootstrap and accept rule (R1 instrument)"
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
    target: is-01m1hncem29wpjyexcmrph4b8n
  - type: blocks
    target: is-01m1hnqhdy9jc1854tdhe87pcj
  - type: blocks
    target: is-01m1hnqpd689spmp39gq0rpsx3
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:13:52.419Z
updated_at: 2026-09-02T18:47:49.550Z
closed_at: 2026-09-02T18:47:49.550Z
close_reason: "Scoring harness delivered: attic/spikes/scoring-harness (attrscore 0.1.0, 71 tests); report scratchpad/spike-scoring-harness.md. Protocol changes: IoU turn assignment, IER as named DER, pyannote collar=0.5 for NIST 0.25, fixed-label cpWER; MeetEval/pyannote quirks logged"
resolution: null
duplicate_of: null
---
Opus agent; code attic/spikes/scoring-harness (package attrscore, pytest); report scratchpad/spike-scoring-harness.md. Must refuse empty hypotheses (instrument guard). MID-FLIGHT ADDITION (sent to the agent): fixed-label (no permutation) cpWER/tcpWER variants and pyannote.metrics IdentificationErrorRate, plus a test showing a consistent-but-wrong name mapping scores 0 under permutation-invariant cpWER and full error under the fixed-label variant.
