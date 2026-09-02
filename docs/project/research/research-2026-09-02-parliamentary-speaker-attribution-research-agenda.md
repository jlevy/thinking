---
title: Named Speaker Attribution for Parliamentary Transcription — Critical Review and Research Agenda
description: A technical review of the Track 02 analysis in the Madrid Open dossier, an evaluation protocol that can show real improvement, the datasets and tools to use, and a ranked set of research directions with pre-registered hypotheses
author: Claude Code
---
# Research: Named Speaker Attribution for Parliamentary Transcription — Critical Review and Research Agenda

**Date:** 2026-09-02 (last updated 2026-09-02)

**Author:** Claude Code

**Status:** Complete

## Overview

This report is the second pass on the problem behind Track 02 of the Madrid Open: given
a live parliamentary session, produce the official-record text within minutes, with
every intervention attributed to the **named** member who spoke it.
The first pass is §11 of the
[Madrid Open dossier](research-2026-09-02-madrid-open-reversa-challenge.md), which
assembled the landscape.
This report does four things that report does not:

1. **Reviews that analysis adversarially** — where its numbers come from domains that do
   not resemble a plenary chamber, where a constraint was misapplied, and what it left
   out. Sixteen defects are listed in §2, and the dossier has been corrected where the
   evidence required it.
2. **Specifies how to measure the problem** so that an improvement is distinguishable
   from noise, metric-shopping, or a change of protocol: a two-layer reference, a fixed
   metric vector with roles, stratification, coverage–accuracy curves, and latency and
   revision accounting (§4).
3. **Inventories the data and tools** that exist, the one dataset that does not, and a
   concrete recipe for building it (§5–§6).
4. **Ranks research directions** by expected gain per unit of effort, states each as a
   falsifiable hypothesis with an instrument, and separates what would be incremental
   from what would be genuinely new (§7–§9).

The framing throughout is the one the metric imposes.
The track scores “how often the right speaker is named.”
Transcription is a solved input to that; attribution is the unmeasured output.
The single most valuable artifact anyone could produce for this problem is a
speaker-attributed benchmark on the actual domain, because every accuracy claim below is
currently an extrapolation from adjacent domains — including, as §3.3 shows, the claims
made by a company that transcribes these sessions in production.

### A note on evidential status

Claims are tagged as in the dossier: **[Primary]** read directly from a source
repository, dataset card, or official document; **[Technical]** from the literature,
mostly via search-result summaries where the primary text could not be opened (see
Methodology); **[Press]** from news coverage; **[Analysis]** this report’s own
reasoning. New for this report: **[Proposed]** marks a design or hypothesis put forward
here and not yet tested.

## Questions to Answer

1. Which conclusions in the dossier’s §11 survive a critical read, and which rest on
   numbers from domains that do not resemble a parliamentary chamber?
2. What is the problem, stated precisely enough to be measured — inputs, outputs,
   regimes, and the sub-problems that can be scored separately?
3. What evaluation protocol would show that a change improved named attribution, and
   under what statistical discipline?
4. Which datasets and tools are usable now, and what has to be built?
5. Which research directions have the highest expected gain per unit of effort, and
   which of them would push the state of the art rather than reproduce it?
6. How should a focused effort be organized so that negative results are kept and
   positive ones are believable?

## Scope

**Included:** the technical problem of speaker-attributed, near-real-time transcription
of parliamentary sessions into official-record form, with the Spanish Cortes Generales
(Congreso and Senado) and their co-official languages as the concrete case; the
2024–2026 literature on speaker-attributed ASR, open-set speaker identification,
diarization, target-speaker extraction, text-side and multimodal attribution, editorial
normalization, and streaming evaluation; public corpora and tooling; one production
operator’s published benchmark and workload data; and the design of an evaluation
protocol and research campaign.

**Excluded:** the competition logistics, organizers, and founders (dossier §1–§10); the
challenge briefs and data, which are not public; any private or credentialed source.

## Findings

### 1. What the analysis so far gets right

**[Analysis]** Five conclusions in dossier §11 hold up and are carried forward
unchanged:

1. **Attribution, not transcription, is where the score is decided.** The metric names
   speaker accuracy; off-the-shelf Spanish ASR is already at low single-digit error on
   parliamentary audio (§3.3 adds in-domain evidence); and every published
   speaker-attributed number is far worse than every published WER.
2. **The audio channel matters more than any model.** The Congreso’s outbound signal
   mixes the interpreter over the original on co-official-language interventions.
   This report confirms it from three further press sources and adds the mechanism
   (§3.2).
3. **A parliament encodes identity redundantly** — the chair’s announcement, the order
   of business, a named audiovisual archive, the camera, the microphone system — and
   fusing those is cheaper and more reliable than pushing an acoustic model.
4. **Calibrated abstention is both a scoring requirement and a technical necessity** at
   watchlist sizes in the hundreds.
5. **The editorial layer can dominate the score** if the reference is the edited record
   rather than verbatim speech.

### 2. Where the analysis so far is weak

**[Analysis]** Sixteen defects, ordered by how much they change a build or research
decision. Each is labeled D-n and referred to later.

**D1. The “order of magnitude” gap is a cross-domain extrapolation.** The 2–3% WER
figures are on monologic, close-microphone parliamentary speech.
The cpWER 30–35 figures are on voice-agent and meeting audio: far-field, conversational,
heavily overlapped. The Albayzin 37.2% DER-with-identity is on multi-genre broadcast
television, and — a detail the dossier missed — with only **74 enrolled speakers over 54
hours** [Technical], so its difficulty came from broadcast acoustics and overlap, not
from watchlist size.
None of the attribution numbers is from a chamber.
A plenary has properties that favour attribution — turns of minutes, one open
microphone, a chair who names the next speaker — so the in-domain attribution error may
be much lower than the proxies and concentrated in a few turn types.
The correct statement is: **the attribution error on parliamentary audio is unmeasured
in public**; the proxies from harder domains say it dominates; the first experiment is
to measure it.

**D2. The Sortformer four-speaker cap is misapplied as a session-level constraint.** The
cap bounds speakers tracked concurrently in the model’s cache.
In a plenary the number of distinct speakers inside any few-minute window is small; with
turn-level windowing driven by chair announcements or the agenda, the cap does not bind
the streaming component.
The real limitation is that Sortformer is diarization, not identification: it does not
consume enrollment profiles.

**D3. DER is the wrong headline metric for the stated objective.** DER is time-weighted,
so a minutes-long speech dominates and a misattributed one-second interjection costs
nothing. “How often the right speaker is named” is most naturally **per intervention**,
which weights every turn equally and matches the record’s one-label-per-intervention
structure. The dossier lists candidate metrics but commits to none and proposes no
stratification.

**D4. No error decomposition.** Nothing in §11 breaks attribution error down by turn
duration, speaker role (chair, minister at the bench, rostrum speaker, interjector),
language, channel condition, or position in the session.
This is the single most informative analysis for deciding what to build, and it is
absent.

**D5. The official record is unused as supervision.** The *Diario de Sesiones* labels
every intervention as `El señor APELLIDO APELLIDO:` or `La señora PRESIDENTA:` and marks
stage directions in parentheses [Technical]. Aligned to the session audio, that is
thousands of hours of speaker-labeled parliamentary speech at zero annotation cost — for
fine-tuning speaker embeddings in-domain, for training speaker-attributed ASR, and for
calibrating thresholds on real trials.
§11 uses the archive only as an enrollment source.

**D6. The same-room advantage is ignored.** VoxCeleb-derived open-set figures come from
cross-condition trials on internet video.
In a parliament, enrollment and test audio share the microphone chain, the room, and the
broadcast codec. The expected shift is large and favourable, and it has to be measured
rather than imported from VoxWatch.

**D7. Score normalization and calibration are under-specified.** §11.7 names QMF only.
The standard recipe — cosine scoring, adaptive s-norm with a cohort drawn from the other
enrolled members, a trained calibrator, and evaluation with Cllr and the detection cost
at the operating point — is what actually controls the false-alarm curve as the
watchlist grows (§3.5).

**D8. Joint speaker-attributed ASR is missing.** §11 assumes a modular
diarize-then-identify pipeline.
Diarization-conditioned and target-speaker Whisper variants, serialized-output training,
and speech LLMs that emit speaker tokens are an alternative family whose error does not
compound across stages, and at least one research system accepts enrolled names as
conditioning (§3.4).

**D9. The interpreter overlay is treated as a wall.** It can be attacked from four
directions (§3.2), one of which — obtaining the interpreters’ own feeds, which exist
before the mix — needs no signal processing at all.

**D10. Latency is analyzed as compute, not as behaviour.** A bounded-lag system revises
earlier output as context arrives.
§11.12 has no metric for revision rate, finalization lag, or the stability of a
published label — so “ready in five minutes” is not yet a measurable claim.
The streaming literature has the instruments (§4.5).

**D11. Editorial normalization is dismissed as a second system.** True for a twelve-hour
build; false as research.
Verbatim ASR output paired with the published record is free training data for the
transform, and the gap can be measured as the difference between a verbatim-referenced
and a record-referenced score.

**D12. The “two chambers from different parts of the world” hedge is unplanned.** §11
flags the question and then builds everything on the Spanish reading.
§3.3 changes the picture: there is a production operator covering Spain, Chile, Peru and
the European Parliament, and its data says Spanish-variety transfer is not the problem.

**D13. The closest historical precedent is cited only as a DER row.** REPERE — named
person identification in French television from audio, video, and on-screen text — is
the same task with a different corpus, and its systems, metric, and results are directly
reusable (§3.6).

**D14. Stage directions are unaddressed.** *Aplausos*, *rumores*, *protestas*, *risas*
are part of the official record and a separable audio-event-detection subtask.

**D15. The scoring harness does not measure restraint.** The rubric rewards knowing when
not to answer; the evaluation must report accuracy at each coverage level, not a single
accuracy.

**D16. Provenance.** Several load-bearing figures were taken from search-result
summaries because the primary texts could not be opened.
A fact-check pass was run for this report; its verdicts are in §2.1 and the dossier was
corrected accordingly.

#### 2.1 Fact-check verdicts on the dossier’s §11 figures

**[Analysis]** Eighteen figures from the dossier’s §11 were checked against primary
sources where the network allowed, and otherwise against multiple independent summaries.
Verdicts: 10 confirmed, 5 adjusted, 3 unverified.
The dossier has been corrected for every adjusted item.

| # | Claim | Verdict | What the check found |
| --- | --- | --- | --- |
| 1 | Albayzin RTVE 2022: DER 37.2% with identity assignment, 44.34% diarization-only | Confirmed | Intelligent Voice system, ISCA archive. Added context from a second pass: **74 enrolled speakers, 54 hours**. The “500+ hours” database size was not confirmed. |
| 2 | Open-set EER 1.43% → 3.04% at 100 → 700 speakers | Confirmed | arXiv 1904.01269; neural systems hold their advantage as the set grows. |
| 3 | Streaming Sortformer: 4-speaker cap; 13.24% vs 42.56% DER; 0.32 s chunk 19.32/11.50 | Confirmed | arXiv 2507.18446 and the model card; the cap is on concurrently tracked speakers (see D2). |
| 4 | Estonia HANS: 1,500 h; target 93–95%; ~5% error; recognizes MPs and ministers | Confirmed | e-Estonia, Finestmedia, IPU; the “10-minute clips” detail is consistent but not directly confirmed. |
| 5 | Portugal STAAR: WER 1.7–11.3% | **Unverified** | WhisperX-based, with diarization, IJST 2024, 724 hours processed; the WER range could not be read from an accessible source. |
| 6 | Pangeanic: €462,835; €2.85 and €1.65 per minute; 2025, extendable | Confirmed for the lot | The framework total is reported as “3.9 millones”; the exact €3,990,315.14 and the €919,669 2025 spend were **not independently confirmed**. |
| 7 | Congreso outbound signal carries the interpreter over the original plus subtitles | Confirmed by a second pass | The fact-check pass could not reach a primary source; the interpreter-overlay pass found three (El Debate, El Español, elDiario.es) and added the 2–4 s remote-interpreter lag. |
| 8 | whisper-turbo 597× on H100; pyannote community-1 31 s/h, precision-2 14 s/h | Adjusted | 597× and 404× at batch 32 confirmed; **31 s/h is the AMI figure; DIHARD3 short files run at 37 s/h**; precision-2 is 14 s/h on both. |
| 9 | Overlap: 1.2% news, 10.4% debates, ~16% heated debate; 6–14% of words | Confirmed | ETAPE and meeting-corpus literature. |
| 10 | Bundestag: over half of speeches heckled, 3.49 on average | Confirmed | Hailer (2026), ScienceDirect. |
| 11 | VoxPopuli: Spanish 166 h, 305 speakers; 1.8K h transcribed | Confirmed, one fix | **16 languages** with transcribed data, not 15 (README and paper). |
| 12 | EuroSpeech: 61k h, 22 parliaments; Spain? | Adjusted | Figures confirmed from the README; **Spain absent** from both `countries/` (23 entries) and `data_sourcing/` (26). |
| 13 | Short-duration EER 8.72% → 12.8% (3.59 s → 2.05 s) | Confirmed | arXiv 1810.10884. |
| 14 | AA-WER v2.0: Scribe v2 2.3%, Gemini 3 Pro 2.9%, Gemini 3 Flash 3.1% | Adjusted | **Gemini 3 Flash is 3.2%**; 3.1% is Gemini 2.5 Pro; Voxtral Small is 3.0%. |
| 15 | AssemblyAI cpWER 30.17 / Scribe v2 35.26; NOTSOFAR tcpWER 22.99 / 14.27 | Confirmed (vendor) | AssemblyAI benchmark page across DiPCo, CALLHOME, NOTSOFAR, AMI; the CHiME figures were not re-checked. |
| 16 | Vanilla large-v3 on CV13: es 4.38, ca 13.67, gl 12.46, eu 38.85; FLEURS es 15.01 | **Unverified** | The Spanish FLEURS figure is implausibly high for large-v3 and is probably a pipeline-specific or misattributed number; needs the paper. |
| 17 | Co-official languages permitted: Catalan, Basque, Galician, Valencian, Aranese, Asturian | Adjusted | **Asturian is not co-official and is not covered**; the reform covers languages with official status in an autonomous community: Catalan (and Valencian as its legal name in Valencia), Basque, Galician, Aranese. |
| 18 | ECAPA-TDNN 0.87% EER; pyannote 4.0.3 VRAM >9.54 GB vs 2.59 GB | Adjusted | The issue’s profiling table shows **1.59 GB** for 3.3.2 (the title says 2.59); 9.54 GB and the 72-minute file are confirmed; the ECAPA figure is the original paper’s. |

### 3. What the deeper research changes

This section reports what eight parallel research passes added to the picture.
Each subsection ends with what it changes.

#### 3.1 The problem, restated precisely

**[Analysis]** Inputs, in decreasing order of availability to a participant:

| Input | Availability | Identity signal |
| --- | --- | --- |
| Conference-system microphone events (seat, delegate, mic on/off) | private to the chamber; exposed by Televic CoCon and Bosch DICENTIS APIs | near-deterministic |
| Floor audio, isolated | only with chamber cooperation | acoustic |
| Interpreters’ own feeds | exist before the broadcast mix; interpreters work remotely | translation, and removes the overlay |
| Public stream audio (interpretation mixed on co-official-language turns) | public | acoustic, degraded on the multilingual tail |
| Video (camera on speaker; on-screen name-and-group overlay) | public | visual, textual |
| Order of business / agenda | public, in advance | prior over candidates |
| Chair’s spoken announcements | inside the transcript | near-deterministic when present |
| Member roster with groups and roles | public | candidate set |
| Archive of past interventions, named and segmented | public | enrollment |

Outputs: a time-ordered sequence of interventions, each carrying **(speaker name or
abstention, language, verbatim text, record-form text, timestamps, evidence and
confidence)**, plus stage directions, delivered incrementally with a bounded
finalization lag.

**[Analysis]** Sub-problems, each scorable on its own:

1. Voice activity and segmentation, including hallucination suppression on non-speech.
2. Language identification at sub-utterance granularity.
3. Transcription per language, with code-switching.
4. Turn segmentation.
5. **Named attribution of each turn**, with abstention.
6. Editorial normalization to record form.
7. Stage-direction detection.
8. Incremental delivery: latency, revision, finalization.

**[Analysis]** Regimes that change what a number means, recorded with every result: turn
type (rostrum, bench reply, chair procedural, interjection, question time); language and
channel condition (Spanish; co-official on an isolated channel; co-official
interpretation-mixed); audio source (floor feed, public stream, archive MP4); candidate
set (unconstrained roster, agenda-constrained); latency mode (offline, bounded-lag with
the lag stated).

#### 3.2 The interpreter overlay, confirmed and decomposed

**[Press]** Three independent reports from September 2023 (El Debate, El Español,
elDiario.es) describe the same design: when a deputy speaks Catalan, Basque or Galician,
the single broadcast audio track carries the original voice with the Spanish
interpreter’s voice superimposed, plus burned-in Spanish subtitles.
The interpreters work remotely, which introduces a **2–4 second lag** between source and
interpretation.
Neither the YouTube stream nor the archive MP4s expose selectable tracks.
One report notes that users of the Congreso’s co-official-language web portal “could
only hear their co-official language by eliminating the dubbed Spanish superimposed on
the parliamentarian’s voice,” which hints that a lower-gain or original-only variant may
exist on that portal; this is unconfirmed.
A 2026 contract notice (BOE-B-2026-16554) formalizes acquisition of an **automatic live
subtitling system**, separate from the 2024 translation-and-transcription framework.

**[Technical]** The reference design exists next door: the European Parliament’s
multimedia service offers **up to 32 selectable audio channels per stream** — the floor
language plus interpretation into each official language — and on-demand recordings keep
that structure.

**[Technical]** What the signal-processing literature offers against the mix:

- **Speaker verification under overlap** degrades severely; target-speaker extraction as
  a preprocessing step recovered a **65.7% relative EER reduction** on overlapped
  multi-talker verification (arXiv 1902.02546), and recursive attentive pooling extracts
  per-speaker embeddings from multi-speaker recordings (arXiv 2408.17142).
- **Target-speaker extraction toolkits** with enrollment conditioning are mature (WeSep:
  ECAPA-TDNN enrollment driving TF-GridNet extraction; USEF-TSE and LExt drop the
  fixed-length embedding).
  Blind separation reaches 23–26 dB SI-SNRi on synthetic two-speaker mixtures
  (TF-GridNet, MossFormer2, SPMamba) and degrades sharply on real recordings.
- **No published work addresses separating simultaneous interpretation from its source
  speech** — two languages, one lagging the other by seconds — on real data.
  The EPIC interpreting corpus has aligned source and interpretation but was built for
  translation studies.
- **Language identification** for the four Iberian languages is available off the shelf
  (SpeechBrain ECAPA on VoxLingua107, 6.7% overall error, with elevated Spanish–Galician
  confusion), and a diarization-then-LID cascade for institutional speech reported 10%
  relative language-diarization error reduction and over 8% relative WER reduction
  (Valente et al., Interspeech 2024). Whisper’s own LID is one token per 30-second
  window and is not usable for switch detection.

**[Analysis] What this changes.** Four attacks on the overlay, ranked by feasibility:

1. **Obtain the interpreters’ feeds** before mixing.
   They exist as separate streams; whether an external party can get them is a
   contractual question, not a technical one, and it is the first thing to ask.
2. **Enroll the interpreters.** The pool is roughly a dozen people.
   Their voices are then a small closed set; the interpreter’s channel becomes a
   recognized speaker whose Spanish output *is* the translation the record needs.
3. **Target-speaker extraction on the deputy** using the archive enrollment profile,
   then ASR and identification on the extracted signal, with LID as a gate.
4. **Blind separation**, last, because nothing in the literature has been validated on
   this mixture type.

Direction 3 on real interpreted audio is a research contribution in its own right (§8).

#### 3.3 A production operator’s own benchmark and workload

**[Primary]** The research for this report surfaced a company that transcribes
parliamentary sessions in production and publishes its evaluation: **Parlamento.ai**,
whose GitHub organization `Parlamento-ai` carries `open-source-asr` (August 2026), a
reproducible study of whether open ASR models can replace the paid APIs it uses.
The repository was cloned and read directly.
Its coverage, per the study’s own workload data, is Chile, Spain, the United States, the
European Parliament, Brazil and Peru.

**[Primary]** Key numbers, all from the repository:

| Item | Figure |
| --- | --- |
| Sample | 168 parliamentary clips, 13.79 hours, 24 per group: Spanish, Catalan, Galician, Basque, English, Portuguese, multilingual (European Parliament) |
| Metric | word-level edit distance between an open model’s output and each paid API’s output, median over APIs — **agreement, not accuracy**; the README says so explicitly: “No existe una transcripción humana corregida” |
| Paid APIs’ disagreement among themselves | 7.17% on the 72-clip Spanish+English+Portuguese set; 5.10% on Spanish alone |
| Best open model, Spanish (Peninsular and Latin American pooled) | `whisper-large-v3-turbo`, **3.25%** difference from the paid consensus |
| Catalan | turbo, 9.56% (paid disagreement 11.73%) |
| Galician | turbo, 14.71% (paid 12.98%) — outside the operating band |
| Basque | `whisper-large-v3-eu`, 21.29% (paid 15.06%) — outside the band; the README notes this model declares Basque Parliament training data, so its result needs a temporal hold-out before it counts as generalization |
| Throughput on the study’s 8 GB GPU | turbo 35.6 audio-hours per hour; Granite Speech 4.1 133.4; Parakeet TDT 0.6B v3 86.8 |
| Sustained concurrency test (faster-whisper, int8, 5 s windows) | 8 simultaneous sessions at p95 latency 2.36 s, all windows on time |
| Production load, 90 days, five-minute windows | active sessions p50 **3**, p95 **12**, p99 **17**, max **31** |
| Volume, 30 days | **1,869 audio-hours**; Chile 789.5 h, Spain 461.4 h, US 284.4 h, EU 234.3 h, Brazil 48 h, Peru 45.4 h |
| Paid transcription cost, 30 days | **USD 1,334.68**, i.e. **USD 0.71 per audio-hour** (Deepgram nova-3 and OpenAI transcribe models) |
| Estimated occupancy of one turbo GPU at that load | 10.4% |

**[Analysis] What this changes.** Four things.

- **In-domain ASR is not the problem, and Spanish-variety transfer is not the problem.**
  Turbo’s disagreement with paid APIs on pooled Peninsular and Latin American Spanish is
  smaller than the paid APIs’ disagreement with each other.
  That retires D12’s accent worry for Spanish and confirms the dossier’s ranking of raw
  WER as the lowest-leverage term.
- **Galician and Basque are the open tail**, by the operator’s own numbers, and the
  operator has no ground truth to say whether the open models or the paid ones are
  wrong.
- **The market price is a tenth of what §11.2 implied.** The dossier priced the task at
  the Congreso’s human-service contract, €1.65–2.85 per minute; the API cost is USD 0.71
  per *hour*. The per-minute contract price is what a chamber pays for a deliverable
  record; the per-hour price is what raw ASR costs.
  The difference is the value of everything downstream of ASR — attribution, editing,
  publication — and it is roughly two orders of magnitude.
- **Even the production operator measures agreement, not accuracy, and does not measure
  attribution at all.** The `open-source-asr` study has no speaker metric.
  The benchmark proposed in §5.1 would be the first ground-truth evaluation in this
  domain, and the operator’s own methodology section says such a reference is what it
  lacks.

**[Analysis]** Whether Parlamento.ai has any relationship to Reversa or to the Madrid
Open pipeline is **not established**: no source connects them, and Reversa’s public
positioning is regulatory compliance.
Its coverage — Spain plus Chile, Peru and the European Parliament — is nevertheless the
most concrete public example of what “two chambers from different parts of the world”
could mean, and its workload profile is the best available description of what a
production system in this niche actually handles: a median of three concurrent sessions
and a tail of thirty.

#### 3.4 Joint speaker-attributed ASR

**[Technical]** The 2025–2026 literature has moved from cascaded diarize-then-transcribe
toward models that condition transcription on speaker activity or emit speaker structure
directly:

| System | What it does | Conditioning | Weights | Best reported figure |
| --- | --- | --- | --- | --- |
| DiCoW (BUT, ICASSP 2025) | target-speaker Whisper via frame-level Silence/Target/Non-target/Overlap masks injected into every encoder layer | diarization output | open, CC BY 4.0 | cpWER 18.1% AMI-SDM, 18.4% NOTSOFAR-1 |
| SE-DiCoW (ICASSP 2026) | self-enrolled: selects the enrollment segment by cross-attention | diarization + enrollment | open | 52.4% relative tcpWER reduction vs DiCoW on the EMMA multi-domain benchmark |
| SA-DiCoW (arXiv 2510.03723) | DiCoW encoder + serialized-output decoder, end-to-end multi-talker | diarization | open | cpWER 18.1% AMI-SDM, 17.2% Libri3Mix |
| Dixtral (Mistral/BUT, Interspeech 2026) | DiCoW encoder inside the Voxtral speech LLM | diarization | not yet | macro cpWER **15.4%** across AMI, NOTSOFAR-1, LibriSpeechMix, Mixer6, vs **44.4%** for Gemini 3.0 Flash |
| MOSS Transcribe Diarize 0.9B (OpenMOSS) | single-pass long-form transcription with speaker labels, 90 minutes, 50+ languages | none | open, 0.9B | cpCER 7.37 Podcast, 12.76 Movies, 15.83 AISHELL-4 [Primary, README] |
| TagSpeech (ACL 2026) | joint ASR, diarization and timestamps | none | open | best DER on AMI and AliMeeting, ~28% relative on AMI |
| SpeakerLM (AAAI 2026) | speaker diarization and recognition with a **speaker registration** mechanism that maps enrolled names into the output | audio + enrolled embeddings with names | not released | outperforms cascades; no cpWER located |
| NVIDIA multitalker-parakeet-streaming 0.6B | streaming multi-talker ASR | speaker activity | open | English only |

**[Technical]** Commercial enrollment: Speechmatics accepts voiceprints (5–30 s clips)
and names, **up to 50 identities per session**; OpenAI’s `gpt-4o-transcribe-diarize`
accepts **up to four** named reference clips; pyannoteAI and Picovoice Eagle offer
voiceprint identification (Azure AI Speaker Recognition was retired on 2025-09-30 and
Amazon Connect Voice ID reached end of support on 2026-05-20, so neither hyperscaler
enrollment API exists any more); Gemini transcription, ElevenLabs Scribe v2, AssemblyAI
Universal-3.5 and Deepgram expose anonymous diarization only.

**[Analysis] What this changes.** No system outputs real names from audio alone; every
named output today goes through enrollment.
The commercial enrollment caps (4, 50) are an order of magnitude below a 350-seat
roster, so the roster has to be handled by a candidate-set constraint or a self-hosted
identifier. The DiCoW family is the strongest open line for the transcription side:
multilingual, open weights, and a 52% relative tcpWER gain from *enrollment
conditioning* — exactly the input a parliament has for free.
MOSS Transcribe Diarize is the natural long-form, low-overlap baseline; its Podcast and
Movies results are the closest published regime to a plenary.
The published benchmarks still overweight overlap: nobody reports cpWER on Europarl-ASR
or VoxPopuli, which is another argument for §5.1.

#### 3.5 Speaker identification at watchlist scale

**[Primary]** Current open embedding extractors, EER on VoxCeleb1-O/E/H (read from the
WeSpeaker, 3D-Speaker and ReDimNet2 repositories):

| Model | Params | Vox1-O | Vox1-E | Vox1-H | License |
| --- | --- | --- | --- | --- | --- |
| ReDimNet2-B6 (VoxBlink2 + VoxCeleb2, LM) | 12.3M | **0.23** | 0.35 | 0.67 | MIT |
| WeSpeaker W2V-BERT2.0-MFA (LM + AS-norm + QMF) | large | **0.138** | 0.285 | 0.625 | Apache-2.0 |
| WeSpeaker SimAM-ResNet100 (LM + AS + QMF) | 50.2M | 0.202 | 0.421 | 0.795 | CC BY 4.0 |
| WeSpeaker ResNet293 (LM + AS + QMF) | 28.6M | 0.425 | 0.641 | 1.146 | CC BY 4.0 |
| 3D-Speaker CAM++ (LM + AS) | 7.2M | 0.659 | 0.803 | 1.569 | Apache-2.0 |
| ECAPA-TDNN 1024 (LM + AS + QMF) | 14.7M | 0.707 | 0.894 | 1.615 | CC BY 4.0 |
| ReDimNet2-B3 | 4.1M | 0.42 | 0.66 | 1.22 | MIT |

**[Primary]** Two recipe facts from the same repositories: cosine scoring beats PLDA
under margin-based training (WeSpeaker ResNet34: 0.797 vs 1.207 EER), and adaptive
s-norm is worth roughly 16% relative (ResNet293: 0.532 to 0.447), with QMF adding about
5% more.

**[Technical]** Open-set at scale: VoxWatch (arXiv 2307.00169) shows the maximum
out-of-set score rising with watchlist size, inflating false alarms; **AS-norm alone is
not guaranteed to help open-set identification**, while score calibration and
multi-system fusion give the largest gains, with FAR at 5% FRR improving 21% relative
(3.7% to 2.9%) at the largest watchlist.
Novoselov et al. (arXiv 1904.01269) report EER roughly doubling from 100 to 700 enrolled
speakers, and that discriminatively trained neural systems hold their advantage as the
set grows. Trainable AS-norm (arXiv 2504.04512) improves EER 4.1% and minDCF 10.6%
relative over standard AS-norm; language-dependent s-norm (arXiv 2007.07689) is worth
about 3.3%. Short duration: ERes2NetV2 reports 0.98% EER at 3 s against 1.48% at 2 s on
Vox1-O. The Iberian cross-lingual study (arXiv 2607.01161, IberSPEECH 2026) finds that
language switching shifts a speaker’s embedding systematically, that language mismatch
dominates speaker variability in that shift, and that Spanish–Galician is the smallest
shift.

**[Analysis] What this changes.** The recipe for a 350-person same-room watchlist is now
specific enough to pre-register (§7, R2): ReDimNet2-B6 or W2V-BERT2.0-MFA embeddings;
centroid enrollment from several archive clips per member; cosine scoring; adaptive
s-norm with the other members as cohort, language-dependent where a member is bilingual;
a logistic calibrator trained on in-domain trials; a threshold set for a false-alarm
rate below 1/N per session, with everything under it emitted as “unknown.”
The open question D6 raises — how much same-room enrollment helps — is still unmeasured;
the 30–50% relative figures sometimes quoted for matched conditions are generic and are
not relied on here.

#### 3.6 The precedent: REPERE

**[Technical]** REPERE (2012–2014) evaluated, on French television, exactly this task:
“who is speaking?” and “who is on screen?”, **by name**, using audio, video, and OCR of
on-screen name overlays, scored with an Estimated Global Error Rate (EGER). On the 2013
test, the PERCOL consortium’s supervised speaker identification reached **EGER 24.4%**
and its unsupervised variant **36.3%**; PERCOL ranked first in 2014. The winning
architecture fused speaker diarization with voice biometrics, face detection and
recognition, and OCR of overlays, propagating names from overlaid text to co-occurring
speaker and face clusters.

**[Technical]** Modern components for the same design: active speaker detection at 92.3%
(TalkNet), 94.1% (Light-ASD), 95.2% (LoCoNet) and 95.5% (LoCoNet + TalkNCE) mAP on
AVA-ActiveSpeaker, all with open weights; ArcFace/InsightFace for face matching; and a
2026 Italian parliamentary pipeline that runs OCR preserving reading order, then a
vision-language model for transcription refinement and speaker identification, linking
speakers to the Chamber’s knowledge base by SPARQL and fuzzy matching (arXiv
2603.28103). The Congreso’s archive videos carry an on-screen name-and-group overlay
[Technical, search summary; verify on a downloaded clip].

**[Analysis] What this changes.** The “unsupervised naming from overlays” idea is twelve
years old and worked; in a parliament the overlay is the chair’s announcement and the
on-screen chyron, and the fusion design in R3 is a reimplementation of PERCOL with
better components rather than a new idea.

#### 3.7 Text-side attribution

**[Technical]** DiarizationLM (arXiv 2401.03506) — an LLM post-processing ASR and
diarization output — cut word diarization error rate by **55.5% on Fisher and 44.9% on
CALLHOME**; text-only speaker-change detection is competitive with audio in short
conversational contexts (arXiv 2506.11344); an ensemble LLM trained on three ASR tools’
transcripts generalizes across ASR systems (Speech Communication 2025). **No published
system parses formulaic floor-yielding phrases** (“tiene la palabra”, “I call on…”) for
speaker assignment; the search for one was exhaustive and came back empty.

**[Analysis] What this changes.** The chair-announcement parser in R3 is a gap in the
literature, not a reproduction, and DiarizationLM is the template for the correction
stage that consumes it.

#### 3.8 Conference-system metadata

**[Technical]** Both dominant parliamentary conference systems expose the attribution
signal directly. Televic’s CoCon API is a publish/subscribe HTTP service (`/CoCon/` on
port 8890) whose event models carry microphone on/off, delegate identity, and
request-to-speak queue state.
Bosch DICENTIS (OMNEO) exposes delegate name, seat, and active-microphone status over a
REST API and drives automatic camera switching from microphone events.

**[Analysis] What this changes.** In a chamber where the transcription system is
integrated with the conference system, attribution is a join on microphone events and
the research problem shrinks to the exceptions: members speaking on a neighbour’s
microphone, interjections without a microphone, and the chair.
For the public-data setting the metadata is unavailable, but any design should have a
slot for it, and any evaluation should report what fraction of turns it would resolve.

#### 3.9 Streaming and revision metrics

**[Technical]** The simultaneous-translation and streaming-ASR literature has the
instruments D10 asks for:

| Metric | Measures | Origin |
| --- | --- | --- |
| Average Lagging (AL), DAL, LAAL, StreamLAAL | delay between input consumption and output emission; LAAL corrects AL’s length bias; StreamLAAL extends it to unsegmented streams | Ma et al. 2019; Cherry and Foster 2019; Papi et al. 2022; Macháček et al. 2025 |
| Computation-aware vs computation-unaware latency | wall-clock including inference vs the timer paused during inference (a lower bound) | SimulStreaming |
| Unstable Partial Word Ratio, Unstable Partial Segment Ratio | fraction of emitted partial words later changed; frequency of revision events | Shangguan et al. 2020 |
| Normalized Erasure | tokens deleted across revisions over final length | Arivazhagan et al., IWSLT 2020 |
| Flicker | displayed words changing or disappearing; addressed by partial-hypothesis reranking | Bruguier et al. 2023 |

Reference points: Whisper-Streaming’s LocalAgreement policy at 3.3 s mean latency on
European Parliament speech; SimulStreaming about five times faster; NeMo cache-aware
FastConformer at 80–160 ms chunks; pyannote’s Live-1 streaming diarization claims
sub-300 ms latency at 11.2% DER [vendor]; AssemblyAI’s streaming diarization emits
labels live with a single revision about 0.5 s after stream end [vendor]. Bounded-lag
designs with a finalization pass are standard: two-pass RNN-T plus attention rescoring,
streaming deliberation with fast and slow encoders, and U2-style CTC first pass with
full-context second pass.

**[Analysis] What this changes.** §4.5 adopts these definitions; the five-minute claim
becomes: finalization lag p95, name revision rate, and first-versus-final name accuracy,
all reported.

#### 3.10 Data and tooling

**[Primary/Technical]** Corrections and additions to the dossier’s dataset table:

| Corpus | Language | Size | Speaker labels | License | Note |
| --- | --- | --- | --- | --- | --- |
| **ParlamentParla v2.0** (OpenSLR 59) | Catalan, Parliament of Catalonia | **611 h** (211 clean + 400 other) | **yes**, with gender; speaker-disjoint splits | CC BY 4.0 | [Primary, README] |
| **Basque Parliament 1** (`gttsehu`) | Basque + Spanish, bilingual | ~1,445–1,462 h train + 17 h supervised | **yes**, plus per-segment language tag | not stated | closest thing to a Spanish-state SA corpus with code-switch labels |
| **Nos_ParlaSpeech-GL** (Proxecto Nós) | Galician, Parliament of Galicia | 1,600+ h, auto-aligned | unconfirmed | CC BY 4.0 | plus Nos_TranscriSpeech-GL, 53 h manual |
| **ParlaMint-ES, -ES-CT, -ES-GA, -ES-PV** (v5.0) | Spanish, Catalan, Galician, Basque; text only | ~15M words (ES) | name, party, gender, birth year, role | CC BY 4.0 | no audio alignment for any Spanish-state corpus |
| **RTVE / Albayzin** | Spanish broadcast | 500+ h; a 2020 subset reportedly includes parliamentary material from 2016 | named, closed set (74 speakers in SDIAC 2022) | licence agreement | [Technical] |
| **VoxPopuli** | European Parliament | Spanish 166 h transcribed, 305 speakers | yes | CC0-style | [Primary] |
| **EuroSpeech** | 23 national parliaments | 61k h | no | TBD | **Spain absent** [Primary, repository tree]; the aligner is the reusable part |

**[Primary/Technical]** Raw sources for Spain: the Congreso’s *intervenciones* open data
carries speaker, group, initiative, organ and date per intervention (no timestamps or
video links); the Archivo Audiovisual offers MP4 at session, agenda-item, and
**individual-intervention** granularity; the *Diario de Sesiones* is HTML for current
legislatures with the speaker-label and stage-direction conventions in D5, and since
2023 prints co-official-language interventions with a Spanish translation; the Senado
integrated its open-data catalogues into datos.gob.es in February 2026. No open-source
project was found that scrapes and aligns Congreso audio with transcripts end to end; a
`civis-api` wrapper covers the open-data JSON only.

**[Primary]** Tooling: `ctc-forced-aligner` (MMS-300m-1130, windowed 30 s with 2 s
overlap, all four languages); WhisperX alignment models for es, ca, eu and gl in its
default table; the EuroSpeech `alignment_pipeline` (VAD and diarization segmentation,
Whisper draft, CER-based two-stage alignment to PDF/DOCX/HTML/SRT/TXT transcripts);
MeetEval for cpWER, tcpWER, ORC-WER and DER; dscore and pyannote.metrics for DER; Lhotse
for manifests; `yt-dlp` for Canal Parlamento.

#### 3.11 The editorial gap, quantified

**[Technical]** Every corpus built from official parliamentary records documents the
same transform between speech and record:

| Corpus | What it measured |
| --- | --- |
| Europarl-ASR (Interspeech 2021) | 1,300 h with three transcript tiers — official, filtered, verbatimized; 17.5 h manually verbatimized; automatic filtering plus verbatimization improved ASR WER by **9 points** over training on raw official text |
| EuroSpeech (NeurIPS 2025) | of aligned parliamentary segments, **41% fall under CER 10%, 65% under CER 20%, 78% under CER 30%**; the rest cannot be aligned at all |
| Finnish Parliament (2023) | transcribers remove hesitations, fix grammar, and convert spoken to written forms; cleanup drops 4.9% of audio in the 2015–2020 set and **10.3%** in the more heavily edited 2008–2016 set |
| Danish FT Speech (2020) | official transcripts omit disfluencies, correct slips, add context |
| Althingi (2017–2019) | post-editing ASR output is faster than re-transcription **only below about 25% WER** |
| Swiss SPC_R (2025) | a two-step GPT-4o correction of Whisper output against the minutes — an LLM editorial transform in production |
| UK Hansard (Mollin 2007) | omits repetitions, corrects grammar; conventions shift over time |

**[Technical]** Tooling for the text side, Spanish and co-official: NeMo text-processing
carries WFST inverse-text-normalization grammars for Spanish (es-ES and es-US variants);
BERT-family punctuation restoration reaches roughly 94% overall accuracy with Spanish
macro-F1 near 60%; Softcatalà’s Catalan–Spanish NMT reports BLEU 87.5 on its internal
test and 24.2 on Flores-200; BSC’s SalamandraTA covers Spanish, Catalan, Basque and
Galician; Apertium is a rule-based baseline for the closely related pairs; SenseVoice
and PANNs (AudioSet mAP 0.439) detect applause and laughter out of the box,
unbenchmarked on chamber acoustics.

**[Technical]** No standard “record-WER” exists.
The nearest established instrument is **HTER**, the human-targeted translation edit rate
from machine-translation post-editing: word-level insertions, deletions, substitutions
and shifts needed to reach the target, normalized by length.
Deployments do not publish a verbatim-versus-record split: Estonia reports 93–95%
correctness against the final record; Portugal reports its range against the edited
*Diário*.

**[Analysis] What this changes.** The editorial gap is not noise and not small: on the
largest corpus, a third of aligned segments differ from the record by more than a fifth
of their characters.
§4.2 therefore carries both an editorial-gap metric and HTER against the record, and R6
is a learnable task with free paired data, not a caveat.

### 4. Measurement: a protocol that can show improvement

#### 4.1 Reference construction

**[Proposed]** Two reference layers for every evaluated session:

- **Verbatim layer.** What was actually said, with disfluencies, restarts, and
  interjections, with word-level timestamps.
  Produced by correcting an ASR draft against the audio and forced-aligning it; a sample
  is checked by a second annotator.
- **Record layer.** The published *Diario de Sesiones*, aligned at intervention level
  using its own speaker labels and, where alignment succeeds, at word level.

Every intervention in both layers carries: canonical roster id; role at that moment;
language(s); start and end; channel condition (isolated, interpretation-mixed); and
whether the chair announced the speaker in the preceding 30 seconds.
The two layers make the editorial gap a number rather than a caveat.

#### 4.2 Metric vector, with roles

**[Proposed]** Recorded on every experiment, fixed before the first measurement.
Roles follow the repository’s experiment-loop convention: `outcome` is what an accept
rule scores; `cost` qualifies a win; `guard` rejects regardless of outcome; `mechanism`
explains and never decides.

| Metric | Definition | Role | Why |
| --- | --- | --- | --- |
| **Turn attribution accuracy at coverage c** (TAA@c) | among reference interventions the system chose to name at the threshold giving coverage c, the fraction named correctly; report the full coverage–accuracy curve and TAA@{100, 95, 90}% | outcome | the stated objective, with the restraint weighting built in |
| **cpWER**, **tcpWER** (5 s collar), verbatim reference — **fixed-label form** | roster ids as speaker labels with **no permutation search**; MeetEval’s permutation-invariant cpWER is reported alongside but cannot score a wrong name, because it re-maps labels to the best match | outcome | word-level attribution; what a fully automatic record would score |
| **record-WER** | WER of record-form output against the record layer | outcome for normalization work | the number a stenographer cares about |
| **HTER against the record**; **edited-segment fraction** | word-level edits (with shifts) per reference word to reach the record; share of interventions needing any edit | outcome for normalization work | post-editing effort, the metric deployments implicitly optimize |
| **Editorial gap** | record-WER of the verbatim reference itself against the record layer | mechanism | the floor imposed by editing |
| **WER**, verbatim reference, Whisper-normalized | speaker-agnostic | mechanism | isolates transcription |
| **DER**, dscore, 0.25 s collar, overlap scored | anonymous labels, optimal mapping | mechanism | comparability with the literature; never decides |
| **Identification error rate** (pyannote.metrics `identification`), 0.25 s collar | time-weighted error with **fixed named labels**, no permutation | mechanism | the time-based counterpart of TAA; the only off-the-shelf scorer that penalizes a wrong name |
| **Cllr, minDCF, actDCF** at the deployed threshold | on the identification trials a session generates | mechanism / guard | actDCF far above minDCF means the threshold is wrong |
| **Speaker-count error** | predicted vs reference distinct speakers | guard | collapse or over-splitting |
| **Hallucination rate** | inserted words per hour in reference non-speech | guard | invented text fails regardless of attribution |
| **Finalization lag** p50/p95; **name revision rate**; **normalized erasure** on text; **first-vs-final name accuracy** | §3.9 definitions | cost | the five-minute claim, made measurable |
| **GPU-seconds per audio-hour**, peak VRAM | end to end | cost | feasibility; Parlamento.ai’s load profile is the sizing reference |

**[Analysis]** DER is demoted to `mechanism` for the reason in D3. TAA is defined with
coverage because a system that names 60% of turns at 99% and abstains on the rest is,
under this rubric, possibly better than one naming 100% at 90%, and a single accuracy
hides that.

#### 4.3 Stratification

**[Proposed]** Every outcome metric is reported overall and by turn type, duration
bucket (<3 s, 3–10 s, 10–60 s, >60 s), language and channel condition, candidate-set
condition, and chair-announcement presence.
The stratified table is the deliverable; the headline is its summary.

#### 4.4 Abstention and calibration

**[Proposed]** The attribution component emits, per turn, a distribution over the
candidate set plus an “unknown” mass.
Sweep the threshold; plot coverage against TAA; report the area and the fixed points.
Calibration is checked with expected calibration error over turns and Cllr over trials.
A change that raises TAA@100% and lowers TAA@90% is not an improvement under the rubric.

#### 4.5 Latency and revision

**[Proposed]** Log every emission with its wall clock and every later change to it.
Report finalization lag, name revision rate and text normalized erasure separately, and
first-published-name accuracy against final-name accuracy so that “early and wrong” is
visible. Use computation-aware latency; the computation-unaware figure is a lower bound
only.

#### 4.6 Fixing the protocol

**[Proposed]** One versioned scoring harness: MeetEval for permutation-invariant cpWER,
tcpWER and DER, plus fixed-label variants of cpWER and tcpWER and pyannote.metrics’
identification error rate for named labels; a per-turn scorer for TAA and the coverage
curve; the Whisper normalizer for WER; fixed collars; overlap always scored; roster ids,
not name strings. A figure without the harness version and the regime fields of §3.1 is
not a result.

#### 4.7 Statistical discipline

**[Proposed]** Sessions are the unit of independence; turns within a session are
correlated. Compare systems **paired by session**; report the mean and range of the
per-session differences; the interval is a **paired *t* interval on the per-session
differences**, and the accept rule is that the 95% interval excludes zero and no guard
is breached. Weight sessions by scored turns, or by precision once the heterogeneity of
the gain across sessions has been estimated from the first real comparison.
This replaces the first draft’s median-and-bootstrap rule: the sizing simulation (§10.3)
found that the percentile bootstrap of the mean rejects a true null 8.2% of the time at
20 sessions, that the bootstrapped median is conservative and costs about a third of the
benchmark in power, and that the paired *t* interval is calibrated at every size tested.
With 20 sessions the interval on overall TAA is about ±3.6 points and the smallest
detectable change on the short-turn stratum about 15 points; an improvement inside the
interval is “no detectable effect.”
Prefer uniformly medium-length sittings, since the variance runs on the harmonic mean of
turns per session.
This is the accept rule in §9, written before anything is measured and
amended once, by simulation, before the first measurement.

### 5. Datasets: what to use and what to build

**[Analysis]** For training and adaptation, the usable corpora are ParlamentParla
(Catalan, speaker-labeled), Basque Parliament 1 (bilingual, speaker- and
language-labeled), Nos_ParlaSpeech-GL (Galician), VoxPopuli Spanish (European
Parliament, speaker-labeled), and the Diario-aligned Congreso and Senado material the
recipe below produces.
For evaluation, none of them is the target domain with named attribution, which is why
the benchmark has to be built.

#### 5.1 The benchmark that does not exist

**[Proposed]** Build recipe, refined with §3.10:

1. **Select sessions.** 20–50 Congreso plenaries and committee sessions across
   legislatures XIV–XV, stratified over question time, legislative debate, and sessions
   with co-official-language interventions; add Senado sessions for a second chamber
   and, if the “different parts of the world” reading matters, Chilean sessions, whose
   Senado and Cámara both publish downloadable video and speaker-labeled records.
2. **Acquire audio and video** at intervention granularity from the Archivo Audiovisual,
   and the full-session stream, keeping both so the channel condition is a variable;
   inspect the MP4 audio layout with `ffprobe` to settle whether any original-only track
   exists.
3. **Parse the record** from the Diario HTML into interventions with names, roles, stage
   directions, and, post-2023, original-language and translation blocks.
4. **Align** record to audio at intervention level with the EuroSpeech aligner, then at
   word level with `ctc-forced-aligner`.
5. **Produce the verbatim layer** for a subset by correcting a `whisper-large-v3-LoS`
   draft against the audio, then forced-aligning.
6. **Build the enrollment set** from held-out archive clips for every roster member,
   several clips per member, with the evaluation sessions excluded.
7. **Annotate regimes** automatically (duration, LID, chair-announcement parser) and by
   hand for a sample.
8. **Freeze splits** and the harness version; publish the manifests, not the audio.

### 6. Tools

The full inventory (about 260 tool rows, 35 datasets and 23 services, each with links,
licence, language coverage, key numbers, recorded signals and a label) is the companion
report
[Tooling Inventory for Real-Time Parliamentary Transcription with Named Speaker Attribution](research-2026-09-02-parliamentary-transcription-tooling-inventory.md).
This section keeps what a reader needs to start: the label scales, the shortlist by
pipeline stage, the index from research direction to toolchain, and the list of things
to avoid.
The inventory was built from four parallel briefs, consolidated with a same-day
GitHub sweep of 163 repositories, and corrected by a critical review; the companion
report’s Methodology section records the passes and what could not be verified.

#### 6.1 Maturity, access and licence scales

**[Proposed]** Every tool row carries one of five labels.
The label is a judgment, but it is made from recorded signals (stars, licence, last
push, release cadence, whether weights are downloadable, and whether the thing has been
reproduced or deployed by someone other than its authors), and the signals are printed
next to it so the judgment can be checked.
GitHub signals were collected in one sweep on 2026-09-02 with the GitHub API. Hugging
Face pages are blocked at this environment’s network gateway, so the 85 cited `HF:` ids
were resolved through a server-side fetch instead (companion report §11) and carry
values tagged `(gemini-url_context)`: checked, but without citation metadata, so weaker
than a page read directly.
Three cited ids do not exist and one failed a fabrication check; all four are marked in
place.

| Label | Meaning | Typical signals |
| --- | --- | --- |
| **Production** | used in deployed systems by third parties; stable releases; permissive licence; maintained | thousands of stars, pushes within weeks, tagged releases, docs |
| **Mature research** | open code and weights, reproduced or benchmarked by others, actively maintained by a research group | hundreds of stars, pushes within months, weights on Hugging Face, an evaluation others have run |
| **Research** | paper with code or weights from a single group; usable but not yet reproduced or hardened | tens to hundreds of stars, recent, may lack releases or a licence |
| **Preview** | announced or API-only; weights not released, or an unreleased successor | model card or paper without downloadable weights; vendor preview |
| **Legacy** | still useful as a reference implementation or baseline but no longer maintained | archived flag, or no push in over a year (before 2025-09-02) |

**Licence is recorded, not scored.** It is an independent axis: it says what you may
later do with a result, never how good or how mature the tool is, and it is not a
selection filter here.
This programme is research, benchmarking and demo work, so a **non-commercial weight
licence** (DiariZen’s CC BY-NC 4.0, the MMS aligner weights, InsightFace’s models,
several challenge-derived checkpoints) is fully in scope, and those tools are ranked on
performance and ease of use like any other.
What the licence column buys is foresight: it names, per row, what would have to be
swapped or relicensed if a demo became a product, so that decision is costed rather than
discovered late. A **no-licence** repository is likewise not excluded; it is flagged,
because “no licence” is not open source whatever the README says, and it makes
redistribution the user’s problem rather than the author’s. Commercial services and
datasets do not take a maturity label; they take an access class instead.

**Access** (datasets and services): **open** (download without agreement) / **gated**
(registration, token or click-through) / **restricted** (institutional agreement or
bespoke terms) / **paid** (fee or catalogue membership) / **retired** (no longer
offered).

**Licence class** (every row): **permissive** (MIT, BSD, Apache-2.0, CC BY, CC0,
Unlicense) / **copyleft** (GPL, AGPL, MPL, CC BY-SA) / **non-commercial** (CC BY-NC,
research-only model terms) / **none** (no licence file, or NOASSERTION not resolved) /
**commercial** (closed weights or a paid service).

#### 6.2 Shortlist by stage

**[Proposed]** One row per item of the build-on-first list, in stage order.
“Verified how” names the strongest source read for that row; “Serves” lists the research
directions of §7 the row feeds.

| Stage | Tool | Licence class | ca/eu/gl coverage | Label | Verified how | Serves |
| --- | --- | --- | --- | --- | --- | --- |
| 1 Acquisition and record parsing | Congreso `intervenciones` JSON, Diario de Sesiones HTML, Archivo Audiovisual MP4 | open data (official terms) | co-official interventions appear twice in the Diario, original and translation | n/a, official source (access: open) | portal pages (search); OpenParliamentTV ES README (raw) notes that congreso.es returns 403 to bare requests | R1, R3, R6, R11 |
| 1 Acquisition and record parsing | [OpenParliamentTV-Tools](https://github.com/OpenParliamentTV/OpenParliamentTV-Tools), `ES` implementation: merges the `intervenciones` JSON with the Diario HTML by Needleman-Wunsch on surname sequence, aeneas sentence alignment, Wikidata linking over 3,830 MPs | copyleft (GPL-3.0) | n/a; ~95% match on substantive speeches, role-only chair turns unmatched (readme) | Research | raw `optv/parliaments/ES/README.md`; not in the same-day signal sweep | R1, R3 |
| 1 Acquisition and record parsing | [yt-dlp](https://github.com/yt-dlp/yt-dlp); [FFmpeg](https://github.com/FFmpeg/FFmpeg) | permissive (Unlicense); LGPL or GPL by build | n/a | Production | repo-meta: 188,537★ pushed 2026-08-30; 63,888★ pushed 2026-09-02 | R1, R7 |
| 2 ASR | `whisper-large-v3-turbo` on [faster-whisper](https://github.com/SYSTRAN/faster-whisper) + [CTranslate2](https://github.com/OpenNMT/CTranslate2) | permissive (MIT) | nominal for all four via Whisper’s language set; beat the Galician specialist in the Parlamento-ai sample, 14.71% vs 20.37% (readme) | Production | repo-meta; local `open-source-asr/README.md` | R1, R6, R7 |
| 2 ASR | `HF: BSC-LT/whisper-large-v3-LoS-punctuated` | apache-2.0 (gemini-url_context) | es, ca, eu, gl in one model, 8,110 h balanced (search) | Mature research | card read (gemini-url_context): **trained on ParlamentParla v3, Nos_ParlaSpeech-GL, 3CatParla and Common Voice**, so it cannot be scored on any of them | R1, R6, R8 |
| 2 ASR | [facebookresearch/omnilingual-asr](https://github.com/facebookresearch/omnilingual-asr), the permissive fallback | permissive (Apache-2.0 code and models, README §License) | `spa_Latn`, `cat_Latn`, `eus_Latn`, `glg_Latn` in `lang_ids.py`; 7B-LLM-ASR CER 1.3 / 1.4 / 0.8 / 1.4 on Meta’s own sets (review, raw) | Research | README and `lang_ids.py` read raw by the review; 2,911★ pushed 2025-12-30 (review API check, not in repo-meta) | R1, R5 |
| 3 Diarization | [pyannote.audio 4.x](https://github.com/pyannote/pyannote-audio) + `HF: pyannote/speaker-diarization-community-1` | permissive code (MIT); weights CC BY 4.0, gated | language-agnostic | Production | local README: multi-corpus DER table, 31 s per audio hour; repo-meta 10,500★ pushed 2026-09-02 | R1, R7, R8 |
| 3 Diarization | [DiariZen](https://github.com/BUTSpeechFIT/DiariZen), run head to head with pyannote and used where it wins | permissive code; weights CC BY-NC 4.0 (usable here; pyannote masks are the swap for a product) | language-agnostic | Mature research | local README DER table; the WavLM-based system that tops several challenge sets | R1, R8 |
| 4 Embeddings | [WeSpeaker](https://github.com/wenet-e2e/wespeaker): extractors, AS-norm, QMF, calibration and DET scripts | permissive (Apache-2.0; weights follow VoxCeleb CC BY 4.0) | no Iberian evaluation | Production | `examples/voxceleb/v2/README.md` (raw); `wespeaker/bin` files (gh) | R1, R2, R4, R5 |
| 4 Embeddings | [ReDimNet2](https://github.com/PalabraAI/redimnet2)-B6 `vb2+vox2` LM checkpoint as the extractor | permissive (MIT) | no Iberian evaluation | Mature research | local README: 0.29 EER Vox2-only, 0.23 with the LM checkpoint; repo-meta 85★ pushed 2026-08-28 | R1, R2, R4 |
| 5 Scoring | [pyannote.metrics](https://github.com/pyannote/pyannote-metrics) `IdentificationErrorRate`; [MeetEval](https://github.com/fgnt/meeteval) cpWER and tcpWER; [dscore](https://github.com/nryant/dscore) DER | permissive (MIT; MIT; BSD-2) | n/a | Production | gh tree (`identification.py` present); local MeetEval README; repo-meta | every R |
| 5 Scoring | [PYLLR](https://github.com/bsxfan/PYLLR) and [expected_cost](https://github.com/luferrer/expected_cost) for Cllr; [net:cal](https://github.com/EFS-OpenSource/calibration-framework) for ECE | permissive (MIT; MIT; Apache-2.0) | n/a | Legacy; Legacy; Production | repo-meta: pushed 2023-02-21, 2025-06-11, 2026-04-16 | R2, R3, R4 |
| 6 Alignment | torchaudio [`MMS_FA`](https://github.com/pytorch/audio) or [ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner) | permissive API (BSD-2; BSD per README); default MMS weights CC BY-NC 4.0 | MMS covers es, ca, eu, gl | Production; Mature research | README §License (raw); `LICENSE` returns 404 at `main`; repo-meta | R1 |
| 6 Alignment | `HF: Qwen/Qwen3-ForcedAligner-0.6B`, or NeMo NFA with `HF: HiTZ/stt_eu_conformer_ctc_large` (apache-2.0, but **trained on Basque Parliament**, so never scored on it), for anything shipped | permissive (Apache-2.0; Apache-2.0 toolkit, HiTZ card not opened) | 11 languages claimed, list not confirmed; eu only on the NFA path | Research; Production (NFA) | search; repo-meta for the parent repos | R1, R6, R7 |
| 7 Manifests | [lhotse](https://github.com/lhotse-speech/lhotse) + [pyannote.database](https://github.com/pyannote/pyannote-database) | permissive (Apache-2.0; MIT) | no recipe yet for ParlamentParla, Basque Parliament, Nos_ParlaSpeech-GL, 3CatParla or EuroSpeech (gh) | Production | gh recipe listing; repo-meta | R1, R4 |
| 8 Adaptation data | `HF: gttsehu/basque_parliament_1` (CC0-1.0); ParlamentParla v2 (CC BY 4.0) | permissive | eu and es bilingual, 1,462 h; ca, 211 h clean + 400 h other, speaker ids with gender, speaker-disjoint splits | access: open | HF cards (search); local ParlamentParla README | R1, R4, R6 |
| 8 Adaptation data | VoxPopuli speech-to-speech interpretation pairs | data CC0; code and models CC BY-NC 4.0 | es as source ≈1.6k h, es as target ≈1.2k h; no ca/eu/gl | access: open | local VoxPopuli README table | R5 |
| 9 Streaming | [SimulStreaming](https://github.com/ufal/SimulStreaming) inside [WhisperLiveKit](https://github.com/QuentinFuxa/WhisperLiveKit) | permissive (MIT; Apache-2.0) | inherits Whisper’s set, the only streaming wrapper that does | Mature research; Production | raw WhisperLiveKit README names SimulStreaming backends; repo-meta 666★ / 10,988★ | R7 |
| 9 Streaming | `HF: nvidia/diar_streaming_sortformer_4spk-v2`, state reset per window | CC BY 4.0 for v2 per its card (search); v2.1 mirrors NVIDIA Open Model License | language-agnostic; four output slots, no eviction | Production | WhisperLiveKit README (raw) ships it as the recommended backend; card not opened | R7 |
| 10 Joint SA-ASR | [TS-ASR-Whisper](https://github.com/BUTSpeechFIT/TS-ASR-Whisper) (DiCoW, SE-DiCoW) fed with pyannote masks | permissive code (Apache-2.0); weights CC BY 4.0 per HF card, unverified; bundled DiariZen weights CC BY-NC 4.0 | Whisper large-v3 backbone; no target-language evaluation | Mature research; SE-DiCoW Research | local README (Apache code, DiariZen set-up step); repo-meta 121★ pushed 2026-08-04 | R8 |
| 10 Joint SA-ASR | [MOSS-Transcribe-Diarize](https://github.com/OpenMOSS/MOSS-Transcribe-Diarize) as the long-form baseline | permissive (Apache-2.0) | 50+ languages, none Iberian evaluated | Mature research | local README (cpCER table, MLC-SLM win); repo-meta 1,805★ pushed 2026-09-02 | R8 |
| 11 LID | `HF: speechbrain/lang-id-voxlingua107-ecapa` | permissive (Apache-2.0) | ca, eu, gl in the 107 | Production | search; speechbrain repo-meta 11,802★ | R1, R3 |
| 12 Text | [NeMo-text-processing](https://github.com/NVIDIA/NeMo-text-processing) ITN (es) + `HF: BSC-LT/salamandraTA-7b-instruct` for the record | permissive (Apache-2.0); SalamandraTA card not opened | ITN has es only; SalamandraTA covers es, ca, eu, gl | Production; Mature research | gh directory listing; search | R6 |
| 12 Text | [Salamandra](https://github.com/langtech-bsc/salamandra), [ALIA](https://github.com/langtech-bsc/alia), [Latxa](https://github.com/hitz-zentroa/latxa) as the LLM fuser | permissive (Apache-2.0; Apache-2.0; MIT) | in-language LLMs for ca, eu, gl | Research | review repo check; signals not collected | R3, R6 |
| 13 Multimodal | [Light-ASD](https://github.com/Junhua-Liao/Light-ASD) (weights in `weight/`) | permissive (MIT) | n/a | Legacy (no push since 2025-03-23) | README (mAP 94.06 on AVA val); repo-meta | R10 |
| 13 Multimodal | [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) PP-OCRv5; [facenet-pytorch](https://github.com/timesler/facenet-pytorch); InsightFace’s models are stronger and are usable here, with facenet as the permissive swap | permissive (Apache-2.0; MIT) | Spanish explicit; Latin recogniser covers ca/gl/eu orthography (search) | Production; Mature research | repo-meta 88,695★; review API check, pushed 2025-09-16 | R3, R10 |
| 14 Annotation | [Label Studio](https://github.com/HumanSignal/label-studio) for the verbatim layer | permissive core (Apache-2.0); Enterprise commercial | n/a | Production | repo-meta 28,190★ pushed 2026-09-02 | R1 |

#### 6.3 Index from research direction to toolchain

**[Proposed]** One row per research direction of §7, so a reader starting from a
hypothesis finds its toolchain in one place.

| Direction | Shortlist tools | Data | Scorer | Licence note (tracked, not a filter) |
| --- | --- | --- | --- | --- |
| **R1** in-domain benchmark and baseline | Congreso open data + Diario HTML + Archivo MP4; OpenParliamentTV-Tools ES as merge template; yt-dlp, FFmpeg; MMS_FA or ctc-forced-aligner; lhotse + pyannote.database; Label Studio; baseline `whisper-large-v3-LoS-punctuated`, pyannote community-1, ReDimNet2-B6 cosine | 20–50 Congreso sessions to be built (§5.1 of the agenda); basque_parliament_1 and ParlamentParla v2 for adaptation | pyannote.metrics identification error rate; MeetEval cpWER and tcpWER (fixed-label variant to be written); dscore DER; per-turn TAA and coverage scorer (to be written) | MMS aligner weights are CC BY-NC and community-1 weights are gated: both fine here, both would need swapping in a product; Diario text terms govern redistribution of manifests |
| **R2** candidate-set constraint, normalization, calibration | WeSpeaker AS-norm, QMF, `score_calibration.py`; ReDimNet2; PYLLR and expected_cost for Cllr; net:cal for ECE | R1 trials; agenda (orden del día) as the candidate set; VoxWatch protocol as the reference curve | Cllr, min-Cllr, actDCF vs minDCF; FAR at fixed FRR; coverage–TAA curve | none blocking; PYLLR and expected_cost are Legacy (MIT, dormant) |
| **R3** structured-context fusion | chair-announcement parser (to be written); spaCy es and ca, Stanza; RapidFuzz; SPARQLWrapper and Wikidata Toolkit; PaddleOCR for the overlay; Salamandra, ALIA or Latxa as the LLM fuser in the DiarizationLM pattern | Congreso roster and intervention open data; ParlaMint ES metadata; the on-screen overlay where present (existence unverified) | TAA by turn type with per-source ablation; chair-announcement coverage | DiarizationLM weights carry Llama licences and are English-only (Legacy); Salamandra and ALIA Apache-2.0, Latxa MIT |
| **R4** weakly supervised in-domain speaker modelling | WeSpeaker training recipes; ReDimNet2 training pipeline; clovaai voxceleb_trainer; VoxBlink2 OSI scripts (Legacy) | Diario-aligned trials from R1; basque_parliament_1 speaker labels; VoxCeleb2 and VoxBlink2 for pretraining | FAR at fixed FRR against watchlist size vs the VoxWatch curve; EER and minDCF via WeSpeaker `compute_det.py` | VoxCeleb CC BY 4.0; VoxBlink2 licence not confirmed; VoxWatch has no code |
| **R5** interpretation-mixed tail | interpreter enrolment with WeSpeaker; WeSep as a reimplementation target; SpeechBrain SepFormer and ESPnet TF-GridNet as blind baselines; asteroid for SI-SNRi | VoxPopuli speech-to-speech pairs (es ≈1.6k h source) to synthesize overlays; real interpreted Congreso segments; EPIC (ELRA, paid) only if licensed | MeetEval cpWER and tcpWER; TAA recovered vs isolated floor audio; SI-SNRi on synthetic mixes | WeSep has no released weights and USEF-TSE and LExt no usable artifact, so all three are reimplementation targets rather than licence problems; VoxPopuli models CC BY-NC (data CC0), usable here |
| **R6** record-form normalization | `whisper-large-v3-LoS-punctuated`; NeMo-text-processing ITN es; punctuators; SalamandraTA for the co-official leg; Salamandra or Latxa fine-tune; Apertium as auditable baseline; Europarl-ASR verbatimization recipe (Legacy) and SPC_R recipe (paper only) | (verbatim ASR, Diario) pairs from R1; EuroSpeech CER distribution; Europarl-ASR transcript layers | record-WER and HTER with jiwer under a versioned normalizer; sacrebleu and COMET on the translation leg | ITN has no ca/eu/gl grammars; SalamandraTA card not opened; Apertium GPL-2.0 applies on distribution; Europarl-ASR NOASSERTION |
| **R7** bounded-lag architecture with revision accounting | SimulStreaming inside WhisperLiveKit; Streaming Sortformer v2 with state reset per window; NeMo cache-aware FastConformer (es only via Nemotron); diart; Silero VAD | R1 sessions replayed at wall-clock rate | SimulEval AL and LAAL (archived; vendor or reimplement); finalization lag, name revision rate, normalized erasure logger (to be written) | SimulEval CC BY-SA 4.0 and archived; Sortformer v2 CC BY 4.0 per card (search), v2.1 NVIDIA OML; Nemotron licence unverified |
| **R8** enrolment-conditioned joint SA-ASR | TS-ASR-Whisper (DiCoW, SE-DiCoW) with pyannote masks; MOSS-Transcribe-Diarize baseline; Dixtral for the LLM decoder; multitalker-parakeet as the English streaming comparison | R1 benchmark; AMI and NOTSOFAR-1 for sanity; EMMA leaderboard submission script | MeetEval tcpWER; pyannote.metrics identification error rate on roster ids; TAA | DiCoW weights CC BY 4.0 per HF card, unverified; DiariZen weights CC BY-NC (usable here; swap for a product); NOTSOFAR data licence and HF token; multitalker-parakeet NVIDIA OML |
| **R9** stage-direction detection | PANNs (has the Applause, Laughter, Chatter, Hubbub classes; Legacy); BEATs; EfficientAT (Legacy, low compute); CLAP zero-shot (Legacy, CC0); SenseVoice inline events (no Iberian ASR) | Diario stage directions from R1 as reference; AudioSet class set | precision and recall per class against the record’s annotations (scorer to be written) | BEATs checkpoint terms separate from the MIT repo; SenseVoice model under the FunASR model licence; AST weights on Dropbox |
| **R10** multimodal identity | Light-ASD (weights in-repo, Legacy); facenet-pytorch and DeepFace for face matching; face-alignment; PaddleOCR or Tesseract for the overlay; Qwen3-VL for layout-plus-text reasoning | Archivo Audiovisual video; member photo gallery from Congreso open data; AVA-AVD; REPERE (ELRA) as the precedent | TAA lift on interjections and bench replies; ASD mAP on a labelled subset | InsightFace models are research-only and LoCoNet has no licence: both usable here, both flagged for a product; overlay presence on every intervention unverified |
| **R11** conference-system integration | Televic CoCon control API; Bosch DICENTIS .NET or REST API; no public tool | the chamber’s own microphone events (private) | exception rate: turns not resolved by microphone metadata; TAA on the residual | API access is contractual; not testable on public data |

#### 6.4 Avoid or withdrawn

**[Technical]** Two separate lists.
The first is what to actually avoid, on the merits: the tool does not exist, does not
run, does not cover the languages, or would corrupt a measurement.
The second is licence foresight, which excludes nothing here — every row in it is
admissible for this programme’s research, benchmarking and demo work, and is selected on
performance like anything else.
The companion report’s §7 carries the full version with sources.

**Avoid on the merits.**

| Item | Why | Use instead |
| --- | --- | --- |
| WeSep, USEF-TSE, LExt for R5 | no released weights, no code, or an unresolved licence *and* no artifact — nothing to run | reimplement in WeSep’s architecture set; SepFormer and TF-GridNet as blind baselines |
| Kyutai STT; Canary, Parakeet, Nemotron for the co-official tail | en and fr only; no ca/eu/gl | SimulStreaming; LoS, BSC, HiTZ and Nós specialists |
| `xezpeleta/whisper-large-v3-eu` as an *evaluation* model | trained on Basque Parliament plenary audio, so it scores its own training set; fine as a *system*, invalid as a measurement | `HiTZ/whisper-large-v3-eu` (Common Voice 13) with a held-out period; or keep it and report a temporal hold-out (F2) |
| 3CatParla as parliamentary data | broadcast television despite the name | ParlamentParla v2 |
| Azure AI Speaker Recognition | retired 2025-09-30; APIs no longer accessible (search) | pyannoteAI, Picovoice Eagle, or a self-hosted roster matcher |
| Amazon Connect Voice ID | closed to new customers 2025-05-20; end of support 2026-05-20 (search) | same |
| Speechmatics, pyannoteAI or OpenAI enrolment as the *sole* identifier | enrolment caps of 50, unpublished and 4 against a 350-member roster; the cap, not the licence, is the problem | a roster matcher on WeSpeaker or ReDimNet2; keep one vendor as a comparison row (agenda §10.10 shows the caps do fit per agenda item) |

**Licence and terms to track, not to avoid.** Admissible now; the note says what a
productisation would have to change.

| Item | Terms | Note for later |
| --- | --- | --- |
| DiariZen weights | CC BY-NC 4.0 (README §License), inherited by DiCoW’s bundled pipeline | strong diarizer, use it; swap in pyannote community-1 masks if the work is productised |
| Meta MMS, XLS-R, `mms-lid`, and the `MMS_FA` aligner weights | CC BY-NC 4.0; fairseq host repo archived 2025-09-30 | the only aligner weights covering es, ca, eu and gl; Omnilingual ASR (Apache-2.0) is the permissive replacement |
| InsightFace pretrained models | research-only terms, including the pip auto-downloads (readme) | strongest face models; facenet-pytorch (MIT) or DeepFace if shipped |
| VoxPopuli pretrained models | CC BY-NC 4.0 (data is CC0) | data and models both usable here |
| Moonshine non-English models | Moonshine Community Licence, non-commercial below USD 1M revenue (search) | usable; `whisper-large-v3-turbo` is the permissive alternative |
| NVIDIA Sortformer and multitalker-parakeet checkpoints | NVIDIA Open Model Licence, not OSI (v2 is CC BY 4.0 per its card) | usable; resolve before deployment |
| whisper-timestamped, aeneas, tssep, audapolis, LinTO | AGPL-3.0 | fine locally and for a demo; propagates only if you host it as a service |
| Gated weights (pyannote community-1, several datasets) | click-through or token | costs an account, not a licence problem |

### 7. Research directions, ranked

**[Proposed]** Ranked by expected gain in TAA per unit of effort given the evidence in
§3. Each carries its hypothesis, instrument, and regime; the registry form is in §9.

**R1. Build the in-domain benchmark and measure the real baseline.** Everything else is
unmeasurable without it, and the baseline is itself a finding: the first public
ground-truth figure for named attribution on parliamentary audio, in a domain where even
the production operator measures API agreement instead.
Baseline: `whisper-large-v3-LoS-punctuated`; `pyannote` community-1 for turn
segmentation; ReDimNet2-B6 cosine against archive enrollment, no normalization,
unconstrained roster.
Expected: high TAA on long rostrum turns, collapse on short and overlapped turns.
Effort: weeks.

**R2. Candidate-set constraint plus normalization and calibration.** The §3.5 recipe.
Hypothesis: the coverage–TAA curve dominates R1’s at every coverage, and out-of-set
false alarms at least halve.
Cheapest large gain available; tests D6 and D7 directly.

**R3. Structured-context fusion for naming.** A chair-announcement parser (a gap in the
literature, §3.7); the agenda as prior; the calibrated acoustic posterior; the on-screen
overlay where present; a rule-plus-probability fuser first, then an LLM fuser given the
same evidence as text, in the DiarizationLM pattern.
Hypothesis: TAA on short and bench turns rises most; ablate each source.
Measure chair-announcement coverage first, because the parser is only as good as the
chair’s habit.

**R4. Weakly supervised in-domain speaker modelling.** Fine-tune the extractor and the
calibrator on Diario-aligned trials; measure FAR at fixed FRR against watchlist size
in-domain versus the VoxWatch curve.
Hypothesis: in-domain adaptation halves FAR at 350 enrolled speakers relative to
off-the-shelf. This is where the same-room advantage (D6) becomes a number.

**R5. The interpretation-mixed tail.** On real interpreted segments, in the §3.2 order:
obtain feeds; enroll interpreters; target-speaker extraction on the deputy; blind
separation last. Hypothesis: extraction recovers at least half of the TAA and WER loss
relative to isolated floor audio; interpreter enrollment yields a usable Spanish
translation layer at no machine-translation cost.
Highest uncertainty and clearest novelty.

**R6. Record-form normalization as a learned transform.** Train on (verbatim ASR output,
record text) pairs; measure record-WER against the editorial gap.
Hypothesis: a fine-tuned normalizer closes at least half the gap without raising
verbatim WER. The gap it targets is measured elsewhere: only 41% of EuroSpeech’s aligned
segments fall under CER 10% and 65% under CER 20%; Europarl-ASR’s verbatimization of
official transcripts was worth 9 WER points (§3.11).

**R7. Bounded-lag architecture with revision accounting.** Incremental processing with a
finalization pass; measure finalization lag, name revision rate, and first-vs-final name
accuracy at lags of 30 s, 2 min and 5 min.
Hypothesis: at a 2-minute lag, name revision rate is under 5% and TAA equals the offline
system’s within the session-bootstrap interval.
Parlamento.ai’s sustained-concurrency numbers (eight 5-second-window sessions on an 8 GB
GPU at p95 2.36 s) say the compute side is not the constraint.

**R8. Enrollment-conditioned joint speaker-attributed ASR.** Adapt SE-DiCoW or the
TS-ASR-Whisper stack to take the candidate set’s profiles and emit roster ids; compare
cpWER and TAA with the modular pipeline, with MOSS Transcribe Diarize as the long-form
baseline. Hypothesis: the joint model wins on overlapped and short turns and loses
nothing on long ones.
Highest ceiling, highest cost.

**R9. Stage-direction detection.** Audio-event tagging for applause, laughter, murmur;
precision and recall against the record’s annotations.
Small, separable, and part of what “official record” means.

**R10. Multimodal identity.** Active-speaker detection plus face matching against a
roster gallery, and OCR of the on-screen overlay, as fuser inputs — PERCOL with 2026
components. Hypothesis: on interjections and bench replies, visual identity raises TAA
more than any acoustic change.

**R11. Conference-system integration as the production path.** Where CoCon or DICENTIS
events are available, attribution is a join; the research question becomes the exception
rate. Hypothesis: microphone metadata resolves over 95% of turns, and the residual is
concentrated in interjections and the chair.
Not testable on public data; listed so the design keeps the slot.

### 8. What would be genuinely new

**[Analysis]** Most of §7 is careful engineering on known components.
Five items would be contributions rather than reproductions:

1. **A public Spanish parliamentary speaker-attributed benchmark** with verbatim and
   record layers and regime annotations (R1). Nothing comparable exists; the production
   operator in this niche has no ground-truth reference of its own.
2. **Open-set identification at watchlist scale with matched-condition enrollment,
   measured in-domain** (R2, R4). The open-set literature measures cross-condition
   internet audio; the regime every real deployment lives in has no public curve.
3. **A chair-announcement parser as an attribution source**, evaluated with ablations
   (R3). No published system does this.
4. **Separation of simultaneous interpretation from source speech on real broadcast
   mixtures** (R5). Two languages, one lagging the other by seconds, is a mixture type
   the separation literature has not addressed on real data.
5. **Editorial normalization as a measured task** with a defined editorial gap and a
   learned transform (R6).

### 9. Organizing the effort as a campaign

**[Proposed]** The repository’s experiment-loop skill fits this problem: one artifact
per experiment, negative results recorded, criterion named before measuring, spread with
every number, regime with every result.
Seed values:

- **Campaign question.** Which combination of evidence sources and models maximizes turn
  attribution accuracy at 95% coverage on Spanish parliamentary sessions under a
  two-minute finalization lag, and what does each source contribute?
- **Instance axis.** Enrolled-speaker count (50, 150, 350, roster plus government and
  witnesses), swept later.
- **Metric vector.** §4.2, roles as given.
- **Comparison shape.** `paired` by session against the standing best; `determination`
  for the coverage claims.
- **Evidence tier and accept rule.** Exploratory until R1 exists; then confirmatory:
  accept when the 95% paired *t* interval on the per-session differences in TAA@95%
  excludes zero and no guard is breached (§4.7, as amended by the sizing simulation in
  §10.3).

Hypothesis registry seeds:

| Id | Claim | Criterion | Instrument | Regime |
| --- | --- | --- | --- | --- |
| H-001 | The modular baseline’s TAA@100% exceeds 95% on rostrum turns over 60 s and is below 50% on turns under 3 s | TAA stratified; determination | R1 harness | archive MP4, unconstrained roster |
| H-002 | Agenda constraint plus AS-norm and calibration raises TAA@95% by at least 10 points over H-001 | TAA@95%; paired; +10 | R1 harness | same |
| H-003 | Chair-announcement parsing alone attributes at least 80% of rostrum turns correctly with no audio | TAA; determination | text-side scorer | Diario-aligned sessions |
| H-004 | In-domain embedding adaptation halves FAR at FRR 5% for 350 enrolled speakers versus off-the-shelf | FAR; paired; −50% | trial scorer | same-room trials |
| H-005 | Target-speaker extraction on interpreted segments recovers at least 50% of the TAA loss versus isolated floor audio | TAA on the interpreted subset; paired | R5 subset | mixed channel |
| H-006 | A learned normalizer closes at least 50% of the editorial gap without raising verbatim WER | record-WER and WER; paired | R6 harness | Congreso plenaries |
| H-007 | At a 2-minute lag, name revision rate is under 5% and TAA is within the session interval of offline | revision rate, TAA; determination | streaming logger | live-stream replay |
| H-008 | Visual identity (ASD + face + overlay OCR) raises TAA on turns under 10 s by more than the best acoustic change | TAA stratified; paired | R10 harness | archive video |
| Q-001 | Which audio channel does the inherited pipeline consume, and is an isolated floor feed or the interpreters’ feed obtainable? | open question | — | — |
| Q-002 | Which two chambers, and which five languages? | open question | — | — |
| Q-003 | Does the co-official-language portal serve an original-only or reduced-interpretation audio variant? | open question; settle with `ffprobe` and a listening test | — | — |

### 10. Spikes and quick tests

**[Analysis]** The agenda’s attribution numbers are all borrowed from other domains
(§3.3), and the benchmark that would settle them is weeks of work (R1). The spikes below
were chosen to move decisions before R1 exists: each is a time-boxed test of one
hypothesis or one instrument, run where it could be run.
Three runnability tiers apply throughout.
**Sandbox**: this session’s environment, which reached GitHub (including release assets)
and PyPI only, on four CPU cores with no GPU, no Hugging Face, no congreso.es and no
YouTube. **Net**: a GPU box with Hugging Face, YouTube and congreso.es access.
**Chamber**: floor feeds, interpreters’ feeds or conference-system events that only the
Congreso can supply.
Eleven spikes ran in the sandbox; the rest are catalogued in §10.13 with tier, effort
and the decision each changes.

Every spike’s code, data and full report live under `attic/spikes/` in the working tree,
which is gitignored; the reports are summarised here and a separate repository is the
intended home (see Next Steps).
Numbers measured in this session are tagged (run); numbers read from documentation
(readme); numbers from search summaries (search).
The convention for each entry is hypothesis, what ran, result, verdict, and what to do
next.

#### 10.1 Chair-announcement parsing as a text-only attribution instrument (H-003, H-002, R3)

**Hypothesis.** H-003: chair-announcement parsing alone attributes at least 80% of
rostrum turns correctly with no audio.
Secondary readings for H-002 (agenda-constrained candidate sets) and R3 (what name
resolution needs).

**What ran (Sandbox).** The official ParlaMint distribution was unreachable and the
`clarin-eric/ParlaMint` samples hold four utterances per file, so full corpora were
located on GitHub: ParlaMint-ES 3.0a (450 Congreso sittings, 2015–2023, 76,369
utterances, 842 speakers) from `calzada/PARLAMINT-ES-MC`; the Congreso XV Diario (183
sittings to 2026-05-07, 29,476 utterances) parsed from the `OpenParliamentTV-Data-ES`
proceedings HTML; and ParlaMint-ES-GA (301 Galician sittings) as a cross-language check.
A 16-rule parser, every rule observed in the data, links each regular turn to the
preceding chair turn, masks backward-looking sentences (thanks, time warnings, calls to
order), extracts the governed person mention, and resolves it against the roster with a
structured token matcher (particles dropped, surname keys, sex from the honorific,
parliamentary group as tie-break, government portfolios by date).
Two data-quality corrections were needed first: ParlaMint-ES gives thirteen ministers a
second person record, and the XV Diario has 23 typographic variants in its own speaker
labels; merging them moved XV precision from 0.929 to 0.974.

**Result (run).**

| Stratum | ParlaMint-ES (n) | coverage / precision / overall | Congreso XV (n) | coverage / precision / overall |
| --- | --- | --- | --- | --- |
| rostrum proxy: ≥150 words and a speaker change | 23,440 | 0.993 / 0.981 / **0.948** | 9,024 | 0.995 / 0.988 / **0.931** |
| ≥600 words and a speaker change | 11,906 | — / 0.990 / **0.972** | 5,382 | — / 0.988 / **0.971** |
| all regular turns | 32,551 | 0.836 / 0.961 / 0.773 | 12,179 | 0.867 / 0.974 / 0.784 |
| all regular turns plus “no announcement ⇒ same speaker continues” | 32,551 | — / — / **0.936** | 12,179 | — / — / **0.922** |
| turns under 30 words | 3,542 | 0.674 / 0.883 / 0.550 | 922 | 0.601 / 0.929 / 0.485 |

The canonical formula “tiene la palabra” produces 46% of announcements; a bare vocative
(“Señor Pedreño.”, “Señora ministra.”) another 21%, so a parser matching only the
canonical formula would miss a fifth of hand-offs.
The chair supplies both surnames in about half of announcements (precision 0.999), a
title or role only in 23–30% (precision 0.86–0.94, all the residual errors being the
wrong minister for a bare “señor ministro”), and a first surname only in 10–19%. Two in
five members share a first surname with another member, and the parliamentary group only
halves that; both surnames together are near-unique (97–98%). Catalan hand-offs (“té la
paraula”) are 1.35% of XV chair turns and 0% of ParlaMint-ES, new since the September
2023 rule change, and were 48% of the residual failures before Catalan rules were added.
Precision transfers to Galician (0.953) but coverage does not (0.438) without
per-language patterns.
Agenda structure gives a median of 10–12 distinct speakers per Diario debate section and
37–38 per sitting against a 350-member chamber, a 12–25× reduction available before
anyone speaks; in the text-only setting the constraint adds only +0.012–0.016 because
the chair’s formula is already discriminative.

**Verdict.** H-003 is **supported on rostrum turns and refuted as a claim about all
turns**, on two independently produced records of the same chamber seven years apart.
The short-turn tail (49–55% under 30 words) is where audio must pay for itself, and the
whole measurement is an upper bound: the record’s hand-off is a clean sentence, whereas
live it is clipped, spoken over cross-talk and must survive ASR of exactly the rare
accented name token.

**Next.** S9 (the TTS-to-ASR round trip on 200 announcement sentences) to see how much
survives recognition; the LLM-parser comparison (§10.8); a phonetic or fuzzy roster
matcher for the first-surname stratum; treat the text-only floor of 0.93–0.95 on rostrum
turns, not chance, as the baseline every acoustic identifier must beat.

#### 10.2 The scoring harness (the R1 instrument, §4.6)

**Hypothesis.** The §4 protocol can be implemented as one versioned harness whose
invariants are testable before any real data exists.

**What ran (Sandbox).** `attrscore` 0.1.0: JSONL, MeetEval SegLST/STM and NIST RTTM I/O;
turn matching; TAA@coverage with the full coverage–accuracy curve and stratification;
ECE and MCE; speaker-count error and hallucination rate; cpWER and tcpWER in
permutation-invariant and fixed-label forms; identification error rate and DER;
finalization lag, name revision rate, normalized erasure and first-versus-final
accuracy; paired comparison with bootstrap and the accept rule; an instrument guard that
refuses an empty or malformed run; and a synthetic parliament-like generator used only
as a test fixture. 3,526 lines of source, 882 of tests, 71 tests passing in 8.5 s (run).

**Result (run).** Three findings changed the protocol rather than the code.
First, the literal “maximal temporal overlap” assignment rule of the first draft is
degenerate under containment: a 2 s interjection inside a 4-minute rostrum turn
intersects the rostrum turn by its whole 2 s, so raw overlap assigns it to the wrong
reference turn. On a perfect hypothesis the literal rule scored TAA 0.837 overall and
0.493 on interjections where a correct instrument returns 1.000; intersection-over-union
assignment fixes it and is now the protocol.
Second, permutation-invariant cpWER and DER cannot score a wrong name: with every word
right and every name rotated to another member, MeetEval cpWER is 0.0000 and DER 0.0000,
while fixed-label cpWER is 1.4906, pyannote’s identification error rate 0.9987 and TAA
0.0000. The fixed-label forms and IER are the outcomes; the permuted forms are reported
beside them as the name-permutation credit, which on the synthetic baseline forgave
about half the word-level attribution error (cpWER 0.090 permuted versus 0.185 fixed).
Third, pyannote.metrics takes a centred collar, so the NIST 0.25 s protocol needs
`collar=0.5`; with that constant the two DER tools agree to four decimals, and a test
locks it.
Tooling quirks worth knowing: `meeteval.der` downloads `md-eval-22.pl` on first
use (blocked here; vendored instead); MeetEval 0.4.3 imports an undeclared `simplejson`;
pyannote.metrics 4.1’s low-latency spotting metric cannot be constructed without a
subclass fix.

**Verdict.** The instrument works and is ready to score real data; two of its findings
(IoU matching; fixed-label scoring) are now in §4.6, and one (the collar constant) would
have made every DER incomparable with the literature.
Open: the guard limits (5 hallucinated words per hour, mean absolute speaker-count error
1.0) are placeholders to be set from the first real baseline and frozen; record-WER,
HTER and the trial scorer (Cllr, minDCF, actDCF) await the record layer and per-turn
score distributions; text normalization is not yet wired in; the comparison statistic
defaults to the median and should switch to the mean with a paired *t* interval (§10.3,
§4.7).

#### 10.3 How many sessions R1 needs (§4.7, §5.1)

**Hypothesis.** The proposed 20–50 sessions, paired by session, resolve the differences
the campaign registers.

**What ran (Sandbox).** A simulation of paired comparisons with real session sizes
(ParlaMint TEI headers give full-sitting extents for 63 sittings across 21 corpora;
ParlaMint-ES median 74 utterances; Congreso XV 58.7 speeches per session), stratum
shares and base accuracies swept, a between-session random effect, a shared per-turn
difficulty, and a session × system interaction (the heterogeneity of the improvement
across sessions), with five interval estimators evaluated on 4,000 null benchmarks.
Nothing in it is a measurement of a real system.

**Result (run).** The accept rule as first written is not a 5% test: the percentile
bootstrap of the mean rejects a true null 8.2% of the time at 20 sessions and 5.9% at
100; BCa is worse; the bootstrapped median is conservative (3.8% at 20 sessions, 0.6% at
100\) and detects a +3-point gain 44% of the time where the mean manages 64%; the paired
*t* interval on per-session differences is calibrated at every size tested (4.5–5.3%).
For overall TAA with 80-turn sessions and a moderately uneven gain, 20 sessions resolve
about 3.6 points, 40 about 2.6, 50 about 2.3; +10 is detectable at any size above the
15-session floor, +5 with 11 sessions, +3 with 32, +2 with 75. Every stratified claim is
under-powered at 20 sessions: the smallest detectable change on the interjection stratum
is 15 points and a determination of its level carries ±9.3; a 10-point gain on
interjections alone needs 45 sessions, an H-005-shaped subset claim 45–180. The whole
study collapses to Var(per-session difference) = h² + b²·E[1/nₛ] with b about 37.5 and h
the per-session gain sd, so the variance runs on the harmonic mean of turns per session
and lognormal session lengths waste about half the turns; pairing is worth 2.25× at the
defaults (20 paired sessions do the work of 45 unpaired).
A pilot cannot size the benchmark, because the interaction term, the one parameter that
decides the answer, returns exactly zero in 28% of 20-session pilots.
The reference turn list decides whether the short strata exist at all: on a
Diario-derived list (about 2/8/90 by stratum) a short-turns-only improvement cannot move
overall TAA by more than 2.1 points.

**Verdict.** Fix the interval and the statistic first (free, worth 10–15 sessions);
build **40 sessions** as the campaign minimum, 30 as the absolute floor, 100 as the
stretch for stratified claims; prefer uniformly medium-length sittings; report the
standard deviation of per-session differences in the first real comparison so that h
stops being a guess.
§4.7 and §9 are amended accordingly.
Limitations: h is invented; correctness is modelled as Bernoulli given a latent, whereas
real errors cluster within a turn block, so every MDE here is optimistic by an unknown
factor.

#### 10.4 A CPU-only audio slice on synthetic Spanish (R1/R2 plumbing)

**Hypothesis.** A full named-attribution pipeline (VAD, ASR, speaker embeddings,
open-set identification with abstention, LID) can be assembled and run end to end from
what the sandbox can reach, and each stage’s CPU cost can be measured.

**What ran (Sandbox).** Every model came from a GitHub release asset, Hugging Face,
OpenSLR and the PyTorch download host being unreachable: Silero VAD v5, Whisper
tiny/small/medium int8 through sherpa-onnx, a Spanish-only NeMo FastConformer CTC model,
a Spanish streaming Zipformer (Kroko; licence unresolved), a Spanish Moonshine export,
seven ONNX speaker embedders (3D-Speaker CAM++ and ERes2Net, WeSpeaker CAM++ and two
ResNets, NeMo TitaNet small and large), whisper-tiny as a language identifier, and ten
Piper voices.
The corpus is Piper VITS voices reading ParlaMint-ES text: nine base voices
with pitch and rate perturbations fill 25 roster slots (20 enrolled, 5 out-of-set),
seven sessions, 179 turns, 1,446 s of speech.
The only human audio is 30 language-identification clips and 8 Mandarin
speaker-verification clips.
The run was interrupted by the usage limit after VAD, LID and two embedding passes; the
ASR stage was completed single-threaded on resumption.

**Result (run).** Cost on four contended cores: VAD at 0.011 real-time factor; NeMo CTC
0.046 (596 MB); Zipformer 0.061; whisper-tiny 0.203 because sherpa-onnx pads every
segment to 30 s; CAM++ embedding 0.099; ERes2Net 0.199; LID 0.138. VAD found 192
segments for 179 turns with 99.9% of reference speech, no spurious and no missed turns.
Turn-level WER on the synthetic reference (Whisper basic normalizer, no number
verbalization): NeMo CTC 12.5%, Zipformer 14.6%, whisper-tiny 28.2%, the last driven by
insertions on long turns.
Open-set identification: ERes2Net 100% closed-set top-1 with zero out-of-set false
alarms at every threshold; CAM++ 78% top-1 with in-set and out-of-set scores overlapping
so that no threshold separates them, and only 47% on segments under 3 s. With CAM++ at
the 95%-coverage threshold, 13 of 192 segments abstain and 121 of 179 named segments are
correct. On the eight human clips only ERes2Net and the two TitaNets separate the three
speakers; CAM++ and the WeSpeaker models do not.
LID: 29 of 30 human clips, Spanish 24/24 and Catalan 21/24 on the synthetic probes.
Breakage worth knowing: the default-named `silero_vad.onnx` and v4 over-merge whole
exchanges; sherpa-onnx 1.13.7 ignores `max_speech_duration`; the NeMo CTC model drops
segment-initial words on 71 of 179 turns; whisper-medium is out of memory on this box.

**Verdict.** Both questions answered: most of a stack is obtainable from release assets
alone, and the chain runs well under real time on four cores, given the Spanish-only
models rather than Whisper.
Every identification number is TTS fingerprinting, not speaker verification: the
perturbed sibling voices are too far apart to be hard (same-base centroid cosine 0.43
against 0.15 for different bases), and the human cross-check reverses the embedder
ranking. Treat identification as a plumbing test that passed; quote none of it.
The per-stage costs and the breakage list are the durable output.

**Next.** Human audio before anything else; pyannote 3.1 as the segmentation and
clustering baseline; large and Spanish-fine-tuned ASR on a GPU; embedders on real trial
lists; the `attrscore` export so the same run reports TAA; resolve the Kroko licence.

#### 10.5 What the Parlamento.ai study can and cannot teach (§3.3)

**Hypothesis.** The only published in-domain multi-model study on Spanish-state
parliamentary audio can supply accuracy anchors, speaker-attribution baselines and
serving figures.

**What ran (Sandbox).** `Parlamento-ai/open-source-asr` at commit `ac431ae` (results of
2026-08-06; 168 audios, 13.79 h, seven language groups of 24) re-analysed from its
published JSON; the audio and transcripts are not in the repository.

**Result (run, from the study’s own files).** The metric is word-level edit distance to
the median of three paid APIs; there is no human transcript anywhere
(`referencia_humana: false`). The paid panel’s own disagreement is the floor: median 5.1
points on es, 5.3 on the EP mix, 6.1 en, 8.3 pt, 11.7 ca, 13.0 gl, 15.1 eu, with Nova-3
absent from gl and eu so those rows are a single vendor pair.
`whisper-large-v3-turbo` is the best open model in five of seven groups and sits inside
the floor everywhere except gl (1.13×) and eu (1.41×). Signal-to-noise ratio, not
silence fraction, drives disagreement, and the strongest SNR dependence belongs to the
paid panel itself (ρ −0.54): the reference degrades faster than the systems measured
against it. On one clean-silence Catalan clip, whisper-large-v3 reaches 73% and the
3CatParla Catalan fine-tune 238% (a decode loop) while turbo scores 24%, below the paid
floor: the three co-official fine-tunes all lose to their base model (the Nós Galician
turbo is 38% worse than generic turbo and 1.9× slower), consistent with adaptation on
clean read or broadcast speech stripping silence robustness.
On Basque the two leading specialists are the two models that declare Basque Parliament
training data, and the uncontaminated HiTZ Whisper is indistinguishable from turbo, so
the entire specialist advantage is consistent with contamination.
The Open ASR Leaderboard carries no signal here (Spearman ρ −0.14 with parliamentary
agreement). Serving: turbo sustains 8 concurrent 5 s windows at p95 2.36 s on an 8 GB
GPU; batching, not concurrency, is the capacity knob (36 → 72 stable 5-minute-chunk
sessions); the workload is 68% backlog bursts at 10% duty cycle; Nova-3 is 77% of the
ASR bill; self-hosting beats the USD 0.71 per audio-hour line item below USD 1.83 per
GPU-hour all-in. Speaker attribution: nothing, anywhere; the one diarization-capable
candidate (MOSS-Transcribe-Diarize) was discarded on throughput before producing a
quality number, and the EP sample excludes chunks that mention the interpretation
channel.

**Verdict.** Agreement is not accuracy and the study says so; it cannot anchor WER,
cannot separate contamination from skill on Basque, and contains no attribution data.
It does establish turbo as the baseline to beat, the SNR dependence of the paid
references, the fragility of the co-official fine-tunes on silence, and realistic
serving and workload figures.
Two follow-ups are registered: silence robustness of the fine-tunes (F1) and a temporal
hold-out for the Basque and Galician specialists (F2).

#### 10.6 Whisper hallucination on chamber-like non-speech (S6; the §4.2 guard)

**Hypothesis.** VAD gating alone satisfies the hallucination guard (inserted words per
hour of non-speech) before the R1 baseline is run; if not, a decoding-side filter is
needed.

**What ran (Sandbox).** Three one-hour 16 kHz streams alternating Piper Spanish speech
(six voices reading ParlaMint-ES text, convolved with a synthetic RT60 0.4 s room on a
−52 dBFS floor) with non-speech spans from six classes: applause and laughter (ESC-50
clapping and laughing, three overlapped layers, trimmed to their energetic region),
gavel (ESC-50 wood knocks), murmur (16-talker babble from the TTS pool), room tone, and
digital silence; 79 speech chunks (13,951 reference words) and 80 non-speech spans (1.66
h) with exact ground truth by construction.
ASR is whisper-small int8 through a purpose-written greedy decoder on onnxruntime,
because sherpa-onnx does not expose `no_speech_prob`, `avg_logprob` or the compression
ratio, which are the decoding-side guard under test (the decoder matches sherpa-onnx’s
C++ output on real speech).
Two regimes: contiguous 30 s windows over the whole hour, and Silero VAD segments only;
filters applied after decoding so every threshold is scored from the same units.
Whisper-medium is out of memory on this box, and the VAD arm completed on one of the
three streams: repeated attempts to finish the other two were killed by container
restarts.
The VAD-versus-ungated contrast below is therefore reported **paired on the one
stream both arms covered**, which is the honest comparison; the ungated per-class rates
are also given across all three streams, where more data is available.

**Result (run).** Across all three streams, ungated whisper-small emits **401
hallucinated words per hour** of non-speech: gavel 1,468 (repetition loops such as “toc
toc toc…”), applause 697, murmur 115, laughter 75, digital silence 72 (“y” twenty
times), room tone 3.5. The applause output is the dangerous case for a record:
“aplausos”, “gracias”, “muchas gracias”, “muy bien”, plausible chamber language rather
than garbage.

Paired on the single stream both arms cover, **VAD gating makes the problem worse, not
better**: 288.8 words per hour ungated against **518.8 with VAD**. The mechanism is
visible per class. VAD removes the impulsive and silent classes almost entirely (0.2% of
applause time, 0.8% of gavel, 1.0% of room tone and 0% of digital silence passed as
speech, taking gavel from 1,415 to 11 words per hour and applause and digital silence to
zero), and then passes **92.5% of babble**, which Whisper transcribes as fluent Spanish
(“de la comunidad de la iglesia de los últimos días”): murmur goes from 139 to **3,216**
words per hour, laughter from 66 to 311 (“jajaja…” loops).
Gating also costs real speech, raising the deletion rate from 0.19 to 0.27.

The decoding-side signals overlap badly at this model size: `no_speech_prob` has a p95
of 0.68 on real speech units and a p05 of 0.64 on non-speech units, so the threshold
that suppresses every hallucination discards 14% of real words.
Filter by filter on the paired stream: `no_speech_prob > 0.6` reaches zero
hallucinations but raises speech WER from 0.292 to 0.357; `avg_logprob < −1.0` alone
leaves 212 per hour, because the long, confident hallucinations pass it; Whisper’s own
default conjunction also leaves 212; the **compression-ratio filter (> 2.4) alone cuts
289 to 89 per hour at essentially no WER cost** (0.292 to 0.295); all three together
reach zero on the ungated arm at WER 0.361, and 7 per hour on the VAD arm at WER 0.319.
Speech WER is high in every configuration (0.29–0.36) and deletion-dominated, a property
of the small model on reverberant TTS rather than of the guard.

**Verdict.** VAD gating alone does not satisfy the guard: it fixes applause, knocks and
silence and fails on babble, which a chamber supplies constantly.
The `no_speech_prob` gate cannot be tuned at whisper-small without losing real words,
which reproduces the published warning that hallucinations carry high `avg_logprob` and
low `no_speech_prob` exactly where the naive filters are needed.
The one free filter is the compression ratio, which removes two thirds of the
hallucinated words at no measurable WER cost.
The practical guard is the compression-ratio filter plus a babble-aware VAD threshold
rather than VAD alone, and the harness’s placeholder of 5 words per hour is reachable
only with the full conjunction at a 30% speech cost.
Set the guard from the first real baseline, but expect the order of 10 words per hour
for a gated pipeline, not 5. Limitations: synthetic streams with a synthetic room;
ESC-50 clapping close-miked rather than a chamber; one model size; the VAD arm partial
at the time of writing.

**Next.** Finish the VAD arm on all three streams and the `condition_on_previous_text`
regime; run F1 (the co-official fine-tunes on the same streams); test the larger Whisper
sizes and `whisper-large-v3-turbo` on a GPU, since the Parlamento.ai analysis (§10.5)
found turbo the only Whisper variant that does not loop on silence; add babble to the
VAD tuning set.

#### 10.7 The interpreter-overlay flip curve and LID on the same mixtures (S10, S11 synthetic; R5, H-005)

**Hypothesis.** There is a level ratio below which an embedding of the mixed Congreso
feed (deputy in Catalan, Spanish interpreter 2–4 s behind) still identifies the deputy,
and language identification can flag the mixed condition before identification fails.

**What ran (Sandbox).** Forty deputy turns from two Catalan Piper voices over
ParlaMint-ES-CT text, forty interpreter renderings from two Spanish Piper voices over
ParlaMint-ES text (four voice pairings, same and cross gender, seven Spanish impostors),
each voice with a 28 s held-out enrollment and each Catalan voice a second enrollment on
Spanish text for the cross-language probe.
Linear mixing at active-region RMS ratios from −12 to +12 dB in 3 dB steps, lags of 2, 3
and 4 s, three window types (the deputy’s full span with its clean head; 6 s and 3 s
slices wholly inside the overlap; a variant with the interpreter’s run-out).
Four embedders were run; the two CAM++ exports were disqualified by their own
clean-speech controls (7.7–20% EER on full clips, 37–54% on 3 s clips) and kept only as
a negative result, leaving ERes2Net with TitaNet-large as a check.
LID is Whisper’s utterance-level language token, small and tiny, on whole turns and on
short windows; the frame-level VoxLingua107 model was unreachable.

**Result (run).** On a mid-turn slice the embedding follows the louder talker: the flip
(mixture closer to the interpreter’s enrollment than to the deputy’s) sits at 0 dB
within ±0.6 dB for both models, all lags and all pairings; language mismatch and
cross-gender pairing buy nothing.
The clean head is the only margin, about +1.5 dB per second of lag: +2.6, +3.8 and +5.7
dB on the full span at lags of 2, 3 and 4 s. Verification against a mid-point threshold
fails 1–4 dB before the flip (+1.4 dB on the full span, −2.9 dB on 6 s and −4.0 dB on 3
s of overlap), and cross-language enrollment costs a further 0.2–0.9 dB. Closed-set
identification goes from 100% to 0% inside about 9 dB centred on 0 dB. LID fails at or
before the embedding: at 0 dB whisper-small reports Catalan on 50% of full turns and 15%
of 3 s slices (whisper-tiny 12% and 0%), and on clean 3 s clips tiny is right only 47%
of the time, so it is not a segment-level LID at all.
Letting a window run into the interpreter’s 2.5 s run-out cuts the Catalan rate from 93%
to 38% at −12 dB.

**Verdict.** The overlay is a level problem with no comfortable operating band: at equal
level an unextracted pipeline is inverted, not degraded, so H-005’s bar of recovering
half the loss is a minimum.
LID is not a safety net; it gates only while the deputy is already 6 dB or more louder,
and only with whisper-small.
The §3.2 ordering of attacks stands with sharper reasons: the interpreters’ feeds first,
interpreter enrollment buys a mixed-condition flag rather than attribution, extraction
holds the leverage and must move the operating point by more than about 9 dB of
effective ratio. Usable today is a rule, not a threshold: attribute acoustically only
below about −6 dB estimated ratio, prefer windows that include the pre-interpretation
head, never cross the turn end.
Limitations: TTS voices (0% clean EER gives it away, so only the curve’s shape and the
flip’s position relative to 0 dB transfer); one global gain, whereas the broadcast
chain’s AGC and ducking would push the ratio toward the interpreter exactly when the
interpreter speaks; Catalan only; CPU-only, so whisper-small ran on half the turns.

**Next.** S1 and S2 are blocking: measure the interpreter-to-deputy active-region ratio
on ten real interpreted clips as a distribution within a turn (below −6 dB this is a
boundary-detection problem, near 0 dB extraction is mandatory); re-run the grid with AGC
and ducking once S2 says what the chain does; feed the curve into S22 as its
before-and-after axis; treat whisper-small as the LID floor and benchmark a real
segment-level LID on a networked box.

#### 10.8 An LLM as chair parser, against the regex (S21, R3)

**Hypothesis.** An LLM given the chair turn, the agenda title and the session roster
names the next speaker better than the 16-rule parser, at a cost and latency a live
pipeline can carry.

**What ran (Sandbox).** 600 Congreso XV chair turns stratified over the regex’s classes
(200 canonical “tiene la palabra”, 150 bare vocatives, 50 Catalan or other-language
hand-offs, 100 role-only references such as “Señor ministro.”, 100 regex failures),
ground truth as in §10.1; `gemini-2.5-flash` at temperature 0 with thinking off,
prompted with the chair text, the agenda title, the session roster with aliases and
roles, and the previous speaker (the analogue of the regex’s within-session carry-over);
responses cached; the same scorer as §10.1; McNemar on paired outcomes.
Total API spend about USD 1.33.

**Result (run).**

| Stratum | n | regex precision / overall | LLM precision / overall | regex + continuation | hybrid (regex first, LLM fallback) + continuation |
| --- | --- | --- | --- | --- | --- |
| canonical | 200 | 1.000 / 0.990 | 1.000 / 0.995 | 0.995 | 1.000 |
| bare vocative | 150 | 0.958 / 0.907 | 0.978 / 0.880 | 0.940 | 0.960 |
| other language | 50 | 0.956 / 0.860 | 0.867 / 0.780 | 0.860 | 0.880 |
| role only | 100 | 0.937 / 0.740 | 0.765 / 0.520 | 0.770 | 0.840 |
| regex failures | 100 | 0.000 / 0.000 | 0.794 / 0.270 | 0.650 | 0.800 |
| all 600 | 600 | 0.960 / 0.752 | 0.933 / 0.748 | 0.875 | **0.920** |

Projected to all 12,179 XV regular turns with the design weights: regex plus
continuation 0.906, hybrid 0.940, with the LLM called on 18.6% of turns.
Latency p50 0.86 s, p95 1.22 s at 8-way concurrency; cost about USD 0.94 per 1,000 chair
turns for the flash model and USD 0.18 under hybrid routing, USD 0.30 and 0.06 for
flash-lite (prices from search, the pricing page being unreachable).

**Verdict.** Alone the LLM does not beat the regex (0.748 versus 0.752, McNemar p =
0.92): it loses role-only references by as much as it wins the regex’s own failures.
Behind the regex as a fallback it wins clearly (0.920 versus 0.875 on the sample, 28
wins to 1 loss, p = 1.1e-7). Neither latency nor cost is the obstacle in a live
pipeline. The single highest-value follow-up is extracting the per-question agenda
heading, which names the addressed minister, is empty for 42% of sampled rows today, and
holds essentially all of the LLM’s residual error.
Limitation: the input is the edited record, not ASR output (S9 remains the test of the
live case).

#### 10.9 A cross-service ASR harness, smoke-tested on the one reachable service (A3, §11.5)

**Hypothesis.** The cross-service, cross-language protocol (companion inventory §10) can
be implemented as one harness with adapters for every service and open model, a
per-language normalizer, clip-level paired bootstrap intervals, caching, and cost and
latency logging, and smoke-tested from the sandbox.

**What ran (Sandbox).** `asr-bench`: manifests, fourteen adapters (Gemini, Google
Speech-to-Text v2, Amazon Transcribe batch and streaming, Deepgram, OpenAI, AssemblyAI,
Speechmatics, ElevenLabs, Gladia, Soniox, Rev.ai, sherpa-onnx Whisper, faster-whisper),
a Whisper-basic normalizer with a diacritics variant, WER and CER with a clip-level
paired bootstrap, response caching keyed by service, model, settings and clip hash, a
pricing file with (docs)/(search)/(assumed) provenance on every entry, a language-code
probe with a negative control, and a report writer; 172 tests, no network needed.
The smoke set is 30 Piper synthetic clips per language for es-ES, es-419, ca and en; eu
and gl have no Piper voice.

**Result (run).** Only Gemini was reachable with working credentials: two Gemini models,
240 calls, WER 1.1–1.4% on Spanish, 2.9% on Catalan and English for the general model,
3.1–7.9% for the transcription model, p50 latency 2–3 s, about USD 0.18–0.20 per
audio-hour (assumed).
Amazon Transcribe could not run: the AWS key in the environment is malformed (14
characters) and every call fails authentication; its language coverage (ca-ES, eu-ES,
gl-ES) was read from the shipped botocore enum instead.
Google Speech-to-Text v2 returned 401; the other nine hosts are blocked from the
sandbox. Gemini’s language code is unvalidated: a `zz-ZZ` hint returned a transcript, so
“accepted” is not evidence of support, and the hint still steers output (a Catalan clip
came back Galician-flavoured under `gl-ES`). The Whisper basic normalizer inflates
Catalan WER by up to 7 points by splitting `l’home` and `col·legi` into two tokens; the
harness now pins the real behaviour in a test.
A latent bug had prevented the report from ever building (an unescaped percent sign);
fixed.

**Verdict.** The instrument exists and is tested; the smoke numbers are synthetic clips
on one service and say nothing about the question.
The protocol’s first real pass needs a box with egress to the other services, valid AWS
credentials or a bucket, mirrored open-model weights, and the ground-truth samples
(Basque Parliament 1 test, ParlamentParla clean test, the BSC code-switching set,
VoxPopuli-es, the Congreso verbatim sample).
Two protocol amendments come out of the smoke: a negative-control language code in every
run, and a Catalan-aware normalizer rather than Whisper’s basic one.

#### 10.10 The Congreso XV dataset, its generator, and the commercial-cap fit (S23; S7 in part)

**Hypothesis.** A free, in-domain, per-speech dataset with media links exists and can
supply the text-side benchmark inputs and answer S23 (do agenda-constrained candidate
sets fit under the 50- and 4-identity caps of commercial enrollment APIs).

**What ran (Sandbox).** `OpenParliamentTV-Data-ES` (HEAD of 2026-06-29, “in
development”): 180 plenary sessions of the XV legislature, 10,569 speech records, each
with speaker, speaker type, agenda item and type, minute-resolution timing, a Congreso
MP4 URL and the Diario text; plus its generator, `OpenParliamentTV-Tools` (GPL-3.0, 4
stars), whose parse and merge path was run offline on the same inputs.

**Result (run).** Records deduplicate to 9,441 unique clips (1,128 records are the same
clip fanned out under several agenda items), 907 audio hours; clip duration median 360
s, p10 60 s, p90 720 s, nothing under 60 s; government members median 120 s, question
time 60 s; per session a median of 39 distinct speakers (p90 50, max 71); 427 speakers,
271 with five or more speeches; 39% share a first surname.
Per numbered agenda item in the media index, distinct speakers are at most 4 for 66.7%
of items, at most 10 for 90.7% and at most 50 for 100%; per Diario debate section the
median is 10 (§10.1). So the 50-identity cap of a Speechmatics-style API fits every
agenda window and the 4-identity cap fits two thirds of numbered items, with the caveat
that agenda sets exclude interjectors and the chair.
Co-official-language passages by a stopword heuristic are 54 of 9,399 speeches (0.6%) in
43 sessions, mostly Catalan; the dataset’s own language tag is `es` for every record and
is unusable. Quality flags: the generator’s merge is surname-based sequence alignment
with binary 1.0/0.5 confidence, timing is index matching not audio alignment (aeneas
covered 3 of 180 sessions), chair turns are dropped by design (61.5% of Diario speaker
markers), stage directions sit inline in the speech text, three sessions carry
placeholder MP4 URLs, and the real unmatched share is about 2.4% once the fan-out
duplicates are removed.
Verdict on the generator: reuse the scraper and proceedings parser, rewrite the merge
and timing layer, never treat its output as alignment ground truth.

**Verdict.** The text-side inputs of the benchmark (turn table with durations, roster
with aliases, agenda candidate sets, dual-language flags) exist today for free; the
per-clip MP4 manifest (`attic/spikes/manifest/congreso-xv-media.jsonl`) is the input for
S1 and S13 the day a box with congreso.es access exists.
S23 is answered: commercial caps are not the obstacle to an enrollment API; the
350-member roster with agenda windowing fits.
The record has no sub-minute stratum, which confirms §10.3’s warning that the reference
turn list decides whether short-turn hypotheses are testable.

#### 10.11 The record census (S7; bounds for R9 and R11)

**Hypothesis.** The published record bounds three quantities from text alone, before any
audio work: how often stage directions occur and in what classes (R9’s detector target),
what fraction of voice events the record cannot attribute to a person (R11’s residual),
and whether a sub-three-second stratum exists in the record at all.

**What ran (Sandbox).** One shared classifier over two conventions so the sources are
comparable: ParlaMint TEI `vocal`, `kinesic`, `incident`, `gap` and `note` elements
against Congreso XV Diario parentheticals, split on the Diario’s own compound separator
into atoms, each multi-labelled, editorial apparatus rejected.
Denominators are the XV media-index span (956.9 h over 180 sessions) and, for Galician,
the Diario’s own session open and suspend time notes (1,283 h over 268 sittings); the
ParlaMint-ES denominator is estimated from word counts and is judgment, roughly ±20%.

**Result (run).** Stage directions run at about **50 atoms per audio-hour in all three
corpora** (47,391 in Congreso XV, 83,421 in ParlaMint-ES, 67,754 in ParlaMint-ES-GA).
**Applause dominates at 29.7 per hour in XV**, 24.6 and 25.5 in the other two, roughly
one every two minutes, followed by murmurs 5.7, quoted interjections 4.9, pauses 2.3,
protests 2.1, gestures 1.9 and laughter 1.6. **91.6% of XV applause marks are the bare
word “Aplausos”** and only 6.1% carry an intensity word, so the record offers almost no
gradation to learn from.
The rate is not stationary: applause per sitting rises 2.6-fold across ParlaMint-ES from
2015 to 2022.

On attribution, the Diario **never opens an unattributed turn**: zero of 29,476 speaker
markers carry a non-person label.
Voices it cannot name are demoted into parentheses instead — 4,680 quoted interjections,
of which 2,233 (47.7%) name nobody, split between one unnamed deputy, several deputies
at once, and the public gallery.
That gives **R11 a record-layer residual of 6.8%** (2,323 of 34,156 voice events), a
floor microphone metadata cannot touch because those voices are off-mic by definition,
and a lower bound besides, since editors omit what they do not hear.
Separately, 12,159 chair markers give an office rather than a name.

Turn length by role is starkly asymmetric: the chair’s median turn is 22 words in XV
against a member’s 638, and **the chair takes 54–59% of turns but only 5–12% of the
words** — which is why a time-weighted metric cannot see the chair and a per-turn metric
is dominated by it.

The short-turn question is now settled empirically rather than by inference.
At the measured 145 words per minute, three seconds is about seven words; 16.4% of XV
turns are under ten words and 41.9% under thirty.
But **all 9,441 XV clip durations are exact multiples of 60 seconds**, the smallest
non-zero being 60 s, so the media index is quantised to the minute and the sub-minute
stratum cannot exist in it.
Only **32.0% of Diario speaker markers have a clip at all**.

**Verdict.** R9 gains a numeric target and a scoring shape: binary event detection at
about 30 events per hour, recall measured per sitting because the spread runs from 20 to
41 per hour, and no corpus-wide rate prior as a decoding constraint.
R11 gains a hard 6.8% floor plus a design item it must own, a chair registry driven by
the 482 handover marks.
H-001’s short-turn clause is unscoreable on the media-linked layer and needs either the
verbatim layer or a Diario re-parse that restores the chair turns.

**Two corrections to earlier notes.** The XV Diario carries **42,860 stage-direction
marks**, not the approximately 28,300 estimated when the dataset was first described.
And **ParlaMint-ES is a worse resolution target than the Diario it derives from**: it
drops the `who` attribute on 74.5% of chair utterances and truncates 1,457 of 1,636
action descriptions to a bare “El señor” or “La señora”, which are exactly the
chair-handover lines the Diario writes in full.
Work that needs chair attribution should parse the Diario, not ParlaMint.

#### 10.12 Does the chair’s hand-off survive recognition? (S9; the load-bearing test)

**Hypothesis.** The text-only floor of §10.1 was measured on the edited record.
Live, the announcement is spoken and the surname is the rare token ASR gets wrong.
S9 tests how much of the floor survives recognition, because every recommendation
resting on §10.1 is an upper bound until it does.

**What ran (Sandbox).** 200 announcement turns sampled from the 12,179 Congreso XV
regular turns, stratified over ten rule and mention-form classes, plus 24
no-announcement controls to test whether ASR *invents* hand-offs.
The synthesised span is the tail of the linked chair turn capped at 60 words (median 15
words, 6.1 s), so the thank-you clause that must be masked sits inside the clip.
Six Piper Spanish voices; a degraded arm adds a synthetic room (RT60 0.35–0.60 s),
three-talker bench babble at 12 dB SNR and pink room tone at 22 dB. ASR is whisper-small
int8 and a Spanish NeMo FastConformer CTC model, both from disk, greedy, one thread.
Scoring uses the §10.1 spike’s own resolver and definitions; the port was checked
against its stored predictions with zero mismatches on all 224 turns.

**Result (run).**

| Condition | coverage | resolved | precision | overall | rostrum-scaled |
| --- | --- | --- | --- | --- | --- |
| Record text (the §10.1 baseline) | 1.000 | 0.945 | 0.979 | **0.925** | 0.931 |
| whisper-small, clean, parser as written | 0.710 | 0.490 | 0.949 | **0.465** | 0.505 |
| whisper-small, clean, + punctuation and case repair | 0.960 | 0.675 | 0.963 | 0.650 | 0.643 |
| whisper-small, clean, + repair + phonetic fallback | 0.960 | 0.825 | 0.939 | **0.775** | **0.787** |
| whisper-small, degraded, + repair + phonetic | 0.945 | 0.780 | 0.891 | 0.695 | 0.699 |
| NeMo CTC es, clean, + repair + phonetic | 0.935 | 0.825 | 0.921 | 0.760 | 0.774 |

**The dominant failure is not name garbling.** It is a lost sentence boundary: ASR
writes “Gracias, señor presidente, señora Nogueras, cuando quiera” as one comma-spliced
sentence, the parser’s backward-looking *gracias* mask swallows it, and the hand-off
disappears. That single defect accounts for 24.5 of the 53.5 points lost, and a roughly
40-line repair (split clauses before a vocative, restore capitals after an honorific)
recovers coverage from 0.710 to 0.960 while costing nothing on record text.

The announced surname survives ASR verbatim only **44.7%** of the time clean and 36.2%
degraded, against a word error rate of 0.161 on the same spans: **WER understates the
damage to attribution threefold**. Surnames marked Basque or Catalan survive 32.3%
against 48.2% for Castilian.
A Spanish phonetic key beats plain fuzzy matching (0.775 against 0.735) with thresholds
flat between 70 and 78, at a precision cost of 0.963 to 0.939.

**Confident misattribution stays rare.** Wrong-member errors run 1–2.5%, and a
same-surname collision occurred once in 200 turns.
The parser abstains rather than guessing, so ASR damage shows up as **lost coverage, not
false names** — which is the failure mode the rubric’s restraint line rewards.

By stratum, role-only announcements ("señora ministra") barely notice ASR because they
never depended on a name; the bare vocative and last-mention strata, which need both a
sentence boundary and a surname, lose the most.

**Verdict.** The text-first spine survives recognition at a discount of roughly fifteen
points, but only with two repairs the current parser lacks.
Quote **~0.79 rostrum clean and ~0.70 degraded** for the live path, not 0.93–0.95. Two
further findings change tool choice: whisper-small and the Spanish CTC model land within
1.5 points of each other despite the CTC model’s much better WER elsewhere, and CTC is
ten times cheaper (real-time factor 0.11 against 1.16), so **punctuation quality and
name-token accuracy, not WER, should pick the ASR**. Roster-biased decoding, which was
never tested, becomes the highest-value untested lever.

**Limitations.** TTS is cleaner and more evenly paced than a chair speaking over a noisy
chamber, and the degraded arm is synthetic.
At n = 200 the 95% interval is about ±0.058. No cross-talk, and no real hand-off clipped
by a chair talking over applause.

#### 10.13 Catalogue of further spikes

**[Proposed]** The remaining spikes, each the cheapest test that could falsify or
de-risk a hypothesis, with tier, effort and the decision it changes.
Dependency order: S24 first (longest lead); S1 opens the Net tier and S2, S3, S8 share
its downloads; S4 and S15 are independent; S13 needs S1 and §10.1; S14 needs S4 or S13
and §10.2; S11, S16 and S22 follow S2; S12 then S17; S5 then S18 and S19.

| Id | What it measures | Tier | Effort | Decision it changes |
| --- | --- | --- | --- | --- |
| S24 | The chamber letter: floor feed, interpreters’ feeds, conference-system event export, archive track layout (drafted; see §3.8, R11) | Chamber | hours to write, weeks to answer | R11, R5 attack 1, Q-001 |
| S1 | Archive audit: `ffprobe` track layout of session and per-intervention MP4s for three co-official sessions; Diario interventions versus clips; co-official portal variant (Q-003) | Net | half a day | whether §5.1’s enrollment recipe works as written; whether co-official clips enroll the interpreter |
| S2 | Interpretation listening test: lag and interpreter-to-original level ratio on ten clips | Net | half a day | which of the four R5 attacks to pursue |
| S3 | Chyron OCR yield on 30 min archive plus 30 min live video at 1 fps | Net | one day | whether OCR joins the chair announcement as a fuser input; defers R10’s face pipeline |
| S8 | Camera-cut count: for every turn under 10 s, is the speaker on camera | Net | half a day | H-008 lives or dies |
| S4 | Watchlist-scale identification on a same-room proxy (VoxPopuli-es 305 speakers; ParlamentParla), N in {50, 150, 300}, AS-norm on/off, FAR at FRR 5% versus VoxWatch | Net, CPU | two days | whether the same-room advantage is real; whether R2 suffices without R4 |
| S5 | Diario-to-audio alignment yield on five sessions through the EuroSpeech pipeline, by language and turn length | Net, GPU | two days | R1’s cost per session; R4’s weak-supervision volume; R6’s paired data |
| S13 | Per-intervention clip identification on the Congreso: enroll from sessions A–C, test on D; TAA@100% by clip duration; out-of-set via held-out members | Net, CPU | two days | H-001’s rostrum clause on the real chamber; real trials for R2 |
| S14 | Candidate set, AS-norm and calibration on S4 or S13 scores through the harness: coverage–TAA curve | Net | one day | H-002 on proxies; whether the constraint alone carries the gain |
| S15 | Cross-lingual enrollment shift in-domain on Basque Parliament 1 (enroll es, test eu and vice versa; language-dependent s-norm) | Net, CPU | one day | whether bilingual members need per-language enrollment |
| S12 | Editorial gap on 30 hand-verbatimized minutes (rostrum, bench, chair): record-WER and HTER of verbatim against the Diario | Net, human | two days | whether the campaign can score against the record before R6 |
| S17 | Prompted-LLM normalizer on S12’s pairs, post-cutoff sessions only | Net | one day | whether H-006 needs training |
| S16 | Interpreter pool count: embed interpreter-only windows from ten sessions, cluster, check stability | Net, CPU | one day | R5 attack 2 |
| S22 | Target-speaker extraction on real interpreted clips with archive enrollment; cosine before and after; WER against the Diario’s original-language block; LID after extraction | Net, GPU | three days | H-005; whether attack 3 deserves a research effort |
| S18 | Joint speaker-attributed ASR off the shelf (MOSS-Transcribe-Diarize chunked by agenda item; DiCoW with pyannote) on three sessions | Net, GPU | two days | whether R8 survives six-hour audio |
| S19 | Revision replay: one session through the audio stack at a 2-minute finalization window with the harness logger | Net | two days | H-007; whether R7 needs a two-pass design |
| S20 | Sortformer under turn windowing, whole session versus per-window reset | Net, GPU | one day | only if R7 chooses Sortformer |
| F1 | Silence robustness of the co-official fine-tunes (3CatParla, LoS, Nós-gl, xezpeleta-eu) versus turbo on the S6 non-speech streams | Net | one day | whether adapted models need a decoding-side guard |
| F2 | Temporal hold-out for Basque and Galician specialist contamination: audio dated after their training cutoffs versus before | Net | two days | whether the specialists’ advantage is skill or contamination |

Dead ends, recorded so they are not re-proposed: aligner yield on synthetic speech
(measures the aligner, whose CER curve EuroSpeech already published); re-measuring
Spanish WER on parliamentary audio (settled by §3.3); blind separation of interpreter
mixtures as a first spike; a face gallery before S8; Sortformer as a session-level
diarizer over six hours; any spike whose only outcome is DER; hallucination tests on
isolated 5 s clips; a same-room enrollment test on TTS voices; estimating the commercial
cap from an API trial rather than from the candidate-set histogram.

#### 10.14 What the spikes changed

- **§4.6 harness.** Turn assignment by intersection over union, not raw overlap;
  fixed-label cpWER/tcpWER and identification error rate as outcomes with the permuted
  forms as the name-permutation credit; the pyannote collar constant; an instrument
  guard.
- **§4.7 and §9 accept rule.** A paired *t* interval on per-session differences, “the
  interval excludes zero”, mean rather than median, turn- or precision-weighted once the
  gain’s heterogeneity is known.
- **§5.1 benchmark.** 40 sessions as the campaign minimum, uniformly medium-length
  sittings preferred; the reference turn list (Diario-derived versus audio-aligned) is a
  prior decision because the Diario-derived list has no sub-minute stratum; the Congreso
  XV dataset supplies the record layer, the roster and the per-clip manifest for free.
- **§7 R3 and §9 H-003.** Supported for rostrum turns on two records (0.93–0.95),
  refuted for all turns (0.77–0.78, 0.92–0.94 with the continuation rule), and now
  measured through recognition: about **0.79 clean and 0.70 degraded**, and only with a
  punctuation-and-case repair and a phonetic roster fallback the parser did not have
  (§10.12). The live floor, not the record floor, is what an acoustic identifier must
  beat; the failure mode is lost coverage rather than false names, which suits the
  restraint line.
- **§7 R2 and §9 H-002.** Candidate-set sizes are measured (median 2 per numbered item,
  10–12 per debate section, 37–39 per session); the commercial caps fit (S23), so an
  enrollment API is admissible as a comparison row.
- **§3.3 and §6.** `whisper-large-v3-turbo` is the open baseline; the co-official
  fine-tunes are fragile on silence and the Basque specialists are contamination-suspect
  until F2 runs; API agreement is a drift monitor, never a ranking.
- **§7 R9 and R11.** R9 gains a numeric target (about 30 applause events per audio-hour,
  50 stage directions of all classes, 92% of applause marks carrying no intensity word)
  and a per-sitting recall protocol.
  R11 gains a hard 6.8% record-layer residual that microphone metadata cannot reach, and
  a chair registry as a design item.
  The record’s chair turns must come from the Diario, not from ParlaMint, which drops
  the speaker attribute on three quarters of them (§10.11).
- **Environment.** Everything acoustic that matters next (S1, S2, S4, S5, S13) needs one
  box with a GPU, Hugging Face, YouTube and congreso.es access; the sandbox’s reach
  (GitHub release assets and PyPI) was enough for the instrument, the text side and
  synthetic guards, and for nothing that touches real chamber audio.

### 11. Evaluation strategy

**[Analysis]** The user-level question is whether credible, realistic evaluations can be
run cheaply enough to drive a research–eval loop.
This section answers it in seven parts: the evaluation approaches compared, the evals
per research direction, the loop itself by turnaround tier, the budget, the
cross-service ASR question, the risks to credibility, and an ordered recommendation.
The underlying inventory of every evaluation resource (about 80 rows across ten
sub-problems, with data, access, metric, cost and compute per run) is §9 of the
[companion tooling inventory](research-2026-09-02-parliamentary-transcription-tooling-inventory.md),
cited here as (inventory §9.n); the cross-service ASR protocol is its §10, cited as
(inventory §10.n); the spikes of §10 are cited as (§10.n). Numbers keep their provenance
tags: (gh), (readme), (search).

#### 11.1 Evaluation approaches compared

**[Analysis]**

| Approach | Validly measures | Cannot measure | Data | Cost to build | Cost per run | Turnaround | Contamination risk | When to use |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| (a) Unit and synthetic: TTS sessions, overlays, ESC-50 | plumbing, format round-trips, scorer invariants, throughput; degradation under overlay gain and lag; the hallucination guard on long non-speech (S6) | any accuracy; open-set FAR (TTS voices are unnaturally tight); room, overlap, the broadcast mix | Piper es and ca voices on Diario text, no eu or gl voice (gh); ESC-50 (gh); VoxPopuli S2S, 1.2k h into Spanish (readme) | CPU-hours; 1 GPU-h per 10 h overlay set | seconds | seconds to minutes | none claimed | every commit; never in a results table |
| (b) Public proxy corpora | ca and eu WER at ±1 point; same-room identification at N in {50, 150, 300} on VoxPopuli-es and ParlamentParla ids (S4); cross-lingual shift (S15); literature-comparable DER; the FAR-versus-watchlist shape | the Congreso microphone chain and register; named attribution against a roster; short turns (segments are pre-cut); es-419 | VoxPopuli-es 166 h, 305 speakers; ParlamentParla 611 h; Basque Parliament 1 test 9.6 h, CC0; AMI, VoxConverse, VoxCeleb, VoxWatch lists; Nos_TranscriSpeech-GL gated | five lhotse manifests | GPU-A 10–40 min per split; 1–2 GPU-h to embed 611 h | hours | high for ASR: VoxPopuli in Granary and Granite, Basque Parliament in xezpeleta, ParlamentParla likely in the BSC line (inventory §10.2.5); low for speaker trials | nightly; week one for the first ca, eu, es numbers |
| (c) API agreement, Parlamento.ai style | drift, regressions, a shortlist, per-language failure modes | which system is right; the eu and gl tail, where the paid panel disagrees by 13–15 points (gh); any speaker metric | any audio, no reference; Congreso XV clips, since Parlamento.ai’s are not redistributable | none | USD 0.71 per audio-hour (gh) | hours | consensus bias | monthly canary; never a ranking |
| (d) Text-side on the record | announcement coverage and precision; candidate-set sizes; surname ambiguity; stage-direction base rates; turn-length priors; cap fit (S23); the R11 residual | anything acoustic; word timing (XV timing is per speech); interjections the record omits; whether an announcement was audible | ParlaMint-ES, 15M words; Congreso XV, 180 sessions, 10,569 speeches, 8.5M words, per-speech MP4 links | parsing done (§10.1) | CPU minutes | minutes | LLMs have read the Diario: post-cutoff sessions for LLM components | now, in the sandbox; every commit for the parser |
| (e) Diario-aligned benchmark with a verbatim layer | TAA@c, fixed-label cpWER and tcpWER, IER, record-WER, HTER, editorial gap, stratified per §4.3; the instrument for H-001, H-002, H-005, H-006, H-008 | production latency (an archive MP4 is not the feed); R11; the interpreted tail beyond a few hours | 20–50 sessions, both layers, roster ids, regimes | record layer: parsing; verbatim EUR 60–160 per session-hour; 5 sessions EUR 2–6k, 50 sessions EUR 25–65k (inventory §9.12) | 250 h: 7 GPU-A h plus 2 H100 h | hours for 5 sessions, days for 50 | C0 after 2026-03; the verbatim layer is never published | per milestone; confirmatory |
| (f) Human post-edit effort: HTER, edit time | editing minutes per session-hour, the cost a chamber pays; whether record-WER tracks effort; the break-even near 25% WER (Althingi, search) | fast iteration; editor skill and tooling confound it | system output, the record, two or more editors | none | 2–4 annotator-hours per session-hour (inventory §9.12) | weeks | order and learning effects | quarterly, for R6 and before any deployment claim |
| (g) Live-replay latency and revision | finalization lag p50/p95, name revision rate, normalized erasure, first-versus-final name accuracy at 30 s, 2 min, 5 min, computation-aware | accuracy; jitter and encoder delay unless the stream itself is replayed | any session with a record layer; the Canal Parlamento stream for H-007 | the logger (no reference implementation located) | free; 1× real time, 8 concurrent on an 8 GB GPU (gh) | hours per session | none | nightly on one session; H-007 on 20 or more from the stream |
| (h) Leaderboards and vendor claims | accepted language codes; ordering on English and read speech; RTFx | in-domain WER for es, ca, eu, gl; cross-vendor comparability; reproducibility (a third of AA-WER is unreleased) | Open ASR Leaderboard CSV (gh), AA-WER, vendor pages | none | not re-runnable | immediate | high: test sets in training mixes, self-selected subsets | shortlisting only |

Credible numbers arrive in this order.
The text-side evals run in the sandbox today, and §10.1 already puts H-003 near its
threshold (R3 below).
The XV clips are pre-segmented and named, so identification against an enrollable roster
of 271 speakers is measurable the day a box with congreso.es access exists, with no
aligner and no annotator (S13). The proxy corpora give ca and eu intervals the same
week; a record-layer smoke benchmark with verified labels follows within three weeks;
the protocol’s 4 h double-annotated verbatim sample, ordered in week one, adds WER,
cpWER and the editorial gap two weeks later.
Interjection and interpreted-tail claims wait for the 20–30-session build.
This departs from inventory §9.12, which prices the smoke set with all four annotation
layers at once: label verification and timestamp spot-checks (EUR 0.5–2k) are what TAA
needs, and the verbatim layer is cheaper and better sampled as the 4 h stratified sample
than as whole sessions.

#### 11.2 Evals per research direction

**[Proposed]** “Improvement” is read under the amended §4.7 rule: the 95% paired *t*
interval on the per-session differences excludes zero and no guard is breached.
Session counts come from the sizing simulation (§10.3), with 80-turn sessions, a base
TAA of 86.5 and a moderately uneven gain (per-session gain sd 2.9 points): a 10-point
gain is detectable at any benchmark size above the 15-session floor, 5 points with 11
sessions, 3 with 32, 2 with 75; a 10-point gain on interjections alone needs 45. At 20
sessions the benchmark resolves about 3.6 points overall, at 40 about 2.6, at 50 about
2.3; the short-turn stratum resolves nothing finer than 15 points at 20 sessions.
Every number is conditional on the session × system interaction, which nothing has
measured yet; the first real paired comparison must report the standard deviation of the
per-session differences so that the sizing stops being a guess.

##### R1. Build the in-domain benchmark and measure the real baseline

| Item | Detail |
| --- | --- |
| Decision | does the baseline match H-001 (rostrum over 60 s above 95%, under 3 s below 50%); which stratum first |
| Primary eval | Diario-aligned benchmark and roster trials (inventory §9.5, §9.4): TAA@{100, 95, 90}% per §4.3; fixed-label cpWER on the verbatim subset; §4.6 harness, IoU matching (§10.2) |
| Cheap proxy | S13 on the XV clips (rostrum); the VoxPopuli-es cpWER pilot (inventory §9.5) |
| Improvement | a determination; at 20 sessions the rostrum interval is about ±2.1 points and the short-turn interval ±9.3, which reaches ±4 only at 100 sessions (§10.3) |
| One run | 5 sessions: 45 min GPU-A ASR plus 15 min H100 diarization; 50 sessions: 7 GPU-A h plus 2 H100 h |
| Invalidated by | enrollment overlapping eval sessions; raw-overlap matching (16-point floor error, 51 on interjections, §10.2); no inter-annotator error bar |

The XV dataset gives labels and timing for 10,569 speeches and enrollment for 271
speakers, so the record layer comes first.
The record omits most interjections (S7): the under-3 s clause needs the verbatim layer.

##### R2. Candidate-set constraint plus normalization and calibration

| Item | Detail |
| --- | --- |
| Decision | does the agenda set (median 2 speakers per numbered item in the Congreso XV media index, 10–12 per Diario debate section, 37–39 per session; §10.1, §10.10) plus AS-norm and calibration dominate R1’s curve; does calibration add anything |
| Primary eval | coverage–TAA sweep (inventory §9.10): TAA@95% paired by session; Cllr and actDCF as guards; ECE over turns |
| Cheap proxy | S14 on S4 and S13 scores; FAR at FRR 5% against the VoxWatch curve |
| Improvement | H-002’s +10 points of TAA@95%, detectable at any size above the 15-session floor; higher TAA@100% with lower TAA@90% does not count (§4.4) |
| One run | seconds once posteriors are logged |
| Invalidated by | random subsets standing in for agenda sets; a calibrator fitted on eval sessions; top-1 outputs without an unknown mass |

Ablate constraint, AS-norm and calibration separately; the gain may be all constraint.

##### R3. Structured-context fusion for naming

| Item | Detail |
| --- | --- |
| Decision | do announcements, agenda prior and calibrated posterior, fused by rules then an LLM, raise TAA on short and bench turns; is an LLM stage needed |
| Primary eval | benchmark TAA@95% on bench, chair and interjection strata, paired by session, one source ablated at a time; the announcement-presence stratum (§4.3) |
| Cheap proxy | text-only coverage on ParlaMint-ES and Congreso XV (inventory §9.11); S9 for what survives ASR |
| Improvement | H-003 is a determination (80% of rostrum turns from text); a 10-point bench-reply gain needs about 18 sessions (MDE 8.3 points at 20), on interjections about 45 (§10.3) |
| One run | text in seconds; fusion on 5 sessions in minutes |
| Invalidated by | announcements edited into the record (text coverage bounds the audible rate from above); ASR-garbled surnames; an LLM fuser that has read the Diario |

S9 (§10.12) measures the live path at about 0.79 on rostrum turns clean and 0.70
degraded, after a punctuation repair and a phonetic fallback.
The chair-parser spike (§10.1) settles H-003 for rostrum turns on the record and refutes
it as a claim about all turns: on two independent records of the Congreso the text-only
pipeline puts the right person on 93–95% of rostrum turns (150 words or more with a
speaker change) and 97% of turns over 600 words, on 77–78% of all regular turns, 92–94%
with the rule that no announcement means the previous speaker continues, and on 49–55%
of turns under 30 words.
The short-turn claim waits for the benchmark, and the live claim waits for S9, because
the record’s clean hand-off sentence is an upper bound on what survives ASR.

##### R4. Weakly supervised in-domain speaker modelling

| Item | Detail |
| --- | --- |
| Decision | does in-domain fine-tuning halve FAR at FRR 5% at 350 enrolled (H-004); does calibration alone take the gain |
| Primary eval | Congreso roster trials (inventory §9.4): FAR at fixed FRR at N in {50, 150, 350}; Cllr, minDCF, actDCF; one trial list; bootstrap over speakers |
| Cheap proxy | archive-enrollment trials on ParlamentParla and Basque Parliament (inventory §9.4); S15; the Iberian cross-lingual SV set from Common Voice |
| Improvement | FAR halved on the same trials with at least 100 held-out impostor speakers; every N reported, since EER roughly doubles from 100 to 700 enrolled (§3.5) |
| One run | 611 h embedded in 1–2 GPU-h; the 907 h of unique XV clips in about 3 GPU-h, once |
| Invalidated by | enrollment and test from one session; eval speakers in training trials; extractors pretrained on VoxPopuli audio (C1); TTS same-room tests |

Run the off-the-shelf arm (S4, S13) first; if S14’s calibration alone halves FAR, R4’s
training stays parked.

##### R5. The interpretation-mixed tail

| Item | Detail |
| --- | --- |
| Decision | which of the four attacks to fund; does extraction recover half the TAA and WER loss on interpreted segments (H-005) |
| Primary eval | the real interpreted-segment subset (inventory §9.6): TAA and WER paired by segment against the isolated variant if the portal serves one (Q-003), else WER against the Diario’s original-language block |
| Cheap proxy | the simulated overlay and VoxPopuli S2S mixing (inventory §9.6); S2 and S10 for level ratio and flip point; S16 for the interpreter pool |
| Improvement | half the paired ΔTAA and ΔWER on 60–100 or more segments (inventory §10.4); 50 sessions may hold a few hours (54 of 9,399 XV speeches, 43 sessions) |
| One run | mixing on CPU; scoring 10 h in 17 min on GPU-A plus extraction |
| Invalidated by | linear mixing at constant lag (no AGC, ducking or shared encoder); an unmeasured level ratio; an interpreter enrolled from a mixed clip |

H-005 needs isolated floor audio no public source provides; until S24 or Q-003 delivers
it, score WER against the original-language text and TAA against the same speaker’s
Spanish turns, paired by segment.

##### R6. Record-form normalization as a learned transform

| Item | Detail |
| --- | --- |
| Decision | does a learned or prompted normalizer close half the editorial gap without raising verbatim WER (H-006); is training needed (S17) |
| Primary eval | record layer plus verbatim subset (inventory §9.7): record-WER and HTER as outcomes, the gap as floor, verbatim WER as guard; paired by clip |
| Cheap proxy | Europarl-ASR’s two manual tiers (9 WER points, en); EuroSpeech CER tiers; S12’s 30 hand-verbatimized minutes; NeMo es ITN tests |
| Improvement | record-WER cut by half the gap with verbatim WER inside its interval; HTER beside it, since record-WER can fall while edits rise |
| One run | text only, seconds; an LLM normalizer, minutes |
| Invalidated by | the normalizer choice (§11.6); an LLM that has read the Diario; alignment-filtered pairs that keep easy segments; over-editing |

##### R7. Bounded-lag architecture with revision accounting

| Item | Detail |
| --- | --- |
| Decision | does a 2-minute lag cost nothing in TAA and keep name revisions under 5% (H-007); is a two-pass design needed |
| Primary eval | live-stream replay (inventory §9.9): finalization lag p50/p95, name revision rate, normalized erasure, first-versus-final name accuracy at 30 s, 2 min, 5 min; TAA paired against offline |
| Cheap proxy | S19 on one archive session; ESIC conventions; Parlamento.ai’s concurrency data, 8 sessions at p95 2.36 s (gh) |
| Improvement | revisions under 5% is a determination; “within the session interval” is equivalence at about ±3.6 points with 20 sessions and ±2.6 with 40 (§10.3): state ±3, use 30 or more |
| One run | wall-clock bound: 25 h at 8 concurrent in 3–4 h |
| Invalidated by | archive replay standing in for the feed (no jitter or encoder delay); computation-unaware timing; a logger that records finals only |

##### R8. Enrollment-conditioned joint speaker-attributed ASR

| Item | Detail |
| --- | --- |
| Decision | does a joint model (SE-DiCoW or TS-ASR-Whisper with candidate profiles; MOSS as long-form baseline) beat the modular pipeline on overlapped and short turns without losing on long ones |
| Primary eval | the verbatim subset: fixed-label cpWER and tcpWER (5 s collar), TAA, speaker-count error as guard; paired by session; stratified by duration and overlap |
| Cheap proxy | the VoxPopuli-es cpWER pilot; AMI SDM and NOTSOFAR-1 against DiCoW’s 18.1 and 18.4 (§3.4); S18 on three sessions against the record layer |
| Improvement | fixed-label cpWER lower on the under-10 s and overlapped strata by more than the paired interval; equivalence within ±2 points over 60 s; speaker count no worse |
| One run | 5 sessions in 1–2 GPU-h; MOSS chunked at its 90-minute cap |
| Invalidated by | permutation-invariant cpWER (every name wrong still scores 0.0, §10.2); anonymous labels without a naming step; overlap-heavy proxies |

cpWER needs verbatim text, so this test waits for the 4 h sample and the VoxPopuli-es
pilot.

##### R9. Stage-direction detection

| Item | Detail |
| --- | --- |
| Decision | does one applause detector cover most of the record’s marks, and at what precision |
| Primary eval | Diario stage directions aligned to audio (inventory §9.8): precision and recall per class, time-anchored through the verbatim layer |
| Cheap proxy | ParlaMint base rates (S7); ESC-50 folds; AudioSet applause and laughter AP (PANNs mAP 0.439 overall) |
| Improvement | F1 per class against a PANNs baseline; recall against the record is agreement with the stenographer, not the room |
| One run | seconds; 0.2–0.5 annotator-hours per session-hour in the verbatim pass |
| Invalidated by | editorial under-recording; priors from 5 s isolated clips; applause over speech unscored |

##### R10. Multimodal identity

| Item | Detail |
| --- | --- |
| Decision | do active-speaker detection, face matching and overlay OCR raise TAA on turns under 10 s by more than the best acoustic change (H-008); does the camera show interjectors |
| Primary eval | the benchmark with archive video: TAA on the under-10 s stratum, paired against the best acoustic system (a difference of differences) |
| Cheap proxy | S8, a manual camera-cut count that can kill H-008 in half a day; S3, chyron OCR yield on archive and live video |
| Improvement | on interjections 15 points need about 19 sessions and 10 points about 45 (§10.3); S8’s on-camera fraction bounds the gain before any model runs |
| One run | 1 fps OCR and ASD over 25 h in a few GPU-h |
| Invalidated by | archive video with chyrons the live feed lacks; a face gallery built before S8; director habits differing by session type |

##### R11. Conference-system integration as the production path

| Item | Detail |
| --- | --- |
| Decision | do microphone events resolve more than 95% of turns, and where does the residual sit |
| Primary eval | none on public data; a join against the benchmark once CoCon or DICENTIS events exist (S24) |
| Cheap proxy | S7’s text-side residual; Congreso XV turn-type shares as the fraction a seat map would resolve |
| Improvement | exception rate under 5%, residual concentrated in interjections and the chair; a determination on whichever sessions carry events |
| One run | seconds |
| Invalidated by | seat-to-person mapping errors; event clocks unaligned to the audio; the record’s omission of interjections |

#### 11.3 The research–eval loop

**[Proposed]** Every result at every tier carries the harness version, the normalizer
version, the contamination flag and the regime fields of §3.1, or it is not a result
(§4.6).

| Tier | What runs | When | Budget | What it can settle |
| --- | --- | --- | --- | --- |
| Seconds | the harness suite (71 tests in 8.5 s, §10.2); scorer invariants (a perfect hypothesis scores TAA 1.0 everywhere; every name wrong scores fixed-label cpWER 1.49, permuted 0.0); the 22 NeMo es ITN grammar files; normalizer round-trips; the parser regression on the 12,179-turn XV table | every commit | CPU only | nothing about hypotheses; it guards the instrument, where the matching-rule error was caught |
| Minutes | 1–2 h smoke sets: Piper synthetic sessions (§10.4); ESC-50 long streams (S6); the mixture flip curve (S10); the BSC ca–es test, 4 h 09 min in 7 min on GPU-A; 1 h of XV clips in 2 min on GPU-A or 20 min on CPU | every pipeline commit | under 0.1 GPU-h | the hallucination guard (S6), the acoustic-ID gate on mixed segments (S10), parser robustness (S9) |
| Hours | proxy splits: Basque Parliament test in 16 min, ParlamentParla clean_test in 10, VoxPopuli-es test in 10, VoxConverse in 22 on an H100; S4 embeddings, 1–2 GPU-h; S13 on 20 sessions of XV clips; the 5-session smoke benchmark, 25 h, in 1–2 GPU-h end to end | nightly | 2–5 GPU-h per night | H-002 on proxies and the smoke set; H-004’s off-the-shelf arm; H-001’s rostrum clause (S13); S15; whether R8 survives long audio (S18); the first ca and eu intervals |
| Days | the 20–50-session benchmark: 250 h in 7 GPU-A h of ASR, 2 H100 h of diarization and 30 wall-clock hours of streaming replay at 8 concurrent; the service matrix: 25 h, two language conditions, 13 services, about USD 600 and 40 GPU-A h (inventory §10.5) | per milestone; weekly for the standing best | 10–20 GPU-h and USD 100–600 per pass | H-001 both clauses; H-002 confirmatory; H-007 at about ±3.6 with 20 sessions; R3’s bench-reply gain at 18 or more; R8 |
| Weeks | the verbatim layer: 4 h double-annotated in two weeks, 5 sessions in 2–3 weeks; the post-edit study; the chamber’s answer to S24; Nos_TranscriSpeech-GL access; the RTVE licence for SDIAC | per quarter | annotator money, §11.4 | H-006 and the editorial gap; HTER; H-005 on the real subset; R11 |

The Congreso XV dataset makes the text-side tiers free today: the parser regression, the
candidate-set histogram, the roster-ambiguity table and the S23 cap fit run in the
sandbox on CPU. The audio tiers do not: every model in the sandbox came from GitHub
release assets and ran through sherpa-onnx on CPU (§10.4), and with neither congreso.es
nor Hugging Face reachable, no clip was downloaded and no pyannote, ReDimNet2 or LoS
checkpoint pulled. The nightly and daily tiers need one box with both, a mirrored weight
store (protocol §5) and one 8 GB GPU; the H100 figures are conveniences, not
requirements.

#### 11.4 Cost and speed budget

**[Proposed]** The minimal stack, cumulative, at the rates in inventory §9.12 and
protocol §5.

| Item | First four weeks | First quarter, cumulative |
| --- | --- | --- |
| Annotator-hours | 55–100: the 4 h verbatim sample, double-annotated and adjudicated (34); label verification on 5 sessions (12–38); timestamp spot-checks (8–25); a 1 h ParlamentParla audit (3–4) | 170–450: adds the 5-session verbatim layer (40–80); label and timestamp verification on 20–30 sessions (50–225); a 10-session-hour post-edit pilot (20–40); stage directions in the verbatim pass (5–12); the 10% second-annotator sample |
| Euros | 1.7–3.9k | 5–18k |
| GPU-hours, 8 GB GPU-A | 50–70: the cheapest credible ASR pass (10); S13 and S4 embeddings (5–7); nightly runs (30–45); S18 (2) | 300–350: nightly runs (about 200); eight full-benchmark passes (30–40); the open-model side of the service matrix (40); R8 (20); streaming replay (30) |
| API dollars | 60–150: the three sandbox services on the 25 h pass, two language conditions; one canary | 2–3k: the 13-service matrix on 60 h (1.5–2k), the 25 h pass on the internet-box services (600), canaries |
| Wall-clock | week 1 text-side and proxies; week 2 S13 and the smoke set’s record layer; weeks 3–4 the verbatim sample returns | 20–30 sessions with verified labels by week 10; the service matrix by week 8; the post-edit pilot by week 12 |

Assumptions: annotators at EUR 25–40 per hour and 3–4 hours per audio-hour to correct a
LoS draft (inventory §9.12); about 5 h per session; sessions after 2026-03 only; 35.6
audio-hours per hour on GPU-A for large-v3-turbo (readme); a box with congreso.es and
Hugging Face from week two; the Chilean sample deferred until Q-002 is answered.
The 50-session set with all four layers (EUR 25–65k, 4–8 months) is deliberately outside
the quarter: 32 sessions detect a 3-point overall gain, 40 bring a 10-point interjection
claim to 80% power, and 50 buy 2.3-point resolution and nothing qualitative (§10.3), so
the marginal money goes to verified labels on 40 sessions (record layer, EUR 15–60 per
session-hour) and to the interjection stratum rather than to whole-session verbatim
text.

#### 11.5 Cross-service and cross-language ASR

**[Analysis]** The protocol concludes that no source combines ground truth, the services
and the four Spanish-state languages on parliamentary audio (inventory §10.1). The Open
ASR Leaderboard covers es and pt on read speech and no ca, eu or gl; AA-WER is English
only with a third unreleased; Parlamento.ai’s study is agreement with a paid panel whose
members disagree among themselves by a median 5.1 points on es, 11.7 on ca, 13.0 on gl
and 15.1 on eu, at ±2–3 points per language (gh); vendor figures are self-selected read
sets with no eu or gl number from anyone; Albayzin 2024 has audited eu and es ground
truth but only challenge systems.
On the eu and gl tail nobody knows which side is wrong.

The sample design (inventory §10.2): 2–3 h per language for es, ca, en and pt and 4–6 h
for eu and gl, sized from a clip-level standard deviation of 8–12 points; paired,
because a difference on identical clips costs a quarter to a third of that; at least 25
speakers per language, none above 8% of words; 3–6 min chunks cut at turns and
stratified by speaker, duration, noise, gender, variety and date; a 4 h Congreso
verbatim sample, double-annotated and adjudicated (EUR 1.0–1.4k, two weeks), sessions
after 2026-03 only; CER primary for eu; a C0/C1/C2 flag on every cell.

Amazon Transcribe, Google Chirp 3 and Gemini run from the AWS-plus-Google sandbox;
Deepgram (which lists none of ca, eu, gl), AssemblyAI, Speechmatics, ElevenLabs, OpenAI,
Azure, Gladia, Soniox and Rev.ai need an internet box; open models need mirrored
weights.
The 25 h first pass costs about USD 600 in API fees and 40 GPU-A hours, the 60 h
matrix USD 1.5–2k, ground truth EUR 1.5–2.1k, engineering 3–4 weeks (inventory §10.5).

Two departures: the protocol’s first pass scores ParlamentParla clean_test as ground
truth while noting it is aligned to official text, so its ca numbers should wait for the
1 h hand-audit it proposes; and the Chilean sample is deferred until Q-002 says a Latin
American chamber is in scope.

#### 11.6 Risks to credibility

**[Analysis]**

- **Contamination, now measured rather than suspected.** Reading the model cards
  (companion report §11) confirmed it reaches the **shortlist**:
  `BSC-LT/whisper-large-v3-LoS-punctuated`, the baseline ASR model, declares training on
  **ParlamentParla v3, Nos_ParlaSpeech-GL, 3CatParla and Common Voice**, which are the
  Catalan and Galician ground truth this plan proposed, so those pairings measure
  memorisation rather than accuracy.
  `HiTZ/stt_eu_conformer_ctc_large`, the shortlist aligner, declares **Basque
  Parliament** and reports 4.21 WER on its own training corpus.
  VoxPopuli or Europarl-ASR sit in the declared training of Granite Speech 4.1,
  Qwen3-ASR and Qwen3-ForcedAligner, all four NVIDIA ASR models, distil-whisper and
  WavLM-base-plus-sv; the Diario is in every LLM. Mitigation: the C0/C1/C2 flag applies
  per **(system, set) pair**, not per corpus; the Galician leg needs a source LoS has
  not seen, or is reported C2; F2’s temporal hold-out extends to Catalan and Galician;
  headlines only from sessions after 2026-03, with session dates in the published
  manifests.
- **Agreement is not accuracy.** The paid panel disagrees most on eu and gl, and it
  degrades faster than the systems on low-SNR clips (§10.5). Mitigation: agreement as a
  monthly drift monitor only; ground truth on the tail first.
- **Synthetic optimism.** TTS voices are unnaturally tight, so open-set FAR looks far
  better than it is; linear mixing at constant lag ignores AGC, ducking and the single
  encoder; no eu or gl Piper voice exists.
  Mitigation: synthetic numbers never in a results table; every synthetic curve anchored
  to a real clip measured in S2.
- **Permutation-invariant cpWER forgives wrong names.** With every word right and every
  name rotated, MeetEval cpWER and DER are both 0.0000, while fixed-label cpWER is 1.49
  and IER 0.9987 (§10.2). Mitigation: the fixed-label forms and IER are the outcomes;
  the permuted forms are reported beside them as the name-permutation credit.
- **DER time-weighting hides short turns.** A 2 s interjection inside a 4-minute turn is
  under 1% of the time, and the matching rule alone moved interjection TAA by 51 points
  on a perfect hypothesis (§10.2). Mitigation: DER as mechanism only; TAA per turn and
  stratified; IoU matching; the 0.25 s collar passed to pyannote as `collar=0.5`.
- **Normalizer choice.** Number spelling, `l·l` variants, apostrophes and stripped
  accents move ca by several points (inventory §10.7). Mitigation: one versioned
  normalizer on both sides, Whisper’s stricter one as a second column, the version in
  every row.
- **Vendor language-code acceptance.** Deepgram lists none of the three, Scribe lists eu
  without a tier, auto-LID collapses gl into es.
  Mitigation: hinted and auto-detect both reported, detected language logged,
  language-collapse and wrong-language guards, a cell reported only at 100% manifest
  coverage.
- **No public named-attribution benchmark for Spanish.** REPERE is French television
  behind an ELRA fee; SDIAC is 74 speakers under an RTVE licence.
  Mitigation: the benchmark is the deliverable, S13 on the XV clips is the interim
  anchor, and the manifests are published so the set can be rebuilt.
- **A reference without an error bar.** Mitigation: 100% double annotation on the 4 h
  sample and 10% elsewhere, with inter-annotator WER (expected 1–3 points) reported as
  the floor below which differences are noise.
- **Accept-rule calibration.** The percentile bootstrap of the mean rejects a true null
  8.2% of the time at 20 sessions and 5.9% at 100; the bootstrapped median is
  conservative (3.8% at 20) and wastes about a third of the benchmark in power; the
  paired *t* interval on per-session differences is calibrated at every size tested
  (§10.3). Mitigation: the amended §4.7 rule (paired *t*, “the interval excludes zero”,
  turn-weighted or precision-weighted mean once the gain’s heterogeneity is estimated),
  pre-registered contrasts, power reported with every negative result.

#### 11.7 Recommendation

**[Proposed]** In order, with the go/no-go signal for each.

1. **Freeze the harness and the text-side evals this week**: attrscore 0.1.0 with IoU
   matching, the parser regression on the XV table, the candidate-set histogram, the ITN
   tests. Go for R3’s parser as a fuser input: coverage at or above 80% at precision at
   or above 95% on rostrum turns, already met on turns over 500 words; no-go if S9’s
   audio version falls below 60%.
2. **Stand up the box with congreso.es and Hugging Face access and run S13**: twenty XV
   sessions, enrollment from earlier sessions, held-out members as impostors, TAA@100%
   by clip duration and out-of-set FAR. Go: rostrum TAA@100% above 90% off the shelf
   confirms the R1 shape and puts R2 next; below 80%, R4 moves ahead of R3.
3. **Run the cheapest credible ASR pass from the sandbox** (inventory §10.5): Basque
   Parliament test, BSC CS, VoxPopuli-es flagged C2, a Europarl-ASR subset, with turbo,
   large-v3, LoS and the three sandbox services.
   Go: LoS within 2 points of the best reachable service on ca and eu keeps the open
   stack as the default; otherwise budget the internet-box matrix at once.
4. **Build the 5-session record-layer smoke benchmark and order the 4 h verbatim
   sample** in the same week (EUR 1.7–3.9k together), sessions after 2026-03 only.
   Go for the 20–30-session build: the smoke set reproduces S13’s rostrum figure inside
   its interval and inter-annotator WER on the sample is 3 points or less.
5. **Run the nightly loop for two weeks before any confirmatory claim**: synthetic,
   proxies and the smoke set under the harness, every row with its version, flag and
   regime. Go: two weeks with no instrument-guard failure and no unexplained drift on the
   30-clip canary.
6. **Gate R5 on S1 and S2 before any extraction work.** Go for S22: an interpreter level
   ratio near 0 dB and an original-only portal variant (Q-003); at +10 dB or more,
   pursue the feeds through S24 and take the deputy’s identity from non-acoustic
   evidence.
7. **Extend to 40 sessions with the record layer, not 50 with every layer**, and spend
   verbatim money on the stratified sample and the interjection stratum; prefer
   uniformly medium-length sittings, since the variance runs on the harmonic mean of
   turns per session (§10.3). Go: the standing best’s overall TAA@95% interval under
   ±2.5 points and the interjection interval under ±8.
8. **Streaming last**: nightly one-session replay at a 2-minute lag on the smoke set;
   H-007 confirmatory on 20 or more sessions replayed from the live stream, not the
   archive. Go: name revision rate under 5% and the paired TAA difference inside ±3.

## Key Insights

1. **Every accuracy number for named attribution in this domain is borrowed from another
   domain, including the production operator’s.** The dossier’s ~10× claim mixed
   parliamentary WER with meeting-room cpWER and a 74-speaker broadcast challenge;
   Parlamento.ai measures agreement with paid APIs because it has no ground truth and no
   speaker metric at all.
   The benchmark in §5.1 is therefore the highest-leverage artifact, and its baseline
   number is a publishable result by itself.
2. **Raw ASR and Spanish-variety transfer are settled; Galician and Basque are not.** On
   the operator’s 168-clip parliamentary sample, `whisper-large-v3-turbo` differs from
   the paid consensus by 3.25% on pooled Peninsular and Latin American Spanish, less
   than the paid APIs differ from each other (5.10%), while Galician (14.71%) and Basque
   (21.29%) fall outside the operating band.
3. **The market price of raw transcription is USD 0.71 per audio-hour; the chamber pays
   €1.65–2.85 per minute for a record.** The two-orders-of-magnitude gap is the value of
   attribution, editing, and publication — the parts nobody has benchmarked.
4. **Every named output today goes through enrollment, and the commercial caps are 4 and
   50 identities.** A 350-seat roster needs a candidate-set constraint or a self-hosted
   identifier; the open embedding models (ReDimNet2-B6 at 0.23% EER, MIT licence) make
   self-hosting the obvious choice.
5. **The right diarization metric for this task is per-turn accuracy with coverage, not
   DER.** DER cannot see a misattributed interjection; the rubric can.
6. **The interpreter overlay has a non-technical fix and a novel technical one.** The
   interpreters’ feeds exist before the mix; failing that, extracting the deputy from a
   two-language, time-offset mixture is unaddressed in the literature and is the most
   original experiment on the list.
7. **The chair-announcement parser is a gap.** Text-side diarization correction is a
   live subfield with large reported gains, and nobody has published the obvious
   parliamentary special case.
8. **REPERE already solved the shape of this problem in 2013**, at EGER 24.4%, by fusing
   voice, face, and on-screen text; R3 and R10 are that design with components that are
   now an order of magnitude better.
9. **The production load is small.** A median of three concurrent sessions and a tail of
   thirty, on one operator’s 90-day data; a single GPU sits at 10% occupancy.
   Latency work should target revision behaviour, not throughput.
10. **Enrollment conditioning is worth more than model size on the transcription side.**
    SE-DiCoW’s 52% relative tcpWER reduction over DiCoW came from selecting an
    enrollment segment, which a parliament supplies for free.
11. **The editorial gap is a third of the data, not a footnote.** On EuroSpeech only 65%
    of aligned parliamentary segments are within CER 20% of the record; Europarl-ASR
    gained 9 WER points by verbatimizing its training text.
    Any score against the published record is measuring editing policy as much as
    recognition until the two are separated.
12. **The text-only floor is high, and survives recognition at a discount.** Chair
    announcements alone put the right person on 93–95% of rostrum turns on two
    independent records, and 92–94% of all regular turns with the continuation rule
    (§10.1). Through ASR that becomes about 79% clean and 70% degraded, and only with a
    punctuation-and-case repair and a phonetic roster fallback (§10.12). An acoustic
    identifier earns its keep on the short-turn tail, where text reaches 49–55%, and
    must beat the live floor, not chance.
13. **The standard scoring tools forgive a wrong name.** A perfect transcript with every
    speaker rotated to another member scores 0.0 on MeetEval cpWER and 0.0 on DER, and
    1.49 on fixed-label cpWER (§10.2); only the fixed-label forms, identification error
    rate and per-turn TAA measure what a record needs.
14. **The benchmark’s size hinges on a variance nobody has measured.** Forty sessions is
    the campaign minimum, short-turn claims need 45 or more, and the first draft’s
    bootstrap rule was not a 5% test (§10.3); the interval estimator was worth more than
    a dozen sessions.
15. **The in-domain data is already free on the text side.** The Congreso XV dataset
    gives 9,441 named clips (907 h), the roster, the agenda and minute-level timing for
    180 sessions, so the text-side evaluation tiers run today (§10.10, §11.3); it has no
    sub-minute stratum, so the short-turn hypotheses wait for audio alignment.
16. **Cross-service ASR quality per language is unmeasured, and the tail is where the
    references disagree.** No source combines ground truth, the services and the four
    Spanish-state languages on parliamentary audio; on Basque and Galician the paid
    panel disagrees with itself by 13–15 points (§11.5), so the protocol in the
    companion inventory’s §10 is the only route to a defensible ranking.
17. **The stack is chosen on performance; licence is tracked beside it, not against
    it.** Of about 260 tools inventoried, the shortlist is fourteen items (§6.2), picked
    for accuracy, language coverage and ease of running.
    Licence is a separate axis recorded per row: DiariZen, MMS and InsightFace weights
    are non-commercial and are used anyway, since this is research and demo work, with
    the permissive swap named so a productisation is costed rather than discovered late.
    What genuinely excludes a tool is having nothing to run (WeSep, LExt), the wrong
    languages (Kyutai, Parakeet for ca/eu/gl), a retired service, or training on the
    data it would be evaluated on.

## Recommendations

**[Analysis]**

1. **Build R1 before anything else.** Forty sessions with the record layer (verified
   labels, spot-checked timing), a 4 h stratified verbatim sample, and the harness in
   §4.6 and §10.2; publish the manifests.
   Twenty sessions settle H-002 and any gain over four points; only forty make the
   short-turn claims testable (§10.3).
2. **Pre-register H-001 through H-003 and run them in that order.** They are cheap, they
   decompose the problem, and their stratified tables will reorder the rest of the list.
3. **Ask the four questions that change the build** — audio channel and interpreter
   feeds (Q-001), chambers and languages (Q-002), the scored reference layer, and the
   metric definition — before committing to R5 or R6.
4. **Treat Galician and Basque as the ASR frontier**, with temporal hold-outs, because
   the specialist models were trained on parliamentary data.
5. **Keep a slot for conference-system metadata** in every design, and report the
   fraction of turns it would resolve; that is the production path.
6. **Use the campaign discipline in §9.** The ranking in §7 is a prior; the ledger
   should be what changes it.
7. **Stand up one box with a GPU, Hugging Face, YouTube and congreso.es access** and run
   S1, S2, S8 and S13 in its first week (§10.12); everything the sandbox could not touch
   is on the other side of that box.
8. **Run the evaluation loop by tier (§11.3)** with the harness as the instrument and
   the text-side evals in every commit; take headline numbers only from sessions after
   the model cutoffs and from the fixed-label metrics.

## Next Steps

- [x] Write the Diario parser and the chair-announcement parser; measure announcement
  coverage (H-003 on the record: §10.1).
- [x] Stand up the scoring harness; version it (attrscore 0.1.0, §10.2).
- [x] Size the benchmark and fix the accept rule (§10.3, §4.7).
- [ ] Send the chamber letter (S24; drafted) for the feeds, the event export and the
  archive track layout.
- [ ] Stand up the Net-tier box; download five Congreso sessions with co-official
  interventions and settle Q-003 with `ffprobe` (S1); run S2, S8, S13 the same week.
- [ ] Run S9 (announcement sentences through TTS and ASR) to see how much of the
  text-only floor survives recognition.
- [ ] Order the 4 h double-annotated verbatim sample and verify labels on five sessions
  (§11.4); run the cheapest credible ASR pass from the sandbox services (§11.5).
- [ ] Build the R1 evaluation set (40 sessions, record layer) and enrollment set from
  the Congreso XV manifest; run H-001, then H-002 and H-003; publish the stratified
  tables.
- [ ] Run F1 and F2 (fine-tune silence robustness; temporal hold-out for the Basque and
  Galician specialists) before any specialist model enters a results table.
- [ ] Move the spike code out of the gitignored `attic/` into its own repository, per
  the repository’s rule that a line of work with code and data gets one; open the
  campaign directory there with the experiment-loop skill, registry seeded from §9 as
  amended.
- [ ] Re-verify the search-summary figures flagged in §2.1, and every `HF:` model id in
  the companion inventory, against their primary pages when network access allows.

## Methodology

This report was produced in one session on 2026-09-02, after the dossier’s §11 was
written.
The critical review in §2 was written first, from the dossier text alone, before
any new research, so that the review was not shaped by what the research found.

Eight research passes then ran in parallel, each with a bounded brief: joint
speaker-attributed ASR; speaker identification at watchlist scale; Spanish parliamentary
data and alignment tooling; editorial normalization; streaming metrics and multimodal
identity; the interpreter overlay and speech separation; the identity of the two
chambers and Spanish accent transfer; and a fact-check of the dossier’s figures.
Their briefs are summarized in §3 and their sources are in the References.
The fact-check pass returned 10 confirmed, 5 adjusted and 3 unverified verdicts on the
dossier’s eighteen load-bearing figures (§2.1); every adjustment has been applied to the
dossier.

**Network constraint.** The session’s egress policy allowed only GitHub hosts and
web-search summaries.
Figures marked **[Primary]** were read directly from repositories: the pyannote,
DiariZen, WeSpeaker, 3D-Speaker, ReDimNet2, MOSS Transcribe Diarize, TS-ASR-Whisper,
SimulStreaming, ctc-forced-aligner, WhisperX, MeetEval, EuroSpeech, VoxPopuli,
ParlamentParla and Parlamento.ai repositories.
Figures marked **[Technical]** or **[Press]** came through search-result summaries of
papers, vendor pages, and news reports; their primary texts were not opened and they
have not been checked line by line.
The web-search budget was exhausted before the Parlamento.ai–Reversa relationship could
be probed further; that question is open.

**Third pass (same day).** §6, §10 and §11 were added.
The tooling inventory was built from four parallel briefs (ASR; speaker; joint, text and
multimodal; data and services), a same-day GitHub API sweep of 163 repositories for
stars, licence, last push and archived flag, a consolidation pass into 266 rows with
uniform columns, and a critical review of the labels against the definitions and the
repository evidence; the full tables are the companion inventory report and §6 keeps the
shortlist and the index.
Eleven spikes ran in the sandbox tier (§10); their code and data are under the
gitignored `attic/` directory and their reports are summarised in §10, with the numbers
tagged (run). The evaluation strategy (§11) was written from an inventory of about 80
evaluation resources and a cross-service ASR protocol, both in the companion report (§9
and §10 there). Several agent runs were interrupted by a usage limit part-way and were
resumed from their on-disk state; the §6 text was assembled from the writer’s completed
part files, and the §11 draft was reconciled by hand with the final sizing result, which
reversed the draft’s recommendation on the accept rule.

**What could not be verified:**

- Whether the Congreso’s co-official-language portal serves an original-only audio
  variant (Q-003).
- Whether the Archivo Audiovisual MP4s carry a name-and-group overlay on every
  intervention; the claim comes from a search summary.
- The generic “30–50% relative EER reduction” for matched-condition enrollment; it is
  not relied on.
- SpeakerLM’s cpWER, which no source reported.
- Any relationship between Parlamento.ai and Reversa or the Madrid Open.
- The existence and composition of the Albayzin 2020 parliamentary subset.
- Every Hugging Face model and dataset id in §6 and the companion inventory: the pages
  could not be opened, so ids, licences and language lists are as their authors
  published them.
- LDC and ELRA prices for DIHARD III, CALLHOME, REPERE and EPIC.
- Whether the three Congreso XV sessions with placeholder MP4 URLs have audio on the
  portal.
- Deepgram’s handling of ca, eu and gl: the API was unreachable and no key was present,
  so the adapter is written but untested.

## References

**Production operator (primary)**

- [Parlamento-ai/open-source-asr — reproducible ASR study on parliamentary audio, with workload and cost data](https://github.com/Parlamento-ai/open-source-asr)
- [Parlamento.ai — home](https://parlamento.ai/)
- [Parlamento.ai — technology](https://parlamento.ai/p/tech)
- [Parlamento.ai expansion plan — Diario Financiero](https://www.df.cl/df-lab/innovacion-y-startups/el-plan-de-expansion-de-la-startup-de-ia-que-une-a-exasesor-de-pinera-con)

**Joint speaker-attributed ASR (technical)**

- [BUTSpeechFIT/TS-ASR-Whisper — DiCoW and target-speaker Whisper](https://github.com/BUTSpeechFIT/TS-ASR-Whisper)
- [BUTSpeechFIT/DiCoW — inference](https://github.com/BUTSpeechFIT/DiCoW)
- [DiCoW: Diarization-conditioned Whisper for target speaker ASR — Computer Speech and Language](https://www.sciencedirect.com/science/article/abs/pii/S088523082500066X)
- [SE-DiCoW: Self-enrolled diarization-conditioned Whisper — arXiv 2601.19194](https://arxiv.org/abs/2601.19194)
- [Adapting Diarization-Conditioned Whisper for End-to-End Multi-Talker Speech Recognition — arXiv 2510.03723](https://arxiv.org/abs/2510.03723)
- [Grounding Spoken LLMs in Multi-Speaker Audio via Diarization Conditioning (Dixtral) — arXiv 2606.18134](https://arxiv.org/abs/2606.18134)
- [EMMA leaderboard — BUT-FIT](https://huggingface.co/spaces/BUT-FIT/EMMA_leaderboard)
- [TagSpeech — arXiv 2601.06896](https://arxiv.org/abs/2601.06896)
- [SpeakerLM: End-to-End Versatile Speaker Diarization and Recognition with Multimodal LLMs — arXiv 2508.06372](https://arxiv.org/abs/2508.06372)
- [OpenMOSS/MOSS-Transcribe-Diarize](https://github.com/OpenMOSS/MOSS-Transcribe-Diarize)
- [MOSS Transcribe Diarize technical report — arXiv 2601.01554](https://arxiv.org/abs/2601.01554)
- [VibeVoice-ASR technical report — arXiv 2601.18184](https://arxiv.org/abs/2601.18184)
- [DM-ASR — arXiv 2604.22467](https://arxiv.org/abs/2604.22467)
- [Speaker Targeting via Self-Speaker Adaptation for Multi-talker ASR — arXiv 2506.22646](https://arxiv.org/abs/2506.22646)
- [nvidia/multitalker-parakeet-streaming-0.6b-v1](https://huggingface.co/nvidia/multitalker-parakeet-streaming-0.6b-v1)
- [A Unified Speech LLM for Diarization and Speech Recognition in Multilingual Conversations — arXiv 2507.02927](https://arxiv.org/abs/2507.02927)
- [SA-SOT — arXiv 2403.02010](https://arxiv.org/abs/2403.02010)
- [gpt-4o-transcribe-diarize — OpenAI docs](https://developers.openai.com/api/docs/models/gpt-4o-transcribe-diarize)
- [Speaker identification — Speechmatics docs](https://docs.speechmatics.com/speech-to-text/features/speaker-identification)
- [Identification with voiceprints — pyannoteAI docs](https://docs.pyannote.ai/tutorials/identification-with-voiceprints)
- [Azure Speaker Recognition REST API](https://learn.microsoft.com/en-us/rest/api/speakerrecognition/)

**Speaker verification and open-set identification (technical)**

- [wenet-e2e/wespeaker — VoxCeleb recipe results](https://github.com/wenet-e2e/wespeaker/tree/master/examples/voxceleb/v2)
- [WeSpeaker pretrained models](https://github.com/wenet-e2e/wespeaker/blob/master/docs/pretrained.md)
- [modelscope/3D-Speaker](https://github.com/modelscope/3D-Speaker)
- [PalabraAI/redimnet2](https://github.com/PalabraAI/redimnet2)
- [VoxWatch: An open-set speaker recognition benchmark on VoxCeleb — arXiv 2307.00169](https://arxiv.org/pdf/2307.00169)
- [VoxBlink2 and the open-set speaker-identification benchmark — arXiv 2407.11510](https://arxiv.org/html/2407.11510v1)
- [Experiments on Open-Set Speaker Identification with Discriminatively Trained Neural Networks — arXiv 1904.01269](https://arxiv.org/pdf/1904.01269)
- [Trainable Adaptive Score Normalization for Automatic Speaker Verification — arXiv 2504.04512](https://arxiv.org/abs/2504.04512)
- [Cross-Lingual Speaker Verification with Language-Dependent Score Normalization — arXiv 2007.07689](https://arxiv.org/pdf/2007.07689)
- [Disentangling Speaker and Language Effects in Cross-Lingual Speaker Verification for Iberian Languages — arXiv 2607.01161](https://arxiv.org/html/2607.01161v1)
- [Kiwano: an open-source toolkit for speaker verification — arXiv 2606.22369](https://arxiv.org/pdf/2606.22369)
- [ERes2NetV2 — Interspeech 2024](https://www.isca-archive.org/interspeech_2024/chen24l_interspeech.html)
- [Intelligent Voice system for the IberSpeech 2022 Speaker Diarization and Identity Assignment Challenge](https://www.isca-archive.org/iberspeech_2022/shrestha22_iberspeech.pdf)
- [An Overview of the IberSpeech-RTVE 2022 Challenges — Applied Sciences 13(15):8577](https://doi.org/10.3390/app13158577)
- [Albayzin databases — Cátedra RTVE, Universidad de Zaragoza](http://catedrartve.unizar.es/albayzindatabases.html)

**Interpreter overlay, separation, and language identification (press and technical)**

- [El Debate, 20 September 2023 — doblaje al español en la primera sesión plurilingüe](https://www.eldebate.com/espana/20230920/doblaje-solo-espanol-primera-sesion-plurilinguee-congreso-sirve-reafirmar-lengua-comun_140931.html)
- [El Español, 19 September 2023 — pinganillos y traductores autónomos](https://www.elespanol.com/espana/politica/20230919/congreso-compra-pinganillos-ficha-autonomos-traducir-plenospoliglotas/795420837_0.html)
- [Servimedia — subtitulado por accesibilidad](https://www.servimedia.es/noticias/congreso-aclara-transcribe-pantalla-intervenciones-castellano-accesibilidad-para-personas-discapacidad/3970912)
- [BOE-B-2024-12817 — contrato de traducción, interpretación, transcripción y subtitulado](https://www.boe.es/diario_boe/txt.php?id=BOE-B-2024-12817)
- [BOE-B-2026-16554 — sistema de subtitulado automático en directo](https://www.boe.es/diario_boe/txt.php?id=BOE-B-2026-16554)
- [European Parliament Multimedia Centre — webstreaming with selectable interpretation channels](https://www.europarl.europa.eu/website/multimedia-centre/en/webstreaming.html)
- [Slator — Spain allows Basque, Catalan and Galician in congressional sessions](https://slator.com/spain-allows-the-use-of-basque-catalan-and-galician-in-congressional-sessions/)
- [Exploring Spoken Language Identification Strategies for Multilingual Broadcast and Institutional Speech — arXiv 2406.09290](https://arxiv.org/abs/2406.09290)
- [speechbrain/lang-id-voxlingua107-ecapa](https://huggingface.co/speechbrain/lang-id-voxlingua107-ecapa)
- [Target speaker extraction for multi-talker speaker verification — arXiv 1902.02546](https://arxiv.org/pdf/1902.02546)
- [Recursive Attentive Pooling for Extracting Speaker Embeddings from Multi-Speaker Recordings — arXiv 2408.17142](https://arxiv.org/abs/2408.17142)
- [wenet-e2e/wesep — target speaker extraction toolkit](https://github.com/wenet-e2e/wesep)
- [WeSep: a modular and cue-composable framework for target speaker extraction — arXiv 2607.27436](https://arxiv.org/html/2607.27436)
- [USEF-TSE — arXiv 2409.02615](https://arxiv.org/pdf/2409.02615)
- [Listen to Extract: onset-prompted target speaker extraction — arXiv 2505.05114](https://arxiv.org/pdf/2505.05114)
- [TF-GridNet — arXiv 2211.12433](https://arxiv.org/abs/2211.12433)
- [MossFormer2 — arXiv 2312.11825](https://arxiv.org/html/2312.11825v1)

**Multimodal identity and text-side attribution (technical)**

- [PERCOLI at REPERE 2013 — ResearchGate](https://www.researchgate.net/publication/257207780)
- [The REPERE corpus — ResearchGate](https://www.researchgate.net/publication/266504604)
- [TalkNet-ASD](https://github.com/TaoRuijie/TalkNet-ASD)
- [LoCoNet — arXiv 2301.08237](https://arxiv.org/pdf/2301.08237)
- [TalkNCE — arXiv 2309.12306](https://arxiv.org/html/2309.12306)
- [AVA-AVD — arXiv 2111.14448](https://arxiv.org/abs/2111.14448)
- [ArcFace / InsightFace](https://insightface.ai/arcface)
- [Transcription and Recognition of Italian Parliamentary Speeches Using Vision-Language Models — arXiv 2603.28103](https://arxiv.org/abs/2603.28103)
- [DiarizationLM — arXiv 2401.03506](https://arxiv.org/abs/2401.03506)
- [Do We Still Need Audio? Text-based speaker diarization — arXiv 2506.11344](https://arxiv.org/abs/2506.11344)
- [LLM-based speaker diarization correction: a generalizable approach — Speech Communication 2025](https://doi.org/10.1016/j.specom.2025.103224)
- [Televic CoCon API guide](https://manuals.plus/m/e4b32f39ab18a388953d977c4ba39f7919ee60808609d12c8f5469a962fc9c7a)
- [Bosch DICENTIS integration — Extron](https://www.extron.com/article/bosch)

**Streaming and revision metrics (technical)**

- [Thinking Slow about Latency Evaluation for Simultaneous Machine Translation — arXiv 1906.00048](https://arxiv.org/pdf/1906.00048)
- [Meta-evaluation of simultaneous speech translation latency (StreamLAAL) — arXiv 2509.17349](https://arxiv.org/html/2509.17349v2)
- [Shangguan et al., streaming stability metrics — arXiv 2006.01416](https://arxiv.org/abs/2006.01416)
- [Bruguier et al., flickering reduction — IEEE SLT 2023](https://ieeexplore.ieee.org/document/10023016)
- [Modeling and improving text stability in live captions — Google Research](https://research.google/blog/modeling-and-improving-text-stability-in-live-captions/)
- [ufal/SimulStreaming](https://github.com/ufal/SimulStreaming)
- [SimulStreaming — IWSLT 2025](https://aclanthology.org/2025.iwslt-1.41/)
- [Turning Whisper into Real-Time Transcription System — arXiv 2307.14743](https://arxiv.org/abs/2307.14743)
- [Two-pass streaming ASR — arXiv 2004.11544](https://arxiv.org/pdf/2004.11544)
- [Streaming deliberation — arXiv 2212.07650](https://arxiv.org/pdf/2212.07650)
- [How “Real” is Your Real-Time Simultaneous Speech-to-Text Translation System? — TACL 2025](https://direct.mit.edu/tacl/article/doi/10.1162/tacl_a_00740/128861)
- [pyannoteAI Live-1 streaming diarization](https://www.pyannote.ai/blog/introducing-live-1-streaming-diarization)
- [AssemblyAI streaming speaker diarization](https://www.assemblyai.com/blog/streaming-speaker-diarization)
- [NeMo cache-aware streaming FastConformer — Hugging Face blog](https://huggingface.co/blog/nvidia/nemotron-speech-asr-scaling-voice-agents)

**Data and tooling (primary and technical)**

- [Congreso de los Diputados — Intervenciones (open data)](https://www.congreso.es/es/opendata/intervenciones)
- [Congreso de los Diputados — Archivo audiovisual](https://www.congreso.es/es/archivo-audiovisual)
- [Congreso de los Diputados — Videoteca](https://www.congreso.es/es/videoteca)
- [Senado — datos abiertos](https://www.senado.es/web/relacionesciudadanos/datosabiertos/informaciodatosabiertos/index.html)
- [Senado — integración en datos.gob.es, febrero 2026](https://www.senado.es/web/actividadparlamentaria/actualidad/noticias/NoticiasDetalle/index.html?id=2026_02_03_DATOSAABIERTOS)
- [Canal Parlamento — YouTube](https://www.youtube.com/@CanalParlamento-Congreso_Es)
- [Chile — Senado multimedia tools](https://www.senado.cl/contexto-historico/ver-escuchar-y-compartir-conozca-las-herramientas-multimedia-con-que)
- [Chile — Cámara de Diputados open data](https://opendata.camara.cl/)
- [ParlaMint-ES 5.0 — CLARIN.SI](https://www.clarin.si/repository/xmlui/handle/11356/2004)
- [clarin-eric/ParlaMint](https://github.com/clarin-eric/ParlaMint)
- [ParlamentParla — OpenSLR 59](https://www.openslr.org/59/)
- [CollectivaT-dev/ParlamentParla](https://github.com/CollectivaT-dev/ParlamentParla)
- [gttsehu/basque_parliament_1](https://huggingface.co/datasets/gttsehu/basque_parliament_1)
- [HiTZ/basqueparl](https://huggingface.co/datasets/HiTZ/basqueparl)
- [proxectonos/Nos_Parlaspeech-GL](https://huggingface.co/datasets/proxectonos/Nos_Parlaspeech-GL)
- [Nos_TranscriSpeech-GL — Zenodo](https://zenodo.org/records/7717140)
- [SamuelPfisterer/EuroSpeech](https://github.com/SamuelPfisterer/EuroSpeech)
- [facebookresearch/voxpopuli](https://github.com/facebookresearch/voxpopuli)
- [MahmoudAshraf97/ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner)
- [m-bain/whisperX — alignment models](https://github.com/m-bain/whisperX/blob/main/whisperx/alignment.py)
- [LLM-ForcedAligner — arXiv 2601.18220](https://arxiv.org/abs/2601.18220)
- [Montreal Forced Aligner models](https://github.com/MontrealCorpusTools/mfa-models)
- [NeMo Forced Aligner](https://docs.nvidia.com/nemo-framework/user-guide/latest/nemotoolkit/tools/nemo_forced_aligner.html)
- [fgnt/meeteval](https://github.com/fgnt/meeteval)
- [nryant/dscore](https://github.com/nryant/dscore)
- [pyannote/pyannote-metrics](https://github.com/pyannote/pyannote-metrics)
- [Lhotse — VoxPopuli recipe](https://lhotse.readthedocs.io/en/latest/_modules/lhotse/recipes/voxpopuli.html)
- [pyannote/pyannote-audio](https://github.com/pyannote/pyannote-audio)
- [BUTSpeechFIT/DiariZen](https://github.com/BUTSpeechFIT/DiariZen)

**Editorial normalization and text post-processing (technical)**

- [Europarl-ASR — mllpresearch/Europarl-ASR](https://github.com/mllpresearch/Europarl-ASR)
- [Europarl-ASR: A Large Corpus of Parliamentary Debates for Streaming ASR Benchmarking and Speech Data Filtering/Verbatimization — Interspeech 2021](https://www.isca-archive.org/interspeech_2021/garcesdiazmunio21_interspeech.html)
- [EuroSpeech: A Multilingual Speech Corpus — NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/file/58ea63de01321ee52d06b48026981c40-Paper-Datasets_and_Benchmarks_Track.pdf)
- [Finnish parliament ASR corpus: analysis, benchmarks and statistics — PMC](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10040906/)
- [FT Speech: Danish Parliament Speech Corpus — arXiv 2005.12368](https://arxiv.org/abs/2005.12368)
- [Manual Post-editing of Automatically Transcribed Speeches from the Icelandic Parliament — arXiv 1807.11893](https://arxiv.org/pdf/1807.11893)
- [SPC_R: Swiss parliament corpus with GPT-4o correction — arXiv 2506.07726](https://arxiv.org/abs/2506.07726)
- [The Hansard hazard — Mollin 2007](https://www.researchgate.net/publication/250229183)
- [AI-powered verbatim records system (HANS) — IPU](https://www.ipu.org/ai-use-cases/ai-powered-verbatim-records-system-hans)
- [The influence of AI on grammatical correction in Portuguese Parliament plenary session reports — Intersteno Tiro](https://tiro.intersteno.org/2025/12/the-influence-of-ai-on-grammatical-correction-in-portuguese-parliament-plenary-session-reports-first-observations/)
- [NVIDIA/NeMo-text-processing — inverse text normalization grammars](https://github.com/NVIDIA/NeMo-text-processing)
- [xashru/punctuation-restoration — W-NUT 2020](https://github.com/xashru/punctuation-restoration)
- [Softcatala/nmt-models](https://github.com/Softcatala/nmt-models)
- [BSC-LT/salamandraTA-2b-instruct](https://huggingface.co/BSC-LT/salamandraTA-2b-instruct)
- [BSC-LT/whisper-large-v3-LoS-punctuated](https://huggingface.co/BSC-LT/whisper-large-v3-LoS-punctuated)
- [SenseVoice — audio event detection](https://github.com/QwenAudio/SenseVoice)
- [PANNs — arXiv 1912.10211](https://arxiv.org/abs/1912.10211)
- [What is lost in normalization? — arXiv 2409.02449](https://arxiv.org/html/2409.02449v4)
- [HTER — Specia and Farzindar 2010](https://aclanthology.org/2010.jec-1.5/)

**Fact-check sources (primary and press)**

- [Hailer, Decoding discourse: gendered heckling in German Bundestag debates — ScienceDirect](https://www.sciencedirect.com/science/article/pii/S0176268026000091)
- [Short utterance compensation in speaker verification — arXiv 1810.10884](https://arxiv.org/pdf/1810.10884)
- [pyannote-audio issue #1963 — VRAM regression in 4.0.3](https://github.com/pyannote/pyannote-audio/issues/1963)
- [Whisper v3-Turbo on H100: 597× realtime — InferenceBench](https://inferencebench.io/blog/whisper-large-v3-turbo-597x-realtime-asr-benchmark/)
- [AssemblyAI benchmarks — cpWER](https://www.assemblyai.com/benchmarks)
- [Artificial Analysis — AA-WER speech-to-text leaderboard](https://artificialanalysis.ai/speech-to-text)
- [Pangeanic will implement AI transcription technology in the Spanish Parliament](https://blog.pangeanic.com/pangeanic-will-implement-ai-transcription-technology-in-spanish-parliament)
- [Why Spain has allowed regional languages to be spoken in Congress — The Local](https://www.thelocal.es/20230919/why-spain-has-allowed-regional-languages-to-be-spoken-in-congress)

<!-- This document follows common-doc-guidelines.md.
See github.com/jlevy/practical-prose and review guidelines before editing.
-->
