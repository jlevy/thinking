---
type: is
id: is-01m1hpgbseaj3p0h7smg0rexxb
title: "Congreso XV-legislature per-speech dataset (OpenParliamentTV-Data-ES, 180 plenary sessions): document schema, licence and coverage; derive the text-side benchmark inputs — turn table with durations, roster, agenda candidate sets, dual-language flags, chair turns from original Diario HTML"
kind: task
status: closed
priority: 1
version: 6
spec_path: docs/project/research/research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md
labels:
  - data
  - sandbox
dependencies:
  - type: blocks
    target: is-01m1hpgctcdyv9mthqzy6k8665
  - type: blocks
    target: is-01m1hnqbgnm2c2n76tgg0r59cz
parent_id: is-01m1hn7gch0xb2sv9fw6a4qes1
created_at: 2026-09-02T18:36:07.341Z
updated_at: 2026-09-02T18:46:15.529Z
closed_at: 2026-09-02T18:46:15.528Z
close_reason: Documented in scratchpad/congreso-dataset-note.md and congreso-xv-stats.md
resolution: null
duplicate_of: null
---
Cloned to attic/data/OpenParliamentTV-Data-ES (gitignored). Inspected 2026-09-02: 180 XV-legislature plenary sessions (2023–2025), 10,569 speeches, 427 distinct speakers; per speech: people[] (name, group), agendaItem, dateStart/dateEnd (HH:MM; median 360 s, p10 60 s, p90 720 s, max 6,120 s), media.videoFileURI (direct MP4, duration), textContents (Diario text with page anchors, language tag), debug.confidence (1.0 for 9,399; 0.5 for 1,170 role-only chair turns). original/ has raw interventions JSON and Diario HTML incl. chair turns. Licence: Congreso aviso-legal on media/text; data repo declares none — check before publishing derived manifests. Chair-parser agent notified to extend its analysis; th-ikyq (S7/S9/S23) depends on this.
