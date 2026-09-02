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
correlated. Compare systems **paired by session**; report median and range across
sessions; bootstrap over sessions for intervals.
With 20–50 sessions the interval on TAA will be a few points wide; an improvement inside
it is “no detectable effect.”
This is the accept rule in §9, written before anything is measured.

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

**[Primary/Technical]** Grouped by stage:

| Stage | Tool | Note |
| --- | --- | --- |
| Acquisition | `yt-dlp`; Archivo Audiovisual MP4; Congreso open-data JSON; `ffprobe` | check audio track layout |
| Record parsing | regex over Diario HTML speaker labels; stage-direction extraction | conventions in D5 |
| Alignment | EuroSpeech `alignment_pipeline`; `ctc-forced-aligner` (MMS); WhisperX aligners for es/ca/eu/gl | long-audio, non-verbatim tolerant |
| ASR | `BSC-LT/whisper-large-v3-LoS-punctuated`; `whisper-large-v3-turbo` via faster-whisper; Granite Speech 4.1 or Parakeet TDT for throughput; `HiTZ` and `proxectonos` specialists for eu/gl | Parlamento.ai’s throughput table is the sizing reference |
| Streaming | SimulStreaming (LocalAgreement successor); NeMo cache-aware FastConformer | computation-aware latency |
| Diarization | `pyannote` community-1; DiariZen (non-commercial weights); Streaming Sortformer for online segmentation | windowed per turn |
| Speaker embeddings | ReDimNet2 (MIT); WeSpeaker; 3D-Speaker; SpeechBrain | cosine + AS-norm + calibration |
| Target-speaker extraction | WeSep; USEF-TSE | for the interpretation-mixed tail |
| LID | SpeechBrain VoxLingua107 ECAPA | frame or segment level |
| Joint SA-ASR | TS-ASR-Whisper / DiCoW, SE-DiCoW; MOSS Transcribe Diarize | R8 |
| Text-side | DiarizationLM-style corrector; a chair-announcement parser (to be written) | R3 |
| Multimodal | TalkNet / LoCoNet; InsightFace; PaddleOCR or equivalent for chyrons | R10 |
| Text post-processing | NeMo text-processing ITN grammars (es); BERT-family punctuation restoration; `whisper-large-v3-LoS-punctuated` for punctuation in-model | R6 |
| Translation for the record | Softcatalà NMT (ca–es); SalamandraTA (es, ca, eu, gl); Apertium as baseline | co-official interventions need a Spanish translation in the record |
| Audio events | SenseVoice; PANNs, BEATs, or AST | R9 |
| Scoring | MeetEval; dscore; pyannote.metrics; a per-turn TAA/coverage scorer (to be written) | one harness, versioned |
| Campaign record | the repository’s `experiment-loop` skill: registry, ledger, accept rule | §9 |

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
  accept when the paired median improvement in TAA@95% across sessions exceeds the
  bootstrap 95% interval’s half-width and no guard is breached.

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

## Recommendations

**[Analysis]**

1. **Build R1 before anything else.** Twenty sessions with both reference layers and the
   harness in §4.6 is enough to start; publish the manifests.
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

## Next Steps

- [ ] Download five Congreso sessions with co-official-language interventions and
  inspect the MP4 audio layout with `ffprobe`; settle Q-003.
- [ ] Write the Diario parser and the chair-announcement parser; measure announcement
  coverage on those sessions (input to H-003).
- [ ] Stand up the scoring harness (MeetEval plus the per-turn TAA/coverage scorer);
  version it.
- [ ] Build the R1 evaluation set (20 sessions) and enrollment set; run H-001.
- [ ] Run H-002 and H-003; publish the stratified tables.
- [ ] Open a campaign directory with the experiment-loop skill: idea board, registry
  seeded from §9, accept rule as written.
- [ ] Re-verify the search-summary figures flagged in §2.1 against their primary texts
  when network access allows.

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
