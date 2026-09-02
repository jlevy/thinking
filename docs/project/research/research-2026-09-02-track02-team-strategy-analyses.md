---
title: Track 02 Team Strategy — Four Analyses in Full
description: The strategy analyses behind §12 of the Madrid Open dossier — a portfolio of five team bets, five architectures differing in where the speaker's name comes from, a differentiation memo, a hedging plan for a brief nobody has seen, and the design of a member knowledge base and multimodal identity stack
author: Claude Code
---
# Research: Track 02 Team Strategy — Four Analyses in Full

**Date:** 2026-09-02 (last updated 2026-09-02)

**Author:** Claude Code

**Status:** Complete

## Overview

This report carries, in full, the four analyses that §12 of the
[Madrid Open dossier](research-2026-09-02-madrid-open-reversa-challenge.md) summarises.
Each was produced independently from the dossier and its two companion reports, the
[research agenda](research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md)
and the
[tooling inventory](research-2026-09-02-parliamentary-transcription-tooling-inventory.md),
without seeing each other’s output.

They converged, which is the reason to keep all four rather than only the synthesis: all
four independently placed the chair’s announcement rather than the waveform at the
centre of the design, and all four made calibrated abstention with visible provenance
the second pillar. Where they differ is in emphasis and in the hour-by-hour detail that
§12 compresses away, which is what a team executing on the day would actually need.

The four are: a **portfolio** of five team-level bets with rubric arithmetic and cut
lists; five **architectures** distinguished by where attribution comes from; a
**differentiation** memo on what the field will miss; and a **hedging** plan for the
unknowns that survive until 09:00 on the day.

### A note on evidential status

Tags follow the companion reports: **(measured)** or a citation to the agenda’s §10
spike results means a number produced by a run in this programme; **(search)** means a
web-search snippet whose page was not opened; **[J]** or an explicit note marks the
analysis’s own judgment.
Several headline numbers, including the chair-parser floor, are measured on the *edited
record text* rather than on recognised audio, and are upper bounds until spike S9 runs.
Section references of the form “agenda §n” and “inventory §n” point at the two companion
reports; bare “§n” points at the dossier.

## 1. Strategy Portfolio: Five Bets for 3 October

**Constraints, verified in the dossier.** 09:00 presentation, 09:30 data, 14:00 lunch
with the clock running, 17:00 public checkpoint, 20:45 freeze, 21:00 live demos on
unseen data (dossier §4): 11.25 build hours.
Rubric 45/20/15/10/10 (dossier §5). Metric: “how fast the transcript is ready, and how
often the right speaker is named.
Measured on real transcripts” (dossier §10); only Luma adds “within five minutes across
five languages”. You inherit a live pipeline on two chambers from different parts of the
world (dossier §10), with no public code (dossier §9.1). Credits arrive on the day
(dossier §4). Teams are three (dossier §1); each split names three core roles and what a
fourth and fifth add.
Hours count from 09:30: hour 6 is 15:30, hour 9 is 18:30. Scores are my judgment unless
cited.

### 1. Read the Chair (evidence fusion for the name)

**Bet.** The name, not the words, is where the error lives (dossier §11.9), and the
cheapest lever on it is text the pipeline already produces: on two records of the
Congreso, chair announcements plus a continuation rule name 92–94% of regular turns and
93–95% of rostrum turns with no audio (agenda §10.1). The belief: most teams will tune
ASR or diarization and leave the chair unread.

**Build.** Keep the inherited transcript and diarization.
Add the 16-rule announcement parser ("tiene la palabra" is only 46% of hand-offs, bare
vocatives another 21%; agenda §10.1), a roster matcher (both surnames are 97–98% unique;
39% of members share a first surname; agenda §10.1, §10.10), the agenda as candidate set
(median 2 speakers per numbered item and 10–12 per debate section, against 350; agenda
§10.10), an LLM fallback behind the regex (hybrid 0.940 against 0.906 for regex plus
continuation, LLM called on 18.6% of turns at p50 0.86 s; agenda §10.8), and the
pipeline’s speaker score as tiebreak within the candidates.
Every named turn carries its evidence: announcement sentence and timestamp, agenda slot,
acoustic margin (dossier §11.10).

**Rubric arithmetic.** Metric 32/45: strong on the name wherever the chair speaks;
latency inherited. Restraint 13/20: abstention present but secondary.
Auditability 12/15: evidence chips are native.
Ship 6/10. Ambition 7/10. About 70. Beaten by Strategy 4 if chamber B’s chair does not
announce speakers and enrollment audio exists; by Strategy 3 if abstentions are scored
gently and inherited attribution is already above 90%.

**Plan.** 09:30–10:00 the four questions (dossier Rec.
2) and the raw output format.
10:00–11:30 run the pipeline unchanged on one session, score with a fixed-label scorer:
checkpoint “Baseline”.
11:30–13:30 parser on the pipeline’s ASR output for chamber A; measure coverage and
precision there, not on the record, an upper bound (agenda §10.1). 13:30–15:30 roster,
agenda constraint, continuation rule, fusion; rescore: checkpoint “Fused”, hour 6.
15:30–17:00 chamber B patterns: precision transfers across languages, coverage does not
(Galician 0.438 without per-language rules; agenda §10.1). 17:00 the stratified
before/after table. 17:30–19:30 LLM fallback and abstention threshold.
19:30–20:00 unseen-file rehearsal: checkpoint “Rehearsed”.
20:00–20:45 margin.

**Team.** A: parser and roster matcher.
B: pipeline integration, scorer, evidence output.
C: chamber B patterns, then demo.
A fourth reads 50 chair turns per chamber and derives rules from what the chair actually
says; a fifth owns owner liaison and the demo script.
Non-coders do the reading and the failure catalogue.

**Cut list.** Hour 6: drop the LLM fallback; drop the acoustic tiebreak (continuation
rule only). Hour 9: drop chamber B patterns; drop the evidence UI (evidence stays in
JSON).

**Demo.** A transcript where every name has a chip; click it and the chair’s sentence
plays. Beside it, the before/after table by turn length.
The sentence: “We did not change a model; we read the chair.”

**Failure modes.** The parser was validated on the edited record; live it must survive
ASR of exactly the rare accented surname (S9 unrun; agenda §10.1). Chamber B’s chair may
not name speakers, or not in a form the rules match.
A time-weighted metric erases short-turn gains (agenda D3).

### 2. Ready Before They Sit Down (bounded-lag streaming)

**Bet.** The metric’s first clause is speed and Luma names five minutes.
Compute is not the obstacle: `whisper-large-v3-turbo` runs 597× real time on an H100 and
a six-hour session diarizes in minutes (dossier §11.12); the obstacle is behaviour:
revision and finalization (agenda D10, §3.9). The belief: most teams will batch, and a
transcript appearing while the audio plays is the demo the room remembers.

**Build.** WhisperLiveKit with the SimulStreaming backend (inventory §2, stage 9);
turbo, the Whisper variant that does not loop on silence (agenda §10.5–10.6); VAD plus
the compression-ratio filter against hallucination (agenda §10.6). Streaming Sortformer
v2 reset per agenda window (agenda D2), Strategy 1’s regex for names, a two-minute
finalization pass, and a logger for finalization lag p50/p95, name revision rate and
first-versus-final name accuracy (agenda §4.5). If credits land, Speechmatics realtime
adds Catalan, Basque and Galician with a 50-identity cap that fits any agenda window
(inventory §6; agenda §10.10); Amazon Transcribe streaming is the anonymous fallback
(dossier §11.4).

**Rubric arithmetic.** Metric 28/45: the speed clause won outright, the name clause at
the inherited level plus regex.
Restraint 10/20. Auditability 9/15: the revision log is provenance of a kind.
Ship 8/10: Parlamento.ai’s eight concurrent sessions at p95 2.36 s on an 8 GB GPU size
it (agenda §3.3). Ambition 9/10. About 64. Beaten by Strategy 1 whenever latency is
scored as a threshold rather than a gradient.

**Plan.** 09:30–10:00 questions, above all how “fast” is measured.
10:00–12:00 one session file streamed through WhisperLiveKit, words on screen:
checkpoint “Words live”.
12:00–14:00 diarization windows and regex names.
14:00–15:30 finalization pass and revision logger: checkpoint “Names finalize”, hour 6.
15:30–17:00 chamber B and its language.
17:00 the live stream.
17:30–19:30 concurrency and abstention.
19:30–20:00 rehearsal on an unseen file at wall-clock rate.

**Team.** A: streaming stack.
B: names, finalization, logger.
C: front end and demo.
A fourth measures p95 lag by hand against the audio; a fifth keeps the vendor fallback
alive. Non-coders time the stream and script the demo.

**Cut list.** Hour 6: drop Sortformer (batch diarization per two-minute window); drop
concurrency.
Hour 9: drop chamber B’s language; drop the finalization pass (publish once,
marked provisional).

**Demo.** Split screen: audio playing, words at about three seconds, names appearing
then hardening with a “revised” mark, a gauge of p95 finalization lag under two minutes.
The sentence: “The record was ready before the speaker sat down.”

**Failure modes.** Archive replay is not the feed: no jitter, no encoder delay (agenda
§11.2, R7). Early-and-wrong names are visible to the room (agenda §4.5). No GPU and no
credits leaves CPU streaming of a Spanish-only model (agenda §10.4). If scoring is
offline, speed past the threshold buys nothing.

### 3. Defend Every Name (calibrated abstention as the product)

**Bet.** Fifty-five percent of the score is not the metric and all of it can be designed
before the data arrives (dossier Key Insight 1); the rubric’s headline is “knowing when
not to answer”, and a system naming 60% of turns at 99% may beat one naming 100% at 90%
(agenda §4.2). This strategy deliberately concedes coverage: it leaves the pipeline’s
accuracy alone and makes every output defensible.

**Build.** Per-turn posterior over the candidates plus an unknown mass; threshold set
for false alarms below 1/N per session, everything under it emitted as “unidentified
speaker, candidates {…}” (agenda §3.5; dossier §11.7). A coverage–accuracy curve with
the operating point marked (agenda §4.4). An evidence ledger per turn: audio span, ASR
confidence, speaker margin, source of the name.
Strategy 2’s hallucination guard.
A one-page ship story: USD 0.71 per audio-hour for raw ASR against the €1.65–2.85 per
minute the Congreso pays for a record (agenda §3.3; dossier §11.2), retention rules, a
reviewer queue.

**Rubric arithmetic.** Metric 25/45: coverage conceded.
Restraint 18/20. Auditability 14/15. Ship 8/10. Ambition 5/10. About 70, the lowest
variance of the five.
Beaten by Strategy 1 if abstentions count as errors at forced 100% coverage; by Strategy
2 on the demo line.

**Plan.** 09:30–10:00 questions; the deciding one is how an abstention is scored.
10:00–11:30 baseline run, per-turn scores logged.
11:30–13:30 fixed-label scorer and coverage curve on a held-out session: checkpoint
“Curve”.
13:30–15:30 calibrator, threshold, unknown output: checkpoint “Operating point”,
hour 6. 15:30–17:00 ledger and reviewer view.
17:00 the curve and one abstained turn.
17:30–19:00 hallucination guard and ship page.
19:00–20:00 rehearsal.

**Team.** A: scorer and calibration.
B: ledger and pipeline hooks.
C: reviewer view, ship page, demo.
A fourth hand-checks 100 turns to seed the calibrator; a fifth writes the security and
cost answers. Non-coders do the checks and rehearse the abstention story.

**Cut list.** Hour 6: drop the reviewer view (ledger stays JSON). Hour 9: drop the
calibrator (fixed margin threshold); the ship page becomes one slide.

**Demo.** The curve with the chosen point; then one turn where the system declines,
shows two candidates and the reason.
The sentence: “Every name we print we can defend, and every name we cannot, we do not
print.”

**Failure modes.** “Escalating everything does not win either” (dossier §10, Track 01
note); a threshold set on one session over-abstains on the next.
Judges who weight the number may read restraint as timidity.
Calibration from a handful of sessions is fragile (agenda §4.7).

### 4. The Chamber Knows Its Own (same-room acoustic identification)

**Bet.** Published open-set numbers come from cross-condition internet audio; in a
chamber, enrollment and test share the microphone chain and the room, a large,
unmeasured advantage (agenda D6). The Congreso archive is pre-segmented and named: 9,441
clips, 907 hours, 271 speakers with five or more speeches (agenda §10.10). The recipe is
specific: ReDimNet2-B6 (0.23% EER, MIT) embeddings, centroid enrollment, cosine,
adaptive s-norm with the other members as cohort, a logistic calibrator, false-alarm
threshold below 1/N (agenda §3.5). The belief: an ML-strong team can produce the first
in-domain attribution number on the day, high on long turns.

**Build.** Enroll only the members on the day’s agenda; the candidate set is the cheap
fix for a false-alarm curve running from EER 1.43% at 100 enrolled speakers to 3.04% at
700 (dossier §11.7). Identify the pipeline’s segments; add s-norm, calibrator,
abstention. Gate co-official turns: on the interpretation-mixed feed the embedding
follows the louder talker and flips at 0 dB, so attribute acoustically only below about
−6 dB and never across a turn end (agenda §10.7). Stretch: TS-ASR-Whisper with
enrollment conditioning, where SE-DiCoW reports a 52% relative tcpWER gain (agenda
§3.4).

**Rubric arithmetic.** Metric 20–38/45, the widest spread: 38 with named enrollment
audio for both chambers and clean segments, 20 if chamber B has no archive.
Restraint 11/20. Auditability 9/15: a cosine margin is thin evidence.
Ship 5/10. Ambition 8/10. Expected about 62. Beaten by Strategy 1 in every world where
the chair announces speakers; wins where the chair does not and turns are long.

**Plan.** 09:30–10:00 questions; the deciding one is whether named enrollment audio
exists for both chambers.
10:00–12:00 pull and embed clips for the agenda’s members only: checkpoint “Enrolled”.
12:00–14:00 cosine identification on the pipeline’s segments, TAA by duration bucket.
14:00–15:30 s-norm, calibrator, agenda constraint: checkpoint “Normalized”, hour 6.
15:30–17:00 abstention and the overlay gate.
17:00 TAA by duration.
17:30–19:30 short-turn work or DiCoW. 19:30–20:00 rehearsal.

**Team.** A: embeddings and scoring.
B: enrollment acquisition, data plumbing, GPU. C: integration, abstention, demo.
A fourth verifies enrollment labels by ear; a fifth builds the stratified table.
Non-coders check labels and clip quality.

**Cut list.** Hour 6: drop DiCoW; drop full-roster enrollment.
Hour 9: drop the calibrator (s-norm plus a fixed threshold); chamber B goes text-only
via the continuation rule.

**Demo.** TAA by duration bucket beside the VoxWatch curve, then a live clip identified
with its margin.
The sentence: “Enrolled from its own archive, the chamber recognizes its
members.”

**Failure modes.** No enrollment audio for chamber B. Short segments: EER rises about
46% relative from 3.6 s to 2.1 s (dossier §11.7). Cross-lingual shift for bilingual
members (agenda §3.5). Gated pyannote weights and no Hugging Face reach on the day
(inventory §11.3). Embedding all 907 hours is about 3 GPU-hours (agenda §11.2, R4),
hence agenda-only enrollment.
A wrong name with a confident margin is the worst rubric outcome.

### 5. Where the Name Is Lost (the instrument, not the system)

**Bet.** Nobody in this domain has ground truth for attribution; the production
operator’s published study measures agreement with paid APIs and has no speaker metric
(agenda §3.3). The organizers are hiring and say so (dossier §6), and the pipeline’s
probable author is in the room (dossier §8.1). This strategy deliberately does not chase
the 45%: it delivers the stratified error decomposition of the inherited pipeline
(agenda D4, §4.3) plus one fix on the largest stratum.
The belief: the most valuable thing to hand a challenge owner is the table showing where
his pipeline loses the name.

**Build.** A fixed-label scorer with IoU turn matching, because permutation-invariant
cpWER and DER score a transcript with every name wrong as 0.0 (agenda §10.2). Stratify
by duration, role, language, channel condition and announcement presence.
Measure the editorial gap if the reference is the record (only 65% of EuroSpeech’s
aligned segments fall within CER 20% of it; agenda §3.11), the hallucination rate
(expect about 10 words per hour gated; agenda §10.6) and speaker-count error.
Then fix one stratum and re-measure paired by session (agenda §4.7).

**Rubric arithmetic.** Metric 18/45. Restraint 12/20: the coverage curve is a
by-product.
Auditability 15/15. Ship 4/10. Ambition 8/10. About 57, plus the conversation
the score does not measure.
Beaten on the leaderboard by everything above; wins the hiring line if the decomposition
surprises the owner.

**Plan.** 09:30–10:00 questions.
10:00–11:30 baseline run, reference parsed into turns.
11:30–13:30 scorer adapted to their formats, first stratified table: checkpoint “Table”.
13:30–15:30 hand taxonomy of 100 errors by the non-coders: checkpoint “Taxonomy”, hour
6\. 15:30–17:00 the fix for the largest stratum.
17:00 the table. 17:30–19:30 paired re-measurement.
19:30–20:00 findings page and rehearsal.

**Team.** A: scorer.
B: pipeline runs and the fix.
C: findings page and demo.
Fourth and fifth do the taxonomy.
Non-coders carry this strategy: reading errors is the work.

**Cut list.** Hour 6: cut the taxonomy to 40 errors.
Hour 9: drop the fix; present the decomposition alone.

**Demo.** One table, before and after one fix, by stratum.
The sentence: “Here is where your pipeline loses the name, and here is the first stratum
we fixed.”

**Failure modes.** A reference format that takes hours to parse.
A decomposition the owner already knows.
Judges scoring the day as “did not build”.
The fix regresses another stratum with no time to show both.

### Comparison

| Strategy | Metric /45 | Restraint /20 | Audit /15 | Ship /10 | Demo /10 | Total | Robustness to unknowns |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 Read the Chair | 32 | 13 | 12 | 6 | 7 | 70 | High; exposed to chamber B’s chair habits |
| 2 Ready Before They Sit Down | 28 | 10 | 9 | 8 | 9 | 64 | Medium: needs a GPU and latency scored as a gradient |
| 3 Defend Every Name | 25 | 18 | 14 | 8 | 5 | 70 | High; exposed only to how abstention is scored |
| 4 The Chamber Knows Its Own | 20–38 | 11 | 9 | 5 | 8 | 62 | Low: needs enrollment audio, a GPU and a clean channel |
| 5 Where the Name Is Lost | 18 | 12 | 15 | 4 | 8 | 57 | High on delivery, low on score |

### The pick

Strategy 1, with Strategy 3’s abstention and ledger as its floor.
They combine: the fuser produces the evidence the ledger displays, and the threshold is
set on the fused posterior.
The reasons are measured: the text-side floor is 92–94% of regular turns (agenda §10.1),
which any acoustic identifier must beat rather than chance (agenda Key Insight 12); it
needs no GPU, credits or enrollment audio; and the dossier’s reference architecture
places it first after microphone metadata (dossier §11.16).

Strong on ML: layer Strategy 4 on as the acoustic tiebreak, only once 09:00 confirms
named enrollment audio for both chambers, agenda-only; otherwise ML effort goes to the
calibrator and the short-turn stratum, where text reaches only 49–55% (agenda §10.1).
Strong on product engineering: make Strategy 3 primary, borrow only Strategy 1’s regex,
and add Strategy 2’s live stream if someone has run WhisperLiveKit before.

Pipeline good, meaning the 11:30 baseline already names above 90% of turns: Strategy 1’s
marginal gain shrinks, so choose Strategy 3 with Strategy 2 as the demo.
Pipeline poor: Strategy 1, because its evidence does not depend on the pipeline’s
acoustics.
Poor enough that the metric is out of reach for everyone: Strategy 5 turns the
day into the conversation the organizers say they want (dossier §6).

### The common floor

Whatever the bet, do these.
Ask at 09:00 which audio channel the pipeline consumes and whether the floor feed is
separable (dossier §11.3), whether the scored reference is verbatim or the edited record
(dossier §11.13), whether the name is scored per turn, per word or per unit time
(dossier §11.9), whether microphone metadata is in the pipeline (dossier §11.3), and two
the dossier omits: how an abstention is scored, and how speed is measured.
Run the inherited pipeline unchanged on one session and score it before 11:30; that is
the thin slice, and everything after is a paired comparison against it (agenda §4.7).
Score with fixed labels; the permutation-invariant forms forgive a wrong name entirely
(agenda §10.2). Never emit a nearest neighbour below threshold; emit “unidentified
speaker” with the candidates (dossier §11.7). Attach evidence to every name.
Gate the decoder with VAD and the compression-ratio filter, and use turbo: ungated
whisper-small produced 401 hallucinated words per hour of non-speech and VAD alone
passed 92.5% of babble (agenda §10.6). Never attribute acoustically on an
interpretation-mixed segment; flag it (agenda §10.7). Keep a local path for every paid
service; credits arrive on the day (dossier §4). Do not present Spanish transcription as
the contribution: both chambers already run it in production (dossier §11.14, Rec.
9). Rehearse on a file nobody has seen by 20:00; a system that fails live scores nothing
on the other 90% (dossier Rec.
6).

## 2. Five Architectures: Where the Name Comes From

Prepared 2026-09-02 for the Madrid Open Track 02 build on 3 October 2026, from the three
research reports in `docs/project/research/`, cited as dossier §n, agenda §n and
inventory §n. “Measured” means a spike produced the number (agenda §10) or it was read
from a primary repository; “proposed” means untested.
The build window is 11.25 hours, 09:30 to 20:45, with a public checkpoint at 17:00,
about 7.5 hours in (dossier §4). The rubric: 45% metric, 20% restraint, 15%
auditability, 10% shippability, 10% ambition and demo (dossier §5).

### The premise all five share

Three findings fix the frame.

- Words are cheap, names are not.
  `whisper-large-v3-turbo` sits inside the paid panel’s own disagreement on Spanish
  parliamentary clips, 3.25% against 5.10% (agenda §3.3). The only in-language
  named-attribution result is DER 37.2% with identity on RTVE (dossier §11.8); published
  cpWER runs 30 to 35 (dossier §11.9).
- A text-only floor exists and is measured.
  The chair-announcement parser names 93 to 95% of rostrum turns and 92 to 94% of all
  regular turns with the continuation rule, on two records of the same chamber, with no
  audio (agenda §10.1). Every acoustic design must beat that, not chance.
- Five minutes is an architecture constraint, not a compute one (dossier §11.12): the
  risks are a batch job with no recovery window and names that change after publication.

Every architecture shares a spine: ingest, Silero VAD (code MIT, weights Apache-2.0),
ASR, an attribution stage, and one JSONL record per turn carrying name or `UNKNOWN`,
candidates, text, timestamps, evidence, confidence and revisions (agenda §3.1). All five
score through `attrscore` (agenda §10.2). They differ in the attribution stage.

### A1. Chair-Spine (text-first; conservative)

**Theory.** The chair names the next speaker aloud; the name is in the transcript.
Audio supplies words and change points.
A voice never supplies a name.

**Data flow.** VAD → `whisper-large-v3-turbo` on faster-whisper (weights Apache-2.0,
runtime MIT; int8 on CPU) with the compression-ratio filter → chair-turn detector → the
16-rule parser plus RapidFuzz (MIT) over the Congreso `diputados` open data and a dated
portfolio table → continuation rule → LLM fallback on the chair turns the regex cannot
resolve: `gemini-2.5-flash` on day credits, or Salamandra-7B (Apache-2.0) or Latxa (MIT)
locally → pyannote community-1 (CC BY 4.0, gated) change points, used only to detect a
speaker change with no announcement → abstain.

**Evidence.** Measured on the record (agenda §10.1, §10.8): rostrum 0.948 and 0.931 on
ParlaMint-ES and Congreso XV; 0.972 and 0.971 over 600 words; 0.936 and 0.922 on all
regular turns with continuation; the hybrid regex-then-LLM projects 0.940 across all
12,179 XV regular turns, calling the LLM on 18.6% of chair turns at p95 1.22 s and USD
0.18 per 1,000. Not measured: what survives ASR (S9 unrun); the live hand-off is clipped
and spoken over, so the record figure is an upper bound.

**Build order.** Hour 3: parser and roster from the spike code on the released
transcripts; turbo on one session; JSONL out.
Hour 6: change-point trigger, continuation, LLM fallback, coverage curve, provenance
page (the 17:00 checkpoint state).
Hour 9: WhisperLiveKit plus SimulStreaming (Apache-2.0, MIT) with the revision logger;
Catalan and second-chamber rules.

**Latency.** Text is milliseconds; the LLM adds about a second on a fifth of chair
turns.
ASR is the only clock: turbo runs 35.6 audio-hours per hour on an 8 GB GPU (agenda
§3.3), so a six-hour session finalises in about ten minutes batch, or seconds of tail if
streamed. On four CPU cores whisper-tiny ran at 0.20 real-time factor and medium ran out
of memory (agenda §10.4), so a CPU-only day means the Spanish-only NeMo CTC at 0.046,
and co-official turns get no words.

**Restraint and audit.** Abstention is structural, not a threshold: no announcement and
no continuation gives `UNKNOWN`; a role-only “señor ministro” without the agenda heading
gives candidates and no name; a first-surname-only match that collides (two in five
members share one) gives `UNKNOWN` unless the group disambiguates.
Confidence is rule-class precision from the record (canonical 1.000, both surnames
0.999, role-only 0.86 to 0.94) scaled by the S9 survival factor.
Provenance is the chair sentence, its timestamp, the rule id, the roster id and the
alternatives. A judge can read it.

**Fails when; fallback.** No announcement: turns under 30 words score 0.55 and 0.49
(agenda §10.1). ASR garbles the surname.
The second chamber uses another formula (Galician coverage 0.438 without per-language
rules).
Fallback: the LLM parser is language-agnostic given a roster; otherwise `UNKNOWN`
with candidates.

**Traps.** Every LLM has read the Diario; score any LLM stage on post-cutoff sessions
only (agenda §11.1 row d, §11.6). 0.93 to 0.95 is not a live number until S9 runs.

### A2. Cascade (acoustic-first modular)

**Theory.** Voice is identity.
Enroll every candidate from the archive, match each diarized segment, abstain below a
calibrated threshold.

**Data flow.** VAD → turbo → pyannote community-1, or DiariZen (weights CC BY-NC 4.0)
where it wins head to head → ReDimNet2-B6 (MIT, 0.23 EER Vox1-O) or WeSpeaker
W2V-BERT2.0-MFA (Apache-2.0, 0.138) → centroid enrollment from Archivo per-intervention
MP4s, evaluation sessions excluded → cosine with adaptive s-norm, roster as cohort →
logistic calibrator on in-domain trials → agenda candidate set → threshold for false
alarms below 1/N per session → name or `UNKNOWN` (agenda §3.5).

**Evidence.** Measured elsewhere, not here: open-set EER rises from 1.43% at 100
enrolled to 3.04% at 700, and about 46% relative from 3.59 s to 2.05 s segments (dossier
§11.7). The agenda cuts 350 to a median 10 to 12 per debate section and at most 4
speakers for 66.7% of numbered items (agenda §10.1, §10.10). Measured here on TTS
(agenda §10.4): the plumbing runs, every identification number is fingerprinting, and
CAM++ at 47% under 3 s is the one transferable warning.
No in-domain trial has run (S13 unrun).

**Build order.** Hour 3: pre-built centroids loaded; pyannote plus embeddings on one
session; naive top-1. Hour 6: AS-norm, calibrator, candidate set, coverage curve.
Hour 9: Streaming Sortformer v2 (CC BY 4.0, four slots, state reset per window;
inventory §2) for live change points.

**Latency.** pyannote community-1 runs 31 s per audio hour on an H100 (dossier §11.12);
pyannote 4.0.3 peaked at 9.54 GB VRAM on a 72-minute file (dossier §11.6), so chunk per
agenda item. Embeddings on four CPU cores: CAM++ 0.099, ERes2Net 0.199 (agenda §10.4).
The chain is well under real time on any GPU and about 0.4 on CPU with Spanish-only ASR.

**Restraint and audit.** A calibrated log-likelihood ratio with an explicit unknown mass
(agenda §4.4); threshold from the coverage curve; actDCF against minDCF as the guard.
Provenance: enrollment clip ids, score, cohort statistics, candidate set.
Weakness: a cosine score is not legible to a judge.

**Fails when; fallback.** Without in-domain trials the threshold is a guess.
The overlay inverts identity at 0 dB within ±0.6 dB (agenda §10.7): attribute
acoustically only below about −6 dB estimated ratio and never past the turn end.
Fallback: A1 for names, diarization kept for change points.

**Traps.** Enrollment overlapping evaluation sessions invalidates the run (agenda §11.2
R1). Co-official archive clips may enroll the interpreter (S1 unrun).
Extractors pretrained on VoxPopuli (WavLM-base-plus-sv) if the demo set is European
Parliament audio (inventory §11.2).

### A3. Skeleton (record-structure-first)

**Theory.** The session has a published shape before it starts.
Attribution is filling slots in that shape; audio only says which slot you are in.

**Data flow.** Orden del día plus the record’s intervention conventions → a session
skeleton: ordered agenda items, each with an expected speaker sequence (question time:
asking deputy, then the minister named in the heading; debates: proponent, then
spokespersons in known order) → turbo ASR → ASR text and change points aligned into
slots, in the EuroSpeech two-stage CER pattern (licence TBD) or a simpler slot HMM →
slot id gives the name → `UNKNOWN` off-skeleton.

**Evidence.** Measured: agenda windows are small (median 2 per numbered item, at most 10
for 90.7%; agenda §10.10); the per-question heading names the addressed minister and
holds essentially all of the LLM parser’s residual error (agenda §10.8);
OpenParliamentTV’s surname-sequence merge matches about 95% of substantive speeches
(inventory §2, GPL-3.0). Not measured: how often the live order follows the published
one.
In text the constraint adds only +0.012 to +0.016 on top of the chair parser (agenda
§10.1); as a primary signal this is proposed.

**Build order.** Hour 3: skeleton builder from the released agenda.
Hour 6: ASR aligned into slots; scoring.
Hour 9: chair parser as slot-transition evidence; backfill.

**Latency.** Alignment is seconds; ASR as A1. The skeleton can emit the expected name at
slot start, before anyone speaks, and revise; first-versus-final name accuracy (agenda
§4.5) is the number to show.

**Restraint and audit.** Abstain when alignment CER exceeds a threshold, when slot order
is violated, and for every turn the skeleton lacks: interjections, points of order, and
the chair, 61.5% of Diario speaker markers (agenda §10.10). Provenance is an agenda item
id and a public document.

**Fails when; fallback.** The agenda changes on the day.
The second chamber publishes no machine-readable agenda.
Fallback: the skeleton becomes A1’s candidate-set prior, which is what agenda §10.1
measured.

**Traps.** The `intervenciones` open data is published after the session; a demo that
reads it is reading the answer key.
The Diario-derived turn list has no sub-minute stratum (agenda §10.3, §10.10), so a
skeleton system scored against the Diario looks better than it is.

### A4. PERCOL-2026 (multimodal)

**Theory.** Identity is on screen: the camera cuts to the speaker, the chyron names
them, the face matches the gallery.

**Data flow.** Video at 1 fps → PaddleOCR PP-OCRv5 (Apache-2.0) on the chyron region →
RapidFuzz to roster → Light-ASD (MIT, mAP 94.06 AVA) for the visible talker →
facenet-pytorch (MIT; InsightFace is stronger but research-only) against a gallery from
the `diputados` photos → fusion with the chair parser and any acoustic score, rules
first, then an LLM in the DiarizationLM pattern → abstain.

**Evidence.** REPERE’s PERCOL system reached EGER 24.4% supervised by fusing voice, face
and overlay OCR (agenda §3.6); the Albayzin 2020 multimodal track beat audio-only
enrollment (dossier §11.8). Not measured: chyron presence on live against archive video
(S3 unrun); the on-camera fraction for turns under 10 s (S8, agenda §11.2 R10).

**Build order.** Hour 3: OCR on 30 minutes of released video, a yield count, the face
gallery. Hour 6: ASD and face on one session, fused into A1. Hour 9: fusion rules,
coverage curve, revision.

**Latency.** 1 fps OCR and ASD over 25 hours “in a few GPU-hours” (agenda §11.2 R10),
well under real time on a GPU; on CPU, OCR at 1 fps is feasible and ASD is unmeasured.

**Restraint and audit.** Abstain when the OCR string does not match a member above
threshold, when no face is on camera, when sources disagree.
Provenance is the frame timestamp, the crop, the OCR string and the score: the most
demo-legible evidence of the five.

**Fails when; fallback.** No video in the pipeline; chyrons absent on the live feed; the
director shows the chamber, not the interjector.
Fallback: OCR alone as a fuser input to A1, an hour’s work.

**Traps.** Archive video with chyrons the live feed lacks; InsightFace weights are
research-only, admissible here (inventory §7).

### A5. Enrolled-Joint (joint SA-ASR; ambitious)

**Theory.** One model, conditioned on the candidates’ enrollment, emits words and
speaker structure together, so overlap and short turns are handled natively rather than
by a cascade whose errors compound (dossier §11.5).

**Data flow.** VAD → pyannote masks → TS-ASR-Whisper / DiCoW (code Apache-2.0; weights
CC BY 4.0 per the card, unverified; bundled DiariZen weights CC BY-NC 4.0) or SE-DiCoW
self-enrolment → per-speaker transcripts → naming step: ReDimNet2 embedding of each
stream against the candidate set’s archive profiles → roster id or `UNKNOWN`.
MOSS-Transcribe-Diarize 0.9B (Apache-2.0) as the long-form baseline, chunked by agenda
item under its 90-minute cap.

**Evidence.** DiCoW cpWER 18.1% on AMI-SDM; SE-DiCoW cuts tcpWER 52.4% relative against
DiCoW through enrollment conditioning; Dixtral macro cpWER 15.4 against 44.4 for Gemini
3.0 Flash (agenda §3.4). All English meetings, nothing on any Iberian language; MOSS’s
podcast cpCER 7.37 is the nearest regime, and Parlamento.ai discarded MOSS on throughput
before producing a quality number (agenda §10.5). S18 unrun.
No system outputs names from audio alone (agenda §3.4); the naming step is A2’s matcher.

**Build order.** Hour 3: DiCoW on one ten-minute clip with pyannote masks; MOSS on the
same. Hour 6: naming step plus scoring.
Hour 9: agenda-item chunking.
Offline only; streaming is not attempted.

**Latency.** Agenda §11.2 R8 budgets five sessions in 1 to 2 GPU-hours, roughly 12 to 25
times real time; no CPU path.
Bounded lag by per-item chunking; finalization is one chunk.

**Restraint and audit.** The joint model emits no calibrated identity score; restraint
is bolted on through the naming step’s LLR, and the evidence is “the model said so” plus
an embedding score. Weakest of the five on the 35%.

**Fails when; fallback.** Dependency setup (DiariZen weights, Hugging Face gating) eats
hours. Anonymous labels without a naming step.
Whisper-family hallucination inherited.
Fallback: A2.

**Traps.** Permutation-invariant cpWER scores every name wrong as 0.0000 (agenda §10.2);
report fixed-label. Paper numbers come from overlap-heavy meetings and do not transfer.

### Decision table

| Situation on the day | Build | Reason |
| --- | --- | --- |
| Pipeline consumes the public mixed stream; es plus co-official; no GPU | A1 with the Spanish-only NeMo CTC for words; co-official turns flagged `UNKNOWN` (mixed channel) | Overlay flips at 0 dB (agenda §10.7); only Spanish CTC fits four cores (agenda §10.4) |
| Public stream; one 8 GB GPU | A1 spine; A2 as tie-break on rostrum turns only, gated below −6 dB estimated ratio; turbo for words | turbo 35.6 audio-h/h; A2 needs the pre-built calibrator |
| Isolated floor feed available (Q-001) | A1 plus A2 in full; A5 only on an H100 with DiCoW pre-installed | Enrollment pays once the overlay is gone |
| Conference-system events exported (CoCon, DICENTIS) | A join, then A1 for the residual | A database join, not ML (dossier §11.3) |
| Video with chyrons in the pipeline | A4’s OCR as a fuser input to A1; the rest only if S3 yield is high | S3 decides in hour 1 |
| Second chamber non-Spanish | A3 skeleton if a machine-readable agenda exists, else A1 with the LLM-only parser; A2 if GPU | Regex coverage 0.438 without rules (Galician, agenda §10.1); the LLM is language-agnostic given a roster |
| Scored reference is the edited Diario | Add NeMo es ITN (Apache-2.0); keep the verbatim layer; do not build R6 | Dossier §11.13; agenda §3.11 |

### Pre-build before the event

1. Chair parser and roster matcher, packaged, with Catalan rules and the rule-class
   precision table; the hybrid LLM prompt; regression on the 12,179-turn XV table
   (agenda §10.1, §10.8).
2. Run S9 now: 200 announcement sentences, TTS to Whisper to parser; below 60% survival,
   pre-build the phonetic matcher (agenda §11.7).
3. `attrscore` 0.1.0 as a one-command report: IoU matching, fixed-label cpWER, coverage
   curve, revision metrics (agenda §10.2).
4. Candidate-set builder from the orden del día; the per-question heading extractor,
   empty for 42% of rows today (agenda §10.8).
5. Enrollment: the 271 XV speakers with five or more speeches, from 907 hours of unique
   clips; needs a box with congreso.es access, which returns 403 to bare requests
   (inventory §2). Cached ReDimNet2 centroids, S13 trials, calibrator, threshold.
   Without this, A2 does not run.
6. Hallucination guard: VAD plus compression ratio above 2.4 plus a babble-aware VAD
   threshold; expect about 10 words per hour, not 5 (agenda §10.6).
7. Provenance schema and the demo page: per named turn, the chair sentence or frame; per
   `UNKNOWN`, the candidates.
8. Streaming skeleton: WhisperLiveKit with SimulStreaming and the revision logger
   (finalization lag, name revision rate, first-versus-final accuracy; agenda §4.5).
9. Mirrored weights and a container: turbo CT2, pyannote community-1 with the gate
   accepted, ReDimNet2-B6, Light-ASD, PaddleOCR. Credits arrive on the day (dossier §4).
10. The 09:00 questions: which audio channel (Q-001); which two chambers and five
    languages (Q-002); verbatim or Diario as the scored reference; per-turn accuracy or
    cpWER as the metric (dossier §11.9, §11.13).

### What eats the day

- The live hand-off. Every A1 number is an upper bound from a clean record sentence; S9
  has not run. If the surname token does not survive ASR, the floor drops and nothing
  else compensates.
- Short turns. Text gives 0.49 to 0.55 under 30 words (agenda §10.1); EER grows 46%
  relative from 3.6 s to 2 s (dossier §11.7). The Diario-derived reference has no
  sub-minute stratum, so the stratum cannot even be scored against the record (agenda
  §10.3, §10.10).
- The hallucination guard.
  VAD passes 92.5% of babble; decoded units inside murmur hallucinate at 3,216 words per
  hour; `no_speech_prob` cannot be tuned at small without losing 14% of real words; the
  full conjunction costs about 30% of speech (agenda §10.6). Applause produces
  “aplausos” and “muchas gracias”, plausible record text.
- The scorer. Raw-overlap turn matching misassigns a 2 s interjection inside a 4-minute
  turn, a 16-point overall error and 51 on interjections on a perfect hypothesis;
  permutation-invariant cpWER and DER score every name wrong as 0.0; pyannote’s collar
  must be 0.5 for the NIST 0.25 s protocol (agenda §10.2).
- The overlay. Identity inverts at 0 dB; LID fails at or before the embedding; nothing in
  the literature separates interpretation from source (agenda §3.2, §10.7). The only
  11-hour move is to flag mixed segments and abstain.
- Plumbing. The default-named `silero_vad.onnx` over-merges whole exchanges; the NeMo CTC
  model drops segment-initial words on 71 of 179 turns; pyannote 4.0.3 needs six times
  the VRAM of 3.3.2 on long files (agenda §10.4, dossier §11.6).
- Revision. The chair resolves an identity after the turn begins; a system that names
  early must backfill.
  Without the logger, the transcript at T+5 disagrees with the one at T+30 (dossier
  §11.12).

### Verdict

Build A1, the Chair-Spine.
Add A2 as a gated tie-break on rostrum turns only if a GPU is present and the calibrator
was built before the event; add A4’s chyron OCR only if the pipeline carries video and
the hour-1 yield is high.
A3 folds into A1 as its candidate-set prior.
A5 is the right research direction (agenda §7 R8) and the wrong eleven-hour build: no
Iberian evaluation, no calibrated identity score, and a dependency chain nobody has
stood up.

## 3. Differentiation: Where the Unfair Advantage Is

Date 2026-09-02. Citations (dossier §n, agenda §n, inventory §n) refer to the three
research reports.
“Measured” marks a number a spike produced or a primary source reports;
everything else is this memo’s judgment.
Assumed crowd: Whisper, a diarizer, cosine against an enrollment set, MeetEval cpWER.

### 1. Ten differentiating ideas, ranked by expected value in twelve hours

**1. Text-first attribution: the chair names the speaker, audio breaks ties.** Insight:
on two independent records of the Congreso, a 16-rule parser of the chair’s hand-off
("tiene la palabra", bare vocatives, Catalan “té la paraula”) plus the rule “no
announcement means the same speaker continues” names the right member on 93–95% of
rostrum turns and 92–94% of all regular turns with no audio (measured, agenda §10.1). A
regex-first, LLM-fallback hybrid projects to 0.94 (measured, §10.8). Why others miss it:
they start from the waveform, and no published system parses floor-yielding phrases
(agenda §3.7). Cost: the parser exists in `attic/spikes/chair-parser` (1,596 lines);
porting it to ASR output with fuzzy surname matching is 2–3 h. Wins: the 45% metric on
the turns that carry the time, plus auditability, since the evidence is a quoted
sentence at a timestamp.
Why it might not work: 0.93–0.95 is measured on the edited record, an upper bound; live
hand-offs are clipped and the surname is the token ASR gets wrong (S9 never ran); two in
five members share a first surname (measured, §10.1). If the chambers are not Spanish
(Q-002) the structure transfers but the regexes do not.

**2. Score yourself with fixed-label metrics and a coverage–accuracy curve, and ship the
scorer.** (Correctness demonstration.)
Insight: with every word right and every name rotated to another member, MeetEval cpWER
and DER both score 0.0000; fixed-label cpWER scores 1.49 and per-turn accuracy 0.0
(measured, agenda §10.2). Why others miss it: they install meeteval and trust the
number. Cost: `attrscore` exists (3,527 lines, 71 tests); 1 h to wire it in and print
TAA@{100,95,90}. Wins: honesty at the 17:00 checkpoint, and the coverage curve is
restraint as a picture rather than a sentence (20% and 15%). Why it might not work: if
the organizers score permutation-invariantly, our own numbers look worse than the
leaderboard; rehearse the explanation.

**3. A provenance card on every name, and an explicit “unidentified, candidates {…}”
output.** (Product.)
Insight: one mechanism serves the metric, the 20% restraint line and the 15%
auditability line: each attribution carries its evidence (chair sentence at T, agenda
slot, acoustic margin against the cohort, channel flag) and a calibrated confidence;
below threshold it emits the candidate list, not the nearest neighbour (dossier §11.10,
§11.16). Reversa sells a traceable chain, and the judges are three law graduates
(dossier §8.1). Why others miss it: a name string is the natural output of a cosine
match. Cost: 2 h of UI plus a JSON schema.
Wins: 35% of the rubric with no model work.
Why it might not work: an uncalibrated confidence is theatre, so check ECE on held-out
sessions first (agenda §4.4); UI time competes with the metric.

**4. Agenda-constrained candidate sets with a per-session false-alarm budget.** Insight:
the order of business gives a median of 2 distinct speakers per numbered item, 10–12 per
debate section and 37–39 per sitting against a 350-member chamber, a 12–25× reduction
before anyone speaks (measured, agenda §10.1, §10.10). Open-set EER roughly doubles from
100 to 700 enrolled speakers (agenda §3.5), so the naive design sits on a known-bad
operating point. Recipe (agenda §3.5): ReDimNet2-B6 centroids, cosine, adaptive s-norm
with the other members as cohort, a logistic calibrator, a threshold at false-alarm rate
below 1/N per session.
Why others miss it: they enroll everyone.
Cost: 2 h if the agenda is in the provided data.
Wins: metric on short turns, restraint.
Why it might not work: text-only, the constraint added only +0.012–0.016 because the
chair’s formula is already discriminative (measured, §10.1), so the gain is on the
acoustic path; interjectors and the chair are not on the agenda; the same-room advantage
is unmeasured (agenda D6).

**5. Hallucination guard: compression ratio plus VAD, and turbo rather than the
fine-tunes.** Insight: ungated whisper-small emits 401 hallucinated words per hour of
non-speech, and the applause output is plausible chamber language ("muchas gracias",
“aplausos”); VAD alone passes 92.5% of babble; the compression-ratio filter cuts the
rate to 96 per hour at no WER cost, the full conjunction to 1.8 per hour at a 30% speech
cost (measured, agenda §10.6). `whisper-large-v3-turbo` is the only Whisper variant that
does not loop on silence in the operator’s study; the Catalan 3CatParla fine-tune hit
238% on one clean-silence clip (measured, §10.5). Why others miss it: VAD feels like the
fix, and the leaderboard points at the fine-tunes.
Cost: 1 h. Wins: restraint (invented words in an official record are the worst failure)
and insertions in the metric.
Why it might not work: thresholds were measured at whisper-small on synthetic streams;
retune on turbo first.

**6. Keep two text layers, verbatim and record-form, and ask which one is scored.**
Insight: an official record is edited; verbatimizing official text was worth 9 WER
points on Europarl-ASR, and a third of EuroSpeech segments differ from the record by
more than 20% CER (agenda §3.11). Why others miss it: they optimize against whatever
reference they are handed.
Cost: 1 h to keep both, with a prompted normalizer as the record layer.
Wins: potentially the largest single WER swing, and the two layers are an audit trail.
Why it might not work: the stated metric is speed and speaker naming (dossier §10);
words may not be scored at all.
A normalizer that has read the Diario is contamination.

**7. Bounded-lag delivery with revision accounting.** Insight: five minutes is not
throughput (a six-hour session is about 40 s of GPU for ASR, dossier §11.12); it is a
behaviour: publish at T+30 s, finalize at T+2 min, log finalization lag, name revision
rate and first-versus-final name accuracy (agenda §4.5). Why others miss it: they batch
at session end with no recovery window.
Cost: 3 h with WhisperLiveKit, SimulStreaming and Streaming Sortformer reset per window
(inventory §2), plus a revision logger nobody has published.
Wins: the latency half of the metric, shippability, demo.
Why it might not work: archive MP4 makes replay a simulation; streaming decode is worse
than batch; it is the riskiest item to have half-built at 17:00.

**8. Detect interpreter-mixed segments and refuse acoustic attribution on them.**
Insight: on the public Congreso feed a co-official intervention carries the interpreter
over the floor; a mid-turn embedding flips to the interpreter at 0 dB within ±0.6 dB,
LID fails before the embedding does, and the only margin is the clean head at about +1.5
dB per second of lag (measured on TTS, agenda §10.7). Rule: attribute acoustically only
below about −6 dB, prefer windows that include the pre-interpretation head, never cross
the turn end. Enrolling the dozen or so interpreters buys a mixed-condition flag (agenda
§3.2). Why others miss it: they do not know the feed is mixed (dossier §11.3). Cost: 2–3
h. Wins: restraint made visible on the hardest input; ambition.
Why it might not work: it matters only if the provided audio is the mixed feed (Q-001);
co-official passages are 0.6% of XV speeches (measured, §10.10), so the metric impact is
small; the curve is synthetic.

**9. Price the pitch against the incumbent.** (Presentation.)
Insight: the Congreso pays Pangeanic €1.65–2.85 per minute for a record; raw API
transcription costs USD 0.71 per audio-hour; the two orders of magnitude between them
are attribution, editing and publication (agenda §3.3, dossier §11.14). Why others miss
it: they pitch transcription as if nothing existed.
Cost: 30 min for a cost line on screen and one sentence naming the incumbents.
Wins: shippability 10%, and credibility with founders who are hiring (dossier §6). Why
it might not work: for a non-Spanish chamber the figures are illustrative.

**10. Leave a conference-system slot in the fuser and report the exception rate.**
(Product.) Insight: Televic CoCon and Bosch DICENTIS expose microphone-on and
delegate-identity events; where they exist, attribution is a join and the problem
shrinks to interjections and the chair (agenda §3.8, R11). Why others miss it: they
assume the acoustic route is the only one.
Cost: 30 min for a stub input and the share of turns a seat map would resolve.
Wins: shippability and the “doing it for real” conversation.
Why it might not work: untestable on public data; overclaimed, it reads as vapour.

Ideas 1 to 5 will score; 6 and 7 depend on answers at 09:00; 8 is clever and small on
the metric; 9 and 10 are cheap and mostly buy credibility.

### 2. Three demo moments

**A. “The chair told us.”** On screen: the live transcript; a name appears on a rostrum
turn with a citation chip; clicking it plays the four seconds where the chair says
“Tiene la palabra el señor X” and shows the agenda slot and the acoustic score as
secondary evidence. Presenter: “We did not recognise his voice first.
We heard the chair say his name, and here is the sentence.
The voice agreed two seconds later.”
Aimed at auditability (15%) and the metric.

**B. “It declined, then it earned the name.”** (Restraint visible, not described.)
On screen: a co-official intervention or a sub-3-second interjection.
The card shows the channel flag ("interpreter mix, estimated ratio near 0 dB"), the
acoustic margin below threshold, and “Unidentified speaker.
Candidates from the order of business: X, Y, Z.” Three seconds later the chair’s
hand-off resolves it; the card updates with the citation and the revision counter ticks.
The presenter says nothing about restraint; the judge watches the system refuse and then
earn the name. Then a threshold slider: dragging it shows coverage falling and accuracy
rising on held-out sessions, and a judge picks the operating point.
Aimed at restraint (20%).

**C. “Rotate every name, score zero.”** On screen: two scoreboards on the same output
with every speaker deliberately rotated to another member.
Left: MeetEval cpWER 0.00, DER 0.00. Right: fixed-label cpWER 1.49, TAA 0.00,
identification error rate 0.999. Presenter: “The default tool cannot see a wrong name.
Ours can. It is versioned in the repo with 71 tests, and every number we showed today
came from it.” Aimed at auditability and shippability; it also reprices every other
team’s leaderboard claim.

### 3. The contrarian section: where the crowd loses points without noticing

- **Cosine against 350 enrollments names the nearest neighbour.** EER roughly doubles
  between 100 and 700 enrolled speakers; short segments cost 46% relative EER from 3.59
  s to 2.05 s; language switching shifts the same speaker’s embedding (dossier §11.7,
  agenda §3.5). The correct low-margin output is “unknown”, which is also 20% of the
  score.
- **MeetEval cpWER and DER forgive a wrong name** (idea 2). A team optimizing them is
  optimizing diarization consistency, not attribution.
- **DER hides exactly the turns the metric counts.** A 2 s interjection inside a
  4-minute speech is under 1% of the time, and the literal maximal-overlap matching rule
  scored 0.49 on interjections for a perfect hypothesis (measured, §10.2). Match per
  turn by intersection over union.
- **VAD is not a hallucination guard.** It passes babble, where decoded units
  hallucinate at 3,216 words per hour (measured, §10.6).
- **The leaderboard fine-tunes are fragile and two are contaminated.** All three
  co-official fine-tunes lose to generic turbo on the operator’s sample, and the Open
  ASR Leaderboard correlates at ρ −0.14 with parliamentary agreement (measured, §10.5).
  `BSC-LT/whisper-large-v3-LoS-punctuated` declares ParlamentParla and
  Nos_ParlaSpeech-GL in training; the Basque specialists declare Basque Parliament
  (inventory §11.2). If a held-out chamber is one of those, a contaminated model looks
  good for the wrong reason.
- **Spanish WER is settled.** Turbo differs from the paid consensus by 3.25%, less than
  the paid APIs differ from each other (agenda §3.3).
- **Whisper’s language token is not a switch detector.** One token per 30 s; at 0 dB
  whisper-small reports Catalan on 50% of full turns (measured, §10.7).
- **The record is not verbatim.** Scored against the Diario, a perfect transcript loses
  about 9 points to editing policy (agenda §3.11).
- **Whisper’s BasicTextNormalizer inflates Catalan WER by up to 7 points** by splitting
  `l'home` and `col·legi` (measured, §10.9). One versioned normalizer, both sides.
- **Pitching transcription** to a room that knows Pangeanic and Etiqmedia exist (dossier
  §11.14).

### 4. What to build before the day

The rubric’s 55% does not depend on the data (dossier §5). In priority order:

1. **Freeze the harness.** `attrscore` with IoU matching, fixed-label cpWER, IER,
   TAA@coverage, ECE and the revision logger; add the rotated-names self-test and
   record-WER; version it.
   Half a day.
2. **Port the chair parser to ASR output.** RapidFuzz or phonetic surname matching, the
   government-portfolio-by-date table, agenda-heading extraction (the addressed minister
   holds the LLM’s residual error, §10.8), the LLM fallback with a post-cutoff prompt.
   Run S9: 200 announcement sentences through TTS and turbo, to learn the live floor
   before a judge does.
   One day.
3. **Build the enrollment set from the Congreso XV manifest** (`attic/spikes/manifest`:
   9,441 named clips, 907 h, 271 speakers with five or more speeches; congreso.es
   returns 403 to bare requests, inventory §2). ReDimNet2-B6 centroids, s-norm cohort,
   calibrator fitted on sessions held out from enrollment; run S13 for accuracy by clip
   duration. Add the Senado, and hedge Q-002 with a small Chilean roster from Chile’s
   published video and speaker-labelled records (agenda §5.1). Two days on a GPU box.
4. **Enroll the interpreters** from co-official sessions since 2023 (S16, one day) and
   retune the hallucination guard on turbo (F1, one day).
5. **Build the provenance card, the abstention schema, the cost line and the streaming
   shell.** Rehearse the three demo moments on a Canal Parlamento replay until moment B
   works unattended, and the 17:00 slice: one session end to end with names and a
   coverage curve.
6. **Write the 09:00 questions**: which audio channel, and whether floor and interpreter
   feeds are separable; verbatim or record reference; per-turn, per-word or per-time
   metric; microphone metadata in the pipeline or not; which chambers and languages
   (dossier Recommendations 2; agenda Q-001 to Q-003).
7. **A local fallback for every paid API**, since credits arrive on the day (dossier
   §4).

### 5. Ideas considered and rejected

- **Chasing WER or fine-tuning ASR.** Settled at 3.25% on Spanish; the metric may not
  score words.
- **Blind separation of the interpreter mix.** Nothing validated on real mixtures
  (agenda §3.2); a listed dead end (agenda §10.12).
- **Face recognition or chyron OCR as a fuser input.** Overlay presence on the live feed
  is unverified and the camera-cut count (S8) never ran; a gallery is a day for an
  unknown gain.
- **Joint speaker-attributed ASR (DiCoW, SE-DiCoW, MOSS) as the main path.** Highest
  ceiling and cost; MOSS was discarded on throughput before producing a number (§10.5).
- **A commercial enrollment API as the sole identifier.** Caps of 4 and 50 fit per
  agenda window (§10.10), but credits arrive on the day and it is lock-in; a comparison
  row at most.
- **LLM-only chair parsing.** Does not beat the regex alone (p = 0.92, §10.8); fallback
  only.
- **Sortformer over a six-hour session.** Four slots, no eviction; per-window reset or
  nothing (agenda D2).
- **A learned editorial normalizer.** A second system (R6); a prompted layer at most.
- **Stage-direction detection.** Cheap, but unbenchmarked on chamber acoustics and
  invisible if the reference strips them; last hour only.
- **Azure Speaker Recognition and Amazon Voice ID** (retired, inventory §7); **TTS
  numbers in a results table** (plumbing only, §10.4); **pyannote 4.0.3 over six hours
  on a consumer GPU** (a 9.54 GB VRAM spike after clustering, dossier §11.6).

## 4. Hedging: Pre-build, Hour-Zero Decisions, Abort Conditions

Written 2026-09-02 for the Madrid Open, 3 October 2026, Reversa track.
Sources: the challenge dossier (D), the speaker-attribution research agenda (A) and the
tooling inventory (I), cited by section.
**[J]** marks my judgment; everything else is what the docs establish.

Fixed facts the plan rests on: the brief states question, data and metric and nothing
about method (D §10); the score is one number agreed at 09:00 and measured live at 21:00
on unseen data (D §2); the build window runs 09:30 to 20:45 with a public checkpoint at
17:00 (D §4); the rubric is 45% metric, 20% restraint, 15% auditability, 10%
shippability, 10% demo (D §5); provider credits arrive during the event (D §4).

### 1. Uncertainty inventory

Ranked by the cost of guessing wrong, in hours of rework.

| # | Unknown | What the docs establish | Plausible readings | Build delta if wrong |
| --- | --- | --- | --- | --- |
| 1 | What “inherit a live pipeline” means | “A live pipeline that already works on real sessions of two parliamentary chambers” (D §10). Reversa publishes no code (D §9.1). | (a) running service plus modifiable source; (b) black-box service returning transcripts; (c) code to stand up; (d) only its outputs, as the dataset | Decides whether you build ASR and diarization at all or fuse on top of their output. 3 to 5 hours. |
| 2 | The scored reference text | “Measured on real transcripts” (D §10). Official records are edited (D §11.13); on EuroSpeech only 65% of aligned segments are within CER 20% of the record (A §3.11). | verbatim reference; the published record; record with normalization on both sides | Record scored: editorial error can exceed ASR error (D §11.13) and a normalizer joins the critical path. 2 to 4 hours. |
| 3 | How latency and accuracy compose | “How fast the transcript is ready, and how often the right speaker is named” (D §10); five minutes appears only on Luma (D §10, discrepancy table). | latency cap with accuracy scored inside it; weighted sum; both reported, one ranked | Guard versus term decides whether the runner or the fuser gets the afternoon. 2 to 3 hours. |
| 4 | Speaker metric definition | Per-intervention, cpWER, tcpWER and DER-with-identity differ by an order of magnitude (D §11.9). Permutation-invariant cpWER and DER score every name wrong at 0.0 (A §10.2). | per-turn accuracy; word-weighted; time-weighted | Changes threshold tuning and which turns count; short turns vanish under time weighting (A §11.6). Under 1 hour with the harness, 3 without. |
| 5 | Which two chambers | Spanish copy says chambers “de distintos puntos del mundo” (D §10); identity not public (D Methodology; Q-002, A §9). Reversa tracks EU procedures (D §7.1). | Congreso plus Senado; Spain plus a Latin American chamber; Spain plus the European Parliament **[J]**; two non-Spanish chambers | Roster, enrollment, agenda and hand-off rules are Spain-specific; parser precision transfers across languages, coverage does not (A §10.1). 2 to 4 hours per new chamber. |
| 6 | Which five languages, and whether co-official audio is in the test set | Five languages appears only on Luma (D §10). Congreso permits Catalan, Basque, Galician, Valencian and Aranese (D §11.2); 0.6% of XV speeches carry co-official passages (A §10.10). | es plus four co-official; es-ES, es-419, ca, eu, gl **[J]**; es plus EP languages | ASR model and LID. If the held-out set is stratified to include co-official audio it is the whole tail, otherwise a footnote. 1 to 3 hours. |
| 7 | Audio channel | Public feed carries the interpreter over the deputy (D §11.3); floor-feed separability not established (D Methodology; Q-001, Q-003). Embeddings flip at 0 dB; acoustic ID is safe only below about −6 dB (A §10.7). | isolated floor; mixed feed; interpreters’ feeds provided | Co-official turns only: acoustic, or text and agenda alone. Under 1 hour if the fuser carries a channel flag. |
| 8 | Compute and credits | Not stated; credits arrive on the day, providers unnamed (D §4, §6). A CPU-only stack runs under real time with Spanish models (A §10.4). | GPU box; laptop CPU; credits for one ASR API | Model tier, and whether pyannote runs. 1 to 2 hours if the CPU fallback is packaged. |
| 9 | Held-out input format | “Every system runs live against data it has never seen” (D §4). | file drop; URL; live stream | An adapter, under 1 hour; fatal at 21:00 if unhandled. |
| 10 | Whether the pipeline already names speakers | No public deployment claims robust named attribution at plenary scale (D §11.14). | it does not; it does, badly; it does, well | If it names well, the win is calibration, abstention and provenance, not a new identifier. |

**[J]** Items 1 to 3 dominate because each can invalidate a morning’s work; the rest are
afternoon adjustments once the invariant core exists.

### 2. Hour-zero decision tree

One person per check, answers on a whiteboard before anyone opens an editor.
The owners stay in the room all day (D §2): every unanswered item below is a question,
not a guess.

**09:00 to 09:30, during the presentation.** Write the metric verbatim.
Then ask, in this order (D Recommendations 2; A Recommendations 3):

1. Is five minutes a cap or a term?
   Cap: the runner is a guard and the fuser gets the day.
   Term: budget an hour for lag tuning.
2. Is “right speaker” per turn, per word, or per second?
   Set the harness outcome to TAA, fixed-label cpWER or IER (A §4.2); never
   permutation-invariant cpWER (A §10.2).
3. Is the reference the published record or verbatim?
   Record: keep verbatim and record-form layers, normalizer on the critical path (D
   §11.16). Verbatim: normalizer off.
4. Which chambers, which languages, and is co-official audio in the held-out set?
   Spain only: load the Congreso roster, enrollment and agenda sets.
   Anything else: start the roster build from the shipped data immediately.
5. Which audio channel does the pipeline consume, and are interpreters’ feeds available
   (Q-001)? Mixed: co-official turns are text-and-agenda only, flagged as such.
6. Does it expose conference-system microphone events (D §11.3; A §3.8)? Yes:
   attribution is a join; the day is exceptions, calibration and provenance.
7. What is the 21:00 input format?

**09:30 to 10:00, the pipeline and the data.** In parallel, each with a stopwatch:

- **Run it on one provided session, timed.** Slower than real time, or fails: treat it
  as outputs only and bring up the packaged stack.
- **Read its output schema.** Timestamps plus anonymous speaker labels is the gift: the
  pre-built fuser consumes exactly that.
  No timestamps is the trap: nothing downstream can be scored or made incremental.
- **Score it on one labeled session with the harness.** That number is the baseline, the
  17:00 story, and the thing to beat.
- **Play a co-official segment and an applause span.** Interpreter audible over the
  deputy: channel condition mixed.
  Text during applause: gate with VAD plus the compression-ratio filter (A §10.6).
- **Check the language tag.** `es` on every segment is unusable, as in the
  OpenParliamentTV data (A §10.10).
- **Check dependencies.** A paid API without credits, a six-hour batch job, or a
  monolith with no stage boundaries: wrap it, do not edit it.
- **Inventory the data.** Roster, agenda, past labeled sessions, video: each is a fuser
  input (A §3.1); an absent one is an evidence source switched off, not a redesign.
- **Confirm the GPU.** None: sherpa-onnx CPU models, each stage at 0.01 to 0.2 real-time
  factor on four cores (A §10.4).

Selection: timestamped anonymous turns from the pipeline means **fuse** on top of it;
anything less means **replace** with the packaged stack, then fuse.

### 3. Scenario matrix

| Branch | What changes | What stays |
| --- | --- | --- |
| Spain only | Nothing: XV roster, aliases, 271 enrollable speakers and agenda candidate sets are pre-built (A §10.10). | Everything. |
| Two countries | A roster and hand-off rules built from the shipped data; enrollment from whatever labeled audio it provides; turbo already covers es-419 (A §10.5). | Fuser, harness, UI, runner, abstention. The Spanish half keeps its pre-work. |
| Co-official audio present | LID gate and channel flag on; LoS-punctuated or turbo per language (I §2); co-official turns attributed from text and agenda unless the feed is isolated (A §10.7). | Spanish turns, which are the bulk (A §10.10). |
| Co-official audio absent | Multilingual work leaves the critical path. | Everything else. |
| GPU available | turbo on faster-whisper, pyannote community-1, ReDimNet2; an hour of clips in about 2 minutes (A §11.3). | Pipeline shape. |
| No GPU | sherpa-onnx CPU stack with the Spanish CTC model and ERes2Net (A §10.4), or the credited API through the asr-bench adapter (A §10.9); Speechmatics’ 50-identity cap fits every agenda window (A §10.10; I §6). | Fuser, harness, UI. |
| Pipeline good | Fuse on top; the day is calibration, abstention, provenance, latency. | Packaged stack stays cold. |
| Pipeline poor | Fuse on top, run the packaged ASR in parallel, pick per session by harness score. | The pitch: their outputs plus your naming. |
| Pipeline unusable | Packaged stack end to end, Spanish turns first. | Fuser and harness unchanged; the pipeline was never a dependency. |
| Metric leans latency | Runner at a 2-minute finalization window with a revision log (A §4.5); target name revision rate under 5% (H-007, A §9). | Attribution logic; only its timing changes. |
| Metric leans accuracy | Sweep the abstention threshold and report the coverage–TAA curve (A §4.4); a post-session batch pass is fine inside the cap. | Runner still logs lag. |

### 4. The invariant core

Pre-built, in value-per-hour order (**[J]** on the ordering; components per D §11.16 and
A §4.6).

1. **The attribution fuser with abstention and provenance.** Input: turns with
   timestamps, text, an anonymous label and optional acoustic scores.
   Evidence in priority order: microphone metadata if present, chair announcement,
   agenda candidate set, acoustic posterior against the candidate set, overlay OCR (D
   §11.16). Output per turn: a distribution over candidates plus unknown mass, a name or
   abstention at threshold, and the evidence that produced it (A §4.4; D §11.10). The
   text-only floor is 93 to 95% on rostrum turns and 92 to 94% on all regular turns with
   the continuation rule (A §10.1), so this component can carry the metric alone.
   It serves 45%, 20% and 15% of the rubric with one mechanism.
2. **The scoring harness**, attrscore as built (A §10.2): TAA at coverage, fixed-label
   cpWER, IER, DER, hallucination rate, finalization lag and revision rate, IoU turn
   matching, paired comparison.
   One config switch matches the organizers’ definition.
3. **The provenance UI.** One card per turn: name, confidence, abstain marker, the chair
   sentence with its timestamp, the agenda slot, the candidate list, a link to the audio
   span. Reads the fuser’s JSONL. This is both the demo and the auditability score.
4. **The incremental runner.** Chunked processing with a finalization window and a log
   of every emission and revision (A §4.5). It makes the latency claim three checkable
   numbers.
5. **The packaged stack**, one container: Silero VAD, faster-whisper turbo, pyannote
   community-1, ReDimNet2 or WeSpeaker with AS-norm, VoxLingua107 LID; a sherpa-onnx CPU
   variant beside it.
6. **I/O adapters** for RTTM, SegLST, SRT, JSON and plain diarized text, both
   directions.

The 55% of the rubric that is not the metric depends on none of the unknowns in §1;
items 1 to 4 are that 55%, and they also produce the metric.

### 5. Prepared assets to bring

| Asset | Portable across branches | Download risk on the day |
| --- | --- | --- |
| Weights on local disk and a private bucket: whisper-large-v3-turbo (CT2), LoS-punctuated, pyannote community-1, ReDimNet2-B6, WeSpeaker ResNet, Silero VAD, VoxLingua107, Sortformer streaming v2, sherpa-onnx CPU models, MMS_FA | Yes | High. pyannote and Nos_ParlaSpeech are gated behind accepted terms and a token (I §11.3); huggingface.co was unreachable from the research environment (I §11); gigabytes over venue wifi is a lost morning. |
| Vendored `md-eval-22.pl`, pinned `simplejson` for MeetEval, the pyannote `collar=0.5` constant (A §10.2) | Yes | MeetEval fetches md-eval on first use and fails offline. |
| attrscore harness with its tests (A §10.2) | Yes | None. |
| Chair parser, 16 rules plus hybrid LLM fallback, roster matcher (A §10.1, §10.8) | Rules are Spanish and Catalan; the matcher is generic | LLM fallback needs an API key; the regex path nothing. |
| Congreso XV manifest, roster with aliases, agenda candidate sets, precomputed enrollment embeddings for 271 speakers (A §10.10) | Spain only | congreso.es returns 403 to bare requests (I §2); do not plan to scrape live. |
| asr-bench with 14 service adapters and response caching (A §10.9) | Yes, for whichever credits arrive | Each vendor needs a key; keep the negative-control language code. |
| Incremental runner with revision logger | Yes | None. |
| Provenance UI as static HTML over JSONL | Yes | None. |
| Format adapters and an `ffprobe` script for track layout (Q-003) | Yes | None. |
| Rehearsed three-minute demo script and a replay session | Replay data is Spain only | None. |
| A GPU: own laptop or a pre-provisioned cloud instance with weights already pulled | Yes | Quota and image pull; do it the week before. |

Non-portable: the enrollment set, the agenda scraper, and the coverage of the Spanish
hand-off rules. Everything else moves.

### 6. Abort conditions

Each fallback is something that runs on stage.

**Hour 4 (13:30).** Signal: no session has gone end to end through the harness to a
scored number. Fallback: stop component work; run the text-only fuser and the UI on the
inherited pipeline’s raw output and score that.
If that pipeline has not run at all: packaged stack, Spanish turns only, CPU models if
no GPU. Second signal: the roster for a new chamber still does not resolve names.
Fallback: anonymous labels plus chair sentences shown as evidence, abstention wherever
the parser fails, said aloud.

**Hour 7 (16:30, before the checkpoint).** Signal: acoustic identification adds nothing
over the text-only floor on the labeled sessions, paired by session (A §4.7). Fallback:
demote acoustics to evidence-only; spend the time on the coverage–accuracy curve and the
abstention threshold, the 20%. Signal: the runner misses its window or revises names
above 5%. Fallback: batch mode with a post-session pass inside the cap, lag logged and
reported. The checkpoint shows the baseline number, the fused number, and one provenance
card.

**Hour 9 (18:30).** Signal: the 21:00 input format is unhandled, or a full session
exceeds the cap on the hardware in the room.
Fallback: freeze features, build only the adapter, precompute every chunk but the last
and process the tail live.
Failing that: replay a pre-processed session through the UI, labeled as replay, coverage
curve and abstentions visible.
Standing rule: a change that raises TAA at 100% coverage and lowers it at 90% is not an
improvement under this rubric (A §4.4); revert it.

The floor at every hour is one artifact: the pipeline’s transcript, text-only names with
their evidence, abstentions where the evidence is thin.
It is never nothing.

## 5. Multimodal Identity, the Member Knowledge Base and Contextual Priors

The design memo behind the agenda’s R12 to R16 and the dossier’s architecture A4.
Written after the four analyses above, in response to the question of whether member
profiles built up front, from official and open sources, would improve attribution.

Deepens R10 (agenda §7) and A4 (dossier §12.3). Tags: **(measured)** for numbers run in
this programme (agenda §10 and this session’s archive coverage); **(technical)** for
published figures; **(estimate)** for my own; **(proposed)** for design.
S3 and S8 are unrun; every visual claim says which branch it assumes.

### 1. The member knowledge base

The XV manifest holds 370 deputies, 32 government members and 33 other persons over
9,441 clips (measured).
The roster file carries a Wikidata id, names and 4 to 11 alias spellings for 3,830
members across legislatures, plus 11 groups with abbreviations (measured).
Three layers attach to it.

**(a) Voice.** Enrollment needs 5 to 30 s per person (dossier §11.5), several clips for
a centroid (agenda §3.5). The archive gives 97% of the 427 named members at least 5 min
and a median of 47 min (measured), two orders of magnitude more.
Open-web audio (interviews, party channels, podcasts) therefore adds nothing to coverage
except for the four members under a minute, new entrants and non-members, and two
objections say it subtracts.
Condition mismatch: the same-room advantage behind R4 and H-004 is matched microphone
chain, room and codec; a centroid averaged with a studio interview is pulled off the
chamber condition, and condition dominates the embedding shift (agenda §3.5). Label
bootstrap: archive labels come from a surname-sequence merge with about 2.4% unmatched
(agenda §10.10); open-web audio has no label beyond a caption, so a human labels it or
the model being built does, which is circular.
Co-official clips may enroll the interpreter until S1 confirms the track layout.
Its one legitimate use is a cross-condition *test* set.
The layer holds one centroid per language and legislature (per-language until S15 says
otherwise), with the clip and session ids behind each so evaluation sessions can be
excluded; the AS-norm cohort is the other members.

**(b) Face.** Three sources.
First, the archive: every per-intervention MP4 is named, so a gallery bootstraps without
annotation: detect faces at 1 fps, keep the dominant frontal track in podium framing,
label it with the clip’s speaker, drop clips where two large faces compete.
Second, the official congreso.es portrait (a scraper; the site returns 403 to bare
requests, inventory §2) and the Wikidata P18 image reachable from the roster ids, both
with clear licence terms.
Third, party and press photographs, with unclear reuse terms; skip unless the archive
fails a member.
One clean portrait gives usable verification; the gallery stabilises at 5
to 20 in-domain stills across sessions, lighting and pose (estimate).
Recognition models take a 112 px aligned crop, so a face under about 60 px in the frame
is unreliable (estimate); podium framing on a 1080p feed gives 150 px and more, wide
shots 10 to 30 px.
Drift over a legislature (glasses, beards, weight, the mask period) is
handled by dating every template, preferring those within twelve months, and refreshing
after each session from turns where announcement, voice and face agreed, each refresh
logged so it can be rolled back.

**(c) Non-biometric context.** Group with aliases (per speech in the processed session
files, measured); role state by date: chair and vice-presidents (the chair changes 3.07
times per session and 70% of chair markers carry only the office, measured S7),
ministers with portfolio and dates (the parser’s table, agenda §10.1), spokespersons,
committee chairs; committee membership and constituency from the diputados open data
(network); name variants plus a phonetic key per surname built for the four
orthographies, since Soundex-family keys are English; sex from the honorific; speaking
history from the manifest: agenda types, turns per session, median duration, addressed
minister at question time.

Schema (proposed):

```yaml
member:
  id: Q…                                   # Wikidata id, the roster key
  names: {canonical, surnames: [s1, s2], aliases: [...], phonetic: [...], sex}
  affiliations: [{group_id, from, to}]
  roles: [{role, portfolio, organ, from, to}]   # chair, minister, spokesperson, ...
  constituency: …
  committees: [{organ_id, from, to}]
  presence: [{session_id, present, source}]
  speaking: {agenda_types, turns_per_session, median_turn_s, addresses: [Q…]}
  voice: [{centroid_id, language, legislature, clip_ids, seconds, snr, model, built}]
  face:  [{template_id, source, date, face_px, model, built}]
  provenance: {roster_source, last_refresh, refresh_events: [...]}
```

Buildable today from files in hand: roster, aliases, group per speech, agenda items and
types, speaking history, the minister table, surname keys.
Needing a networked box: archive MP4s (voice and face), portraits, Wikidata images,
committee and constituency tables, attendance.
Needing only hours: phonetic keys and the role state machine.

**Licensing and data protection.** Archive media carry the Congreso’s aviso legal, not a
CC licence; research reuse is customary, redistribution of derived material is not
covered. Voice centroids and face templates built to identify people are GDPR Article 9
biometric data; a deputy speaking in public does not lift the category, it makes the
“manifestly made public” and “substantial public interest” bases arguable, and the
chamber as controller has a cleaner basis than a third party.
The AI Act treats remote biometric identification as high-risk, so a product, as opposed
to a study, needs a DPIA and a conformity position.
None of this forbids work on public officials’ public conduct; it means the position has
to be written down: templates stay in-house and manifests are published instead (agenda
§5.1 step 8), identification is restricted to the roster and never applied to visitors
or staff, retention is tied to the legislature, and dossier §12.8’s text-first design
keeps a biometric-free mode available.

### 2. What each layer buys, per failure mode

| Failure mode | Anchor | Layer that fixes it | Expected effect |
| --- | --- | --- | --- |
| 350-roster open-set ceiling | EER doubles from 100 to 700 enrolled (technical, dossier §11.7) | context: candidate sets, median 2 per numbered item, 10 to 12 per debate section (measured §10.1, §10.10) | moves acoustics below the 100-speaker end of the curve; H-002’s +10 TAA@95% is unmeasured; text-only gained only +0.012 to 0.016 (measured) |
| Short turns | text 49 to 55% under 30 words (measured); EER +46% relative from 3.6 s to 2 s (technical) | face and ASD, duration-insensitive: a 2 s turn is 50 frames | if S8 finds a usable face on most sub-10 s turns, 50% to 75 to 85% (estimate); under wide-shot direction, near zero |
| Cross-lingual shift | systematic, condition-dominated (technical, agenda §3.5) | voice: per-language centroids; face, chyron and context are language-invariant | visual and context cross-check exactly the turns where voice is least trusted |
| Interpreter overlay | embedding flips at 0 dB (measured §10.7); acoustics gated off above −6 dB | chyron, context, face; ASD’s audio branch under a two-voice mix is unmeasured | on the co-official tail (0.6% of speeches, 43 sessions), non-acoustic evidence carries attribution alone |
| Same-surname collisions | 39% share a first surname; group halves it; both surnames near-unique (measured) | context (group, sex, portfolio, agenda), then face or voice | most of the first-surname-only stratum, 10 to 19% of announcements (measured) |
| ASR-garbled names | S9 unrun (agenda §10.1) | phonetic keys against the candidate set, not the roster; chyron OCR as a second textual name outside ASR | fuzzy matching against 350 would false-match; against a dozen it is safe (estimate) |

### 3. The visual stack

**Active-speaker detection.** TalkNet 92.3, Light-ASD 94.1, LoCoNet 95.2, LoCoNet with
TalkNCE 95.5 mAP on AVA-ActiveSpeaker (technical, agenda §3.6), scored per face track
for audio-visual sync on movie frames.
Chamber conditions sort into four regimes.
Podium framing: one large frontal face, trivially easy; the useful output is the
negative, an off-camera interjector whose voice does not match the visible lips.
Wide shots: dozens of faces at 10 to 30 px, below the size at which any ASD model works
(estimate).
Cutaways: the director cuts to a reacting minister or the chair while someone
else speaks, so the visible face is not the speaker; this is REPERE’s distinction
between “who is on screen” and “who is speaking”, catching it is ASD’s job, and the
system must abstain visually when the best track scores low.
The unmeasured case is the overlay, where the audio branch hears two voices (F-V4).
Light-ASD is the practical pick (weights in-repo, Apache-2.0, real-time); LoCoNet has no
licence (inventory §3.12).

**Face recognition at roster scale.** 350 identities is small.
ArcFace-class models report verification above 99.5% on LFW-style pairs and mid-90s TAR
at FAR 1e-4 on IJB-C (technical, not re-verified here).
With a dated in-domain gallery, rank-1 on podium frames should exceed 98%, with errors
in profile views, occlusion, motion blur and small faces (estimate).
Against acoustics: voice open-set at 350 sits at 2 to 3% EER on clean full-length trials
and 9 to 13% on 2 to 3 s segments (technical, dossier §11.7), whereas face has no
duration axis and its out-of-set population (ushers, staff, gallery) is separable by
screen region rather than by score.
No published face-ID number exists for parliamentary broadcast; the 98% is a claim to
test (F-V3). InsightFace weights are research-only; facenet-pytorch or DeepFace are the
permissive swaps (inventory §3.12).

**Chyron OCR.** A fixed-position, fixed-font, high-contrast overlay is an easy OCR
problem; PaddleOCR PP-OCRv5 covers Spanish and the co-official orthographies (inventory
§3.12). The open questions are not accuracy: whether the archive MP4 carries a chyron at
all (a search summary says yes, unverified, agenda §3.6), whether the live feed does,
when it appears and how long it persists, whether it names the member or only the group,
and whether the co-official subtitle collides with it.
The string goes through the chair parser’s alias and phonetic matcher; if present, this
is REPERE’s unsupervised naming from overlaid text, a name that never passed through
ASR.

**The VLM route.** A Qwen3-VL-class model reading the frame holistically, constrained to
a JSON schema: shot type (podium, wide, cutaway, bench, chair), overlay text verbatim,
visible face count, rostrum occupied.
Its value is scene classification and robustness to a broadcast redesign.
Its hazards: no calibrated score; cost (1 fps over six hours is 21,600 frames, roughly 1
to 2 GPU-hours for a 4B to 8B model, estimate); and face hallucination, since asked “who
is this” a VLM names a politician from pretraining, uncontrollable and the regulatory
case to avoid. Use it at shot changes only, never for identity.

**What PERCOL got right and what changed.** REPERE’s winning design named clusters, not
frames: diarize audio, track faces, propagate names from overlaid text to co-occurring
voice and face clusters (technical, agenda §3.6). It separated “who speaks” from “who is
on screen”, and it scored fixed named identities, the move agenda §4.2 makes against
permutation-invariant cpWER. What 2026 changes: ASD replaces co-occurrence heuristics;
ArcFace-class embeddings replace the 2013 face leg, PERCOL’s weakest; OCR on clean
overlays is solved; a VLM adds scene understanding; and a parliament supplies what
television never did, a closed roster with an agenda.
EGER 24.4% was measured on open television; with candidate sets the floor should be far
lower (estimate).

### 4. Contextual interpretation as its own signal

**Disambiguation.** The role state machine resolves office-only references: every
residual parser error on “señor ministro” was the wrong minister (measured §10.1), and
the per-question agenda heading, which names the addressed minister and is empty for 42%
of rows today, holds essentially all of the LLM fallback’s residual error (measured
§10.8). Group halves first-surname collisions; sex and the agenda item take most of the
rest.

**Validation.** The knowledge base lets an attribution be checked for plausibility, not
only scored (proposed): a member named on a turn who is in no candidate set for the item
and was not announced; a reply slot attributed to someone other than the addressed
minister; a member attributed simultaneously in two organs (up to five committees run in
parallel, dossier §11.11); a minister named outside their term; a member recorded absent
in the sitting’s votes.
Each contradiction lowers the posterior and pushes toward abstention, not toward the
next-nearest name.
These are the wrong-person errors that DER and permuted cpWER score at
zero (measured §10.2), so the check is the only cheap detector of silent failures
(testable today, F-V5).

**Enrichment.** The entry carries group, role, constituency, agenda item, initiative and
addressee: what the Diario prints and what a legislative twin joins on.

#### Linking press coverage to what members say

A retrieval layer that surfaces, for an intervention, what the member has said publicly
on the topic and how it was reported.

It earns nothing on the 45% metric line, since it changes no name on any turn.
It earns on the product surface: shippability, ambition and demo, and the gap between
transcription at USD 0.71 per audio-hour and the EUR 1.65 to 2.85 per minute a chamber
pays for a record (agenda §3.3, dossier §11.14). That gap is attribution, editing and
publication, and this is publication.

The feedback into attribution is weak and should stay out of the posterior: a member
recently in the news is marginally likelier to speak on the matching item, but the
agenda and the spokesperson list predict that far better; the prior would favour
ministers and party leaders, the easy cases; and same-day coverage quotes the stream or
the record, so the prior would be derived from the thing being attributed.
Presentation only; circularity is the decisive argument.

The real constraint is integrity.
An official record must not absorb press framing or model commentary, and a wrong or
editorialising link on a member’s words is a worse failure than an unattributed turn.
Three layers, physically separated (proposed): the **record** (turn, attribution,
evidence ledger); the **retrieved** layer (source, canonical URL, publisher, date,
retrieval query, similarity score, title and a short snippet under the publisher’s
terms, never paraphrased into the record); the **generated** layer (any summary, marked
with model id, prompt version and date, in a sidecar the record never includes).
Links get their own relevance threshold and abstain path, a date window, and a flag when
coverage predates the legislature.

Practicalities: publisher RSS, GDELT (free, Spanish coverage), Europe Media Monitor,
paid aggregators; publisher terms mostly forbid full-text storage, so keep metadata and
snippet. Retrieval is an embedding index over member plus topic entities, deduped by
canonical URL and near-duplicate title.
Coverage arrives hours to days after a same-day record, so the link layer is
asynchronous and revisable under §4.5. Cost is tens of euros a month at a few thousand
interventions a week (estimate).
It needs egress this environment lacks; none of it has been tested.

### 5. Fusion

Per turn (proposed): a candidate set from the agenda, the roster, the chair and an
explicit “none of these”; four sources, each emitting a calibrated log-likelihood ratio
per candidate or a gate state: text (parse class, alias match, continuation), acoustic
(the §3.5 recipe), visual (face LLR on ASD-positive tracks; chyron string with OCR
confidence and roster-match score), and context as the prior, never double-counted with
the candidate-set constraint.
Fusion is a weighted sum of calibrated LLRs with weights fit by logistic regression on
the benchmark, since calibration plus fusion gave VoxWatch its largest gain (technical,
agenda §3.5). Gates: acoustic weight to zero above −6 dB estimated overlay ratio
(measured rule §10.7), visual to zero when ASD finds no synced face, text to zero with
no announcement in the window.
The unknown mass takes its prior from the out-of-set rate; the threshold is set for a
false-alarm rate under 1/N per session and swept for the coverage-TAA curve (§4.4). When
two strong sources disagree the fuser abstains and emits both with evidence; that is the
plausibility check’s output path.
Provenance is a ledger row per source per turn: time window, raw observation
(announcement sentence, OCR string, face track and frame ids, score vector), calibrated
LLR, gate state, model and knowledge-base version; dossier §12.5’s citation chip is a
view of it.
Scoring follows §4.2, with Cllr and actDCF on the fused posterior, stratified
per §4.3 plus a visual-condition stratum (shot type, on camera, chyron present).
Chyrons arrive seconds late, so fused names are revisable and the revision rate is
logged (§4.5).

### 6. Build order, cost, and when the visual stack is worth it

Offline, before any event (proposed): the context and name-variant layers from files in
hand (hours; they serve A1 through A5 alike); voice centroids from the archive (about 3
GPU-hours for 907 h, scaling inventory §9.4); the bootstrapped face gallery from the
same clips (3.3 M frames at 1 fps, 5 to 9 GPU-hours, estimate) plus Wikidata portraits;
calibrators and thresholds from held-out sessions; the chyron region template; fusion
weights on the smoke benchmark.
Live: streaming ASR into the chair parser, Light-ASD and face matching on the stream,
OCR at shot changes, the fuser and ledger; one GPU.

The verdict, with S3 and S8 unrun: two conditions decide it.
If S3 finds a named chyron on the live feed for most rostrum turns, OCR is an afternoon
of work and joins the fuser at once, as dossier §12.3 already prescribes.
If S8 finds the speaker on camera with a face above about 60 px on most sub-10 s turns,
face plus ASD is the only signal that lifts the short-turn stratum, and it carries the
interpreter-overlaid tail as a side effect.
If the chyron is absent and the director holds wide shots for interjections, the stack
costs a GPU, days of engineering and a biometric compliance position for a few points on
rostrum turns the chair parser already names at 93 to 95% (measured), and it is not
worth it; nor where conference-system events exist (R11, a join), nor in a hackathon
whose inherited pipeline is audio-only.
Independently of both spikes, the context and name layers are worth building now; the
face gallery script is worth writing but not running live until S8 reports.

### 7. Falsification and the cheapest tests

- **F-V1, S3 extended.** Chyron yield by turn type and seconds-to-appearance; name
  versus group-only; archive versus live.
  Falsified if named chyrons cover under half of rostrum turns.
- **F-V2, S8 extended.** For every sub-10 s turn in five sessions, code shot type,
  speaker visibility and face size, and count interjections where the camera stays on
  the rostrum, where ASD must reject the visible face.
  Falsifies H-008 if visible-face coverage is under about 50%.
- **F-V3.** Face rank-1 at N = 350 on the bootstrapped gallery, enrol sessions A to C,
  test D, by face-size bucket and shot type; then a XIV gallery tested on XV members
  present in both, for the template-decay curve behind the twelve-month rule.
  Falsifies “face at 350 is easy” below 95% on podium frames.
- **F-V4.** Light-ASD on the 54 co-official clips: does it still mark the deputy active
  under the two-voice mix.
  Falsifies the claim that visual evidence is unaffected by the overlay.
- **F-V5, sandbox today.** Inject rotated names into the XV record (within group, across
  group, minister for deputy) and measure the fraction the plausibility checks catch at
  a fixed false-alarm rate; falsified if the catch rate is low.
- **The knowledge-base thesis itself.** If S9 shows the chair’s hand-off survives ASR
  above 90% and S8 shows short turns off camera, the name-variant layer is the whole
  story and the visual stack is a demo.
  If S13 shows archive-enrolled acoustics above 90% on short turns, which dossier §11.7
  makes unlikely, the face layer’s margin is small.
