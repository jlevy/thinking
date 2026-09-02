---
type: is
id: is-01m1hnqac48fy0ptsewn0mhetx
title: "Spike S6: hallucination rate on non-speech (ESC-50 applause/laughter/knock/murmur + silence) in hour-long synthetic streams, with/without VAD gating (guard for R1 baseline)"
kind: task
status: closed
priority: 1
version: 5
spec_path: docs/project/research/research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md
labels:
  - spike
  - sandbox
dependencies:
  - type: blocks
    target: is-01m1hn7p30j4w673wv165jhmv7
  - type: blocks
    target: is-01m1hpp3w4vncagb52b5x284dr
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:22:26.691Z
updated_at: 2026-09-02T22:32:35.878Z
closed_at: 2026-09-02T22:32:35.877Z
close_reason: "Superseded by th-r8c4: 10.6 reports the paired stream01 comparison; the two missing VAD passes were abandoned after three container restarts"
resolution: null
duplicate_of: null
---
Opus agent; attic/spikes/hallucination-guard; report scratchpad/spike-hallucination.md. Decides whether VAD gating satisfies the §4.2 hallucination guard or a decoding-side fix is needed.
