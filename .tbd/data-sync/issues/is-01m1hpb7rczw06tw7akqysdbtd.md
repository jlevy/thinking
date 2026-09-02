---
type: is
id: is-01m1hpb7rczw06tw7akqysdbtd
title: "Build asr-bench harness: manifests, adapters for all services + local models, per-language normalizer, scorer with bootstrap CIs, caching, cost/latency, report; smoke-test live on Gemini and Amazon Transcribe with Piper synthetic clips (Opus agent)"
kind: task
status: closed
priority: 1
version: 5
spec_path: docs/project/research/research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md
labels:
  - spike
  - asr
  - sandbox
dependencies:
  - type: blocks
    target: is-01m1hpb9qr3dd8fq3yxr6m1z8x
  - type: blocks
    target: is-01m1hpbafgpdrs8qc8qmgzrrqq
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:33:19.371Z
updated_at: 2026-09-02T20:13:27.887Z
closed_at: 2026-09-02T20:13:27.887Z
close_reason: "asr-bench harness delivered: attic/spikes/asr-bench (172 tests), report scratchpad/spike-asr-bench.md; smoke live on Gemini only (AWS key invalid, Google STT 401, others blocked); language-code negative control fails on Gemini; normalizer inflates ca WER"
resolution: null
duplicate_of: null
---
attic/spikes/asr-bench; report scratchpad/spike-asr-bench.md. Deepgram/OpenAI/AssemblyAI/Speechmatics/ElevenLabs/Gladia/Soniox/Rev adapters are implemented from docs and dry-run tested only: their endpoints are blocked by this sandbox's egress policy.
