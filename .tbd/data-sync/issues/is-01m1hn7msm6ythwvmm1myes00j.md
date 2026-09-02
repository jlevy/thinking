---
type: is
id: is-01m1hn7msm6ythwvmm1myes00j
title: "Spike: CPU audio slice with sherpa-onnx on synthetic Piper Spanish — VAD, Whisper ASR, 3D-Speaker embeddings, open-set ID with abstention, LID (R1/R2 pipeline)"
kind: task
status: closed
priority: 1
version: 6
spec_path: docs/project/research/research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md
labels:
  - spike
dependencies:
  - type: blocks
    target: is-01m1hn7p30j4w673wv165jhmv7
  - type: blocks
    target: is-01m1hncem29wpjyexcmrph4b8n
  - type: blocks
    target: is-01m1hnqpd689spmp39gq0rpsx3
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:13:53.075Z
updated_at: 2026-09-02T20:11:59.623Z
closed_at: 2026-09-02T20:11:59.623Z
close_reason: "Audio-slice spike delivered: scratchpad/spike-audio-slice.md. Full CPU stack from GitHub release assets; VAD 99.9% speech recall on synthetic sessions; WER nemo-ctc 12.5 / zipformer 14.6 / whisper-tiny 28.2 on Piper speech; ID numbers are TTS fingerprinting only; LID 29/30 human clips"
resolution: null
duplicate_of: null
---
Opus agent; code attic/spikes/audio-slice; report scratchpad/spike-audio-slice.md. Establishes which models run here without Hugging Face; synthetic-only numbers, clearly labeled.
