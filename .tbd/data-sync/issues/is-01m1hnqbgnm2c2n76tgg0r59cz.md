---
type: is
id: is-01m1hnqbgnm2c2n76tgg0r59cz
title: "Spike S7+S9+S23 (after chair-parser): record census (stage directions/hour, unattributed turns, turn length by role); parser robustness to ASR name errors via Piper->Whisper; commercial-cap fit from candidate-set histogram"
kind: task
status: closed
priority: 1
version: 3
spec_path: docs/project/research/research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md
labels:
  - spike
  - sandbox
dependencies: []
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:22:27.861Z
updated_at: 2026-09-02T23:08:48.877Z
closed_at: 2026-09-02T23:08:48.877Z
close_reason: S7 (record census, agenda §10.11) and S9 (parser survival through ASR, agenda §10.12) both ran and are integrated; S23 was answered earlier in §10.10
resolution: null
duplicate_of: null
---
Depends on the parsed turn table (th-d88t) and the Congreso XV dataset (th-wq9g). PARTIAL ANSWER for S23 from real data (scratchpad/congreso-xv-stats.md): per agenda item the candidate set is median 2 speakers, p90 10, max 34; 66.7% of items have ≤4 speakers, 90.7% ≤10, 100% ≤50 — so a 50-identity cap fits every agenda item and a 4-identity cap fits two thirds; per session median 39 speakers (max 71). Archive clips have no sub-60 s stratum: interjections are not clips. Remaining: S7 record census (stage directions, unattributed turns), S9 parser robustness through Piper→Whisper.
