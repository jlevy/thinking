---
title: Tooling Inventory for Real-Time Parliamentary Transcription with Named Speaker Attribution
description: Every tool, dataset and service surveyed for the Spanish Congreso and Senado transcription programme (es, ca, eu, gl, plus en and pt), with maturity and access labels made from same-day GitHub signals, a shortlist by pipeline stage, and an index from research direction to toolchain
author: Claude Code
---
# Research: Tooling Inventory for Real-Time Parliamentary Transcription with Named Speaker Attribution

**Date:** 2026-09-02 (last updated 2026-09-02)

**Author:** Claude Code

**Status:** Complete

## Overview

This report is the companion to §6 of the
[research agenda](research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md)
on named speaker attribution for the Spanish Congreso and Senado.
The agenda’s §6 carries the shortlist and the index from research direction to
toolchain; this report carries the full inventory behind them: 260-odd tool rows, 35
datasets and 23 services, each with links, licence, language coverage, key numbers,
recorded signals and a label.

It was built in three passes on 2026-09-02. Four inventory briefs (general and
specialist ASR; diarization, embeddings and speaker identification; joint
speaker-attributed ASR, text-side attribution and multimodal identity; datasets and
services) were consolidated into one table set with uniform columns and provenance tags.
A critical review then checked the labels against the label definitions and the
repository evidence, corrected factual errors, listed gaps, and named the fourteen items
to build on first and the items to avoid.
This report applies every correction from that review and reorganizes the rows under the
agenda’s eight sub-problems (§3.1 there) plus alignment, scoring, data, annotation,
serving, multimodal identity and conference systems.

Three limits apply throughout.
GitHub signals (stars, licence field, last push, archived flag) come from one sweep of
163 repositories on 2026-09-02, so a repository’s state after that date is not
reflected. Hugging Face pages could not be opened from any session, so every `HF:` id is
given as its authors published it and is unverified, including the licence on every
Hugging Face-only model.
Labels are judgments made from the printed signals, not from running anything; the
signals sit beside each label so the judgment can be re-made.

### A note on evidential status

Numbers keep the provenance tags of the briefs: **(gh)** read from the GitHub API in the
same session, **(readme)** read from a raw README or source file, **(search)** taken
from a web-search snippet whose page was not opened, **(brief)** the brief’s own signal
text where the repository is absent from the sweep, **(review)** read by the review from
a raw file or the API but not recorded in the sweep file.
A **(search)** figure is never upgraded here.
**[Proposed]** marks a design put forward by the agenda and not yet tested.

## 1. Labels and Scales

**[Proposed]** Every tool row carries one of five labels.
The label is a judgment, but it is made from recorded signals (stars, licence, last
push, release cadence, whether weights are downloadable, and whether the thing has been
reproduced or deployed by someone other than its authors), and the signals are printed
next to it so the judgment can be checked.
GitHub signals were collected in one sweep on 2026-09-02 with the GitHub API; Hugging
Face model pages could not be opened from this session, so model ids are given as
published by their authors and marked `HF:` where they were not opened.

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

## 2. Shortlist by Stage

**[Proposed]** One row per item of the build-on-first list, in stage order.
“Verified how” names the strongest source read for that row; “Serves” lists the research
directions of §7 the row feeds.

| Stage | Tool | Licence class | ca/eu/gl coverage | Label | Verified how | Serves |
| --- | --- | --- | --- | --- | --- | --- |
| 1 Acquisition and record parsing | Congreso `intervenciones` JSON, Diario de Sesiones HTML, Archivo Audiovisual MP4 | open data (official terms) | co-official interventions appear twice in the Diario, original and translation | n/a, official source (access: open) | portal pages (search); OpenParliamentTV ES README (raw) notes that congreso.es returns 403 to bare requests | R1, R3, R6, R11 |
| 1 Acquisition and record parsing | [OpenParliamentTV-Tools](https://github.com/OpenParliamentTV/OpenParliamentTV-Tools), `ES` implementation: merges the `intervenciones` JSON with the Diario HTML by Needleman-Wunsch on surname sequence, aeneas sentence alignment, Wikidata linking over 3,830 MPs | copyleft (GPL-3.0) | n/a; ~95% match on substantive speeches, role-only chair turns unmatched (readme) | Research | raw `optv/parliaments/ES/README.md`; not in the same-day signal sweep | R1, R3 |
| 1 Acquisition and record parsing | [yt-dlp](https://github.com/yt-dlp/yt-dlp); [FFmpeg](https://github.com/FFmpeg/FFmpeg) | permissive (Unlicense); LGPL or GPL by build | n/a | Production | repo-meta: 188,537★ pushed 2026-08-30; 63,888★ pushed 2026-09-02 | R1, R7 |
| 2 ASR | `whisper-large-v3-turbo` on [faster-whisper](https://github.com/SYSTRAN/faster-whisper) + [CTranslate2](https://github.com/OpenNMT/CTranslate2) | permissive (MIT) | nominal for all four via Whisper’s language set; beat the Galician specialist in the Parlamento-ai sample, 14.71% vs 20.37% (readme) | Production | repo-meta; local `open-source-asr/README.md` | R1, R6, R7 |
| 2 ASR | `HF: BSC-LT/whisper-large-v3-LoS-punctuated` | not verified (card not opened) | es, ca, eu, gl in one model, 8,110 h balanced (search) | Mature research | search only; no published WER table found | R1, R6, R8 |
| 2 ASR | [facebookresearch/omnilingual-asr](https://github.com/facebookresearch/omnilingual-asr), the permissive fallback | permissive (Apache-2.0 code and models, README §License) | `spa_Latn`, `cat_Latn`, `eus_Latn`, `glg_Latn` in `lang_ids.py`; 7B-LLM-ASR CER 1.3 / 1.4 / 0.8 / 1.4 on Meta’s own sets (review, raw) | Research | README and `lang_ids.py` read raw by the review; 2,911★ pushed 2025-12-30 (review API check, not in repo-meta) | R1, R5 |
| 3 Diarization | [pyannote.audio 4.x](https://github.com/pyannote/pyannote-audio) + `HF: pyannote/speaker-diarization-community-1` | permissive code (MIT); weights CC BY 4.0, gated | language-agnostic | Production | local README: multi-corpus DER table, 31 s per audio hour; repo-meta 10,500★ pushed 2026-09-02 | R1, R7, R8 |
| 3 Diarization | [DiariZen](https://github.com/BUTSpeechFIT/DiariZen), run head to head with pyannote and used where it wins | permissive code; weights CC BY-NC 4.0 (usable here; pyannote masks are the swap for a product) | language-agnostic | Mature research | local README DER table; the WavLM-based system that tops several challenge sets | R1, R8 |
| 4 Embeddings | [WeSpeaker](https://github.com/wenet-e2e/wespeaker): extractors, AS-norm, QMF, calibration and DET scripts | permissive (Apache-2.0; weights follow VoxCeleb CC BY 4.0) | no Iberian evaluation | Production | `examples/voxceleb/v2/README.md` (raw); `wespeaker/bin` files (gh) | R1, R2, R4, R5 |
| 4 Embeddings | [ReDimNet2](https://github.com/PalabraAI/redimnet2)-B6 `vb2+vox2` LM checkpoint as the extractor | permissive (MIT) | no Iberian evaluation | Mature research | local README: 0.29 EER Vox2-only, 0.23 with the LM checkpoint; repo-meta 85★ pushed 2026-08-28 | R1, R2, R4 |
| 5 Scoring | [pyannote.metrics](https://github.com/pyannote/pyannote-metrics) `IdentificationErrorRate`; [MeetEval](https://github.com/fgnt/meeteval) cpWER and tcpWER; [dscore](https://github.com/nryant/dscore) DER | permissive (MIT; MIT; BSD-2) | n/a | Production | gh tree (`identification.py` present); local MeetEval README; repo-meta | every R |
| 5 Scoring | [PYLLR](https://github.com/bsxfan/PYLLR) and [expected_cost](https://github.com/luferrer/expected_cost) for Cllr; [net:cal](https://github.com/EFS-OpenSource/calibration-framework) for ECE | permissive (MIT; MIT; Apache-2.0) | n/a | Legacy; Legacy; Production | repo-meta: pushed 2023-02-21, 2025-06-11, 2026-04-16 | R2, R3, R4 |
| 6 Alignment | torchaudio [`MMS_FA`](https://github.com/pytorch/audio) or [ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner) | permissive API (BSD-2; BSD per README); default MMS weights CC BY-NC 4.0 | MMS covers es, ca, eu, gl | Production; Mature research | README §License (raw); `LICENSE` returns 404 at `main`; repo-meta | R1 |
| 6 Alignment | `HF: Qwen/Qwen3-ForcedAligner-0.6B`, or NeMo NFA with `HF: HiTZ/stt_eu_conformer_ctc_large`, for anything shipped | permissive (Apache-2.0; Apache-2.0 toolkit, HiTZ card not opened) | 11 languages claimed, list not confirmed; eu only on the NFA path | Research; Production (NFA) | search; repo-meta for the parent repos | R1, R6, R7 |
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

## 3. Full Inventory by Stage

Rows are grouped under the eight sub-problems of the agenda’s §3.1, then alignment,
scoring, data, multimodal identity and conference systems.
Columns are the consolidated inventory’s: Links are ordered GitHub, `HF:` id, paper,
docs or service; Signals are `stars · pushed date` from the 2026-09-02 sweep where the
repository was in it; Key numbers keep their provenance tags.

### 3.1 Voice activity, segmentation and hallucination suppression

Sub-problem 1 of §3.1 in the agenda: detecting speech and stopping Whisper from emitting
text over silence.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Silero VAD** | Small neural VAD, PyTorch and ONNX | [gh snakers4/silero-vad](https://github.com/snakers4/silero-vad) | MIT (repo-meta) | language-agnostic (6,000+ languages claimed) | <1 ms per 30 ms chunk on one CPU thread; ONNX 4–5× faster; ROC-AUC 0.96 multi-domain, 0.96 AliMeeting, 0.94 VoxConverse, 0.79 MSDWild; ~1.7 MB; v6.2.1 released 2026-02-24 (search/gh) | 10,109★ · pushed 2026-08-24 | Production |
| **TEN VAD** | Low-latency VAD for conversational agents | [gh TEN-framework/ten-vad](https://github.com/TEN-framework/ten-vad) · `HF: TEN-framework/ten-vad` · [PyPI ten-vad](https://pypi.org/project/ten-vad/) | NOASSERTION (repo-meta); verify | language-agnostic | RTF 0.015, 306 KB library; vendor PR curves beat Silero and WebRTC and claim Silero lags several hundred ms on speech-to-non-speech (search; vendor-authored comparison) | 2,256★ · pushed 2026-02-02 | Mature research |
| **WebRTC VAD (py-webrtcvad)** | GMM VAD from the WebRTC stack | [gh wiseman/py-webrtcvad](https://github.com/wiseman/py-webrtcvad) | NOASSERTION (repo-meta) | language-agnostic | fastest and least accurate of the three; the baseline everyone cites (search) | 2,498★ · pushed 2024-07-04 | Legacy |
| **NeMo MarbleNet frame-VAD** | 91.5K-parameter CNN VAD at 20 ms frames, noise-augmented | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/Frame_VAD_Multilingual_MarbleNet_v2.0` · [NGC model](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/vad_multilingual_marblenet) | Apache-2.0 toolkit (repo-meta) | multilingual | 91.5K params; log-mel `n_mels=80` shared with the ASR front end; NeMo, PyTorch and ONNX inference (search) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Production |
| **Calm-Whisper** | Fine-tunes the three decoder attention heads behind most non-speech hallucination | [arXiv 2505.12969](https://arxiv.org/abs/2505.12969) · [Interspeech 2025 PDF](https://www.isca-archive.org/interspeech_2025/wang25b_interspeech.pdf) | paper; code availability not verified (brief) | Whisper large-v3 | 3 of 20 decoder heads drive >75% of hallucinations on UrbanSound; >80% reduction in non-speech hallucination at <0.1% WER cost on LibriSpeech test-clean/other (search) | peer-reviewed; no verified public repo (brief) | Research |
| **SAE hallucination steering** | Sparse-autoencoder steering of Whisper encoder activations | [arXiv 2606.07473](https://arxiv.org/abs/2606.07473) | paper (brief) | Whisper small and large-v3 | hallucination rate 72.63% → 14.11% (small) and 86.88% → 27.33% (large-v3) on a non-speech test set (search) | 2026 preprint (brief) | Research |
| **Whisper hallucination detection from internals** | Studies avg-logprob, compression ratio and no-speech-prob as hallucination detectors | [arXiv 2606.23060](https://arxiv.org/pdf/2606.23060) · `HF: avcton/whisper-hallucination-detection` | paper and HF model (brief) | Whisper family | hallucinations often carry high avg_logprob and low no_speech_prob, so naive threshold filters fail where needed (search) | 2026 preprint; the HF classifier is a small TF model over word-timestamp confidences (brief) | Research |

pyannote segmentation-3.0 (7-class powerset segmentation with overlap detection) ships
inside pyannote.audio, §3.4. No project named “Whisper-CD” could be confirmed by any
brief.

### 3.2 Language identification

Sub-problem 2: deciding which of es, ca, eu or gl is being spoken, ideally inside a
turn. Every shipped model is utterance-level.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **speechbrain/lang-id-voxlingua107-ecapa** | ECAPA LID over VoxLingua107 | [gh speechbrain/speechbrain](https://github.com/speechbrain/speechbrain) · `HF: speechbrain/lang-id-voxlingua107-ecapa` | Apache-2.0 (repo-meta) | ca, eu and gl are all in the 107 (search) | 93.3% accuracy (6.7% error) on VoxLingua107 dev; trained on 6,628 h / 107 languages (search) | 11,802★ · pushed 2026-08-27 (speechbrain/speechbrain) | Production |
| **NeMo `langid_ambernet`** | Compact 1D depthwise-separable + SE LID model | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · [NGC langid_ambernet](https://catalog.ngc.nvidia.com/orgs/nvidia/nemo/models/langid_ambernet) · [arXiv 2210.15781](https://arxiv.org/abs/2210.15781) | Apache-2.0 toolkit (repo-meta) | trained on VoxLingua107, so ca/eu/gl are in-vocabulary (search) | 5.22% error on the VoxLingua107 official eval set (1,609 utterances, 33 languages); 75.8% on FLEURS; ~10× smaller than comparable models (search) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Production |
| **facebook/mms-lid-{126,256,512,1024,2048,4017}** | Wav2Vec2 1B LID heads at six vocabulary sizes | `HF: facebook/mms-lid-4017`, `HF: facebook/mms-lid-256` · [transformers MMS docs](https://huggingface.co/docs/transformers/en/model_doc/mms) | CC BY-NC 4.0, non-commercial (search) | very wide; ca/eu/gl within 256+ | 1B params; no per-language accuracy table on the cards (search) | HF-only; the fairseq host repo is archived (§3.3) (brief) | Mature research |
| **Whisper LID** | The `<\|lang\|>` token from the encoder-decoder, free with ASR | [gh openai/whisper](https://github.com/openai/whisper) · [paper PDF](https://arxiv.org/pdf/2212.04356) | MIT (repo-meta) | ca, es, gl present; eu weaker; confusions cluster among close relatives (search) | 80.3% on the 82 FLEURS languages Whisper covers; one eval reports large-v3 at 94.6% on a 30-language FLEURS subset (search) | 108,319★ · pushed 2026-08-31 (openai/whisper) | Production |
| **speechbrain/lang-id-commonlanguage_ecapa** | ECAPA LID on CommonLanguage, 45 languages | `HF: speechbrain/lang-id-commonlanguage_ecapa` | Apache-2.0 (repo-meta, speechbrain) | Basque and Catalan listed; Galician absent (search) | ~85% claimed; 82.29% reproduced by a user ([issue #874](https://github.com/speechbrain/speechbrain/issues/874)) (search) | 11,802★ · pushed 2026-08-27 (speechbrain/speechbrain) | Research |
| **3D-Speaker LID recipe** | LID training recipe with phonetic information | [gh 3D-Speaker language-identification egs](https://github.com/modelscope/3D-Speaker/tree/main/egs/3dspeaker/language-identification) | Apache-2.0 (repo-meta) | the 3D-Speaker corpus is Mandarin-dialect focused | vendor reports accuracy gains from phonetic fusion (readme) | 3,128★ · pushed 2025-12-08 (modelscope/3D-Speaker) | Research |
| **Lhx94As/Awesome-Spoken-Language-Identification** | Curated index including frame-level and code-switch LID | [gh Lhx94As/Awesome-Spoken-Language-Identification](https://github.com/Lhx94As/Awesome-Spoken-Language-Identification) | no licence (gh) | index only | none | not in the same-day sweep (brief) | Research |

SenseVoice also emits a language id but covers only zh, yue, en, ja and ko; it is
carried once, in §3.7. FLEURS Spanish is `es_419`, so every FLEURS-es figure in these
tables is already a Latin American number.
Frame-level and code-switch LID remains a literature, not a product: nothing published
gives per-language accuracy for ca, eu or gl.

### 3.3 Transcription per language, with code-switching

Sub-problem 3, in four tables: general multilingual models, Spanish and co-official
specialists, the runtimes that serve them, and the separation tools for the
interpretation-mixed tail.

#### General multilingual ASR

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **OpenAI Whisper large-v3** | Encoder-decoder weakly supervised ASR and translation; the reference open model | [gh openai/whisper](https://github.com/openai/whisper) · `HF: openai/whisper-large-v3` · [arXiv 2212.04356](https://arxiv.org/abs/2212.04356) | MIT (repo-meta) | 99 languages; es/ca/eu/gl/pt/en all nominally covered (search) | FLEURS WER from the Whisper-LM paper: ca 5.68, gl 10.06, es 15.01 (search, [arXiv 2503.23542](https://arxiv.org/pdf/2503.23542); the es figure is flagged implausible and unverified in the agenda §2.1); 1.54B params | 108,319★ · pushed 2026-08-31 | Production |
| **Whisper large-v3-turbo** | large-v3 encoder with the decoder pruned 32→4 layers, 809M params | [gh openai/whisper](https://github.com/openai/whisper) · `HF: openai/whisper-large-v3-turbo` | MIT (repo-meta) | same 99 languages (search) | 2–5× faster than large-v3 with minor accuracy loss; best overall agreement with paid APIs in the Parlamento-ai study for es/ca/gl/en/pt, and beat the Galician specialist (14.71% vs 20.37% word difference) (readme) | 108,319★ · pushed 2026-08-31 (same repo) | Production |
| **Distil-Whisper (distil-large-v3.5)** | Distilled Whisper, 98k h training | [gh huggingface/distil-whisper](https://github.com/huggingface/distil-whisper) · `HF: distil-whisper/distil-large-v3.5` · [arXiv 2311.00430](https://arxiv.org/abs/2311.00430) | MIT (repo-meta) | English only (search) | ~1.5× faster than large-v3-turbo; ~6.3× faster than large-v3 (search) | 4,114★ · pushed 2025-01-08 | Legacy |
| **CrisperWhisper** | Whisper fine-tune with verbatim retokenized output and DTW word timestamps | [gh nyrahealth/CrisperWhisper](https://github.com/nyrahealth/CrisperWhisper) · [arXiv 2408.16589](https://arxiv.org/abs/2408.16589) | NOASSERTION (repo-meta); read the LICENSE file before commercial use | Whisper’s set; evaluated in English (search) | ~30 ms mean word-boundary error on read speech, ~41 ms conversational; best published verbatim results on AMI and TED-LIUM (search) | 1,371★ · pushed 2026-08-23 | Mature research |
| **NVIDIA Canary-1B-v2** | FastConformer encoder + Transformer decoder; ASR and translation | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/canary-1b-v2` · [arXiv 2509.14128](https://arxiv.org/abs/2509.14128) | CC BY 4.0 weights (search); NeMo toolkit Apache-2.0 (repo-meta) | 25 European languages: es, pt, en yes; ca/eu/gl no | avg 8.1% WER across 25 FLEURS languages; up to 10× faster than larger models (search); NeMo v3.0.0 released 2026-08-07 (gh) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Production |
| **NVIDIA Canary-Qwen-2.5B** | FastConformer encoder + Qwen3-1.7B decoder; ASR mode plus LLM mode | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/canary-qwen-2.5b` | CC BY 4.0 weights (search) | English only (search) | 5.63% WER, RTFx 418, Open ASR Leaderboard first at its July 2025 release (search); trained on 234k h | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Mature research |
| **NVIDIA Parakeet TDT 0.6B v3** | Token-and-Duration Transducer, offline batch ASR | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/parakeet-tdt-0.6b-v3` · [arXiv 2509.14128](https://arxiv.org/abs/2509.14128) | CC BY 4.0 weights (search) | 25 European languages: es, pt, en yes; ca/eu/gl no (search) | 86.8 h of audio per wall-clock hour, 1,294 MB VRAM; 7.18% word difference vs paid APIs on 72 parliamentary audios (readme) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Production |
| **NVIDIA Nemotron 3.5 ASR streaming 0.6B** | Cache-aware streaming FastConformer with native punctuation and capitalization | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/nemotron-3.5-asr-streaming-0.6b` | open weights; licence not verified, HF page not opened (brief) | 40 locales in 3 tiers; es-ES, es-US, pt-BR, pt-PT, en yes; ca/eu/gl no (search) | FLEURS es 4.11% WER, pt 5.48%, 8.84% avg over 19 locales; 0.56 s streaming latency; chunks 80/160/320/560/1120 ms (search); 28.4 h/h, 1,382 MB VRAM, 9.71% word difference vs paid APIs (readme) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Research (until the card and licence are read) |
| **IBM Granite Speech 4.1 2B / 4.1 2B-NAR / 3.3 8B** | Conformer encoder + Q-Former projector + Granite LLM decoder | [gh ibm-granite/granite-speech](https://github.com/ibm-granite/granite-speech) · `HF: ibm-granite/granite-speech-4.1-2b`, `-4.1-2b-nar`, `-3.3-8b` | Apache-2.0 (repo-meta and search) | en, fr, de, es, pt (+ja in 4.1) (search); ca/eu/gl no | 4.1-2b: 5.33 mean WER on the Open ASR Leaderboard; 4.1-2b-NAR RTFx ~1820 on one H100 (search); 133.4 h/h, fastest general model in the Parlamento-ai run (readme) | 3★ · pushed 2026-07-20 | Mature research |
| **Qwen3-ASR 1.7B / 0.6B** | Open ASR with LID, timestamps, unified streaming and offline decoding | [gh QwenLM/Qwen3-ASR](https://github.com/QwenLM/Qwen3-ASR) · `HF: Qwen/Qwen3-ASR-1.7B`, `Qwen/Qwen3-ASR-0.6B` (+`-hf`) · [arXiv 2601.21337](https://arxiv.org/html/2601.21337v1) | Apache-2.0 (repo-meta) | 52 languages and accents for LID; card lists zh, en, yue, fr, de, it, ja, ko, pt, ru, es (search); ca/eu/gl no | best open model on the multilingual parliamentary sample: 4.45% word difference vs paid APIs; 4.9 h/h, 4,108 MB VRAM (readme) | 3,460★ · pushed 2026-06-26 | Production |
| **Mistral Voxtral Mini 4B Realtime 2602** | Causal 970M audio encoder + 3.4B LM, sliding-window attention, unbounded streaming; no diarization | `HF: mistralai/Voxtral-Mini-4B-Realtime-2602` · [mistral.ai/news/voxtral-transcribe-2](https://mistral.ai/news/voxtral-transcribe-2) | Apache-2.0 (search) | 13 languages; es/pt/en very likely, ca/eu/gl not claimed (search) | delay configurable in 80 ms steps from 80 to 1200 ms plus 2400 ms; 480 ms recommended; Transcribe 2 5.9% avg FLEURS WER vs Whisper 7.4% (search) | weights open; vLLM Realtime API support; MLX, GGUF and ExecuTorch ports (search) (brief) | Production |
| **Mistral Voxtral Mini Transcribe V2** | The batch model of the Transcribe 2 line, which carries Mistral’s diarization (the Realtime model does not); Dixtral uses it as a diarizing baseline | [mistral.ai/news/voxtral-transcribe-2](https://mistral.ai/news/voxtral-transcribe-2) | not confirmed; open-weight status of the batch checkpoint unverified (review) | as the Transcribe 2 line | beaten by Dixtral by 16.0 absolute cpWER on its four-corpus comparison (search) | search only (review) | Preview |
| **Mistral Voxtral Mini 3B / Small 24B** | Audio-chat LLMs with a transcription mode | `HF: mistralai/Voxtral-Mini-3B-2507`, `-Small-24B-2507` · [arXiv 2507.13264](https://arxiv.org/html/2507.13264v1) | Apache-2.0 (search) | multilingual incl. es/pt/en (search) | see paper; superseded for pure ASR by Transcribe 2 | weights open (search) (brief) | Mature research |
| **Kyutai STT** | Delayed Streams Modeling; streaming-native, batchable, word timestamps, semantic VAD | [gh kyutai-labs/delayed-streams-modeling](https://github.com/kyutai-labs/delayed-streams-modeling) · `HF: kyutai/stt-1b-en_fr`, `kyutai/stt-2.6b-en` | code Apache-2.0 (repo-meta); weights CC BY 4.0 (search) | en and fr only (search) | stt-1b-en_fr 0.5 s delay; stt-2.6b-en 2.5 s delay (search) | 3,020★ · pushed 2026-01-26 | Mature research |
| **Meta MMS (mms-1b-all) / wav2vec2-XLS-R** | wav2vec2 with per-language adapters, 1,162 fine-tuned languages | [gh facebookresearch/fairseq](https://github.com/facebookresearch/fairseq) · `HF: facebook/mms-1b-all`, `facebook/wav2vec2-xls-r-1b` · [arXiv 2305.13516](https://arxiv.org/abs/2305.13516) | code MIT (repo-meta); weights CC BY-NC 4.0, non-commercial (search) | 1,107 languages in transformers, incl. es/ca/eu/gl/pt/en (search) | pretrained on ~500k h across 1,400+ languages (search) | 32,230★ · pushed 2025-09-30 · archived | Legacy |
| **Meta Omnilingual ASR** | Successor to MMS with permissive code and models; 7B LLM-ASR variant | [gh facebookresearch/omnilingual-asr](https://github.com/facebookresearch/omnilingual-asr) | Apache-2.0 for code and models (README §License, review raw read) | `spa_Latn`, `cat_Latn`, `eus_Latn`, `glg_Latn` present in `lang_ids.py` (review) | 7B-LLM-ASR CSV: CER 1.3 / 1.4 / 0.8 / 1.4 for es/ca/eu/gl on Meta’s own test sets (review) | 2,911★ · pushed 2025-12-30 (review API check; not in repo-meta) | Research |
| **ESPnet OWSM v3.1 / v4** | Fully open Whisper-style reproduction (E-Branchformer, plus CTC variants) | [gh espnet/espnet](https://github.com/espnet/espnet) · `HF: espnet/owsm_v4_medium_1B`, `espnet/owsm_ctc_v4_1B`, `espnet/owsm_v3.1_ebf` · [arXiv 2401.16658](https://arxiv.org/pdf/2401.16658) | Apache-2.0 toolkit (repo-meta); a low-restriction-data variant is published (search) | multilingual ASR, AST and LID; per-language quality for ca/eu/gl unverified | OWSM-CTC v4: 3 epochs on 320k h of public audio; v3.1 up to 25% faster than v3 (search); training logs published | 9,949★ · pushed 2026-09-02 | Mature research |
| **Moonshine / Moonshine v2** | Edge-first streaming encoder ASR (tiny, small, medium) | [gh moonshine-ai/moonshine](https://github.com/moonshine-ai/moonshine) · [gh moonshine-ai/moonshine-v2](https://github.com/moonshine-ai/moonshine-v2) · [arXiv 2602.12241](https://arxiv.org/abs/2602.12241), [2410.15608](https://arxiv.org/abs/2410.15608), [2509.02523](https://arxiv.org/pdf/2509.02523) | NOASSERTION on the main repo, no licence field on v2 (repo-meta); English models MIT, non-English models incl. Spanish under the Moonshine Community Licence, non-commercial below USD 1M revenue (search) | en, es, ar, ja, ko, zh, uk, vi (search); ca/eu/gl no | v2 latency: Tiny 50 ms, Small 148 ms, Medium 258 ms (43.7× faster than Whisper large-v3) (search) | 10,994★ · pushed 2026-08-31 (moonshine) · 45★ · pushed 2026-02-10 (moonshine-v2) | Mature research |
| **ARK-ASR-3B** | Whisper-style encoder + MLP adapter + Qwen decoder | `HF: Audio8/ARK-ASR-3B` (also `AutoArk-AI/ARK-ASR-3B`) | Apache-2.0 (search) | 19 languages incl. es; pt not confirmed; ca/eu/gl no (search) | 5.04% avg WER, RTFx 490.98 on English short-form; separately reported at 4.76 atop the Open ASR Leaderboard (search; two sources disagree) | weights open; sglang-omni support requested (search) (brief) | Research |
| **MOSS-Transcribe-preview-2B** | 2026 open ASR preview checkpoint (the diarizing sibling is in §3.5) | `HF: OpenMOSS-Team/MOSS-Transcribe-preview-2B` | not verified (brief) | not verified (brief) | 4.87 avg WER on the Open ASR Leaderboard (search only) | search result only; “preview” in the name (brief) | Preview |

SenseVoice (ASR + LID + emotion + audio events, no Iberian coverage) is carried once, in
§3.7.

#### Spanish and co-official specialists, including code-switching

Almost all are Hugging Face-only: no GitHub repository exists to check, so signals are
the briefs’ own.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **BSC-LT/whisper-large-v3-LoS** and **-LoS-punctuated** | whisper-large-v3 fine-tuned on 8,110 h across the four languages of Spain, hours balanced per language; punctuated variant | `HF: BSC-LT/whisper-large-v3-LoS`, `HF: BSC-LT/whisper-large-v3-LoS-punctuated` | not verified; HF card not opened (brief) | es, ca, gl, eu in one model, unique in this inventory | 8,110 h preprocessed; Basque speed-perturbed 0.9×/1.1× to 2,027 h as the balancing reference; trained Nov 2025 at BSC (search); the card shows WER eval code but no published WER table was found (search) | institutional (BSC); HF-only (brief) | Mature research |
| **BSC-LT Catalan line**: `whisper-large-v3-ca-punctuated-3370h`, `faster-whisper-large-v3-ca-punctuated-3370h`, `whisper-bsc-large-v3-cat`, `faster-whisper-bsc-large-v3-cat` | Catalan Whisper fine-tunes with punctuation and capitalization, plus CTranslate2 conversions | `HF: BSC-LT/whisper-large-v3-ca-punctuated-3370h`, `HF: BSC-LT/faster-whisper-large-v3-ca-punctuated-3370h` | per model card, not opened (brief) | ca | 3,370 h Catalan (search); composition given elsewhere as 2,659 h Common Voice 17.0 + 710 h Aina; fine-tuned April 2025, funded by the Generalitat de Catalunya (search) | faster-whisper variants shipped; HF-only (brief) | Mature research |
| **BSC-LT dialectal Catalan**: `faster-whisper-3cat-balearic`, `faster-whisper-3cat-cv21-valencian` | Balearic and Valencian accent adaptations | `HF: BSC-LT/faster-whisper-3cat-balearic`, `HF: BSC-LT/faster-whisper-3cat-cv21-valencian` | not verified (brief) | ca (Balearic, Valencian) | none (search) | relevant to Valencian and Balearic speakers in the Congreso and Senado (brief) | Research |
| **projecte-aina/whisper-large-v3-ca-3catparla** (+ `faster-whisper-` variant) | whisper-large-v3 fine-tuned on 710 h of the 3CatParla broadcast-TV corpus | `HF: projecte-aina/whisper-large-v3-ca-3catparla` · corpus paper [IberSPEECH 2024](https://www.isca-archive.org/iberspeech_2024/hernandezmena24_iberspeech.pdf) | model Apache-2.0; corpus licence not confirmed (search) | ca | WER 0.96 on the 3CatParla test split, in-domain and not comparable to CV or FLEURS (search); corpus 731 h 21 m, manually transcribed | peer-reviewed corpus; HF-only (brief) | Mature research |
| **projecte-aina/stt-ca-citrinet-512**, **whisper-large-v3-tiny-caesar** | NeMo Citrinet Catalan model; small Catalan Whisper | `HF: projecte-aina/stt-ca-citrinet-512`, `HF: projecte-aina/whisper-large-v3-tiny-caesar` | not verified (brief) | ca | none (search) | older architectures; HF-only (brief) | Research |
| **BSC-LT/whisper-timestamped-cs** + **BSCs_Code_Switching_CA-ES_ASR_Test** | Catalan–Spanish code-switching fine-tune and its manually curated evaluation set | `HF: BSC-LT/whisper-timestamped-cs` · `HF dataset: BSC-LT/BSCs_Code_Switching_CA-ES_ASR_Test` · [arXiv 2507.13875](https://arxiv.org/abs/2507.13875) · [Interspeech 2025](https://www.isca-archive.org/interspeech_2025/mena25_interspeech.html) | not verified (brief) | ca–es code-switching | fine-tuned on CAESAR-TINY (2 h); eval set 867 clips / 4 h 09 m, Catalan-dominant with Spanish segments; finding: modest synthetic CS data plus a dominant-language token wins (search) | peer-reviewed; HF-only (brief) | Mature research |
| **HiTZ/whisper-large-v3-eu** | whisper-large-v3 fine-tuned on Common Voice 13 Basque | `HF: HiTZ/whisper-large-v3-eu` (also `-large-v2-eu`, `-large-eu`) | not verified (brief) | eu | 10.62% WER on the CV13 eu eval split (search) | HiTZ / Aholab, UPV-EHU; HF-only (brief) | Mature research |
| **HiTZ/stt_eu_conformer_transducer_large_v2** (+ `_large`, `stt_eu_conformer_ctc_large`) | NeMo Conformer-Transducer Basque, warm-started from `stt_es_conformer_transducer_large` | `HF: HiTZ/stt_eu_conformer_transducer_large_v2` | not verified (brief) | eu (lowercase, no punctuation) | 548 h Basque composite training set (search); 150.7 h of audio per wall-clock hour, fastest model in the Parlamento-ai study, 1,062 MB VRAM, but 35.68% word difference vs paid APIs (readme) | institutional; NeMo-native; HF-only (brief) | Mature research |
| **xezpeleta/whisper-large-v3-eu** (+ tiny/small/medium and `-ct2` builds) | Whisper fine-tunes on `asierhv/composite_corpus_eu_v2.1` (CV18 + Basque Parliament + OpenSLR) | `HF: xezpeleta/whisper-large-v3-eu`, `whisper-tiny-eu`, `whisper-small-eu-ct2`, `whisper-medium-eu-ct2` | not verified (brief) | eu | WER 4.84 on CV18 eu; 6.54 on the composite val split (search); best Basque result vs paid APIs in the parliamentary study (21.29% difference), 5.0 h/h (readme); trained on Basque Parliament plenary audio, a contamination risk for any parliamentary benchmark | community-maintained; HF-only (brief) | Mature research |
| **Proxecto Nós Galician**: `proxectonos/whisper-large-v3-turbo-gl-v1.0`, `proxectonos/Nos_ASR-wav2vec2-large-xlsr-53-gl-with-lm` | Galician Whisper-turbo fine-tune and a wav2vec2 + LM model | [gh proxectonos/language-models](https://github.com/proxectonos/language-models) · `HF: proxectonos/whisper-large-v3-turbo-gl-v1.0`, `HF: proxectonos/Nos_ASR-wav2vec2-large-xlsr-53-gl-with-lm` | repo MIT (repo-meta); model licences not verified (brief) | gl | best Galician specialist tested: 20.37% word difference vs paid APIs, 19.1 h/h, 1,832 MB VRAM, worse than generic Whisper-turbo (14.71%) on that sample (readme); training sets `Nos_Telexornais-GL` 1,100 h, `Nos_RG-Podcast-GL` 328 h, `Nos_ParlaSpeech-GL` 1,600 h (gh) | 6★ · pushed 2025-12-17 | Research |
| **Whisper-LM / whisper-lm-transformers** | n-gram and LLM shallow fusion for Whisper decoding in low-resource languages | [gh hitz-zentroa/whisper-lm](https://github.com/hitz-zentroa/whisper-lm) · [gh hitz-zentroa/whisper-lm-transformers](https://github.com/hitz-zentroa/whisper-lm-transformers) · `HF: HiTZ/whisper-lm-ngrams` · [arXiv 2503.23542](https://arxiv.org/abs/2503.23542) | Apache-2.0, both repos (repo-meta) | eu, gl, ca, es | consistent WER gains for eu and gl, LLM fusion ahead on those two (search); its baseline table gives Whisper large-v3 FLEURS ca 5.68 / gl 10.06 / es 15.01 (search; the es figure is unverified, see above) | 43★ · pushed 2025-05-06 (whisper-lm) · 14★ · pushed 2025-05-06 (whisper-lm-transformers) | Legacy |
| **PRHLT Albayzin 2024 Basque–Spanish S2T** | PRHLT’s bilingual speech-to-text system for the Albayzin 2024 BBS-S2T evaluation | [gh david-gimeno/prhlt-bbs-s2tc](https://github.com/david-gimeno/prhlt-bbs-s2tc) · `HF dataset: gttsehu/Albayzin-2024-BBS-S2T` (not opened) | MIT (review, raw README) | eu–es bilingual | none surfaced | review repo check; signals not collected | Research |
| **marianbasti/whisper-large-v3-turbo-latam** | Turbo fine-tune on CV17 `es` with Spain accents filtered out | `HF: marianbasti/whisper-large-v3-turbo-latam` | not verified (brief) | es (Latin American) | method stated; no independent WER found (search) | individual maintainer; HF-only (brief) | Research |
| **adriszmar/whisper-large-v3-turbo-es** | Turbo fine-tune on CV17 Spanish | `HF: adriszmar/whisper-large-v3-turbo-es` | not verified (brief) | es | WER 6.91% → 5.34% on CV17 es (search) | individual maintainer; HF-only (brief) | Research |
| **zuazo/whisper-large-v2-eu-from-es** | Basque model warm-started from a Spanish checkpoint (by the Whisper-LM author) | `HF: zuazo/whisper-large-v2-eu-from-es` | not verified (brief) | eu | ablation artifact of the Whisper-LM line (search) | tied to a published paper; HF-only (brief) | Research |

The reported WERs are not mutually comparable: 3CatParla 0.96 is in-domain, xezpeleta
4.84 is CV18, HiTZ 10.62 is CV13, Whisper-LM uses FLEURS. Only one re-run on one
held-out set produces a ranking.

#### Runtimes and serving

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **faster-whisper + CTranslate2** | CTranslate2 reimplementation of Whisper; the de facto production runtime | [gh SYSTRAN/faster-whisper](https://github.com/SYSTRAN/faster-whisper) · [gh OpenNMT/CTranslate2](https://github.com/OpenNMT/CTranslate2) | MIT / MIT (repo-meta) | Whisper’s full set, plus any CT2-converted fine-tune (BSC, projecte-aina and xezpeleta all ship ct2 builds) | ~12× realtime large-v3 on an RTX 4070; ~250× at batch 16 on an RTX 4090; ~40% less VRAM than whisper.cpp via int8 (search; blog-sourced); v1.2.1 released 2025-10-31 (gh) | 25,200★ · pushed 2025-11-19 (faster-whisper) · 4,658★ · pushed 2026-08-31 (CTranslate2) | Production |
| **WhisperX** | Batched faster-whisper + wav2vec2 forced alignment + pyannote diarization | [gh m-bain/whisperX](https://github.com/m-bain/whisperX) · [alignment.py](https://github.com/m-bain/whisperX/blob/main/whisperx/alignment.py) | BSD-2-Clause (repo-meta) | per-language alignment models; verified defaults (readme, raw source): `es` → torchaudio `VOXPOPULI_ASR_BASE_10K_ES`; `ca` → `softcatala/wav2vec2-large-xlsr-catala`; `eu` → `stefan-it/wav2vec2-large-xlsr-53-basque`; `gl` → `ifrz/wav2vec2-large-xlsr-galician` | 70× realtime with large-v2 batched on an RTX 4090; word timestamps ±50 ms vs ±500 ms vanilla (search); ca/eu/gl aligners are community XLSR checkpoints of unknown parliamentary-domain quality (readme) | 23,857★ · pushed 2026-08-30 | Production |
| **whisper.cpp** | GGML/GGUF C/C++ Whisper for CPU, Metal, CUDA and Vulkan | [gh ggml-org/whisper.cpp](https://github.com/ggml-org/whisper.cpp) | MIT (repo-meta) | Whisper’s full set | ~8× realtime large-v3 on an RTX 4070 (search); v1.9.3 released 2026-08-20 (gh) | 53,383★ · pushed 2026-08-31 | Production |
| **insanely-fast-whisper** | CLI over transformers with Flash Attention 2 batching | [gh Vaibhavs10/insanely-fast-whisper](https://github.com/Vaibhavs10/insanely-fast-whisper) | Apache-2.0 (repo-meta) | Whisper’s full set | batching-driven speedups (search) | 13,063★ · pushed 2025-10-25 | Mature research |
| **vLLM (audio / transcription)** | Whisper as an encoder-decoder with continuous batching; OpenAI-compatible `/v1/audio/transcriptions` and `/v1/audio/translations`, plus a Realtime API | [gh vllm-project/vllm](https://github.com/vllm-project/vllm) · [transcription docs](https://docs.vllm.ai/en/latest/serving/online_serving/speech_to_text/) | Apache-2.0 (repo-meta) | model-dependent | the encoder is one-shot; only the decoder benefits from continuous batching (search); production-grade Voxtral Realtime support; open issues for CrisperWhisper 2.0 and decoder-prefix support (gh) | 90,794★ · pushed 2026-09-02 | Production |
| **speaches** | OpenAI-compatible faster-whisper server | [gh speaches-ai/speaches](https://github.com/speaches-ai/speaches) | MIT (review) | Whisper’s set via faster-whisper | none | 3,637★ (review API check; push date not collected) | Mature research |
| **Hugging Face transformers pipeline** | Reference implementation for nearly every model above | [gh huggingface/transformers](https://github.com/huggingface/transformers) | Apache-2.0 (repo-meta) | everything | slowest of the runtimes; the compatibility floor (search) | 164,726★ · pushed 2026-09-02 | Production |
| **NVIDIA NeMo (speech toolkit)** | Training and inference framework behind Canary, Parakeet, Nemotron, MarbleNet, Sortformer, MSDD, TitaNet and NFA | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · [diarization docs](https://docs.nvidia.com/nemo-framework/user-guide/latest/nemotoolkit/asr/speaker_diarization/configs.html) | Apache-2.0 (repo-meta) | model-dependent; ASR checkpoints cover es/pt/en, not ca/eu/gl | v3.0.0 released 2026-08-07 (gh); `NVIDIA/NeMo` now resolves to the NVIDIA-NeMo org, pin the new name (gh) | 18,376★ · pushed 2026-09-02 | Production |
| **NVIDIA Riva / ASR NIM** | Hosted and on-prem ASR microservices over Parakeet, Canary and Whisper architectures | [gh nvidia-riva/tutorials](https://github.com/nvidia-riva/tutorials) · [ASR overview docs](https://docs.nvidia.com/deeplearning/riva/user-guide/docs/asr/asr-overview.html) | proprietary service; the tutorials repo has no licence field (repo-meta) | Parakeet TDT 600M NIM covers 25 languages incl. es; `parakeet-ctc-riva-0.6b-unified-ml-cs-es-en-us` is bilingual es/en; no ca/eu/gl (search) | vendor-published throughput and accuracy, not independently reproduced (search) | 173★ · pushed 2026-07-17 | commercial, no label |
| **whisper-timestamped** | Word timestamps and confidence via cross-attention DTW | [gh linto-ai/whisper-timestamped](https://github.com/linto-ai/whisper-timestamped) | AGPL-3.0 (repo-meta), copyleft that propagates into a hosted service | Whisper’s full set | used as the confidence source by some hallucination filters (search) | 2,842★ · pushed 2026-08-17 | Production |
| **WhisperKit** | On-device Whisper for Apple Silicon, streaming-capable | [gh argmaxinc/argmax-oss-swift](https://github.com/argmaxinc/argmax-oss-swift) (formerly `argmaxinc/WhisperKit`) · [arXiv 2507.10860](https://arxiv.org/html/2507.10860v1) | MIT (repo-meta) | Whisper’s set | published on-device latency and quality trade-offs (search) | 6,353★ · pushed 2026-08-13 | Production |

Streaming wrappers and servers are in §3.8; whisper-diarization in §3.5; the
forced-alignment stack in §3.9.

#### Target-speaker extraction and separation for the interpretation-mixed tail

Every SI-SNRi figure comes from synthetic 8 or 16 kHz mixtures and will not transfer to
a chamber feed unmeasured.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **wenet-e2e/wesep** | TSE toolkit: pBSRNN, pDPCCN, TF-GridNet, Spex+, WeSpeaker-conditioned | [gh wenet-e2e/wesep](https://github.com/wenet-e2e/wesep) · [arXiv 2409.15799](https://arxiv.org/abs/2409.15799) · [2026 paper](https://arxiv.org/html/2607.27436) | no LICENSE file; raw fetch 404, GitHub licence null (repo-meta/gh) | language-agnostic | Libri2Mix SI-SDR (dB): BSRNN 13.32 (train-100) / 16.57 (train-360); TF-GridNet 12.09/15.79; Spex+ 12.64/14.57; DPCCN 11.45/13.80 (search); pretrained-model checkbox unticked in the README TODO, no released weights (readme) | 311★ · pushed 2025-10-04 | Research (a reimplementation target, not a dependency) |
| **modelscope/ClearerVoice-Studio** | Enhancement, separation and TSE suite, MossFormer2 family | [gh modelscope/ClearerVoice-Studio](https://github.com/modelscope/ClearerVoice-Studio) · [Interspeech 2025 PDF](https://www.isca-archive.org/interspeech_2025/zhao25f_interspeech.pdf) · [PyPI clearvoice](https://pypi.org/project/clearvoice/) | Apache-2.0 (repo-meta) | language-agnostic | MossFormer2_SS_16K: SI-SNR 22.0 dB on WSJ0-2Mix; AV-MossFormer2 TSE 16K SI-SNRi 14.6 dB (2-spk) / 15.5 dB (3-spk) on VoxCeleb2 mixtures vs AV-TFGridNet 13.7 / 14.2 (search); its best TSE cue is face video, not enrolment audio | 4,458★ · pushed 2025-08-14 | Legacy (over the cutoff by 19 days) |
| **ESPnet-SE / TF-GridNet** | Separation and enhancement recipes inside ESPnet | [gh espnet/espnet](https://github.com/espnet/espnet) · [gh espnet/espnet_model_zoo](https://github.com/espnet/espnet_model_zoo) | Apache-2.0 (repo-meta) | language-agnostic | TF-GridNet is the standard strong baseline on WSJ0-2Mix; 13 TFGridNet paths in the repo (gh code search) | 9,949★ · pushed 2026-09-02 | Production |
| **asteroid-team/asteroid** | Separation research framework with many pretrained models | [gh asteroid-team/asteroid](https://github.com/asteroid-team/asteroid) · [arXiv 2005.04132](https://arxiv.org/abs/2005.04132) | MIT (repo-meta) | language-agnostic | ConvTasNet, DPRNN and DPTNet recipes on WSJ0-2Mix, Libri2Mix and WHAM (gh) | 2,585★ · pushed 2026-05-13 | Mature research |
| **SpeechBrain SepFormer** | Transformer separation, `sepformer-wsj02mix` | [gh speechbrain/speechbrain](https://github.com/speechbrain/speechbrain) · `HF: speechbrain/sepformer-wsj02mix` · [paper PDF](https://arxiv.org/pdf/2010.13154) | Apache-2.0 (repo-meta) | language-agnostic | SI-SNRi 22.3 dB / SDRi 22.4 dB on WSJ0-2Mix with dynamic mixing, 8 kHz (search); blind separation, not TSE | 11,802★ · pushed 2026-08-27 (speechbrain/speechbrain) | Production |
| **BUTSpeechFIT/speakerbeam** | Original SpeakerBeam TSE (enrolment-conditioned) | [gh BUTSpeechFIT/speakerbeam](https://github.com/BUTSpeechFIT/speakerbeam) · [arXiv 1904.09272](https://arxiv.org/abs/1904.09272) | NOASSERTION (repo-meta) | language-agnostic | reference TD-SpeakerBeam Libri2Mix recipes (gh) | 149★ · pushed 2021-10-25 | Legacy |
| **ZBang/USEF-TSE** | Speaker-embedding-free TSE via frame-level cross-attention | [gh ZBang/USEF-TSE](https://github.com/ZBang/USEF-TSE) · [paper PDF](https://arxiv.org/pdf/2409.02615) | NOASSERTION (repo-meta) | language-agnostic | reports leading TSE results; checkpoints downloadable from the repo (search) | 72★ · pushed 2025-07-05 | Legacy |
| **LExt / MC-LExt** | Onset-prompted TSE: concatenate the enrolment to the mixture | [LExt arXiv 2505.05114](https://arxiv.org/abs/2505.05114) · [MC-LExt arXiv 2510.15437](https://arxiv.org/abs/2510.15437) | no code, no licence (brief) | language-agnostic | very large reported SI-SNRi gains from an artificial onset prompt (search) | no code repository found (search) (brief) | Research |
| **joonaskalda/PixIT + pyannote ToTaToNet** | Joint diarization and separation trained with PIT + MixIT on real recordings | [gh joonaskalda/PixIT](https://github.com/joonaskalda/PixIT) · [arXiv 2403.02288](https://arxiv.org/html/2403.02288v1) · `HF: pyannote/separation-ami-1.0` | no licence on the PixIT repo (repo-meta); pyannote.audio code MIT | language-agnostic | >20% relative ASR gain from separated sources; AMI-SDM checkpoint released; training and inference merged into pyannote.audio 3.3.0 (search) | 107★ · pushed 2025-01-10 | Legacy (superseded in-tree) |
| **haoxiangsnr/llm-tse** | Text-guided TSE (LLM-TSE) | [gh haoxiangsnr/llm-tse](https://github.com/haoxiangsnr/llm-tse) | no licence (repo-meta) | none stated | none | 43★ · pushed 2023-10-13 | Legacy |
| **Audio-WestlakeU/NBSS** | SpatialNet / NBC multichannel separation and dereverberation | [gh Audio-WestlakeU/NBSS](https://github.com/Audio-WestlakeU/NBSS) | MIT (repo-meta) | language-agnostic; needs a microphone array | strong multichannel results | 370★ · pushed 2025-01-01 | Legacy |

### 3.4 Turn segmentation (diarization)

Sub-problem 4: who spoke when, without names.
Every DER figure comes from English or Mandarin corpora; no row carries a Spanish,
Catalan, Basque or Galician evaluation.
The hosted pyannoteAI pipelines and Picovoice Falcon are in §6.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **pyannote.audio 4.x** | Reference open diarization framework: segmentation, overlap detection, embedding, clustering, training recipes; produces the RTTM that names are then attached to | [gh pyannote/pyannote-audio](https://github.com/pyannote/pyannote-audio) · [HF org pyannote](https://hf.co/pyannote) · [arXiv 1911.01255](https://arxiv.org/abs/1911.01255) · [docs.pyannote.ai](https://docs.pyannote.ai) | code MIT (repo-meta); community models gated behind an HF user agreement and token (search) | language-agnostic | segmentation-3.0: PyanNet, 7-class powerset over 10 s windows, up to 3 concurrent speakers incl. overlap; 4.0.7 released 2026-06-30 (gh) | 10,500★ · pushed 2026-09-02 | Production |
| **pyannote `speaker-diarization-community-1`** | Free pretrained pipeline shipped with 4.x; successor to 3.1 | `HF: pyannote/speaker-diarization-community-1` · [blog](https://www.pyannote.ai/blog/community-1) | CC BY 4.0, gated (search) | language-agnostic | DER% (pyannote.metrics, collar unstated), benchmark dated 2025-09: AMI-SDM 19.9 · AMI-IHM 17.0 · DIHARD3-full 20.2 · CALLHOME-p2 26.7 · VoxConverse-v0.3 11.2 · AISHELL-4 11.7 · AliMeeting-ch1 20.3 (readme); 31 s per audio hour on AMI (search) | 10,500★ · pushed 2026-09-02 (pyannote/pyannote-audio) | Production |
| **BUTSpeechFIT/DiariZen** | Pruned WavLM-Large + Conformer powerset EEND with VBx clustering | [gh BUTSpeechFIT/DiariZen](https://github.com/BUTSpeechFIT/DiariZen) · `HF: BUT-FIT/diarizen-wavlm-large-s80-md` · [arXiv 2409.09408](https://arxiv.org/abs/2409.09408) | MIT code (repo-meta); weights CC BY-NC 4.0, non-commercial, because RAMC, MSDWild and DIHARD-3 forbid commercial use; the maintainers document the conflict (readme) | language-agnostic | DER%, no collar, Large-s80-v2: AMI-SDM 13.9 · AISHELL-4 10.1 · AliMeeting-far 10.8 · NOTSOFAR-1 16.7 · MSDWild 15.8 · DIHARD3-full 14.5 · RAMC 11.0 · VoxConverse 9.1 (readme); multi-channel WavLM added 2026-01 (readme) | 535★ · pushed 2026-08-04 | Mature research |
| **NeMo `diar_sortformer_4spk-v1`** | Offline end-to-end sort-loss diarizer, maximum 4 speakers | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/diar_sortformer_4spk-v1` · [arXiv 2409.06656](https://arxiv.org/abs/2409.06656) | open weights via NeMo/NGC; NVIDIA Open Model Licence per the joint brief (search), not OSI; resolve before deployment | language-agnostic acoustics | DER%: CH109 6.27 · DIHARD3-eval 16.28 · CALLHOME-p2 2spk 6.49 / 3spk 10.01 / 4spk 14.14; with tuned post-processing 5.85 / 8.46 / 12.59 and DIHARD3 14.76; collar 0.25 s on CALLHOME and CH109 (search); hard four-speaker cap | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Mature research |
| **NeMo `diar_streaming_sortformer_4spk-v2` / v2.1** | Streaming Sortformer with an Arrival-Order Speaker Cache; the front end for multitalker-parakeet (§3.5) | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/diar_streaming_sortformer_4spk-v2`, `HF: nvidia/diar_streaming_sortformer_4spk-v2.1` · [Interspeech 2025 PDF](https://www.isca-archive.org/interspeech_2025/medennikov25_interspeech.pdf) · [NeMo diarization docs](https://docs.nvidia.com/nemo-framework/user-guide/latest/nemotoolkit/asr/speaker_diarization/intro.html) | v2 CC BY 4.0 per its HF card (search, card not opened); the v2.1 mirrors carry the NVIDIA Open Model Licence | language-agnostic acoustics | DER 13.24 on DIHARD III eval (1–4 spk), collar 0.0 s, 1.04 s input buffer; streaming CallHome-eng0 6.2% macro / 6.0% weighted DER at 0.25 s collar; trained on 2,445 h real + 5,150 h simulated; degrades above 4 speakers (search). The model has four output slots and no slot eviction, so the cap is per stream state: reset state per window, or the cap becomes session-level | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Production (WhisperLiveKit ships it as the recommended backend, raw README) |
| **NeMo MSDD (`diar_msdd_telephonic`)** | Multi-scale diarization decoder over a clustering initialization | [gh recipe: multiscale_diar_decoder_infer.py](https://github.com/NVIDIA-NeMo/NeMo/blob/main/examples/speaker_tasks/diarization/neural_diarizer/multiscale_diar_decoder_infer.py) · [NGC diar_msdd_telephonic](https://catalog.ngc.nvidia.com/orgs/nvidia/nemo/models/diar_msdd_telephonic) | Apache-2.0 code (repo-meta) | trained on ~1,500 h of Fisher telephony only; degrades off-domain (search) | superseded in practice by Sortformer (search) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Research |
| **BUTSpeechFIT/VBx** | Bayesian HMM over x-vectors; the standard clustering back end | [gh BUTSpeechFIT/VBx](https://github.com/BUTSpeechFIT/VBx) · [paper PDF](https://arxiv.org/pdf/2012.14952) | no licence file (repo-meta) | language-agnostic | CALLHOME DER 4.42% in the forgiving setup, 14.21% in the fair setup with overlap (search) | 288★ · pushed 2024-01-15 | Legacy |
| **BUTSpeechFIT/DVBx** | Discriminatively trained VBx | [gh BUTSpeechFIT/DVBx](https://github.com/BUTSpeechFIT/DVBx) · [IEEE paper](https://ieeexplore.ieee.org/document/10446119/) | MIT (repo-meta) | language-agnostic | ICASSP 2024 gains over VBx (search) | 28★ · pushed 2024-09-23 | Legacy |
| **Audio-WestlakeU/FS-EEND (incl. LS-EEND)** | Frame-wise streaming EEND with online attractors; LS-EEND handles ≤8 speakers and ≤1 h | [gh Audio-WestlakeU/FS-EEND](https://github.com/Audio-WestlakeU/FS-EEND) · [LS-EEND arXiv 2410.06670](https://arxiv.org/abs/2410.06670) | no licence (repo-meta) | language-agnostic | online DER: CALLHOME 12.11 · DIHARD II 27.58 · DIHARD III 19.61 · AMI 20.76 (search) | 190★ · pushed 2026-05-07 | Research |
| **hitachi-speech/EEND** | Original EEND / EEND-EDA (Chainer) | [gh hitachi-speech/EEND](https://github.com/hitachi-speech/EEND) | MIT (repo-meta) | language-agnostic | reference implementation of the EEND line (gh) | 436★ · pushed 2021-08-30 | Legacy |
| **nttcslab-sp/EEND-vector-clustering** | EEND plus vector clustering for unbounded speaker counts | [gh nttcslab-sp/EEND-vector-clustering](https://github.com/nttcslab-sp/EEND-vector-clustering) | NOASSERTION (repo-meta) | language-agnostic | none | 81★ · pushed 2022-10-18 | Legacy |
| **merlresearch/tssep** | TS-VAD + TS-SEP joint diarization and separation over estimated embeddings | [gh merlresearch/tssep](https://github.com/merlresearch/tssep) · [arXiv 2303.03849](https://arxiv.org/abs/2303.03849) | AGPL-3.0 (repo-meta), copyleft | language-agnostic | LibriCSS and CHiME TS-SEP results in TASLP 2024 (search) | 43★ · pushed 2025-10-27 | Research |
| **AnshKapadia/TS-VAD-plus** | Transformer TS-VAD with ECAPA + WavLM + VBx priors | [gh AnshKapadia/TS-VAD-plus](https://github.com/AnshKapadia/TS-VAD-plus) · [APSIPA 2025 PDF](http://www.apsipa.org/proceedings/2025/papers/APSIPA2025_P333.pdf) | no licence (repo-meta) | language-agnostic | APSIPA 2025 overlap-speech gains (search) | 14★ · pushed 2025-07-28 | Legacy |
| **huggingface/diarizers** | Fine-tuning harness for pyannote segmentation | [gh huggingface/diarizers](https://github.com/huggingface/diarizers) | no licence set (repo-meta) | language-agnostic | claims useful gains from ~10 h of labelled data and ~5 min of GPU (readme); targets pyannote 3.x | 331★ · pushed 2024-06-14 | Legacy |
| **juanmc2005/diart** | Streaming diarization built on pyannote blocks | [gh juanmc2005/diart](https://github.com/juanmc2005/diart) | MIT (repo-meta) | language-agnostic | latency and DER trade-off curves in the repo docs (search) | 2,024★ · pushed 2026-06-19 | Mature research |
| **modelscope/3D-Speaker diarization pipeline** | Clustering pipeline with optional overlap detection; audio and audio-visual | [gh modelscope/3D-Speaker](https://github.com/modelscope/3D-Speaker) | Apache-2.0 (repo-meta) | tuning is Chinese-first (readme) | DER%: AISHELL-4 10.30 · AliMeeting 19.73 · AMI-SDM 21.76 · VoxConverse 11.75 (readme) | 3,128★ · pushed 2025-12-08 | Production |
| **cvqluu/simple_diarizer** | Minimal pipeline wrapper | [gh cvqluu/simple_diarizer](https://github.com/cvqluu/simple_diarizer) | GPL-3.0 (repo-meta), copyleft | language-agnostic | none | 159★ · pushed 2024-05-02 | Legacy |
| **modelscope/FunASR** | ASR toolkit with CAM++ diarization pipelines | [gh modelscope/FunASR](https://github.com/modelscope/FunASR) | MIT (repo-meta) | Chinese-first | none | 20,134★ · pushed 2026-09-02 | Production |
| **desh2608/dover-lap** | Diarization output fusion (DOVER-Lap): label mapping plus voting, with overlap | [gh desh2608/dover-lap](https://github.com/desh2608/dover-lap) | MIT (repo-meta) | language-agnostic | consistent DER gains from system combination; fuses mic-array, pyannote and commercial diarizations before attribution (search) | 94★ · pushed 2026-08-03 | Mature research |
| **wq2012/SpectralCluster** | Constrained spectral clustering from Google diarization papers | [gh wq2012/SpectralCluster](https://github.com/wq2012/SpectralCluster) | Apache-2.0 (repo-meta) | language-agnostic | none | 555★ · pushed 2024-09-25 | Legacy |
| **DongKeon/Awesome-Speaker-Diarization** | Curated diarization literature index | [gh DongKeon/Awesome-Speaker-Diarization](https://github.com/DongKeon/Awesome-Speaker-Diarization) | no licence (repo-meta) | none | index only | 370★ · pushed 2026-03-24 | Mature research |

Protocol warning: DiariZen reports DER without a collar, Sortformer’s CALLHOME and CH109
numbers use a 0.25 s collar, and VBx’s 4.42% is the forgiving setup against 14.21% fair
with overlap. Never compare rows across these tables without renormalizing the protocol.

### 3.5 Named attribution of each turn, with abstention

Sub-problem 5, in four tables: the embedding layer, open-set scoring and calibration,
joint speaker-attributed ASR, and text-side attribution.
Enrolment services are in §6.

#### Speaker embeddings and verification

Every headline number is a closed-set EER on VoxCeleb-family or CNCeleb data, which
overstates performance on a ~350-person watchlist.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **wenet-e2e/wespeaker** | Production-oriented embedding toolkit: ONNX runtime, AS-norm, QMF, PLDA | [gh wenet-e2e/wespeaker](https://github.com/wenet-e2e/wespeaker) · [pretrained models](https://github.com/wenet-e2e/wespeaker/blob/master/docs/pretrained.md) · [arXiv 2210.17016](https://arxiv.org/abs/2210.17016) | Apache-2.0 code (repo-meta); weights inherit the training-set licence (VoxCeleb → CC BY 4.0) (readme) | trained on VoxCeleb, VoxBlink2, CNCeleb; no Iberian evaluation | Vox1-O/E/H cosine + AS-norm + QMF EER%: ResNet293-LM 0.425 / 0.641 / 1.146; SimAM-ResNet34 (VoxBlink2 pretrain) 0.372 / 0.581 / 1.049; ResNet34-LM 0.723 / 0.867 / 1.532; CAM++ recipe 0.659 / 0.803 / 1.569; ECAPA1024-LM 0.707 / 0.894 / 1.615 (readme, `examples/voxceleb/v2`); ReDimNet2-B6-LM and W2V-BERT2.0-MFA in the model list (readme) | 1,402★ · pushed 2026-07-08 | Production |
| **modelscope/3D-Speaker embedding models** | CAM++, ERes2Net/V2, RDINO/SDPN; 200k-speaker Chinese models on ModelScope | [gh modelscope/3D-Speaker](https://github.com/modelscope/3D-Speaker) · [paper PDF](https://arxiv.org/pdf/2306.15354.pdf) | Apache-2.0 (repo-meta) | Chinese-centric training; no Iberian evaluation | Vox1-O EER%: ERes2Net-large 0.52, ERes2NetV2 0.61, CAM++ 0.65, ECAPA-TDNN 0.86, ResNet34 1.05; CNCeleb ERes2NetV2 6.14; 3D-Speaker set ERes2Net-large 6.34 (readme); ONNX export (gh) | 3,128★ · pushed 2025-12-08 | Production |
| **IDRnD/ReDimNet** | Dimension-reshaping architecture, B0–B6 (Interspeech 2024) | [gh IDRnD/redimnet](https://github.com/IDRnD/redimnet) · [paper PDF](https://arxiv.org/pdf/2407.18223) | MIT (repo-meta) | VoxCeleb, VoxBlink2, CNCeleb checkpoints | Vox1-O/E/H leaderboard-grade; checkpoints for vox2, voxblink2 and vb2+vox2+cnc mixes; torch.hub loading (readme); reproduced by WeSpeaker (ReDimNet2 integrated, WeSpeaker README) | 213★ · pushed 2026-08-28 | Mature research |
| **PalabraAI/redimnet2** | Time-pooled dimension-reshaping successor (Interspeech 2026) | [gh PalabraAI/redimnet2](https://github.com/PalabraAI/redimnet2) | MIT (repo-meta) | VoxCeleb2-dev training | 0.29% EER on VoxCeleb1 for the largest config trained on Vox2-dev only; 0.23% for the `vb2+vox2` LM checkpoint (local README); weights plus the training pipeline released (search/gh) | 85★ · pushed 2026-08-28 | Mature research |
| **SpeechBrain `spkrec-ecapa-voxceleb`** | ECAPA-TDNN reference embedding with the easiest API | [gh speechbrain/speechbrain](https://github.com/speechbrain/speechbrain) · `HF: speechbrain/spkrec-ecapa-voxceleb` | Apache-2.0 (repo-meta) | Vox1+Vox2 training | EER 0.80% on Vox1-test cleaned (search) | 11,802★ · pushed 2026-08-27 | Production |
| **NVIDIA TitaNet-L** | 23M-parameter 192-d embedding inside NeMo; the embedding in the whisper-diarization cascade | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/speakerverification_en_titanet_large` · [NGC titanet_large](https://catalog.ngc.nvidia.com/orgs/nvidia/nemo/models/titanet_large) | Apache-2.0 toolkit (repo-meta); model card via NGC/HF (search) | explicitly English-trained (search) | EER 0.66% on VoxCeleb1 cleaned trials; trained on Vox1+2, Fisher, Switchboard, LibriSpeech and SRE (search) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Production |
| **pyannote/wespeaker-voxceleb-resnet34-LM** | pyannote’s wrapper of WeSpeaker ResNet34-LM, used by 3.1 and community-1 | `HF: pyannote/wespeaker-voxceleb-resnet34-LM` | CC BY 4.0 (VoxCeleb-derived) (search) | language-agnostic | upstream Vox1-O 0.723 with LM + AS-norm (readme, WeSpeaker table) | drop-in for pyannote pipelines (search) (brief) | Production |
| **clovaai/voxceleb_trainer** | Metric-learning trainer in the VoxSRC baseline lineage | [gh clovaai/voxceleb_trainer](https://github.com/clovaai/voxceleb_trainer) · [arXiv 2003.11982](https://arxiv.org/abs/2003.11982) | MIT (repo-meta) | VoxCeleb | baseline recipes for Vox1-O/E/H (gh) | 1,176★ · pushed 2026-04-22 | Mature research |
| **Snowdar/asv-subtools** | Kaldi + PyTorch ASV toolkit (XMU) | [gh Snowdar/asv-subtools](https://github.com/Snowdar/asv-subtools) | Apache-2.0 (repo-meta) | Vox and CNCeleb recipes | none | 636★ · pushed 2024-08-05 | Legacy |
| **kiwano-toolkit/kiwano** | Lightweight PyTorch speaker-verification toolkit (Odyssey 2026) | [gh kiwano-toolkit/kiwano](https://github.com/kiwano-toolkit/kiwano) · [arXiv 2606.22369](https://arxiv.org/abs/2606.22369) | the paper claims Apache-2.0 (search); the repo exposes no licence (repo-meta) | unified protocols across corpora | unified protocols and baselines across corpora (search) | 5★ · pushed 2026-05-08 | Research |
| **microsoft/UniSpeech (WavLM-SV)** | WavLM-based speaker-verification heads | [gh microsoft/UniSpeech](https://github.com/microsoft/UniSpeech) · `HF: microsoft/wavlm-base-plus-sv` | NOASSERTION (repo-meta) | VoxCeleb | WavLM-Large SV is among the strongest SSL-based Vox1-O results (search) | 490★ · pushed 2024-04-05 | Legacy |
| **TaoRuijie/ECAPA-TDNN** | Single-file ECAPA reimplementation | [gh TaoRuijie/ECAPA-TDNN](https://github.com/TaoRuijie/ECAPA-TDNN) | MIT (repo-meta) | Vox2 training | EER 0.86 on Vox1-O trained on Vox2 only (gh description) | 827★ · pushed 2024-04-11 | Legacy |

#### Open-set identification, score normalization and calibration

Turning verification scores into a defensible “this is deputy X, or nobody” decision.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **VoxWatch** | First public open-set speaker-ID (watchlist) benchmark on VoxCeleb | [arXiv 2307.00169](https://arxiv.org/abs/2307.00169) | paper; trial and speaker lists stated as released (search) | VoxCeleb (English-centric) | quantifies false-alarm growth with watchlist size and probe duration; calibration and score fusion give large OSI gains (search) | no GitHub repository found; `search/repositories?q=voxwatch` returns only an unrelated project (gh) (brief) | Research |
| **VoxBlink2 OSI benchmark** | 110k+ speaker corpus with an open-set speaker-identification protocol and eval scripts | [gh VoxBlink2/ScriptsForVoxBlink2](https://github.com/VoxBlink2/ScriptsForVoxBlink2) · [arXiv 2407.11510](https://arxiv.org/abs/2407.11510) · [voxblink2.github.io](http://voxblink2.github.io) | NOASSERTION (repo-meta) | 18 languages, YouTube-sourced | `run_eval_ossi.sh` implements the OSSI protocol; annotations only (YouTube links and timestamps), no audio shipped (search) | 88★ · pushed 2024-08-13 | Legacy (still the only OSI eval script found; corpus in §5) |
| **WeSpeaker score norm and calibration** | AS-norm, QMF, PLDA and DET/EER tooling inside the toolkit | [score_norm.py](https://github.com/wenet-e2e/wespeaker/blob/master/wespeaker/bin/score_norm.py) · [score_calibration.py](https://github.com/wenet-e2e/wespeaker/blob/master/wespeaker/bin/score_calibration.py) · [compute_det.py](https://github.com/wenet-e2e/wespeaker/blob/master/wespeaker/bin/compute_det.py) | Apache-2.0 (repo-meta) | language-agnostic | AS-norm alone moves Vox1-O ResNet34 from 0.867 → 0.787; +LM+QMF → 0.659 (readme); files confirmed present in `wespeaker/bin` (gh contents API) | 1,402★ · pushed 2026-07-08 (wenet-e2e/wespeaker) | Production |
| **3D-Speaker scoring** | `compute_score_metrics.py` (EER, minDCF) | [gh compute_score_metrics.py](https://github.com/modelscope/3D-Speaker/blob/main/speakerlab/bin/compute_score_metrics.py) | Apache-2.0 (repo-meta) | language-agnostic | present in `speakerlab/bin`; no `score_norm` file matched in a repo code search (gh) | 3,128★ · pushed 2025-12-08 (modelscope/3D-Speaker) | Mature research |
| **bsxfan/PYLLR** | Python likelihood-ratio calibration: Cllr, min-Cllr, PAV, DET | [gh bsxfan/PYLLR](https://github.com/bsxfan/PYLLR) · [BOSARIS paper PDF](https://arxiv.org/pdf/1304.2865) · [BOSARIS toolkit](https://sites.google.com/site/bosaristoolkit/) | MIT (repo-meta) | language-agnostic | implements the BOSARIS Cllr/DCF theory in Python; Cllr / min-Cllr is the right metric for enrolment-based speaker-ID scores; `bob.measure` also implements Cllr ([docs](https://www.idiap.ch/software/bob/docs/bob/bob.measure/v4.0.0/py_api.html)) (search) | 25★ · pushed 2023-02-21 | Legacy |
| **luferrer/expected_cost** | Expected-cost and Bayes-decision evaluation with calibration diagnostics | [gh luferrer/expected_cost](https://github.com/luferrer/expected_cost) | MIT (repo-meta) | language-agnostic | generalizes DCF to arbitrary cost matrices (search) | 35★ · pushed 2025-06-11 | Legacy |
| **net:cal (calibration-framework)** | ECE, MCE, ACE plus temperature, beta and histogram recalibration | [gh EFS-OpenSource/calibration-framework](https://github.com/EFS-OpenSource/calibration-framework) (published in the brief as `fabiankueppers/calibration-framework`) | Apache-2.0 (repo-meta) | language-agnostic | classification and detection confidence calibration; the practical choice for “is this the named speaker?” confidences (search) | 380★ · pushed 2026-04-16 | Production |
| **SIDEKIT (with BOSARIS port)** | Python ASV toolkit including a translation of BOSARIS (`sidekit.bosaris`) | [docs](https://projets-lium.univ-lemans.fr/sidekit/) · [detplot module](https://projets-lium.univ-lemans.fr/sidekit/_modules/sidekit/bosaris/detplot.html) | open source, LIUM-hosted (search) | language-agnostic | Scores/Key/Ndx containers, DET plots, calibration (search) | not on GitHub; hosted on LIUM infrastructure (search) (brief) | Research |
| **khosravani/IBERSPEECH-RTVE** | Albayzin/IberSPEECH-RTVE 2018 diarization and identity system code | [gh khosravani/IBERSPEECH-RTVE](https://github.com/khosravani/IBERSPEECH-RTVE) | not recorded (review) | Spanish broadcast (RTVE) | none | 2019 (review API check); not in repo-meta | Legacy |
| **yinruiqing/pyannote-db-albayzin2016** | pyannote.database protocol plugin for Albayzin 2016 | [gh yinruiqing/pyannote-db-albayzin2016](https://github.com/yinruiqing/pyannote-db-albayzin2016) | not recorded (review) | Spanish broadcast (Albayzin 2016) | none | 2018 (review API check); not in repo-meta | Legacy |

The gap the briefs agree on: open-set identification is the least-served area.
VoxWatch is the correct protocol with no code; VoxBlink2’s script runs but ships no
audio; every toolkit headline is a closed-set EER; no maintained Albayzin system is on
GitHub.

#### Joint speaker-attributed ASR and speech LLMs

Every cpWER, tcpWER or cpCER here is English or Mandarin; nothing is evaluated on
Spanish and nothing at all on ca, eu or gl.
All emit anonymous labels unless stated.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **DiCoW / TS-ASR-Whisper** (BUT) | Whisper encoder conditioned on frame-level diarization (STNO masks, FDDT layers); target-speaker ASR without speaker embeddings | [gh BUTSpeechFIT/TS-ASR-Whisper](https://github.com/BUTSpeechFIT/TS-ASR-Whisper) · [gh BUTSpeechFIT/DiCoW](https://github.com/BUTSpeechFIT/DiCoW) · `HF: BUT-FIT/DiCoW_v3_3_large` (collection `BUT-FIT/dicow`) · [arXiv 2501.00114](https://arxiv.org/abs/2501.00114) · [EMMA leaderboard](https://huggingface.co/spaces/BUT-FIT/EMMA_leaderboard) | code Apache-2.0 (repo-meta and local README); weights CC BY 4.0 per the HF card, unverified (the local README says only “source codes Apache 2.0”); the full pipeline “must set up DiariZen”, whose weights are CC BY-NC 4.0 (readme) | Whisper large-v3 backbone (es/ca/gl workable, eu weak); no target-language evaluation | ~11% absolute ORC-WER reduction vs a cascade on NOTSOFAR-1; evaluated on AMI and NOTSOFAR-1 (search) | 121★ · pushed 2026-08-04 (TS-ASR-Whisper) · 102★ · pushed 2026-01-28 (DiCoW) | Mature research |
| **SE-DiCoW** | DiCoW plus self-enrolment: an auto-picked enrolment segment cross-attends to disambiguate overlap | branch `se_dicow` of [gh BUTSpeechFIT/TS-ASR-Whisper](https://github.com/BUTSpeechFIT/TS-ASR-Whisper) · `HF: BUT-FIT/SE-DiCoW` · [arXiv 2601.19194](https://arxiv.org/abs/2601.19194) | Apache-2.0 code (repo-meta) | as DiCoW | −52.4% relative macro tcpWER vs DiCoW on EMMA MT-ASR (search) | 121★ · pushed 2026-08-04 (BUTSpeechFIT/TS-ASR-Whisper) | Research |
| **Dixtral** (BUT) | DiCoW encoder + `Voxtral-Mini-3B-2507` LLM decoder (LoRA); TS-ASR plus per-speaker QA and summarization | [gh BUTSpeechFIT/Dixtral](https://github.com/BUTSpeechFIT/Dixtral) · `HF: BUT-FIT/Dixtral`, `HF: BUT-FIT/Dixtral_QA` · [arXiv 2606.18134](https://arxiv.org/abs/2606.18134) | Apache-2.0 (repo-meta) | Voxtral multilingual incl. es; no ca/eu/gl evidence | beats Gemini 3.0 Flash, VibeVoice and Voxtral-Mini-Transcribe-V2 by 29.0 / 19.8 / 16.0 absolute cpWER on AMI, NOTSOFAR-1, LibriSpeechMix and Mixer6 (search); weights open: README §Checkpoints lists both ids (raw) | 16★ · pushed 2026-06-16 | Research |
| **MOSS-Transcribe-Diarize 0.9B** | Single-pass long-form ASR + diarization + timestamps + acoustic events; Qwen3-0.6B decoder over a Whisper-Medium encoder, hotwords, vLLM and SGLang serving | [gh OpenMOSS/MOSS-Transcribe-Diarize](https://github.com/OpenMOSS/MOSS-Transcribe-Diarize) · `HF: OpenMOSS-Team/MOSS-Transcribe-Diarize` · [arXiv 2601.01554](https://arxiv.org/abs/2601.01554) | Apache-2.0 (repo-meta and local README); the “Pro” tier is API-only | 50+ languages, 90-minute single pass (readme) | cpCER AISHELL-4 15.83 / AliMeeting 22.17 / Podcast 7.37 / Movies 12.76; Pro 14.02 / 13.94 / 6.97 / 11.78; first place MLC-SLM at Interspeech 2026, 14 languages (readme) | 1,805★ · pushed 2026-09-02 | Mature research |
| **VibeVoice-ASR** (Microsoft) | Unified transcription + diarization + timestamping, ≤60 min single pass; in Transformers as `vibevoice_asr` and on Azure Foundry | [gh microsoft/VibeVoice](https://github.com/microsoft/VibeVoice) · `HF: microsoft/VibeVoice-ASR`, `-ASR-HF` · report arXiv 2601.18184 · [Transformers docs](https://huggingface.co/docs/transformers/model_doc/vibevoice_asr) | MIT (repo-meta) | 50+ languages, native code-switching (search) | third-party cpCER 24.99 / 29.33 / 48.30 / 42.54, weak on podcast and movies; sub-1 s diarization criticised; weights ~17.3 GB (readme/search) | 53,564★ · pushed 2026-09-02 (the stars belong to the VibeVoice TTS repo; the ASR is a later addition) | Mature research |
| **TagSpeech** (AudenAI) | End-to-end multi-speaker ASR + diarization with temporal grounding; SOT plus interleaved time anchors, emits timestamps and gender, no clustering | [gh AudenAI/Auden](https://github.com/AudenAI/Auden) · `HF: AudenAI/TagSpeech-AMI`, `TagSpeech-Alimeeting`, dataset `AudenAI/UTS` · [arXiv 2601.06896](https://arxiv.org/abs/2601.06896) | Apache-2.0 (repo-meta) | checkpoints only en (AMI) and zh (AliMeeting) (search) | ≤30 s window, chunking needed for long-form; offline only, no streaming (search) | 74★ · pushed 2026-04-02 | Research |
| **SpeakerLM** (Alibaba) | Audio-text MLLM doing diarization and recognition, emitting named output (`Mike: Hello`) from an enrolment registry; one of only three routes to a name | [arXiv 2508.06372](https://arxiv.org/abs/2508.06372) | none published (search) | 600k h speech-to-text pretraining (search) | DER unreported (output format); no public weights (search) | no repo, no weights found (search) (brief) | Research |
| **Speaker-Reasoner** (ASLP@NPU) | Speech LLM with agentic multi-turn temporal reasoning; joint speaker identity, gender, timestamps and transcript | [gh ASLP-lab/Speaker-Reasoner](https://github.com/ASLP-lab/Speaker-Reasoner) · [HF collection ASLP-lab/speaker-reasoner](https://huggingface.co/collections/ASLP-lab/speaker-reasoner) · [arXiv 2604.03074](https://arxiv.org/abs/2604.03074) | Apache-2.0 (repo-meta) | AliMeeting and AISHELL-4 (Mandarin) (search) | gains over baselines on overlap and turn-taking; cpWER not surfaced (search) | 94★ · pushed 2026-05-13 | Research |
| **NVIDIA multitalker-parakeet-streaming-0.6b-v1** | Streaming multitalker ASR: learnable speaker kernels in the Fast-Conformer pre-encode, driven by Sortformer; no enrolment, no speaker query; one instance per speaker | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/multitalker-parakeet-streaming-0.6b-v1` | NVIDIA Open Model Licence, not OSI (search); unverified | English only; multilingual requested, not shipped (search) | trained on AMI, NOTSOFAR-1, Fisher, LibriSpeech, WSJ and SWBD; full-overlap capable; latency and accuracy configurable (search) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Mature research |
| **whisper-diarization** (cascade) | Vocal separation → Whisper → `ctc-forced-aligner` → MarbleNet VAD → TitaNet embeddings → word-level assignment → punctuation realignment | [gh MahmoudAshraf97/whisper-diarization](https://github.com/MahmoudAshraf97/whisper-diarization) + Colab (readme) | BSD-2-Clause (repo-meta) | Whisper’s languages; the NeMo components are language-agnostic (readme) | no published cpWER or tcpWER (readme); CI green (gh); no releases cited | 5,634★ · pushed 2026-08-15 | Mature research |
| **Unified Speech LLM** (2507.02927) | Joint diarization + ASR speech LLM for MLC-SLM (Task I oracle, Task II joint) | [arXiv 2507.02927](https://arxiv.org/abs/2507.02927) | none stated (search) | multilingual conversational (search) | none | no code repository found (search) (brief) | Research |
| **DM-ASR** | Diarization-aware multi-speaker ASR as multi-turn dialogue: speaker- and time-conditioned queries, optional word-level timestamp interleaving | [arXiv 2604.22467](https://arxiv.org/abs/2604.22467) · earlier [arXiv 2506.05796](https://arxiv.org/abs/2506.05796) | none stated (search) | not stated | decouples who and when from what; numbers not surfaced (search) | no repo found (search) (brief) | Research |
| **SA-SOT** | Speaker-aware serialized output training: masked t-SOT auxiliary loss plus speaker-similarity biasing of decoder attention | [arXiv 2403.02010](https://arxiv.org/abs/2403.02010) (ICASSP 2024) | none stated (search) | not stated | improves t-SOT under frequent speaker change (search) | no public repo found (search) (brief) | Research |

The hosted SA-ASR APIs (`gpt-4o-transcribe-diarize` with `known_speaker_references`,
Gemini 3.5 Transcribe, ElevenLabs Scribe v2) are in §6. “Speaker-attributed” is not
“named”: only three paths reach a name, and two of them (OpenAI’s four-speaker enrolment
cap, SpeakerLM’s closed weights) are unusable for a 350-seat chamber, leaving a
roster-matching layer to build.

#### Text-side attribution and LLM post-correction

The parliamentary case (chair announcements, roster linking) is unbuilt as released
code.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **DiarizationLM** (Google) | LLM post-processor over ASR + diarization rendered as compact text; prompt builders, completion parser, WER/WDER/cpWER scorer, unsloth fine-tuning | [gh google/speaker-id (DiarizationLM)](https://github.com/google/speaker-id/tree/master/DiarizationLM) · `HF: google/DiarizationLM-8b-Fisher-v2` (Llama 3), `-13b-Fisher-v1` (Llama 2) · [arXiv 2401.03506](https://arxiv.org/abs/2401.03506) · [demo space](https://huggingface.co/spaces/diarizers-community/DiarizationLM-GGUF) | Apache-2.0 repo (repo-meta); weights carry the Llama licences (readme) | English only (Fisher, CallHome English) (search) | −55.5% relative WDER (Fisher), −44.9% (CallHome-en) with a fine-tuned PaLM 2-S (search) | 456★ · pushed 2025-08-12 (over the cutoff by three weeks) | Legacy |
| **LLM-Diarize-ASR-Agnostic** | Fine-tuned LLM diarization corrector; a weight-averaged ensemble of three ASR-specific models for ASR-agnosticism | [gh GeorgeEfstathiadis/LLM-Diarize-ASR-Agnostic](https://github.com/GeorgeEfstathiadis/LLM-Diarize-ASR-Agnostic) · [HF org bklynhlth](https://huggingface.co/bklynhlth) · [arXiv 2406.04927](https://arxiv.org/abs/2406.04927) | no licence file (repo-meta) | English (Fisher) (search) | the ensemble beats each single ASR-specific model (search) | 24★ · pushed 2024-07-31 | Legacy |
| **Text-based speaker change detection** | Sentence-level speaker-change detection from text alone; single- and multiple-prediction models, strongest on short conversations | [arXiv 2506.11344](https://arxiv.org/abs/2506.11344) | none stated (search) | English dialogue | gains on short-conversation change detection (search) | no code found (search) (brief) | Research |
| **spaCy** (`es_core_news_*`, `ca_core_news_*`) | Spanish and Catalan NER for matching recognized names to a member roster | [gh explosion/spaCy](https://github.com/explosion/spaCy) · [Spanish models](https://spacy.io/models/es) · [facts and figures](https://spacy.io/usage/facts-figures) | MIT (repo-meta); per-model card otherwise | Spanish yes; Catalan `ca_core_news_{sm,md,lg,trf}` 3.8.0 exist (spacy-models releases, verified by the review); no eu or gl core pipelines | per-model NER F-scores on the model pages (not opened) (search) | 33,869★ · pushed 2026-08-24 | Production |
| **Stanza** | Alternative Spanish NER (CoNLL-2002 PER/LOC/ORG/MISC) | [gh stanfordnlp/stanza](https://github.com/stanfordnlp/stanza) · [ACL demo paper](https://aclanthology.org/2020.acl-demos.14.pdf) | NOASSERTION field (repo-meta); Apache-2.0 upstream (gh) | many languages incl. Spanish (search) | 12 public NER datasets; an es-vs-spaCy Europarl comparison sits at [gh tiost100/Multilingual_NER](https://github.com/tiost100/Multilingual_NER) (search) | 7,872★ · pushed 2026-09-02 | Production |
| **RapidFuzz** | Fuzzy matching (Levenshtein, Jaro-Winkler, token-set) for ASR-mangled names against the roster | [gh rapidfuzz/RapidFuzz](https://github.com/rapidfuzz/RapidFuzz) | MIT (repo-meta) | language-agnostic | also the backend that makes `jiwer` fast (§3.10) | 4,108★ · pushed 2026-08-30 | Production |
| **SPARQLWrapper · Wikidata Toolkit** | Query Wikidata for member identity, term, party and chamber | [gh RDFLib/sparqlwrapper](https://github.com/RDFLib/sparqlwrapper) · [gh Wikidata-Toolkit/Wikidata-Toolkit](https://github.com/Wikidata-Toolkit/Wikidata-Toolkit) | NOASSERTION / Apache-2.0 (repo-meta) | language-agnostic | none | 568★ · pushed 2026-04-30 (sparqlwrapper) · 413★ · pushed 2026-08-31 (Wikidata-Toolkit) | Production |
| **Salamandra, ALIA** (BSC) and **Latxa** (HiTZ) | In-language LLMs for the R3 fuser and the R6 normalizer | [gh langtech-bsc/salamandra](https://github.com/langtech-bsc/salamandra) · [gh langtech-bsc/alia](https://github.com/langtech-bsc/alia) · [gh hitz-zentroa/latxa](https://github.com/hitz-zentroa/latxa) | Apache-2.0; Apache-2.0; MIT (review) | es, ca, eu, gl (Salamandra, ALIA); eu (Latxa) | none collected | review repo check; signals not collected | Research |
| **Italian parliamentary VLM pipeline** | OCR preserving reading order, then a large VLM for transcription refinement, element classification and speaker identification from layout plus text | [arXiv 2603.28103](https://arxiv.org/abs/2603.28103) (arxiv.org egress-blocked in the source session) | none stated (search) | Italian; document-based, not audio | speaker-ID accuracy not surfaced (search) | no code surfaced; Wikidata linkage unconfirmed (search) (brief) | Research |
| **SPC_R** (Swiss Parliaments Corpus Re-Imagined) | The closest published parliamentary post-editing recipe: Whisper large-v3 over multi-hour debates → two-step GPT-4o correction against the official protocols, then predicted-BLEU filtering | [arXiv 2506.07726](https://arxiv.org/abs/2506.07726) | none stated (search) | Swiss German → standard German | corpus-construction pipeline; no code surfaced (search) | none (search) (brief) | Research |

### 3.6 Editorial normalization to record form

Sub-problem 6: inverse text normalization, punctuation, normalizers for scoring, and
translation of co-official interventions into Spanish for the record.
Coverage collapses outside Spanish: ITN has no ca/eu/gl, Spanish punctuation restoration
exists only via a third-party checkpoint, and every ca/eu/gl→es translator is Hugging
Face-only.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **NeMo-text-processing** | WFST grammars for inverse text normalization (spoken→written numbers, dates, money, times) and TN | [gh NVIDIA/NeMo-text-processing](https://github.com/NVIDIA/NeMo-text-processing) | Apache-2.0 (repo-meta) | ITN: ar de en es es_en fr he hi hi_en hy ja ko mr pt ru sv vi zh; TN includes es; no ca/eu/gl (gh directory listing) | Spanish ITN taggers present: cardinal, date, decimal, electronic, fraction, measure, money, ordinal, punctuation, telephone, time, whitelist, word (gh) | 495★ · pushed 2026-09-01 | Production |
| **deepmultilingualpunctuation** | Punctuation restoration for ASR output | [gh oliverguhr/deepmultilingualpunctuation](https://github.com/oliverguhr/deepmultilingualpunctuation) · `HF: oliverguhr/fullstop-punctuation-multilang-large` | MIT (repo-meta) | primary models en/it/fr/de(+nl), no Spanish; the README points to `kredor/punctuate-all` for en de fr es bg it pl nl cs pt sk sl (readme) | per-language, per-marker F1 tables; hyphens and colons flagged ambiguous (readme) | 169★ · pushed 2024-08-21 | Legacy |
| **punctuators** | Inference package for punctuation, true-casing and sentence-boundary detection | [gh 1-800-BAD-CODE/punctuators](https://github.com/1-800-BAD-CODE/punctuators) · [HF org 1-800-BAD-CODE](https://huggingface.co/1-800-BAD-CODE) | Apache-2.0 (repo-meta) | multilingual checkpoints incl. Spanish (search) | none | 33★ · pushed 2026-03-23 | Research |
| **punctuation-restoration** (xashru) | W-NUT 2020 transformer recipe, retrainable for new languages | [gh maveryn/punctuation-restoration](https://github.com/maveryn/punctuation-restoration) (published in the brief as `xashru/punctuation-restoration`) | MIT (repo-meta) | en and bn in-repo; no Spanish checkpoint | none | 228★ · pushed 2024-07-29 | Legacy |
| **Whisper normalizers** (in-repo) | Reference text normalizers for WER scoring (`EnglishTextNormalizer`, `BasicTextNormalizer`) | [gh openai/whisper](https://github.com/openai/whisper) (`whisper/normalizers/`) | MIT (repo-meta) | English-specific; `BasicTextNormalizer` is the only non-English option and is a crude unicode and punctuation stripper: for es/ca/eu/gl (numbers, Catalan `l·l`, elisions, Basque morphology) a project-specific normalizer must be written and versioned | none | 108,319★ · pushed 2026-08-31 (openai/whisper) | Production |
| **whisper_normalizer** (standalone package) | Packaged copy of the Whisper normalizers | [gh kurianbenoy/whisper_normalizer](https://github.com/kurianbenoy/whisper_normalizer) | MIT (repo-meta) | as above | none | 79★ · pushed 2026-08-11 | Research |
| **WhisperD** | Whisper fine-tuned to emit disfluencies and fillers (tokenizer extended with "uh"/"um"); the inverse of record-style disfluency removal | [ISCA archive PDF](https://www.isca-archive.org/interspeech_2025/akinrintoyo25_interspeech.pdf) · derivative [gh pevers/whisperd-nl](https://github.com/pevers/whisperd-nl) · `HF: pevers/whisperd-nl` | original not stated; derivative MIT (repo-meta) | English (DementiaBank and in-house); a Dutch derivative; no Spanish | WER 0.24 (medium, fine-tuned) plus filler-inclusion rate and F1; generalizes to unseen patterns (search) | 7★ · pushed 2025-07-01 (pevers/whisperd-nl, the only repo in the sweep) | Research (the Dutch derivative repo is Legacy by the push rule) |
| **SalamandraTA-2b / 7b** (BSC-LT) | Multilingual MT covering all co-official languages of Spain; the ca/eu/gl→es leg | `HF: BSC-LT/salamandraTA-2B`, `-2b-instruct`, `-7b-instruct` · [WMT25 paper, arXiv 2508.12774](https://arxiv.org/html/2508.12774) | per model card, not opened (brief) | es ca eu gl ast an arn plus all official EU languages (search) | 424B-token pretraining; 6,574,251,526 parallel sentence pairs (OPUS + NTEU + Aina); synthetic ca from Aina es-ca (search) | HF-only; `gh BSC-LT/salamandraTA` returns 404 (gh) (brief) | Mature research |
| **Projecte Aina translators** | Catalan-centric bilingual MT, e.g. `aina-translator-eu-ca`, `aina-translator-gl-ca` | [HF: projecte-aina](https://huggingface.co/projecte-aina) (`projecte-aina/aina-translator-*`) | per model card, not opened (brief) | ca ↔ eu, gl, es, en and others (search) | none | HF-only; `gh projecte-aina/translation-service` returns 404 (gh) (brief) | Mature research |
| **HiTZ Basque MT** | Marian NMT es↔eu and en↔eu from HiTZ/IXA (UPV/EHU) | `HF: HiTZ/mt-hitz-es-eu`, `-eu-es`, `-en-eu`, `-eu-en` · [hitz.eus/en/mt](http://www.hitz.eus/en/mt) | per model card, not opened (brief) | eu↔es, eu↔en | es→eu 104,417,271 pairs (12.09M web + 92.33M back-translated OSCAR); eu→es 35,619,691 pairs (12.09M + 23.53M EusCrawl); evaluated on Flores, TaCon and NTREX (search) | HF-only; no MT repo in the `hitz-zentroa` org (gh) (brief) | Mature research |
| **Proxecto Nós Galician MT** | OpenNMT es↔gl and en↔gl | `HF: proxectonos/Nos_MT-OpenNMT-es-gl`, `-gl-es`, `-en-gl` · [project paper](https://ceur-ws.org/Vol-3224/paper07.pdf) | per model card, not opened (brief) | gl↔es, gl↔en | es-gl from authentic plus synthetic corpora; en-gl built via en-pt → pt-gl with Apertium plus OOV transliteration (search) | HF-only; no GitHub org repo surfaced (gh) (brief) | Mature research |
| **Softcatalà nmt-models** | Softcatalà’s Catalan-centric neural translation models | [gh Softcatala/nmt-models](https://github.com/Softcatala/nmt-models) | NOASSERTION (repo-meta); read the licence manually | Catalan pairs (gh) | none | 22★ · pushed 2026-01-17 | Research |
| **Apertium** | Rule-based MT; deterministic and auditable es↔ca/gl/eu | [gh apertium/apertium](https://github.com/apertium/apertium) | GPL-2.0 (repo-meta), copyleft on distribution | es-ca, es-gl and eu are among the largest RBMT pair sets; used in Proxecto Nós’s pt-gl step (search) | none | 113★ · pushed 2026-01-30 | Production |

Punctuation-native ASR (the BSC-LT `-punctuated` Catalan and LoS checkpoints,
projecte-aina 3catparla, §3.3) removes the need for a Catalan punctuation-restoration
model. The in-language LLMs for a learned normalizer (Salamandra, ALIA, Latxa) are in
§3.5.

### 3.7 Stage-direction detection

Sub-problem 7: producing the “Aplausos”, “Rumores” and “Risas” annotations.
AudioSet carries the needed classes; no published accuracy exists for these specific
classes, only 527-class mAP.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **SenseVoice / SenseVoiceSmall** | ASR + language ID + emotion + audio event detection in one pass, events emitted inline with the transcript | [gh QwenAudio/SenseVoice](https://github.com/QwenAudio/SenseVoice) (was `FunAudioLLM/SenseVoice`, redirects) · `HF: FunAudioLLM/SenseVoiceSmall` · [arXiv 2407.04051](https://arxiv.org/pdf/2407.04051) | code MIT (repo-meta); the model is under a separate FunASR MODEL_LICENSE (search) | ASR zh/en/yue/ja/ko only, no Spanish and no Iberian language; AED itself is language-independent | detects bgm, applause, laughter, crying, coughing, sneezing, breathing; Large predicts event start and end, Small allows ≤1 event per utterance with no timestamps; AED is pseudo-labelled, so BEATs and PANNs score better F1 and precision far exceeds recall (search); trained on 400k h; the “50+ languages” Large model is not the released checkpoint (search) | 9,209★ · pushed 2026-08-31 | Production |
| **PANNs** | AudioSet-pretrained CNNs for tagging and frame-level SED | [gh qiuqiangkong/audioset_tagging_cnn](https://github.com/qiuqiangkong/audioset_tagging_cnn) · Zenodo checkpoints (readme) | MIT (repo-meta) | 527 AudioSet classes | mAP 0.439 (Wavegram-Logmel-CNN) vs 0.317 for the Google baseline; `Cnn14_16k_mAP=0.438`; `Cnn14_DecisionLevelMax_mAP=0.385` for SED; class list verified to include Applause /m/028ght, Laughter /m/01j3sz, Cheering /m/053hz1, Chatter /m/07rkbfh, Crowd /m/03qtwd, Hubbub-speech-babble /m/07qfr4h (readme) | 1,773★ · pushed 2024-07-25 | Legacy |
| **BEATs** | Iterative audio pre-training with acoustic tokenizers; the strongest audio-only tagger | [gh microsoft/unilm](https://github.com/microsoft/unilm) (`beats/`) · [arXiv 2212.09058](https://arxiv.org/abs/2212.09058) | MIT repo (repo-meta); checkpoint terms separate | 527 AudioSet classes | mAP 50.6% on AudioSet-2M audio-only with no external data; 98.1% ESC-50 (search) | 22,203★ · pushed 2026-08-26 | Mature research |
| **AST** | Convolution-free attention audio classifier with a documented reproduction | [gh YuanGongND/ast](https://github.com/YuanGongND/ast) | BSD-3-Clause (repo-meta) | 527 AudioSet classes | 0.459 mAP single (weight-averaged), 0.485 ensemble on AudioSet; 95.6% ESC-50; 98.1% Speech Commands V2 (readme); weights on Dropbox, link-rot risk (gh/readme) | 1,473★ · pushed 2023-05-21 | Legacy |
| **CLAP** (LAION) | Contrastive language-audio pretraining giving zero-shot event detection from free-text prompts ("applause", “murmuring”) | [gh LAION-AI/CLAP](https://github.com/LAION-AI/CLAP) · `HF: laion/clap-htsat-unfused` · [paper PDF](https://arxiv.org/pdf/2206.04769) | CC0-1.0 (repo-meta) | prompts English; audio language-agnostic | zero-shot ESC-50 82.6% → 89.1% with 630k-pair data; 89.25% on the music_speech variant; `clap-htsat-unfused` is 153M params (search) | 2,259★ · pushed 2025-05-15 | Legacy |
| **EfficientAT** | MobileNet-style AudioSet models and embeddings; the real-time, low-compute pick | [gh fschmid56/EfficientAT](https://github.com/fschmid56/EfficientAT) | MIT (repo-meta) | 527 AudioSet classes | mAP 0.478, with a params/MACs/mAP table (readme) | 360★ · pushed 2024-11-20 | Legacy |

### 3.8 Incremental delivery: streaming wrappers and servers

Sub-problem 8: latency, revision and finalization.
The streaming Whisper wrappers are the only route to live ca/eu/gl.
Streaming diarization (Streaming Sortformer, diart) is in §3.4; latency scorers in
§3.10.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **ufal/SimulStreaming** | Simul-Whisper’s AlignAtt policy inside the whisper_streaming interface; large-v3, beam search, prompts, unbounded audio | [gh ufal/SimulStreaming](https://github.com/ufal/SimulStreaming) · [arXiv 2506.17077](https://arxiv.org/pdf/2506.17077) (IWSLT 2025 CUNI) | MIT (repo-meta) | Whisper’s full set, the only streaming wrapper that inherits es/ca/eu/gl | AlignAtt stops decoding when cross-attention reaches a “dangerous zone” near the buffer end; no clean published latency table found (search); reused by WhisperLiveKit as a backend (raw README) | 666★ · pushed 2026-07-12 | Mature research |
| **ufal/whisper_streaming** | LocalAgreement-2 streaming policy over any Whisper backend | [gh ufal/whisper_streaming](https://github.com/ufal/whisper_streaming) | MIT (repo-meta) | Whisper’s full set | the widely cited streaming baseline; superseded by SimulStreaming (search) | 3,672★ · pushed 2025-11-12 | Mature research |
| **backspacetg/simul_whisper** | Original Simul-Whisper (AlignAtt + truncation detection), Interspeech 2024 | [gh backspacetg/simul_whisper](https://github.com/backspacetg/simul_whisper) | Apache-2.0 (repo-meta) | Whisper’s set | reference implementation of the policy SimulStreaming adopts (search) | 114★ · pushed 2025-03-30 | Legacy |
| **collabora/WhisperLive** | Near-live WebSocket transcription server; faster_whisper, TensorRT and OpenVINO backends | [gh collabora/WhisperLive](https://github.com/collabora/WhisperLive) | MIT (repo-meta) | Whisper’s set | chunk-size vs accuracy trade-off; ~500–800 ms is the commonly quoted operating point (search) | 4,246★ · pushed 2026-08-31 | Production |
| **QuentinFuxa/WhisperLiveKit** | Streaming ASR + diarization + translation with OpenAI- and Deepgram-compatible APIs; bundles SimulStreaming backends and Streaming Sortformer | [gh QuentinFuxa/WhisperLiveKit](https://github.com/QuentinFuxa/WhisperLiveKit) · [PyPI whisperlivekit](https://pypi.org/project/whisperlivekit/) | Apache-2.0 (repo-meta) | Whisper’s set | streaming plus speaker turns in one process; the closest off-the-shelf fit for “live + who is speaking” (search); README names SimulStreaming backends and “Sortformer / NeMo 3” (raw) | 10,988★ · pushed 2026-09-01 | Production |
| **NeMo cache-aware streaming FastConformer** | Stateful conformer with cached activations; several look-aheads in one checkpoint | [gh NVIDIA-NeMo/Speech](https://github.com/NVIDIA-NeMo/Speech) · `HF: nvidia/stt_en_fastconformer_hybrid_large_streaming_multi` · [arXiv 2312.17279](https://arxiv.org/pdf/2312.17279) | Apache-2.0 toolkit (repo-meta) | model-dependent; the multi-latency checkpoint is English-only; Spanish only via Nemotron (§3.3) | supported worst-case latencies {0, 80, 480, 1040} ms (average ≈ half); Nemotron 3.5 exposes 80/160/320/560/1120 ms chunks (search) | 18,376★ · pushed 2026-09-02 | Production |
| **k2-fsa/sherpa-onnx** (+ icefall) | ONNX Runtime streaming zipformer transducer and CTC, plus VAD, diarization (segmentation + embedding) and TTS; 12 language bindings, embedded targets | [gh k2-fsa/sherpa-onnx](https://github.com/k2-fsa/sherpa-onnx) · [gh k2-fsa/icefall](https://github.com/k2-fsa/icefall) · [pretrained models](https://k2-fsa.github.io/sherpa/onnx/pretrained_models/index.html) | Apache-2.0 (repo-meta, both) | documented streaming zipformers are mainly en/zh/yue; Spanish appears in spoken LID but no documented Spanish streaming zipformer (search) | runs offline on Raspberry Pi, RISC-V, Android, iOS and NPUs; no first-party DER table, re-hosts pyannote and WeSpeaker ONNX models for diarization; v1.13.7 released 2026-09-01 (gh) | 14,569★ · pushed 2026-09-02 (sherpa-onnx) · 1,484★ · pushed 2026-07-16 (icefall) | Production |
| **Vosk (alphacep/vosk-api)** | Kaldi-based offline streaming recognizer, ~50 MB models, zero-latency streaming API, reconfigurable vocabulary | [gh alphacep/vosk-api](https://github.com/alphacep/vosk-api) · [models](https://alphacephei.com/vosk/models) | Apache-2.0 (repo-meta) | 20+ languages incl. es, ca, pt, en (search); eu and gl claimed by some listings, unverified | small models, streaming by construction; accuracy well below Whisper-class models (search) | 15,102★ · pushed 2026-08-09 | Production |
| **tomer9080/WhisperRT-Streaming** | Causal fine-tune of Whisper for 300 ms chunks | [gh tomer9080/WhisperRT-Streaming](https://github.com/tomer9080/WhisperRT-Streaming) | NOASSERTION (repo-meta) | English models; a large-v2 multilingual variant covers en/fr/es/de/pt at 300 ms chunks (search) | 300 ms chunk size (search) | 76★ · pushed 2026-03-31 | Research |

### 3.9 Forced alignment and corpus building

Word- or segment-level timestamps, and turning session video plus a non-verbatim
official transcript into training and evaluation data.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **ctc-forced-aligner** | CTC forced alignment over HF wav2vec2, HuBERT and MMS checkpoints; sentence, word and character granularity, JSON output | [gh MahmoudAshraf97/ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner) · `HF: MahmoudAshraf/mms-300m-1130-forced-aligner` | README §License: “BSD License, note that the default model has CC-BY-NC 4.0 License” (raw); `LICENSE` returns 404 at `main` and the API detects no SPDX licence (repo-meta/gh); the default weights are non-commercial | “1126 more languages” via MMS; es/ca/eu/gl all inside MMS coverage (readme) | claims ≥5× less memory than the torchaudio FA API; `<star>` token insertion for non-verbatim text; GPU path; needs FFmpeg (readme) | 556★ · pushed 2026-07-12 | Mature research |
| **torchaudio `MMS_FA` / `forced_align()`** | First-party CTC forced-alignment API and MMS bundle in PyTorch | [gh pytorch/audio](https://github.com/pytorch/audio) · [tutorial](https://docs.pytorch.org/audio/2.8/tutorials/ctc_forced_alignment_api_tutorial.html) | BSD-2-Clause API (repo-meta); the `MMS_FA` weights are the same CC BY-NC 4.0 MMS weights, fetched from Meta servers | MMS multilingual romanized token set; star-token variant for imperfect transcripts (search) | `torchaudio.pipelines.MMS_FA`, `bundle.get_model(with_star=False)`; emission-based Viterbi (search) | 2,933★ · pushed 2026-09-02 | Production (API); weights non-commercial |
| **Montreal Forced Aligner** (+ mfa-models) | Kaldi-based phone and word aligner with a pretrained model zoo | [gh MontrealCorpusTools/Montreal-Forced-Aligner](https://github.com/MontrealCorpusTools/Montreal-Forced-Aligner) · [gh MontrealCorpusTools/mfa-models](https://github.com/MontrealCorpusTools/mfa-models) · [model docs](https://montreal-forced-aligner.readthedocs.io/en/stable/user_guide/models/index.html) | aligner MIT; models CC BY 4.0 (repo-meta) | acoustic models `spanish/mfa` and `basque/cv` exist; no Catalan and no Galician acoustic model or dictionary in the model repo (gh directory listing) | the dictionary list likewise has spanish and basque only; needs a pronunciation dictionary or G2P per language (gh) | 1,876★ · pushed 2026-08-20 (aligner) · 200★ · pushed 2026-07-08 (mfa-models) | Production |
| **NeMo Forced Aligner (NFA)** | Token, word and segment timestamps from NeMo CTC ASR models | [gh NFA tool README](https://github.com/NVIDIA-NeMo/NeMo/blob/main/tools/nemo_forced_aligner/README.md) · [docs](https://docs.nvidia.com/nemo-framework/user-guide/latest/nemotoolkit/tools/nemo_forced_aligner.html) | Apache-2.0 (repo-meta) | 14+ languages via NeMo checkpoints; Basque via `HiTZ/stt_eu_conformer_ctc_large` (card not opened) | CTC models only, `per_feature` preprocessor required; documented on 1 h+ files; CTM and ASS outputs (search) | 18,376★ · pushed 2026-09-02 (NVIDIA-NeMo/Speech) | Production |
| **Qwen3-ForcedAligner-0.6B / LLM-ForcedAligner** | LLM slot-filling aligner: timestamp tokens as discrete indices, non-autoregressive | [gh QwenLM/Qwen3-ASR](https://github.com/QwenLM/Qwen3-ASR) · `HF: Qwen/Qwen3-ForcedAligner-0.6B` · [arXiv 2601.18220](https://arxiv.org/abs/2601.18220) · [vLLM support](https://docs.vllm.ai/en/latest/api/vllm/model_executor/models/qwen3_asr_forced_aligner/) | Apache-2.0 (repo-meta) | 11 languages claimed; the list was not confirmed, treat ca/eu/gl as unproven (search) | reports 69–78% relative reduction in accumulated averaging shift vs prior aligners; a long-form job “completes within 5 minutes” (search) | 3,460★ · pushed 2026-06-26 (QwenLM/Qwen3-ASR) | Research |
| **gentle** | Kaldi-based English forced aligner with a web UI | [gh strob/gentle](https://github.com/strob/gentle) (published in the brief as `lowerquality/gentle`) | MIT (repo-meta) | English only | still receives commits; superseded here by CTC aligners (gh) | 1,707★ · pushed 2026-07-24 | Mature research |
| **aeneas** | DTW alignment of audio against synthesized TTS of the text | [gh readbeyond/aeneas](https://github.com/readbeyond/aeneas) | AGPL-3.0 (repo-meta), copyleft | sentence and fragment level; language coverage tied to available TTS voices | good for coarse Diario fragment anchoring; not word-level (gh); used by OpenParliamentTV-Tools ES for sentence alignment (raw README) | 2,862★ · pushed 2026-07-25 | Mature research |
| **EuroSpeech pipelines** (`alignment_pipeline` + `download_pipeline` / `data_sourcing`) | Two-stage dynamic alignment for long-form audio against non-verbatim official transcripts, plus per-parliament scrapers, media retrieval, Opus normalisation and Supabase progress tracking | [gh SamuelPfisterer/EuroSpeech](https://github.com/SamuelPfisterer/EuroSpeech) · [arXiv 2510.00514](https://arxiv.org/abs/2510.00514) | licence “currently TBD” (local README); no licence file (repo-meta); blocking for reuse until clarified | 22 European parliaments (README); `data_sourcing/` lists 26 entries with no Spain (local copy) | the closest published match to the Congreso problem: segment → ASR → preprocess heterogeneous transcripts → two-stage align → CER filter; outputs 3–20 s segments with timestamps and per-segment CER; Slurm job scripts (readme) | 6★ · pushed 2026-03-24 | Research |
| **clarinsi/parlaspeech** | Bootstrapping ASR datasets from parliamentary recordings plus transcripts | [gh clarinsi/parlaspeech](https://github.com/clarinsi/parlaspeech) · [project site](https://clarinsi.github.io/parlaspeech/) | Apache-2.0 (repo-meta) | built for HR/CZ/PL/RS off ParlaMint; the method is language-portable | produces word-level alignments and carries ParlaMint speaker metadata through to segments (search) | 9★ · pushed 2026-07-15 | Research |
| **Europarl-ASR verbatimization tools** | Filtering and verbatimization of non-verbatim official transcripts | [gh mllpresearch/Europarl-ASR](https://github.com/mllpresearch/Europarl-ASR) · [MLLP mirror](https://www.mllp.upv.es/git-pub/ggarces/Europarl-ASR/) | NOASSERTION (repo-meta) | English and EP only; the verbatimization recipe transfers to the Diario de Sesiones | three transcript layers: official non-verbatim, noise-filtered, auto-verbatimized (search) | 6★ · pushed 2024-03-30 | Legacy |

WhisperX carries the most-used per-language alignment defaults for es/ca/eu/gl (§3.3);
lhotse, the container for “one 6-hour plenary → many speaker-attributed cuts”, is in
§3.11.

### 3.10 Scoring, evaluation and calibration tooling

Metrics and harnesses.
cpWER solves an optimal speaker permutation and therefore forgives exactly the error
that matters here: right words, wrong named MP. The scorers named here match the eval
harness tooling table in the evaluation inventory.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **fgnt/meeteval** | Meeting-transcription scorer; the modern reference | [gh fgnt/meeteval](https://github.com/fgnt/meeteval) · [arXiv 2307.11394](https://arxiv.org/abs/2307.11394) · [CHiME-2023 paper](https://www.isca-archive.org/chime_2023/neumann23_chime.pdf) · [PyPI meeteval](https://pypi.org/project/meeteval/) | MIT (repo-meta) | language-agnostic | cpWER, tcpWER (`--collar 5`), ORC and tcORC WER (+greedy), MIMO and tcMIMO, DI-cpWER, SISO WER, and DER by wrapping `md-eval-22.pl`; STM/CTM/RTTM/SegLST I/O; alignment visualizer; greedy polynomial approximations replace exponential search (readme/search) | 178★ · pushed 2026-08-11 | Production |
| **nryant/dscore** | The DIHARD-era DER scorer | [gh nryant/dscore](https://github.com/nryant/dscore) | BSD-2-Clause (repo-meta) | language-agnostic | DER, JER, B-cubed, GKT and mutual-information variants; RTTM and UEM in; bundles `md-eval-22.pl` (readme) | 273★ · pushed 2026-04-08 | Production |
| **NIST `md-eval-22.pl`** | The original NIST RT diarization scorer | [script in dscore](https://github.com/nryant/dscore/raw/master/scorelib/md-eval-22.pl) · [gh usnistgov/SCTK](https://github.com/usnistgov/SCTK) | NOASSERTION, NIST terms (repo-meta) | language-agnostic | DER with collar and overlap options (readme); still shipped inside the maintained dscore | 242★ · pushed 2023-11-27 (usnistgov/SCTK) | Legacy |
| **usnistgov/SCTK** | sclite, hubscr, asclite plus the STM and CTM format definitions MeetEval defers to | [gh usnistgov/SCTK](https://github.com/usnistgov/SCTK) | NOASSERTION, US-Government-style terms (repo-meta) | language-agnostic | the citation-grade WER scorer; `asclite` handles multi-speaker overlap (gh) | 242★ · pushed 2023-11-27 | Legacy |
| **pyannote/pyannote-metrics** | Diagnostic diarization and segmentation metrics; the only scorer here with named-identity scoring | [gh pyannote/pyannote-metrics](https://github.com/pyannote/pyannote-metrics) · [docs](http://pyannote.github.io/pyannote-metrics/) | MIT (repo-meta) | language-agnostic | modules confirmed present: `diarization.py`, `identification.py` (IdentificationErrorRate, fixed named labels, no optimal permutation), `spotting.py`, `detection.py`, `segmentation.py`, `binary_classification.py` (DET/EER), `errors/identification.py` (gh tree); purity and coverage also available | 259★ · pushed 2026-09-02 | Production |
| **pyannote/pyannote-database** | Reproducible protocol definitions over RTTM, UEM and LST for diarization corpora | [gh pyannote/pyannote-database](https://github.com/pyannote/pyannote-database) | MIT (repo-meta) | language-agnostic | protocol plumbing behind the pyannote benchmarks; the way to freeze benchmark splits so scores stay comparable (gh/readme) | 121★ · pushed 2026-03-01 | Production |
| **desh2608/spyder** | Fast C++-backed DER with Python bindings | [gh desh2608/spyder](https://github.com/desh2608/spyder) | MIT (repo-meta) | language-agnostic | DER only, without md-eval’s collar subtleties; an order of magnitude faster than dscore for sweeps (gh) | 35★ · pushed 2023-06-29 | Legacy |
| **jitsi/jiwer** | WER, CER, MER and WIL with transform pipelines | [gh jitsi/jiwer](https://github.com/jitsi/jiwer) | Apache-2.0 (repo-meta) | language-agnostic; you supply the normalisation | fast RapidFuzz backend; the default single-stream WER workhorse (gh); never for cpWER | 926★ · pushed 2026-04-16 | Production |
| **mjpost/sacrebleu** | Reproducible MT metrics with version strings | [gh mjpost/sacrebleu](https://github.com/mjpost/sacrebleu) | Apache-2.0 (repo-meta) | BLEU, chrF, TER; multilingual tokenizers | needed only if you score es↔ca/eu/gl interpretation output (gh) | 1,260★ · pushed 2026-08-20 | Production |
| **Unbabel/COMET** | Neural MT evaluation | [gh Unbabel/COMET](https://github.com/Unbabel/COMET) | Apache-2.0 (repo-meta) | multilingual; model checkpoints on HF | reference-based and reference-free (QE) variants (gh) | 778★ · pushed 2026-04-21 | Production |
| **facebookresearch/SimulEval** | Latency metrics for simultaneous speech (AL, LAAL, DAL) | [gh facebookresearch/SimulEval](https://github.com/facebookresearch/SimulEval) | CC BY-SA 4.0 (repo-meta) | streaming ST and ASR latency harness | still the reference definition of AL and LAAL; `--comp_unaware` gives the computation-unaware lower bound; vendor or reimplement rather than depend on an archive (gh) | 126★ · pushed 2024-09-13 · archived | Legacy |
| **Open ASR Leaderboard** | Reproducible harness and public leaderboard reporting WER and RTFx | [gh huggingface/open_asr_leaderboard](https://github.com/huggingface/open_asr_leaderboard) · [HF space](https://huggingface.co/spaces/hf-audio/open_asr_leaderboard) · [HF dataset](https://huggingface.co/datasets/hf-audio/open-asr-leaderboard) · [arXiv 2510.06961](https://arxiv.org/html/2510.06961v1) · [HF blog](https://huggingface.co/blog/open-asr-leaderboard) | Apache-2.0 (repo-meta) | three tracks: English, multilingual (de/fr/it/es/pt via CoVoST-2 + FLEURS), long-form; no ca/eu/gl track | 64 models / 57 open / 18 orgs as of Oct 2025 (search); top scores cited: ARK-ASR-3B 4.76, MOSS-Transcribe-preview-2B 4.87, Granite Speech 4.1 2B 5.33, Canary-Qwen 5.63, Granite 3.3 8B 5.85, so the top of the board spans under one WER point (search) | 245★ · pushed 2026-09-01 | Production |
| **Artificial Analysis AA-WER v2.0** | Duration-weighted WER over ~8 h, with separate streaming and non-streaming leaderboards | [methodology](https://artificialanalysis.ai/articles/aa-wer-v2) · [streaming](https://artificialanalysis.ai/speech-to-text/streaming) · [non-streaming](https://artificialanalysis.ai/speech-to-text/non-streaming) | proprietary; AA-AgentTalk is held out, so it is not reproducible | English-centric | 50% AA-AgentTalk (proprietary) + 25% VoxPopuli-Cleaned-AA + 25% Earnings22-Cleaned-AA (search); VoxPopuli is European Parliament audio, so 25% of this benchmark is in-domain for parliamentary training mixes | covers commercial APIs the Open ASR Leaderboard does not (search) (brief) | commercial, no label |
| **Parlamento-ai** (`open-source-asr`, `diff.parlamento.ai`, `MEPCO`) | Reproducible study of whether open ASR can run parliamentary transcription on own infrastructure, plus a block-vs-streaming latency comparison on one machine | [gh Parlamento-ai/open-source-asr](https://github.com/Parlamento-ai/open-source-asr) · [gh Parlamento-ai/diff.parlamento.ai](https://github.com/Parlamento-ai/diff.parlamento.ai) · [gh Parlamento-ai/MEPCO](https://github.com/Parlamento-ai/MEPCO) · `research/METHODOLOGY.md` | MIT on `open-source-asr`; no licence on the other two (repo-meta) | es, ca, gl, eu, en, pt and multilingual, this programme’s language set | 168 parliamentary audios / 13.79 h / 24 per group; open models reach the paid-API agreement band in es, ca, en, pt and multilingual, gl and eu do not; paid APIs differed 7.17% from each other on the same 72 audios; USD 1,334.68 for 1,869.37 h ≈ USD 0.71/h of paid transcription over 30 days (readme); these are text-agreement with paid APIs, not accuracy against a human reference (readme); all figures verified against the local README | 0★ · pushed 2026-08-07 (open-source-asr) · 1★ · pushed 2026-05-06 (diff.parlamento.ai) · 2★ · pushed 2026-03-26 (MEPCO) | Research |
| **chime-utils** | CHiME-8 DASR data prep, manifests and MeetEval-based scoring for CHiME-6, DiPCo, Mixer6 and NOTSOFAR-1 | [gh chimechallenge/chime-utils](https://github.com/chimechallenge/chime-utils) | MIT (evals inventory, gh) | English meetings | the closest thing to a turnkey SA-ASR scoring pipeline (evals inventory) | 27★ (evals inventory; not in repo-meta) | Research |

DOVER-Lap (diarization fusion) is in §3.4; net:cal, PYLLR and expected_cost (confidence
and LLR calibration) are in §3.5. The per-turn TAA and coverage-curve scorer, the
ECE-over-turns scorer, the finalization-lag and name-revision logger, and the
chair-announcement parser have no public implementation and are harness deliverables.

### 3.11 Data acquisition, preparation and annotation

Getting session media and official metadata in, and human labels out.
For named attribution the primary key is the chamber’s own open data (§5, official
sources), not a scraper and not an ASR vendor.

#### Ingest, manifests and storage

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **yt-dlp** | Media downloader | [gh yt-dlp/yt-dlp](https://github.com/yt-dlp/yt-dlp) | Unlicense (repo-meta) | handles the Canal Parlamento YouTube channel | Congreso streams live on [Canal Parlamento](https://www.youtube.com/@canalparlamento-congreso_es); the institution also publishes MP4s in its Archivo Audiovisual (search) | 188,537★ · pushed 2026-08-30 | Production |
| **FFmpeg / ffprobe** | Demux, resample, segment, probe | [gh FFmpeg/FFmpeg](https://github.com/FFmpeg/FFmpeg) | NOASSERTION (repo-meta); LGPL or GPL depending on the build | universal | required by ctc-forced-aligner and nearly every pipeline here (readme) | 63,888★ · pushed 2026-09-02 | Production |
| **mediamtx** | Media server for SRT, RTMP, WebRTC and HLS ingest | [gh bluenviron/mediamtx](https://github.com/bluenviron/mediamtx) | MIT (review) | protocol-level | none | 20,003★ (review API check; push date not collected) | Production |
| **LiveKit** | WebRTC SFU and media infrastructure | [gh livekit/livekit](https://github.com/livekit/livekit) | Apache-2.0 (review) | protocol-level | none | review API check; signals not collected | Production |
| **Janus Gateway** | WebRTC server | [gh meetecho/janus-gateway](https://github.com/meetecho/janus-gateway) | GPL-3.0 (review), copyleft | protocol-level | none | review API check; signals not collected | Production |
| **lhotse** | Corpus manifests, cuts, long-recording supervision handling, recipes | [gh lhotse-speech/lhotse](https://github.com/lhotse-speech/lhotse) · [voxpopuli recipe](https://lhotse.readthedocs.io/en/v1.24.2/_modules/lhotse/recipes/voxpopuli.html) | Apache-2.0 (repo-meta) | recipes incl. ami, ali_meeting, callhome_english, dihard3, fleurs, commonvoice, mls, notsofar1, voxceleb, voxconverse, voxpopuli, fisher_spanish, mtedx (gh); none for ParlamentParla, Basque Parliament, Nos_ParlaSpeech-GL, 3CatParla or EuroSpeech | supervision segments carry speaker ids natively (gh) | 1,149★ · pushed 2026-09-01 | Production |
| **NeMo Speech Data Processor (SDP)** | YAML-config dataset build pipelines | [gh NVIDIA/NeMo-speech-data-processor](https://github.com/NVIDIA/NeMo-speech-data-processor) | Apache-2.0 (repo-meta) | processors for public corpora incl. MCV and MLS-style sources | declarative and resumable; pairs with NFA (§3.9) (gh) | 218★ · pushed 2026-03-29 | Production |
| **Hugging Face `datasets`** | Audio dataset loading and streaming | [gh huggingface/datasets](https://github.com/huggingface/datasets) | Apache-2.0 (repo-meta) | audio decoding, streaming, Parquet | streaming mode matters for 50k-hour corpora (gh) | 21,888★ · pushed 2026-08-31 | Production |
| **WebDataset** | Tar-shard I/O for large-scale training | [gh webdataset/webdataset](https://github.com/webdataset/webdataset) | BSD-3-Clause (repo-meta) | format-agnostic | sequential-read shards for cluster or object storage (gh) | 3,175★ · pushed 2026-02-09 | Production |
| **faiss** | Vector search and clustering; only for archive-scale clustering when building enrolment from thousands of hours (350 centroids need no vector database) | [gh facebookresearch/faiss](https://github.com/facebookresearch/faiss) | MIT (review) | n/a | none | review API check; signals not collected | Production |

#### Legislature pipelines, open-data wrappers and standards

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **OpenParliamentTV-Tools** (`ES` implementation) | Merges the Congreso `intervenciones` open-data JSON (per-speech MP4, `ORADOR`, HH:MM timing) with the Diario HTML by Needleman-Wunsch on surname sequence; aeneas sentence alignment; Wikidata entity linking over 3,830 MPs | [gh OpenParliamentTV/OpenParliamentTV-Tools](https://github.com/OpenParliamentTV/OpenParliamentTV-Tools) (`optv/parliaments/ES/README.md`) · [gh OpenParliamentTV/OpenParliamentTV-Data-ES](https://github.com/OpenParliamentTV/OpenParliamentTV-Data-ES) | GPL-3.0 (review), copyleft | Spanish Congreso | ~95% match on substantive speeches; congreso.es returns 403 to bare requests; role-only chair turns (`La señora PRESIDENTA:`) cannot be matched; co-official interventions appear twice in the Diario (raw README via the review); the only open project found that aligns Congreso audio with transcripts end to end | review raw README read; signals not collected | Research |
| **politicalwatch/qhld-engine** | Qué hacen los diputados: Congreso activity engine | [gh politicalwatch/qhld-engine](https://github.com/politicalwatch/qhld-engine) | AGPL-3.0 (review), copyleft | Spanish Congreso | none | active (review API check); signals not collected | Research |
| **josiete/SenateGraphQA** | Senado Diario de Sesiones explorer | [gh josiete/SenateGraphQA](https://github.com/josiete/SenateGraphQA) | MIT (review) | Spanish Senado | none | review API check; signals not collected | Research |
| **cabreraalex/congreso-de-los-diputados** | Congreso data scraper | [gh cabreraalex/congreso-de-los-diputados](https://github.com/cabreraalex/congreso-de-los-diputados) | no licence (review) | Spanish Congreso | none | 2020 (review API check) | Legacy |
| **slopezmenend/civis-api** | Third-party wrapper over Congreso open data | [gh slopezmenend/civis-api](https://github.com/slopezmenend/civis-api) | no licence (repo-meta) | Congreso only | dead; read for schema notes only (gh) | 1★ · pushed 2022-07-28 | Legacy |
| **quehacen/que_hacen_api** | Deputies and votes API | [gh quehacen/que_hacen_api](https://github.com/quehacen/que_hacen_api) | GPL-3.0 (repo-meta) | Congreso votes | dead (gh) | 3★ · pushed 2014-12-14 | Legacy |
| **LinTO** (`linto-studio`, `linto-stt`, `linto-diarization`) | French public-sector transcription platform: studio, STT and diarization services | [gh linto-ai/linto-studio](https://github.com/linto-ai/linto-studio) | AGPL-3.0 (review), copyleft | French public sector; other languages via Whisper | none | review API check; signals not collected | Production |
| **aalto-speech/finnish-parliament-scripts** | Scripts behind the Finnish Parliament ASR corpus | [gh aalto-speech/finnish-parliament-scripts](https://github.com/aalto-speech/finnish-parliament-scripts) | GPL-3.0 (review) | Finnish | none | 2018 (review API check) | Legacy |
| **GAIK-project/fi-parliament-asr-benchmark** | Finnish parliament ASR benchmark | [gh GAIK-project/fi-parliament-asr-benchmark](https://github.com/GAIK-project/fi-parliament-asr-benchmark) | not recorded (review) | Finnish | none | 2026 (review API check) | Research |
| **davidilag/FPSC** | Faroese Parliament Speech Corpus tooling, 1,600 h | [gh davidilag/FPSC](https://github.com/davidilag/FPSC) | not recorded (review) | Faroese | 1,600 h (review) | 2026 (review API check) | Research |
| **clarin-eric/ParlaMint** (tooling) | Schema, validation and conversion tooling for parliamentary TEI corpora | [gh clarin-eric/ParlaMint](https://github.com/clarin-eric/ParlaMint) · [encoding docs](https://clarin-eric.github.io/ParlaMint/) | no SPDX licence on the repo (repo-meta); the corpora are CC-licensed | 29 countries and regions in v5.0 incl. ES, ES-CT, ES-GA, ES-PV (search) | defines the speaker and utterance metadata model to mirror for the benchmark (search) | 84★ · pushed 2026-03-31 | Production |
| **Akoma Ntoso tooling** | XML standard for parliamentary and legislative documents | [gh oasis-open/legaldocml-akomantoso](https://github.com/oasis-open/legaldocml-akomantoso) · [gh laws-africa/cobalt](https://github.com/laws-africa/cobalt) · [gh laws-africa/bluebell](https://github.com/laws-africa/bluebell) · [gh SenatoDellaRepubblica/AkomaNtosoBulkData](https://github.com/SenatoDellaRepubblica/AkomaNtosoBulkData) | OASIS NOASSERTION; cobalt NOASSERTION; bluebell GPL-3.0; Italian Senate data CC BY 4.0 (repo-meta) | AKN4EU is the EU profile (search) | Spain does not publish the Diario de Sesiones in AKN; the Italian Senate bulk repo is the live worked example (search) | 84★ · pushed 2022-06-02 (OASIS, Legacy by the push rule) · 27★ · pushed 2026-06-18 (cobalt) · 23★ · pushed 2026-07-13 (bluebell) · 39★ · pushed 2026-09-02 (Senate bulk data) | Mature research |

#### Annotation and transcript editing

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Label Studio** | Multi-type labeling with audio templates | [gh HumanSignal/label-studio](https://github.com/HumanSignal/label-studio) · [speaker segmentation template](https://labelstud.io/templates/speaker_segmentation) · [audio transcription template](https://labelstud.io/templates/transcribe_audio) · [ASR gallery](https://labelstud.io/templates/gallery_asr) | Apache-2.0 core; Enterprise is commercial (repo-meta/search) | wav, aiff, mp3, au, flac, m4a, ogg; region labels with start, end and a speaker label (search) | Enterprise adds an advanced audio interface and a multi-channel transcription template; the OSS audio UI is the weaker part (search) | 28,190★ · pushed 2026-09-02 | Production |
| **ELAN** | Tiered multimedia annotation (MPI Nijmegen) | [download](https://archive.mpi.nl/tla/elan/download) · [manual PDF](https://www.mpi.nl/corpus/manuals/manual-elan.pdf) | free, sources available, Java (search) | hierarchical tiers, one per speaker | desktop, XML (EAF) storage; strong for careful reference annotation, weak for crowd throughput (search) | not on GitHub (brief) | Production |
| **Praat** | Phonetics workbench with TextGrids | [gh praat/praat.github.io](https://github.com/praat/praat.github.io) (published in the brief as `praat/praat`) · [praat.org](https://www.fon.hum.uva.nl/praat/) | no SPDX detected (repo-meta); GPL in practice, verify | TextGrid is the interchange format MFA reads and writes | spot-checking alignment quality, not bulk labeling (gh) | 1,970★ · pushed 2026-08-28 | Production |
| **Audacity label tracks** | Editor with exportable label tracks | [gh audacity/audacity](https://github.com/audacity/audacity) | NOASSERTION (repo-meta); GPL family | simple `start\tend\tlabel` TSV export | lowest-friction rough segmentation; no speaker model (gh) | 17,687★ · pushed 2026-09-02 | Production |
| **Prodigy** | Commercial scriptable annotation with an `audio.manual` recipe | [prodi.gy](https://prodi.gy/) · [audio/video docs](https://prodi.gy/docs/audio-video) · [pricing](https://prodi.gy/buy) | commercial: one-time fee plus 12 months of upgrades; exact price not confirmed (search) | transcription, classification and speaker diarization regions with timestamps (search) | best fit for model-in-the-loop pre-labels from your own ASR and diarizer (search) | commercial, not on GitHub (brief) | commercial, no label |
| **Doccano** | Open-source annotation | [gh doccano/doccano](https://github.com/doccano/doccano) | MIT (repo-meta) | text and sequence only, no audio | usable for transcript-side speaker-tag correction, not audio regions (gh) | 10,762★ · pushed 2026-04-14 | Production |
| **audapolis** | Speaker-named transcript editor with audio | [gh bugbakery/audapolis](https://github.com/bugbakery/audapolis) | AGPL-3.0 (review), copyleft | n/a | none | 1,892★, active (review API check; push date not collected) | Mature research |
| **bbc/react-transcript-editor** | React transcript editor component | [gh bbc/react-transcript-editor](https://github.com/bbc/react-transcript-editor) | not recorded (review) | n/a | none | 620★ · pushed 2024-02 (review API check) | Legacy |

No open Hansard editor was found.

#### Synthetic test material (co-official TTS)

For LID, ITN, normalizer and chair-announcement parser tests on synthetic names; never
for speaker identification.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **aHoTTS** (HiTZ) | VITS voices for eu, gl, ca and es, downloaded from `HiTZ/TTS-*` | [gh hitz-zentroa/aHoTTS](https://github.com/hitz-zentroa/aHoTTS) | Apache-2.0 (review, raw README) | eu, gl, ca, es | none | review repo check; signals not collected | Research |
| **NOS-TTS-API** (Proxecto Nós) | Galician TTS API | [gh proxectonos/NOS-TTS-API](https://github.com/proxectonos/NOS-TTS-API) | GPL-3.0 (review), copyleft | gl | none | review repo check; signals not collected | Research |
| **langtech-bsc/Matcha-TTS** | Matcha-TTS fork; the Matxa Catalan multi-accent voices are HF-only (not opened) | [gh langtech-bsc/Matcha-TTS](https://github.com/langtech-bsc/Matcha-TTS) | MIT (review) | ca (multi-accent) | none | review repo check; signals not collected | Research |
| **projecte-aina/tts-api** | Catalan TTS API | [gh projecte-aina/tts-api](https://github.com/projecte-aina/tts-api) | MPL-2.0 (review), weak copyleft | ca | none | review repo check; signals not collected | Research |
| **piper** and **piper1-gpl** | Local neural TTS; the original is archived, the successor is GPL | [gh rhasspy/piper](https://github.com/rhasspy/piper) · [gh OHF-Voice/piper1-gpl](https://github.com/OHF-Voice/piper1-gpl) | original not recorded; successor GPL-3.0 (review) | multilingual voices | none | piper archived 2025-08 (review API check) | Legacy (piper); Research (piper1-gpl) |

### 3.12 Multimodal identity (ASD, face, OCR, VLM)

Using the chamber camera feed: who is visibly speaking, who they are, and what the name
overlay says. Licence and freshness are the binding constraints, not accuracy.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **TalkNet-ASD** | Audio-visual active speaker detection with long-term temporal features; the de facto ASD baseline with AVA prep and an arbitrary-video demo | [gh TaoRuijie/TalkNet-ASD](https://github.com/TaoRuijie/TalkNet-ASD) · [paper PDF](https://arxiv.org/pdf/2107.06592.pdf) | MIT (repo-meta) | audio + vision | ~92% mAP on AVA-ActiveSpeaker (ACM MM 2021) (search); weights in-repo (gh) | 498★ · pushed 2023-10-23 | Legacy |
| **LoCoNet_ASD** | Long-Short Context Network; more accurate than TalkNet | [gh SJTUwxz/LoCoNet_ASD](https://github.com/SJTUwxz/LoCoNet_ASD) | no licence (repo-meta) | audio + vision | LoCoNet + TalkNCE = 95.5% mAP on AVA val (search); weights on Google Drive, mirror before depending on them (readme) | 59★ · pushed 2023-05-01 | Legacy |
| **Light-ASD** | Lightweight, real-time-oriented ASD (CVPR 2023) with weights shipped in-repo | [gh Junhua-Liao/Light-ASD](https://github.com/Junhua-Liao/Light-ASD) | MIT (repo-meta) | audio + vision | mAP 94.06% on AVA val, weights in `weight/` (readme) | 188★ · pushed 2025-03-23 | Legacy |
| **TalkNCE** | Talk-aware contrastive loss layered onto TalkNet or LoCoNet training (ICASSP 2024) | [gh kaistmm/TalkNCE](https://github.com/kaistmm/TalkNCE) · [arXiv 2309.12306](https://arxiv.org/abs/2309.12306) | MIT (repo-meta) | audio + vision | 95.5% mAP on AVA val with LoCoNet (search) | 18★ · pushed 2025-04-30 | Legacy |
| **InsightFace** (ArcFace) | Face recognition against a member photo gallery | [gh deepinsight/insightface](https://github.com/deepinsight/insightface) | no licence field via the API (repo-meta); the README states code MIT but pretrained models and data are non-commercial research only, including the pip auto-downloads (readme) | vision | none | 29,625★ · pushed 2026-07-27 | Production (weights non-commercial) |
| **facenet-pytorch** | Face detection and recognition with VGGFace2 weights | [gh timesler/facenet-pytorch](https://github.com/timesler/facenet-pytorch) | MIT (review) | vision | none | pushed 2025-09-16 (review API check; stars not collected) | Mature research |
| **DeepFace** | Wrapper over multiple recognition backends plus attributes | [gh serengil/deepface](https://github.com/serengil/deepface) | MIT (repo-meta) | vision | none | 23,367★ · pushed 2026-09-01 | Production |
| **face-alignment** | 2D and 3D landmarks; face-crop preprocessing for ASD and face ID | [gh 1adrianb/face-alignment](https://github.com/1adrianb/face-alignment) | BSD-3-Clause (repo-meta) | vision | none | 7,536★ · pushed 2026-04-06 | Production |
| **PaddleOCR (PP-OCRv5)** | Strongest open option for reading on-screen chyrons and name overlays | [gh PaddlePaddle/PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) · [multilingual docs](https://www.paddleocr.ai/main/en/version3.x/algorithm/PP-OCRv5/PP-OCRv5_multi_languages.html) | Apache-2.0 (repo-meta) | 106 languages incl. Spanish; the Latin recogniser covers ca, gl and eu orthography (search) | >30% multilingual accuracy gain vs PP-OCRv3; slower and heavier than v4 (search) | 88,695★ · pushed 2026-07-22 | Production |
| **Tesseract** | Classic OCR; `spa`, `cat`, `glg`, `eus` traineddata exist | [gh tesseract-ocr/tesseract](https://github.com/tesseract-ocr/tesseract) | Apache-2.0 (repo-meta) | 100+ languages | weak on low-contrast video overlays (asserted, unverified) | 76,308★ · pushed 2026-09-02 | Production |
| **EasyOCR** | Ready-to-use OCR with a simple API | [gh JaidedAI/EasyOCR](https://github.com/JaidedAI/EasyOCR) | Apache-2.0 (repo-meta) | 80+ languages, all Latin scripts (gh) | none | 29,965★ · pushed 2025-12-05 | Production |
| **docTR** | Detection + recognition document OCR | [gh mindee/doctr](https://github.com/mindee/doctr) | Apache-2.0 (repo-meta) | Latin-script focused | none | 6,335★ · pushed 2026-09-01 | Production |
| **Surya** | OCR plus layout, reading order and tables | [gh datalab-to/surya](https://github.com/datalab-to/surya) | Apache-2.0 in the GitHub field (repo-meta); Surya has shipped restricted weight terms historically, verify | 90+ languages (gh) | none | 21,342★ · pushed 2026-08-21 | Production |
| **Qwen3-VL** (chyron reader, speaker-ID VLM) | Open VLM with the strongest surfaced OCR evidence; the layout-plus-text reasoning role from the Italian pipeline | `HF: Qwen/Qwen3-VL-*` · [arXiv 2511.21631](https://arxiv.org/pdf/2511.21631) | Apache-2.0 typical for Qwen3; verify per checkpoint (search) | multilingual incl. es | OCRBench 873 (Qwen3-VL-4B); OCRBench v2 EN 60.68 vs InternVL 56.0; Qwen2.5-VL-72B beat Gemini 1.5-Pro by 9.6% EN / 20.6% ZH on OCRBench (search); no video-frame OCR evidence | active family, weights open (search) (brief) | Mature research |

Excluded on purpose: InternVL and Gemma-3 vision, for which only document-OCR
comparisons surfaced and no video-frame OCR evaluation.

### 3.13 Conference-system integration

The chamber’s own microphone and seat database: the highest-value attribution signal
available, and the only one that yields names without voice biometrics.
Both are commercial systems; access is contractual, so neither takes a maturity label.

| Tool | What it does | Links | Licence | Languages / coverage | Key numbers | Signals | Label |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Televic CoCon** | Conference and meeting management with a third-party control API | [CoCon product page](https://www.televic.com/en/conference/products/meeting-management-software/cocon) · [parliaments](https://www.televic.com/en/conference/markets/parliaments) · [control protocol guide](https://manuals.plus/m/e4b32f39ab18a388953d977c4ba39f7919ee60808609d12c8f5469a962fc9c7a) | commercial; API for third-party control (search) | language-agnostic; participant and meeting database with XML meeting-data export (search) | seat and microphone activation tied to a named delegate record, giving near-perfect ground truth without voice biometrics (search) | vendor pages plus a third-party protocol guide (search) (brief) | commercial, no label |
| **Bosch DICENTIS** | IP conference system with a .NET API (and a RESTful API on CCS 1000 D) | [Bosch community: RESTful API on DICENTIS Wireless](https://community.boschsecurity.com/t5/Conference-Discussion/RESTful-API-programing-on-a-DICENTIS-Wireless-DCNM-WAP/ta-p/23615) · [wireless configuration manual PDF](https://cdn-docs.av-iq.com/other/DCNM-WCH05_Configuration_Manual.pdf) | commercial; API access under Bosch terms (search) | language-agnostic | up to 750 conference devices on one IP network; discussion-device seat and microphone state addressable; .NET API with secure login (search) | first-party API documentation not openly reachable; community post plus a third-party manual mirror (search) (brief) | commercial, no label |

### 3.14 How the cross-brief conflicts were resolved

The consolidated inventory recorded nine rows on which two briefs disagreed.
Each is settled here in favour of the review’s judgment where it gave one, and otherwise
by the same-day repository evidence.

1. **Sortformer family** (Mature research vs Production).
   Streaming `4spk-v2` is **Production**: WhisperLiveKit ships it as its recommended
   diarization backend, a third-party deployment read from the raw README. Offline
   `4spk-v1` stays **Mature research**: no third-party deployment was cited for it.
   Licence: v2 is CC BY 4.0 per its HF card (search, card not opened); the v2.1 mirrors
   and the joint brief’s “NVIDIA Open Model License” apply to v2.1 and v1, not v2. The
   four-speaker cap is per stream state, so the streaming row now says state must be
   reset per window.
2. **MOSS-Transcribe-Diarize** (Preview vs Mature research).
   **Mature research**: Apache-2.0, 1,805★ and the MLC-SLM win are in the local README
   and repo-meta. The ASR-only `-preview-2B` checkpoint stays a separate **Preview** row.
3. **Europarl-ASR** (Mature research vs Research vs Established).
   Tooling row **Legacy** (pushed 2024-03-30); the corpus moves to §5 with access “open
   (MLLP terms, verify)”.
4. **Parlamento-ai** (Research vs Preview).
   **Research**: code, data and method are published under MIT and every figure was
   verified against the local README; single author, no reproduction by others.
5. **spyder** (Mature research vs Research).
   **Legacy**: pushed 2023-06-29.
6. **PYLLR** (Mature research vs Research).
   **Legacy**: pushed 2023-02-21.
7. **ElevenLabs Scribe v2** (Production vs Production/Preview).
   Moved to §6 without a maturity label; the row states that batch diarizes and realtime
   does not.
8. **Whisper text normalizers** (Production vs Research).
   Split into two rows: the in-repo normalizers are **Production**, the standalone
   `whisper_normalizer` package (79★) is **Research**.
9. **SenseVoice** ("Production (elsewhere)" vs Production).
   **Production**, with the language limitation in the coverage cell; the off-scale
   qualifier is dropped.

Repository evidence also settled three licence readings: Dixtral is Apache-2.0
(repo-meta, against the joint brief’s empty field); InsightFace has no licence field via
the API while its README says MIT code with non-commercial models, and both readings are
kept; kiwano’s paper claims Apache-2.0 while the repository exposes none, so the row
says “none”.

## 4. Index from Research Direction to Toolchain

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

## 5. Datasets

Every dataset row from the consolidated inventory, plus the official sources and the
review’s additions, under the access scale of §1. Contamination notes say which models
or leaderboards have already seen the data.

### Official sources

| Dataset | Languages | Size | Speaker labels | Access | Licence | Contamination notes | Links |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Congreso de los Diputados open data** (diputados, intervenciones) and the Diario de Sesiones | es, with ca/eu/gl interventions since 2023 | current legislature; per-speech MP4 with `ORADOR` and HH:MM timing (OpenParliamentTV ES README, raw) | yes: deputy identity per intervention; the primary key for named attribution | open (official open data terms; CSV, JSON, XML) | official terms (search) | none known; the evaluation sessions must be excluded from enrolment | [portal](https://www.congreso.es/es/datos-abiertos) · [diputados](https://www.congreso.es/es/opendata/diputados) · [intervenciones](https://www.congreso.es/es/opendata/intervenciones) |
| **Senado open data** (senators, groups, initiatives) | es | Senate composition and activity | senator identity | open (official terms) | official terms (search) | none known | [portal](https://www.senado.es/web/relacionesciudadanos/datosabiertos/informaciodatosabiertos/index.html) |

### Parliamentary speech corpora

| Dataset | Languages | Size | Speaker labels | Access | Licence | Contamination notes | Links |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **VoxPopuli** | 23 languages; Spanish 166 h transcribed, 305 speakers, 1.6M tokens (search); interpretation subset 17.3k h over 15×15 directions, es as source ≈1.6k h, es as target ≈1.2k h (local README) | 400k h unlabelled | yes: manifests carry speaker ids | open | data CC0; code and pretrained models CC BY-NC 4.0 (local README §License); not a conflict | inside the Open ASR Leaderboard multilingual and long-form sets and 25% of AA-WER; treat as contaminated for held-out evaluation; code repo archived 2023-04-02 | [gh facebookresearch/voxpopuli](https://github.com/facebookresearch/voxpopuli) · `HF: facebook/voxpopuli` · [ACL 2021 PDF](https://aclanthology.org/2021.acl-long.80.pdf) |
| **Europarl-ASR** | English only, 1996–2020 | 1,300 h; 17.5 h with manually revised verbatim plus official transcripts in two evaluation partitions; text scalable to 170M tokens via DCEP | not the headline feature | open (MLLP terms, verify) | NOASSERTION on the repo (repo-meta); MLLP terms (search) | the right shape of benchmark, wrong language; code repo pushed 2024-03-30 | [gh mllpresearch/Europarl-ASR](https://github.com/mllpresearch/Europarl-ASR) · [MLLP mirror](https://www.mllp.upv.es/git-pub/ggarces/Europarl-ASR/) · [Interspeech 2021](https://www.isca-archive.org/interspeech_2021/garcesdiazmunio21_interspeech.html) |
| **Europarl-ST** | 6 languages, 30 directions, incl. Spanish; 2008–2012 EP debates | speech translation corpus | not stated | open | Creative Commons, freely downloadable (search) | relevant only if interpretation is scored | [arXiv 1911.03167](https://arxiv.org/abs/1911.03167) |
| **EuroSpeech** | 19 languages >1k h, 22 >500 h; 22 European parliaments; Spanish coverage not confirmed (`data_sourcing/` lists 26 entries with no Spain, local copy) | 50.5k h at CER<20% (paper abstract), “over 61,000 hours” (readme); segments 3–20 s with CER scores | not stated | open (dataset released publicly) | licence “currently TBD” (local README); no licence file on the repo | +41.8% average WER reduction fine-tuning on 6 low-resource languages (readme); NeurIPS 2025 Spotlight | [gh SamuelPfisterer/EuroSpeech](https://github.com/SamuelPfisterer/EuroSpeech) · [arXiv 2510.00514](https://arxiv.org/abs/2510.00514) · [NeurIPS 2025 D&B poster](https://neurips.cc/virtual/2025/loc/san-diego/poster/121862) |
| **ParlaSpeech 3.0** | HR, CZ, PL, RS | ~6,000 h total, word-level alignments | yes: name, party, gender, age, role on 381,849 segments = 95% of all segments (search) | open | CLARIN.SI, CC-family (search) | the closest existing speaker-attributed parliamentary ASR benchmark | [CLARIN.SI](https://www.clarin.si/repository/xmlui/handle/11356/1833) · [arXiv 2511.01619](https://arxiv.org/html/2511.01619v1) · [site](https://clarinsi.github.io/parlaspeech/) · `HF: classla/ParlaSpeech-RS`, `-PL` |
| **ParlaMint 5.0** (ES, ES-CT, ES-GA, ES-PV) | Spanish national plus three autonomous-region corpora | raw ES-CT 66.79 MB, ES-GA 76.87 MB, ES-PV 61.3 MB; `.ana` versions 830–935 MB; 29 countries and regions (search) | yes: TEI speaker metadata; text only, no audio | open | Creative Commons via CLARIN.SI (search) | the metadata backbone for attribution | [CLARIN.SI 4.0](https://www.clarin.si/repository/xmlui/handle/11356/1859) · [docs](https://clarin-eric.github.io/ParlaMint/) |
| **ParlamentParla** | Catalan | v1.0 320 h (90 clean + 230 other); v2.0 211 h clean + 400 h other (search) | yes: v2.0 adds speaker ids with gender and speaker-disjoint splits (local README) | open | CC BY 4.0 (search); code repo NOASSERTION, pushed 2021-10-21 | the Catalan adaptation set; no known model contamination recorded | [OpenSLR 59](https://www.openslr.org/59/) · [gh CollectivaT-dev/ParlamentParla](https://github.com/CollectivaT-dev/ParlamentParla) · `HF: projecte-aina/parlament_parla` · [LREC/ParlaCLARIN 2022](https://aclanthology.org/2022.parlaclarin-1.18/) |
| **basque_parliament_1** | eu, es, bilingual segments | 1,462 h, 759,192 segments, 2013–2022; dev 7.6 h + test 9.6 h manually audited (search) | yes: speaker and language labels | open | CC0-1.0 (search; HF card) | `xezpeleta/whisper-large-v3-eu` trains on it: a contamination source for any Basque parliamentary benchmark | `HF: gttsehu/basque_parliament_1` · [paper (Appl. Sci.)](https://doi.org/10.3390/app14051951) |
| **BasqueParl** | eu, es; 2012–2020, two terms | text corpus with NER and lemmas | yes: speaker language, gender, party | open (repo NOASSERTION; verify) | NOASSERTION (repo-meta) | pairs with basque_parliament_1 audio for named attribution; code repo pushed 2024-03-08 | [gh ixa-ehu/basqueparl](https://github.com/ixa-ehu/basqueparl) · [LREC 2022](https://aclanthology.org/2022.lrec-1.361/) · [arXiv 2205.01506](https://arxiv.org/abs/2205.01506) |
| **Nos_ParlaSpeech-GL** | Galician, sessions 2015–2022 | >1,600 h, automatically aligned (search) | not stated | restricted | the Galician Parliament’s terms of use, not a standard open licence (search; HF card) | Proxecto Nós’s Galician fine-tunes have seen it | `HF: proxectonos/Nos_Parlaspeech-GL` · [gh proxectonos/corpora](https://github.com/proxectonos/corpora) |
| **Nos_TranscriSpeech-GL** | Galician | 53 h across conferences, debates, speeches and interviews (search) | not stated | gated (request plus terms acceptance) | on request (search) | the human-verified Galician evaluation set | [Zenodo record](https://zenodo.org/records/7717140) |
| **3CatParla** | Catalan | 710 h (search); 731 h 21 m manually transcribed per the IberSPEECH 2024 paper | not stated | not confirmed (corpus release status unclear) | model Apache-2.0; corpus licence not confirmed (search) | broadcast television despite the name; keep it out of any parliamentary evaluation set; `projecte-aina/whisper-large-v3-ca-3catparla` trains on it | `HF: projecte-aina/whisper-large-v3-ca-3catparla` (model page) · [IberSPEECH 2024](https://www.isca-archive.org/iberspeech_2024/hernandezmena24_iberspeech.pdf) |
| **Albayzin 2024 BBS-S2T** | eu, es bilingual | not stated | not stated | not confirmed (HF card not opened) | not confirmed | evaluation set of the Albayzin 2024 Basque–Spanish task; PRHLT system in §3.3 | `HF dataset: gttsehu/Albayzin-2024-BBS-S2T` · [gh david-gimeno/prhlt-bbs-s2tc](https://github.com/david-gimeno/prhlt-bbs-s2tc) |
| **Finnish Parliament ASR corpus** | Finnish | 3,087 h, 449 speakers (search) | yes | open (Kielipankki) | open (search) | methodological comparator | [arXiv 2203.14876](https://arxiv.org/pdf/2203.14876) · [LRE journal](https://dl.acm.org/doi/abs/10.1007/s10579-023-09650-7) |
| **Althingi Parliamentary Speech** | Icelandic, 2005–2016 | 542 h, 197 speakers, with dictionary and LMs (search) | yes | paid (LDC membership or fee) | LDC (search) | none recorded | [LDC2021S01](https://catalog.ldc.upenn.edu/LDC2021S01) · `HF: language-and-voice-lab/althingi_asr` |
| **FT Speech** | Danish | 1,857 h, 434 speakers (search) | yes | open (research release; terms not read) | research release (search) | none recorded | [arXiv 2005.12368](https://arxiv.org/pdf/2005.12368) |
| **FalAR** | European Portuguese, ~20 years | 5,800 h total; 4,850 h with speaker identity (search) | yes: 1,180 speakers with age, gender, party and parliamentary role | not confirmed | not confirmed (search) | the closest published analogue to the intended Congreso benchmark; 2026 preprint, no repo | [arXiv 2605.27062](https://arxiv.org/abs/2605.27062) |
| **SloPal / SloPalSpeech** | Slovak, 2001–2024 | 330k speaker-segmented transcripts, 66M words; SloPalSpeech 2,806 h in ≤30 s segments (search) | yes (speaker-segmented) | not confirmed | not confirmed (search) | anchor-based alignment pipeline, an alternative to EuroSpeech’s two-stage method | [arXiv 2509.19270](https://arxiv.org/abs/2509.19270) |
| **FPSC** (Faroese Parliament Speech Corpus) | Faroese | 1,600 h (review) | not recorded | not confirmed | not recorded | 2026 release | [gh davidilag/FPSC](https://github.com/davidilag/FPSC) |

### General speech, meeting and speaker corpora

| Dataset | Languages | Size | Speaker labels | Access | Licence | Contamination notes | Links |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **BSCs_Code_Switching_CA-ES_ASR_Test** | ca–es code-switching | 867 clips / 4 h 09 m, manually curated (search) | not stated | not verified (HF dataset card not opened) | not verified | the only purpose-built Catalan–Spanish code-switching eval set found; paired with `BSC-LT/whisper-timestamped-cs` | `HF dataset: BSC-LT/BSCs_Code_Switching_CA-ES_ASR_Test` · [arXiv 2507.13875](https://arxiv.org/abs/2507.13875) |
| **Common Voice** | 250+ languages; Catalan among the top five by hours; es, eu, gl present | >13,000 h total; per-language validated hours for v22 not confirmed (search) | anonymous client ids | open | CC0 (search) | read speech, a weak proxy for plenary audio; CV13, CV17 and CV18 train several §3.3 specialists | [commonvoice.mozilla.org](https://commonvoice.mozilla.org) · `HF: fsicoli/common_voice_22_0` |
| **FLEURS** | 102 languages incl. Spanish (`es_419`, Latin American); Basque and Galician presence should be re-checked | ~12 h per language, ~1.4k h total (search) | no | open | CC BY (search) | the Open ASR Leaderboard multilingual track uses it | `HF: google/fleurs` · [XTREME-S paper PDF](https://arxiv.org/pdf/2203.10752) |
| **MLS (Multilingual LibriSpeech)** | 8 languages; Spanish 917.68 h train (search) | >50,000 h total | book readers | open | CC BY 4.0 (search) | read speech; es acoustic pretraining only | `HF: facebook/multilingual_librispeech` · [paper PDF](https://arxiv.org/pdf/2012.03411) |
| **OpenSLR 61, 71–75** | Latin American Spanish accents: Argentine, Chilean, Colombian, Peruvian, Puerto Rican, Venezuelan | read speech (not opened) | not recorded | open (OpenSLR; pages not opened) | not recorded | accent-split evaluation for the LatAm hedge | [openslr.org/71](https://www.openslr.org/71/) |
| **RTVE / Albayzin (IberSPEECH-RTVE)** | Spanish broadcast | RTVE2018: 569 h 22 min audio, ~460 h with subtitles, ~109 h human transcribed, ~37 h with diarization references; 2020, 2022, 2024 editions (search) | yes, incl. an identity-assignment task | restricted (agreement with Cátedra RTVE–Universidad de Zaragoza; “publicly accessible although not necessarily free”) | agreement (search) | the only Spanish benchmark with an explicit speaker-identity-assignment task | [SD 2022 eval plan PDF](https://catedrartve.unizar.es/reto2022/SDIAC2022_Evalplan.pdf) · [Albayzin 2018 paper](https://doi.org/10.3390/app9245412) |
| **AMI Meeting Corpus** | English | ~100 h, multimodal | yes | open | CC BY 4.0 (search) | the diarization baseline behind most §3.4 DER numbers; wrong domain and language | [groups.inf.ed.ac.uk/ami](https://groups.inf.ed.ac.uk/ami/corpus/) |
| **NOTSOFAR-1** | English | 315 meetings, 4–8 speakers, 32 participants, 30 rooms; ~170 h single-channel + ~130 h multi-channel; +1,000 h simulated (search) | yes | gated (own DATA_LICENSE plus an HF token) | code MIT (repo-meta); data under DATA_LICENSE (gh/search) | multi-mic far-field, closer to a chamber array than AMI; DiCoW and Dixtral train and evaluate on it; code repo pushed 2025-02-12 | [gh microsoft/NOTSOFAR1-Challenge](https://github.com/microsoft/NOTSOFAR1-Challenge) · [DATA_LICENSE](https://github.com/microsoft/NOTSOFAR1-Challenge/blob/main/DATA_LICENSE) · [arXiv 2401.08887](https://arxiv.org/abs/2401.08887) · `HF: microsoft/NOTSOFAR` |
| **DIHARD III** | 11 domains | 34.15 h train / 33.01 h eval (search) | yes | paid (LDC) | LDC (search) | DiariZen’s training set, which is why its weights are non-commercial | [LDC / Interspeech paper PDF](https://www.ldc.upenn.edu/sites/default/files/interspeech21-dihard.pdf) |
| **VoxCeleb2 / VoxBlink2** | multilingual YouTube; VoxBlink2 18 languages | VoxCeleb2 ~1M clips / >2,000 h; VoxBlink2 9,904,382 utterances, 111,284 speakers, 16,672 h (search) | yes (celebrity identities) | open (VoxCeleb2); not confirmed (VoxBlink2) | VoxCeleb CC BY 4.0 (search); VoxBlink2 not confirmed | every embedding EER in §3.5 is measured here; WeSpeaker weights inherit the licence | [VoxBlink2 arXiv 2407.11510](https://arxiv.org/pdf/2407.11510) · [VoxSRC paper PDF](https://arxiv.org/pdf/2012.06867) |
| **AVA-AVD** | English video | 243 train / 54 val / 54 test clips = 20.25 / 4.5 / 4.5 h (search) | yes | open (research release) | research release (search) | the ASD mAP figures in §3.12 come from AVA-ActiveSpeaker | [arXiv 2111.14448](https://arxiv.org/pdf/2111.14448) |
| **REPERE** | French TV video, campaigns 2012–2014 | multimodal person annotation | yes: named persons from audio, video and OCR | paid (ELRA) | ELRA catalogue (search) | the historic precedent for named person recognition | [LREC 2012](https://aclanthology.org/L12-1410/) |
| **EPIC** | source it, en, es plus interpreted targets | 357 speeches, 177,295 words; video of source, audio of interpretation (search) | not stated | paid (ELRA) | ELRA (search) | only relevant if interpretation channels are in scope; VoxPopuli’s S2S subset is the free alternative | [ELRA-S0323](https://catalogue.elra.info/en-us/repository/browse/ELRA-S0323/) · [LREC 2006](https://aclanthology.org/L06-1093/) |

## 6. Services and APIs

Hosted ASR, diarization and enrolment-based identification, plus the incumbents already
transcribing Spanish chambers.
Every price is third-party and drifts; treat each as a starting point for a quote.
No maturity labels: the access class is open for a pay-as-you-go API, restricted where a
sales or eligibility gate applies, and retired where the service has closed.

| Service | Languages (ca/eu/gl?) | Enrolment / named-speaker support (caps) | Realtime diarization | Price | Notes | Links |
| --- | --- | --- | --- | --- | --- | --- |
| **Speechmatics** | 55+ languages including Catalan, Basque and Galician; realtime supported (search) | enrolment from 5–30 s of clean single-speaker audio; max 50 speaker identifiers per session, an order of magnitude short of ~350 MPs, so candidates must be pre-filtered per sitting; `max_speakers` (2–20) applies only to non-enrolled speakers; reserved labels UU/S1/S2 forbidden. That enrolment-based ID is offered in ca/eu/gl in realtime is unverified | yes | ~$0.0050/min batch, ~$0.0067/min realtime, and a “from $0.129/hr” Pro figure elsewhere; third-party price figures conflict (search) | cloud plus on-prem container; the only major vendor pairing named enrolment, realtime and all four languages, with the hedge above | [realtime diarization](https://docs.speechmatics.com/speech-to-text/realtime/realtime-diarization) · [realtime speaker ID](https://docs.speechmatics.com/speech-to-text/realtime/speaker-identification) · [batch speaker ID](https://docs.speechmatics.com/speech-to-text/features/speaker-identification) · [languages](https://www.speechmatics.com/languages) · [pricing](https://www.speechmatics.com/pricing) |
| **pyannoteAI `precision-2`** (hosted or self-host pipeline) | language-agnostic, no per-language model | via voiceprints below | no (batch) | see the service row below | DER%: AMI-SDM 15.6 · AMI-IHM 12.9 · DIHARD3-full 14.7 · CALLHOME-p2 16.6 · RAMC 10.5 · VoxConverse 8.5 (readme); 14 s per audio hour, 2.2–2.6× faster than community-1 (search); self-published table naming corpora; closed weights | [docs.pyannote.ai/models](https://docs.pyannote.ai/models) · [pyannote.ai/blog](https://www.pyannote.ai/blog) |
| **pyannoteAI `Live-1`** | language-agnostic | not stated | yes, WebSocket, STT-agnostic | not stated | vendor claim of sub-300 ms latency, launched 2026-07; no public DER table found (the 11.2% sometimes attributed to Live-1 is community-1’s VoxConverse figure in the pyannote README) | [blog: introducing Live-1](https://www.pyannote.ai/blog/introducing-live-1-streaming-diarization) · [streaming benchmark](https://www.pyannote.ai/blog/streaming-diarization-benchmark) |
| **pyannoteAI** (service, voiceprints) | language-agnostic | voiceprint enrolment and identification on top of precision-2; enrolment clip ≤30 s, single speaker, clean; trial 150 h + 10 voiceprints; no published hard cap on paid tiers; voiceprint job outputs deleted after 24 h, voiceprints reusable (search) | via Live-1 (identification requires precision-2) | precision-2 €0.112/hr (Developer) / €0.096/hr (Starter); Developer €19/mo up to 125 h; Starter €99/mo up to 825 h with 3 concurrent jobs (search) | Enterprise self-host; the cheapest credible named-speaker enrolment; backed by the pyannote OSS stack | [pricing](https://www.pyannote.ai/pricing) · [models](https://www.pyannote.ai/md/models) · [identification tutorial](https://docs.pyannote.ai/tutorials/identification-with-voiceprints) · [recurring-meetings blog](https://www.pyannote.ai/blog/speaker-identification-system-recurring-meetings) |
| **AssemblyAI** | multilingual; ca/eu/gl not confirmed | none surfaced | yes (Universal-3.5 Pro Realtime) | streaming $0.45/hr; async diarization +$0.02/hr (search) | diarization only | [pricing blog](https://www.assemblyai.com/blog/speech-to-text-api-pricing) · [realtime models](https://www.assemblyai.com/blog/best-api-models-for-real-time-speech-recognition-and-transcription) |
| **Deepgram** (Nova-3) | Nova-3 Multilingual; ca/eu/gl not confirmed | none | yes | streaming ~~$0.0077/min (~~$0.46/hr); diarization +$0.0020/min (~$0.12/hr); Nova-3 Multilingual + diarization ≈ $0.67/hr (search) | diarization only | [vendor comparison](https://deepgram.com/learn/deepgram-vs-speechmatics-vs-assemblyai) |
| **ElevenLabs Scribe v2 / v2 Realtime** | 99 languages batch, 90+ realtime (search) | none | no: realtime (~150 ms) runs without diarization; realtime diarization is explicitly not a priority per the FAQ and dual-channel is not planned (search/readme) | batch $0.22/hr; realtime $0.39/hr (search) | AA-WER v2 2.3%, ranked first (Feb 2026), but cpCER 37.95 / 36.69 / 11.34 / 17.85, so diarization is far weaker than WER; batch supports up to 48 speakers | [Scribe v2 blog](https://elevenlabs.io/blog/introducing-scribe-v2) · [speech-to-text](https://elevenlabs.io/speech-to-text) · [realtime](https://elevenlabs.io/realtime-speech-to-text) · [capabilities docs](https://elevenlabs.io/docs/overview/capabilities/speech-to-text) · [API pricing](https://elevenlabs.io/pricing/api) |
| **OpenAI `gpt-4o-transcribe-diarize`** | multilingual; language list not confirmed for ca/eu/gl | `known_speaker_names` / `known_speaker_references` from 2–10 s clips; max 4 enrolled speakers, unusable for a 350-seat chamber | no (Transcription API only, batch-oriented) | $2.50 per M audio input tokens, $10 per M output tokens; no separate diarization fee (search) | `diarized_json` required for speaker turns; 16k context; Movies cpCER 23.67 vs MOSS 12.76 (search/readme) | [model docs](https://developers.openai.com/api/docs/models/gpt-4o-transcribe-diarize) · [STT guide](https://developers.openai.com/api/docs/guides/speech-to-text) |
| **Gemini 3.5 Transcribe** | broad multilingual, custom vocabulary biasing (search) | none | no: `-live` streams without diarization | not recorded | batch: ≤8 speakers (≥3 experimental); ≤30 min with diarization, ≤1 h without; Gemini 3 Pro cpCER 27.43 / 32.84 / – / 14.73 (readme/search) | [transcribe docs](https://ai.google.dev/gemini-api/docs/transcribe) · [model page](https://ai.google.dev/gemini-api/docs/models/gemini-3.5-transcribe) |
| **Google Cloud STT v2 (Chirp 3)** | ca-ES is GA; 24 GA + 77+ preview languages; eu-ES and gl-ES not confirmed (search) | none | diarization and LID, batch and streaming per docs (not verified per mode) | ~~$0.016/min standard (~~$0.96/hr); dynamic batch ~$0.18/hr; volume tiers to $0.008/min; 60 free min/month (search) | V2-only model | [Chirp 3 docs](https://docs.cloud.google.com/speech-to-text/docs/models/chirp-3) · [supported languages](https://docs.cloud.google.com/speech-to-text/docs/speech-to-text-supported-languages) · [pricing](https://cloud.google.com/speech-to-text/pricing) |
| **Google Cloud Speaker ID (CCAI)** | contact-centre scoped, not a general STT feature | verification against enrolled voiceprints; no public enrolment-limit documentation (search) | n/a | not recorded | access: restricted availability | [coverage article](https://siliconangle.com/2021/10/01/speaker-id-callers-can-now-use-voice-authenticate/) |
| **Microsoft Azure Speech (STT)** | broad; ca/eu/gl not confirmed | none (speaker recognition retired, below) | yes (STT diarization unaffected by the retirement) | not recorded | none | [release notes](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/releasenotes) |
| **Azure AI Speaker Recognition** | n/a | former speaker verification and identification API | n/a | n/a | access: retired 2025-09-30; APIs no longer accessible (search) | [retirement notice](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/releasenotes) · [migration analysis](https://picovoice.ai/blog/microsoft-azure-ai-speaker-recognition-alternatives/) |
| **Amazon Transcribe** | streaming in Catalan, Basque and Galician among 54 streaming languages, the best language coverage of any hosted vendor here (search) | none | yes: “speaker partitioning” for batch and streaming, included in the base price | standard $0.024/min ($1.44/hr), per-second billing with a 15 s minimum (search) | anonymous speakers only | [developer guide PDF](https://docs.aws.amazon.com/pdfs/transcribe/latest/dg/transcribe-dg.pdf) · [streaming languages announcement](https://aws.amazon.com/about-aws/whats-new/2024/10/amazon-transcribe-streaming-transcription-additional-languages) |
| **Amazon Connect Voice ID** | n/a | historic limits 100,000 speakers per domain, 10,000 per batch job, max 3 watchlists | n/a | n/a | access: retired; closed to new customers 2025-05-20, end of support 2026-05-20 (search) | [end-of-support notice](https://docs.aws.amazon.com/connect/latest/adminguide/amazonconnect-voiceid-end-of-support.html) · [quotas](https://awsfundamentals.com/limits/voiceid) |
| **Gladia** (Solaria-1) | 100+ languages with native code-switching, relevant for es/ca and es/eu (search) | none | yes (diarization included) | $0.61/hr async, $0.75/hr realtime; 10 free hours per month (search) | none | [pricing and comparison](https://www.gladia.io/blog/best-speech-to-text-apis) |
| **Soniox** | 60+ languages (search) | none | yes | $0.10/hr async, $0.12/hr streaming with diarization, LID and formatting bundled, the cheapest streaming-plus-diarization rate found (search) | verify quality on es/ca/eu/gl | [pricing](https://soniox.com/pricing) · [models](https://soniox.com/docs/stt/models) |
| **Rev.ai / Reverb** | 57 languages (search) | speaker ID priced separately and sales-gated (search) | not stated | Reverb ASR $0.02/min; API $0.035/min; human $1.99/min (search) | human transcription tier; the open Reverb ASR model repo is Apache-2.0, 437★, pushed 2025-04-22 (Legacy by the push rule) | [Rev pricing](https://support.rev.com/hc/en-us/articles/18893487380365-Pricing) · [gh revdotcom/reverb](https://github.com/revdotcom/reverb) |
| **Picovoice Eagle + Falcon** (on-device) | language-agnostic voice biometrics; Linux, macOS, Windows, Android, iOS, web, Raspberry Pi (search) | local voiceprints; vendor states no fixed limit on loaded voiceprints but publishes no EER (search) | Falcon on-device diarization; vendor-run DER 10.3% vs Amazon Transcribe 11.1% and Azure 15.7% with no stated dataset or protocol (search) | free tier Falcon 250 min/month, Eagle 100 min/month; paid tiers 25K / 10K min/month (search) | Apache-2.0 bindings, proprietary models, AccessKey-gated (repo-meta/gh); the on-prem answer to the retired Azure service | [gh Picovoice/eagle](https://github.com/Picovoice/eagle) · [gh Picovoice/falcon](https://github.com/Picovoice/falcon) · [product page](https://picovoice.ai/products/voice/speaker-recognition/) · [cookbook](https://picovoice.ai/cookbook/speaker-identification-across-meetings/) · [pricing](https://picovoice.ai/pricing/) · [Falcon docs](https://picovoice.ai/docs/falcon/) |
| **Vocapia VoxSigma** | Spanish yes; ca/eu/gl not confirmed | none stated | not stated | no public per-hour price (search) | broadcast and parliamentary heritage; commercial licence, on-prem | [vocapia.com](https://www.vocapia.com/) · [subtitling](https://www.vocapia.com/subtitling2.html) |
| **Pangeanic** | Spanish plus Valencian and Catalan parliamentary deployments (search) | none stated | not stated | not public | AI transcription deployed at the Spanish Parliament and Les Corts; the incumbent to benchmark against; no public accuracy figures | [announcement](https://blog.pangeanic.com/pangeanic-will-implement-ai-transcription-technology-in-spanish-parliament) · [Les Corts use case](https://pangeanic.com/use-cases/valencian-parliament-transcription) |
| **Etiqmedia (Ágora)** | Spanish regional parliaments; realtime and recorded (search) | none stated | realtime transcription with a human review workflow (search) | not public | regional incumbent (Parliament of Cantabria) | [case study](https://tech4access.com/en/etiqmedia-parliament-of-cantabria/) |
| **Verbit** | courts, legislatures, education; ca/eu/gl not confirmed | none stated | not stated | not public | human-in-the-loop is the product, not the model (search) | [verbit.ai](https://verbit.ai/) |

NVIDIA Riva / ASR NIM (deployable on-prem microservices) is in §3.3 under runtimes.

## 7. Avoid on the Merits, and Terms to Track

The short list from §6.4 of the agenda, then the complete register of non-commercial
weights, unlicensed repositories, non-OSI model terms and archived dependencies, so the
reasons survive edits to the tables.

### What to avoid, on the merits

Nothing is listed here for its licence.
A row is here because the tool does not exist, does not run, does not cover the
languages, or would corrupt a measurement.

| Item | Why | Use instead |
| --- | --- | --- |
| Speechmatics, pyannoteAI or OpenAI enrolment as the identifier | caps of 50, unpublished, and 4 enrolled speakers; vendor lock | a roster matcher on WeSpeaker or ReDimNet2; keep one vendor as a comparison row |
| Azure AI Speaker Recognition | retired 2025-09-30; APIs no longer accessible (search) | the open stack above; Picovoice Eagle if on-device closed weights are acceptable |
| Amazon Connect Voice ID | closed to new customers 2025-05-20; end of support 2026-05-20 (search) | same |
| WeSep, USEF-TSE, LExt for R5 | no licence and no released weights; NOASSERTION and Legacy; no code | reimplement in WeSep’s architecture set; SepFormer and TF-GridNet as blind baselines |
| Kyutai STT; Canary, Parakeet, Nemotron for the co-official tail | en and fr only; no ca/eu/gl | SimulStreaming; LoS, BSC, HiTZ and Nós specialists |
| `xezpeleta/whisper-large-v3-eu` as an evaluation model | trained on Basque Parliament plenary audio; no temporal hold-out | `HiTZ/whisper-large-v3-eu` (Common Voice 13) with a held-out period |
| 3CatParla as parliamentary data | broadcast television despite the name | ParlamentParla v2 |

### Retired services

| Service | Status | Source |
| --- | --- | --- |
| Azure AI Speaker Recognition | retired 2025-09-30; APIs no longer accessible; remove from any design and from the agenda’s references | Microsoft release notes (search) |
| Amazon Connect Voice ID | closed to new customers 2025-05-20; end of support 2026-05-20 | AWS end-of-support notice (search) |
| Google Cloud Speaker ID (CCAI) | restricted availability, contact-centre scoped, no public limits; not retired but not procurable on demand | coverage article (search) |

### Non-commercial or non-OSI weights: usable here, tracked for later

Every row below is **admissible for this programme** (research, benchmarking, demos) and
is selected on performance like any other tool.
The note says only what a productisation would have to change.

| Item | Terms | Note for later |
| --- | --- | --- |
| DiariZen weights (`BUT-FIT/diarizen-*`) | CC BY-NC 4.0, inherited from RAMC, MSDWild and DIHARD-3 (README) | use it; DiCoW’s bundled pipeline inherits the terms, so feed DiCoW pyannote masks if productised |
| Meta MMS, wav2vec2-XLS-R, `mms-lid-*`, and the `MMS_FA` aligner weights behind torchaudio and ctc-forced-aligner | CC BY-NC 4.0 (search; README §License for the aligner) | the only aligner weights covering all four languages; Omnilingual ASR (Apache-2.0) and VoxLingua107 ECAPA are the permissive replacements |
| VoxPopuli code and pretrained models | CC BY-NC 4.0 (data CC0) (local README) | data and models both usable here; models would need replacing if shipped |
| InsightFace pretrained models and data | research only, including pip auto-downloads (readme) | strongest face models; facenet-pytorch or DeepFace if shipped |
| Moonshine non-English models | Moonshine Community Licence, non-commercial below USD 1M revenue (search) | usable; `whisper-large-v3-turbo` is the permissive alternative |
| NVIDIA `diar_sortformer_4spk-v1`, `diar_streaming_sortformer_4spk-v2.1`, `multitalker-parakeet-streaming-0.6b-v1` | NVIDIA Open Model Licence, not OSI (search; unverified for v1 and multitalker-parakeet) | resolve before deployment; v2 is CC BY 4.0 per its card (search) |
| SenseVoice model | separate FunASR MODEL_LICENSE (search) | read before use; irrelevant for Iberian ASR anyway |
| BEATs checkpoints | terms separate from the MIT repo | read before use |
| Kyutai STT weights | CC BY 4.0, but en and fr only | not applicable |
| DiarizationLM weights | Llama 2 and Llama 3 licences (readme); English only | pattern, not weights |

### No licence, NOASSERTION unresolved, or licence file missing

VBx, FS-EEND, TS-VAD-plus, huggingface/diarizers, PixIT, llm-tse, WeSep, kiwano,
LoCoNet, LLM-Diarize-ASR-Agnostic, civis-api, EuroSpeech (licence “currently TBD”),
ctc-forced-aligner (`LICENSE` 404, README says BSD with NC default model),
clarin-eric/ParlaMint tooling, VoxBlink2 scripts, USEF-TSE, speakerbeam,
EEND-vector-clustering, UniSpeech, py-webrtcvad, TEN VAD, CrisperWhisper,
WhisperRT-Streaming, Softcatalà nmt-models, Europarl-ASR, BasqueParl, ParlamentParla
code, SCTK and md-eval (NIST terms), FFmpeg (build-dependent), Audacity, Praat, Stanza
(Apache-2.0 upstream), SPARQLWrapper, Riva tutorials, the OASIS and cobalt Akoma Ntoso
repos, the two curated Awesome lists.
A NOASSERTION field means GitHub could not classify the file, not that none exists; read
the file before depending on the repository.

### Copyleft that propagates only if you host it as a service

Fine locally and for a demo; the obligation attaches on distribution or hosting.

AGPL-3.0: whisper-timestamped, aeneas, tssep, audapolis, LinTO, qhld-engine.
GPL: simple_diarizer, que_hacen_api, Apertium (GPL-2.0, distribution only), bluebell,
OpenParliamentTV-Tools, NOS-TTS-API, piper1-gpl, Janus Gateway.
CC BY-SA 4.0: SimulEval (archived).

### Archived or stale dependencies not to build on

facebookresearch/fairseq (archived 2025-09-30), facebookresearch/voxpopuli code
(archived 2023-04-02), facebookresearch/SimulEval (archived 2024-09-13; vendor or
reimplement AL and LAAL), rhasspy/piper (archived 2025-08), and every row labelled
Legacy in §3.

## 8. Rows Without a Verified Link

“Verified” means the row names a GitHub repository present in `repo-meta.jsonl`, the
only same-day-checked source.
No Hugging Face page was opened by any brief, so every `HF:` id in this report is
constructed from a published model or dataset id and is unverified; that caveat covers
every NeMo checkpoint, the pyannote community weights and the SpeechBrain recipes as
well as the rows below.

**Tier 1: paper only, no code and no downloadable artifact.** Calm-Whisper; SAE
hallucination steering; Whisper hallucination detection from internals (plus an unopened
HF classifier); VoxWatch (the correct open-set protocol; a repository search returned
only an unrelated project); LExt and MC-LExt; SpeakerLM (the only published route to
named output from an enrolment registry); the Unified Speech LLM of arXiv 2507.02927;
DM-ASR; SA-SOT; text-based speaker change detection (arXiv 2506.11344); the Italian
parliamentary VLM pipeline (arXiv 2603.28103; arxiv.org was egress-blocked, so a link
inside the PDF cannot be ruled out); SPC_R (arXiv 2506.07726).

**Tier 2: Hugging Face id only, no GitHub repository in the sweep.** ASR: Voxtral Mini
4B Realtime, Voxtral Mini Transcribe V2, Voxtral Mini 3B and Small 24B, ARK-ASR-3B,
MOSS-Transcribe-preview-2B; every Spanish and co-official specialist except Proxecto
Nós, Whisper-LM and the PRHLT system (BSC-LT LoS and LoS-punctuated, the BSC-LT Catalan
line, BSC-LT dialectal Catalan, projecte-aina 3catparla, Citrinet and tiny-caesar,
BSC-LT whisper-timestamped-cs and its test set, HiTZ whisper-large-v3-eu, the HiTZ
Conformer-Transducer, xezpeleta, marianbasti, adriszmar, zuazo);
pyannote/wespeaker-voxceleb-resnet34-LM; facebook/mms-lid-*; Qwen3-VL; SalamandraTA, the
Projecte Aina translators, HiTZ Basque MT, Proxecto Nós Galician MT, WhisperD (only the
Dutch derivative repo is verified); the HF-hosted datasets in §5 (Europarl-ST,
ParlaSpeech 3.0, ParlaMint, basque_parliament_1, Nos_ParlaSpeech-GL,
Nos_TranscriSpeech-GL, 3CatParla, Albayzin-2024-BBS-S2T, Common Voice, FLEURS, MLS,
OpenSLR sets, RTVE/Albayzin, the Finnish, Icelandic, Danish, Portuguese, Slovak and
Faroese parliamentary corpora, AMI, DIHARD III, VoxCeleb2/VoxBlink2, AVA-AVD, REPERE,
EPIC).

**Tier 3: vendor, portal or off-GitHub only.** pyannoteAI precision-2 and Live-1;
SIDEKIT (LIUM-hosted); Artificial Analysis AA-WER (proprietary benchmark); Congreso and
Senado open data; ELAN; Prodigy; every service in §6; Televic CoCon and Bosch DICENTIS
(first-party Bosch API documentation was not openly reachable; the row rests on a
community post and a third-party manual mirror).

**Tier 4: a GitHub repository is named but absent from `repo-meta.jsonl`, so its stars,
licence and last push were not checked on 2026-09-02.**
`Lhx94As/Awesome-Spoken-Language-Identification`; `proxectonos/corpora`;
`espnet/espnet_model_zoo` (the parent `espnet/espnet` is verified);
`tiost100/Multilingual_NER`; `chimechallenge/chime-utils` (signals from the evaluation
inventory); the two deep links still pointing at the pre-rename `NVIDIA-NeMo/NeMo` path
(the MSDD inference recipe and the NFA README; the org itself is verified as
`NVIDIA-NeMo/Speech`); and every repository added from the review’s own API check, whose
licence and push date come from that check and not from the sweep file: omnilingual-asr,
speaches, mediamtx, livekit, janus-gateway, faiss, facenet-pytorch, prhlt-bbs-s2tc,
OpenParliamentTV-Tools and -Data-ES, qhld-engine, SenateGraphQA,
congreso-de-los-diputados, linto-studio, finnish-parliament-scripts,
fi-parliament-asr-benchmark, FPSC, audapolis, react-transcript-editor, salamandra, alia,
latxa, aHoTTS, NOS-TTS-API, Matcha-TTS, tts-api, piper, piper1-gpl, IBERSPEECH-RTVE,
pyannote-db-albayzin2016.

## 9. Evaluation Resources

Every evaluation resource located for the agenda, organised by the sub-problems of the
agenda’s §3.1, with class A (public benchmark), B (proxy or synthetic) or C (to build);
metrics are the agenda’s §4.2 under the §4.6 protocol.
The agenda’s §11 uses these rows for its strategy and cites them as (inventory §9.n).

Scope: evaluation resources only, organised by the sub-problems of §3.1 of
`research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md`. Metrics are
**not** redefined here; every row names the metric from **§4.2** (TAA@c, cpWER/tcpWER,
record-WER, HTER, editorial gap, WER, DER, Cllr/minDCF/actDCF, speaker-count error,
hallucination rate, finalization lag / name revision rate / normalized erasure,
GPU-seconds per audio-hour) with the protocol fixed in **§4.6** (MeetEval collars,
overlap always scored, roster ids) and the stratification of **§4.3**.

**Provenance key.** (gh) = GitHub API or raw file read this session; (readme) =
repository README read this session; (search) = web-search result, page not opened.
Hugging Face pages cannot be opened in this environment: HF ids are cited from search
results and marked *(search, not opened)*. Tags mark the sourced factual cells (sizes,
labels, licences, reference results); every compute figure derives from the *Compute
basis* below rather than from a per-row source.

**Compute basis** (used in every “Compute per run” cell, so numbers are comparable):
*GPU-A* = one 8 GB RTX 2060 SUPER, faster-whisper int8_float16 beam 1 — measured
throughput `whisper-large-v3-turbo` **35.6 audio-h per wall-hour**, `large-v3` 9.6,
Parakeet TDT 0.6B v3 86.8, Granite Speech 4.1 133.4 (readme,
[Parlamento-ai/open-source-asr](https://github.com/Parlamento-ai/open-source-asr));
*GPU-B* = one H100 — pyannote `community-1` diarization **31 s per audio-hour** (~116×
real time), `precision-2` 14 s (readme,
[pyannote-audio](https://github.com/pyannote/pyannote-audio)); *CPU* = 8 threads on an
i7-12700K — faster-whisper `small` int8 batch 8 transcribes 13 min of audio in 51 s =
**15× real time** (readme, [faster-whisper](https://github.com/SYSTRAN/faster-whisper));
large-v3-turbo ≈ 3–4× and large-v3 ≈ 1.3× real time on the same CPU are *extrapolated*
from that README’s relative GPU timings, not measured.
Embedding extraction and scoring are negligible beside ASR. API cost anchor: paid ASR at
**USD 0.71 per audio-hour** over 1,869 h in 30 days (gh,
`research/results/economia.json` in open-source-asr).

* * *

### 9.1 ASR per language (es, ca, eu, gl; LatAm es, en, pt)

| Eval | Class | Data (lang, size, labels) | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **VoxPopuli ASR** | A | es 166 h, **305 speakers**, 1.6M tokens; per-split `asr_[split].tsv` with id, transcript, **speaker id** (readme) | WER (§4.2 `mechanism`), Whisper normalizer; speaker ids allow a cpWER pilot | CC0-style, script download | free (~tens of GB) | test split a few h → GPU-A ≈10 min; CPU ≈1 h | s2t-transformer-L es: **15.3 WER** on Europarl-ST test (readme) | EP not Congreso; non-verbatim official transcripts; in almost every model’s training data | [gh](https://github.com/facebookresearch/voxpopuli), [ACL](https://aclanthology.org/2021.acl-long.80.pdf) |
| **Europarl-ASR v1.0** | A | **en only**, 1,263 h / 33,002 speeches / 1,046 speakers; **17.5 h manually verbatimized** dev+test, *spk-dep* and *spk-indep*; 3 transcript tiers (readme) | WER offline and streaming per partition; the two tiers give the **editorial gap** | CC BY 4.0; 18 GiB tar.gz + SHA-256 | free | 17.5 h → GPU-A ≈30 min; CPU ≈5 h | verbatimization worth ~9 WER points (§3.11) | English — the value is the **two-layer reference design**, not an es number | [gh readme](https://github.com/mllpresearch/Europarl-ASR), [Interspeech 2021](https://www.isca-archive.org/interspeech_2021/garcesdiazmunio21_interspeech.html) |
| **Europarl-ST v1.1** | A | 9 langs incl. **es**; dev/test 3–6 h per direction; 72 ST directions (search) | WER (ASR) / BLEU (ST); official splits | CC BY-NC 4.0, direct download | free | 3–6 h → GPU-A ≈10 min | es ASR **15.3 WER** (readme) | non-commercial licence; 2008–2012 material; non-verbatim | [mllp](https://www.mllp.upv.es/europarl-st/), [arXiv 1911.03167](https://arxiv.org/pdf/1911.03167) |
| **EuroSpeech** | A | 22 parliaments, 61k h; **train/dev/test per language split by whole session**; CER tiers <10/20/30% (search) | WER on the language’s test split; report the CER tier used | HF `disco-eth/EuroSpeech` (search, not opened); repo licence **TBD** (readme) | free | 10 h slice → GPU-A ≈17 min; CPU ≈3 h | 41% of aligned segments under CER 10%, 65% under 20% (§3.11) | **Spain absent** (§3.10); no speaker labels; licence unresolved | [gh](https://github.com/SamuelPfisterer/EuroSpeech), [NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/file/58ea63de01321ee52d06b48026981c40-Paper-Datasets_and_Benchmarks_Track.pdf) |
| **ParlamentParla v2/v3** | A | ca, Parliament of Catalonia, 611 h (211 clean + 400 other); **speaker ids + gender, speaker-disjoint splits** (readme) | WER on the released test split; usable for speaker-disjoint SV trials | CC BY 4.0, OpenSLR 59 | free | test ≈ few h → GPU-A ≈10 min | used as the base of the BSC code-switching test (search) | auto-aligned; single chamber; read-style long turns | [OpenSLR 59](https://www.openslr.org/59/), [gh](https://github.com/CollectivaT-dev/ParlamentParla) |
| **Basque Parliament 1 / Albayzin-2024 BBS-S2T** | A | **eu+es**, 1,462 h, 759,192 segments of 3–10 s, per-segment **language tag**; train-clean 1315.5 h, **dev 7.6 h, test 9.6 h, eval 23.8 h** (search) | global WER on eval; challenge ranking protocol | HF `gttsehu/basque_parliament_1`, `gttsehu/Albayzin-2024-BBS-S2T` (search, not opened) | free | 9.6 h → GPU-A ≈16 min, CPU ≈2.5 h; 23.8 h → GPU-A ≈40 min | semi-supervised pipeline: WER 16.57 → **4.02**; PRHLT eval **3.44 WER**; Vicomtech won 2024 (search) | segment-level, no speaker labels; models trained on it (`whisper-large-v3-eu`) need a temporal hold-out (§3.3) | [HF](https://huggingface.co/datasets/gttsehu/basque_parliament_1), [eval plan](https://catedrartve.unizar.es/reto2024/BBS-S2T2024_Evalplan.pdf), [Varona 2024](https://gtts.ehu.eus/gtts/NT/fulltext/Varona2024.pdf) |
| **Nos_ParlaSpeech-GL / TranscriSpeech-GL** | A | gl, 1,600+ h auto-aligned parliament 2015–2022; **53 h manual, 4 domains** (search) | WER; TranscriSpeech is the only manually transcribed gl reference | ParlaSpeech: HF `proxectonos/Nos_Parlaspeech-GL`; TranscriSpeech: **Zenodo, T&C + access request** | free after request | 53 h → GPU-A ≈1.5 h; CPU ≈16 h | none located for gl parliament ASR | ParlaSpeech transcripts are automatic; gl is the open tail (14.71% API disagreement, §3.3) | [HF](https://huggingface.co/datasets/proxectonos/Nos_Parlaspeech-GL), [Zenodo 7717140](https://zenodo.org/records/7717140) |
| **3CatParla** | A | ca broadcast TV, **731 h 21 min**, manual transcripts verified with 4 ASR systems (search) | WER on released test split | CC BY 4.0, HF `projecte-aina/3catparla_asr` (search, not opened) | free | test ≈ few h → GPU-A ≈10 min | `projecte-aina/whisper-large-v3-ca-3catparla` published on it (search) | broadcast, not chamber; fine-tuned models on the leaderboard are in-domain-trained | [IberSPEECH 2024](https://www.isca-archive.org/iberspeech_2024/hernandezmena24_iberspeech.pdf) |
| **Common Voice v26.0 es/ca/eu/gl** | A | test clips **es 15,904 / ca 16,416 / eu 14,809 / gl 15,368**; mean clip 4.9–5.4 s → **≈21–24 h each**; accent+variant tags (gh, computed) | WER, Whisper-normalized; accent stratum (es carries 11 accent labels: `mexicano`, `rioplatense`, `chileno`…) | CC0, direct download | free | 22 h → GPU-A ≈37 min; CPU ≈6 h | Open ASR Leaderboard includes `common_voice` (search) | read speech; heavy contamination; single-speaker clips → no attribution signal | [cv-dataset](https://github.com/common-voice/cv-dataset/tree/main/datasets/scripted-speech) |
| **FLEURS ca_es / gl_es / es_419** | A | ~350 test sentences per language (~1–1.5 h); n-way parallel (search) | WER; also the standard LID protocol | CC BY 4.0, HF `google/fleurs` | free | 1.5 h → GPU-A ≈3 min | XTREME-S baselines (search) | **no Basque**; read, clean; tiny — a session-level bootstrap (§4.7) will not close on it | [HF](https://huggingface.co/datasets/google/fleurs), [lhotse recipe](https://github.com/lhotse-speech/lhotse/blob/master/lhotse/recipes/fleurs.py) |
| **MLS Spanish** | A | es is the largest non-English subset; dev/test ~10 h each; read audiobooks (search — the exact es train hours were not confirmed in this session) | WER | CC BY 4.0, OpenSLR 94 | free | 10 h → GPU-A ≈17 min; CPU ≈3 h | MLS paper baselines (search) | audiobooks — furthest regime from a plenary; contamination | [OpenSLR 94](https://www.openslr.org/94/), [MLS paper](https://arxiv.org/pdf/2012.03411) |
| **Open ASR Leaderboard** | A | en short-form (AMI, Earnings22, GigaSpeech, LibriSpeech, SPGISpeech, TEDLIUM, VoxPopuli) + multilingual + long-form; results CSV published (search) | WER + **RTFx**, one harness | Apache-2.0 code; HF datasets | free | full English suite ≈ 30–40 h audio → GPU-A ≈1 h | 74 models tracked; best open 4.46 WER vs best paid 4.43 (gh, cached in open-source-asr `leaderboard.json`) | English, clean audio; “no predice el rendimiento en audio parlamentario en español” (gh) | [gh](https://github.com/huggingface/open_asr_leaderboard), [paper](https://arxiv.org/html/2510.06961v1) |
| **Artificial Analysis AA-WER** | A | ~8 h: AA-AgentTalk 50% (proprietary), VoxPopuli-Cleaned-AA 25%, Earnings22-Cleaned-AA 25% (search) | duration-weighted WER; streaming and non-streaming boards | leaderboard public; AgentTalk held out | free to read, not to rerun | n/a (vendor-run) | published per-provider WER | English; one third of the set is unreleasable → not reproducible | [methodology](https://artificialanalysis.ai/speech-to-text/methodology) |
| **Parlamento.ai `open-source-asr`** | B | 168 parliamentary clips, **13.79 h**, 24 each in es/ca/gl/eu/en/pt/multilingual | **word-level edit distance vs the median of 3 paid APIs — agreement, not accuracy** | MIT code; **the clip manifest is deliberately not in the repo** (gh, `research/sample.py`) | free code; audio not redistributable | 13.79 h → GPU-A ≈23 min | turbo 3.25% (es), 9.56% (ca), 14.71% (gl), 21.29% (eu); paid APIs disagree 7.17% among themselves (readme) | agreement only; no ground truth, **no speaker metric**; not re-runnable on the same audio | [gh](https://github.com/Parlamento-ai/open-source-asr) |
| **Diario-aligned Congreso verbatim layer** | C | es/ca/eu/gl, 20–50 plenary+committee sessions; verbatim + record layers, word timestamps, roster ids (§4.1, §5.1) | WER, record-WER, HTER, editorial gap | to build; publish manifests not audio (§5.1 step 8) | see §9.12 below | 250 h → GPU-A ≈7 h per full-system pass | none — would be the first (§8.1) | annotation cost dominates; see §9.12 | [Archivo audiovisual](https://www.congreso.es/es/archivo-audiovisual) |

* * *

### 9.2 Code-switching and language identification

| Eval | Class | Data (lang, size, labels) | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **BSC Code-Switching CA-ES ASR Test** | A | 867 recordings, **4 h 09 min**, ca-dominant with systematic intra-sentential es segments; manually inspected; sourced from Corts Valencianes + ParlamentParla v3 (search) | WER; the only public ca–es CS reference | HF `BSC-LT/BSCs_Code_Switching_CA-ES_ASR_Test` (search, not opened) | free | 4.15 h → GPU-A **7 min**; CPU ≈1.2 h | comparative study of CS ASR for ca–es (search) | small; no speaker labels; parliament-derived but utterance-level | [HF](https://huggingface.co/datasets/BSC-LT/BSCs_Code_Switching_CA-ES_ASR_Test), [arXiv 2507.13875](https://arxiv.org/pdf/2507.13875) |
| **Albayzin BBS-S2T (CS subset)** | A | eu+es, segments that contain a switch, inside the 9.6 h test / 23.8 h eval (search) | WER overall and on the bilingual stratum | HF `gttsehu/Albayzin-2024-BBS-S2T` (search, not opened) | free | as §1 | PRHLT 3.44 WER eval (search) | switch labels are per segment, not per word | [eval plan](https://catedrartve.unizar.es/reto2024/BBS-S2T2024_Evalplan.pdf) |
| **CS-FLEURS** | A/B | 4 test sets, 113 CS language pairs / 52 langs, incl. a 60-pair {ar, zh, hi, **es**}-X set; TTS-generated + real-voice-read (search) | WER/CER per pair | Interspeech 2025 release (search) | free | per-pair sets are ~1 h → GPU-A ≈2 min | paper baselines (search) | **synthetic or read** switching; no Iberian-pair guarantee; lexical switching, not chamber prosody | [arXiv 2509.14161](https://arxiv.org/abs/2509.14161), [paper](https://www.isca-archive.org/interspeech_2025/yan25c_interspeech.pdf) |
| **VoxLingua107 dev** | A | 1,609 segments, 33 languages (search) | LID error rate; segment level | CC BY 4.0 model + data | free | minutes on CPU | SpeechBrain ECAPA **6.7% error**, with elevated es–gl confusion (§3.2) | dev set is YouTube, not chamber; **no sub-utterance switch protocol** | [HF model](https://huggingface.co/speechbrain/lang-id-voxlingua107-ecapa), [SB recipe](https://github.com/speechbrain/speechbrain/tree/develop/recipes/VoxLingua107) |
| **FLEURS LID** | A | 102 langs incl. ca/gl/es_419, ~350 test utts each | LID accuracy | CC BY 4.0 | free | minutes | XTREME-S LID baselines (search) | no Basque; utterance-level only | [HF](https://huggingface.co/datasets/google/fleurs) |
| **Language-diarization on institutional speech** | A(ref) | broadcast + institutional, multilingual | language-diarization error; cascade diarize-then-LID | paper only | n/a | n/a | 10% rel. language-DER and >8% rel. WER reduction (§3.2) | data not released; reproduce the protocol, not the numbers | [arXiv 2406.09290](https://arxiv.org/abs/2406.09290) |
| **Synthetic switch-point set** | B | concatenate ParlamentParla (ca) and VoxPopuli (es) segments at known switch times | frame-level LID error at known boundaries; switch-detection F1 with tolerance | free from two CC-licensed corpora | ~1 GPU-hour to build | build once, score in seconds | none | concatenation splices have no coarticulation → **overstates** switch detectability | [OpenSLR 59](https://www.openslr.org/59/), [voxpopuli](https://github.com/facebookresearch/voxpopuli) |

* * *

### 9.3 Diarization

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results (pyannote `legacy` / `community-1` / `precision-2`) | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **AMI (IHM, SDM)** | A | en meetings, ~100 h, 4 speakers, high overlap | DER, 0.25 s collar, overlap scored (§4.2 `mechanism`) | free download; standard BUT split | free | ~9 h eval: GPU-B ≈5 min; CPU ≈3–5 h | IHM **18.8 / 17.0 / 12.9**; SDM **22.7 / 19.9 / 15.6** (readme) | meeting acoustics ≠ chamber; 4 speakers, not 350 | [AMI](https://groups.inf.ed.ac.uk/ami/corpus/), [BUT setup](https://github.com/BUTSpeechFIT/AMI-diarization-setup) |
| **NOTSOFAR-1 (CHiME-8 t2)** | A | en meetings, ~315 meetings / ~170 h SC; eval 102 h SC | **tcpWER** (speaker-attributed) primary, tcORC-WER secondary | MIT code; HF `microsoft/NOTSOFAR` (search, not opened) | free | eval 102 h: GPU-A ≈3 h ASR + GPU-B ≈1 h diar; CPU impractical | baseline **tcpWER 28.3%**; DiCoW 18.4% (§3.4) | far-field rooms; heavier overlap than a plenary | [gh](https://github.com/microsoft/NOTSOFAR1-Challenge), [task](https://www.chimechallenge.org/challenges/chime8/task2/index) |
| **DIHARD III** | A | 11 domains, en+zh, eval ≈33 h (search) | DER **without collar**, overlap scored — the strict protocol | **LDC2022S12/S14, licence + fee (price not located)** | LDC fee | 33 h: GPU-B ≈17 min | full: **21.4 / 20.2 / 14.7** (readme) | paid; no parliamentary domain | [LDC2022S14](https://catalog.ldc.upenn.edu/LDC2022S14), [baseline](https://github.com/dihardchallenge/dihard3_baseline) |
| **VoxConverse v0.3** | A | broadcast/press-event video, test **43.5 h**, 232 sessions, up to 15+ speakers (search) | DER, 0.25 s collar | free download | free | 43.5 h: GPU-B ≈22 min; CPU ≈15 h | **11.2 / 11.2 / 8.5** (readme) | **closest public acoustic analogue** to press-conference/chamber audio; still no named labels | [gh](https://github.com/joonson/voxconverse), [lhotse recipe](https://github.com/lhotse-speech/lhotse/blob/master/lhotse/recipes/voxconverse.py) |
| **CALLHOME (part 2)** | A | telephone, multilingual incl. Spanish | DER, 0.25 s collar; BUT sublists fix the split | **LDC2001S97, fee (price not located)** | LDC fee | ~2 h: minutes | **28.5 / 26.7 / 16.6** (readme); DiarizationLM cut WDER 44.9% (§3.7) | 8 kHz telephone; 2–7 speakers | [LDC2001S97](https://catalog.ldc.upenn.edu/LDC2001S97), [sublists](https://github.com/BUTSpeechFIT/CALLHOME_sublists) |
| **AliMeeting** | A | zh meetings, 118.75 h (test 10 h) (search) | DER; also cpCER for SA-ASR | OpenSLR 119, free | free | 10 h: GPU-B ≈5 min | ch1 **24.5 / 20.3 / 15.2** (readme) | Mandarin; cross-lingual transfer only | [OpenSLR 119](https://www.openslr.org/119/), [lhotse](https://github.com/lhotse-speech/lhotse/blob/master/lhotse/recipes/ali_meeting.py) |
| **MSDWild** | A | in-the-wild video, 80 h, 2–4 speakers, 72 s mean (search) | DER; audio-visual variant available | free (repo) | free | 80 h: GPU-B ≈40 min | **25.4 / 22.8 / 17.3** (readme) | short clips; wild acoustics | [gh](https://github.com/X-LANCE/MSDWILD) |
| **REPERE phase 2** | A | fr TV, multimodal, **named** persons | DER here; **EGER** for the named task (§3.6) | ELRA **REPERE Evaluation Package ELRA-E0044**, 620 Gb (search); price not located | ELRA fee | large | **7.9 / 8.9 / 7.4** DER (readme); PERCOL EGER 24.4% supervised / 36.3% unsupervised (§3.6) | the only public precedent for *named* attribution; French TV, 2012–14 | [ELRA-E0044](http://catalog.elra.info/en-us/repository/browse/ELRA-E0044/), [ISLRN](https://www.islrn.org/resources/360-758-359-485-0/) |
| **AVA-AVD / Ego4D** | A | audio-visual, hard | DER | free / registration | free | large | AVA-AVD **49.7 / 44.6 / 37.1**; Ego4D dev **51.2 / 46.8 / 39.0** (readme) | worst-case anchors — useful only as a floor | [arXiv 2111.14448](https://arxiv.org/abs/2111.14448) |
| **Replayed-room / RIR augmentation of a real session** | B | any session + MUSAN noise + simulated or measured RIRs | DER and speaker-count error under matched vs augmented conditions | lhotse `musan`, `rir_noise` recipes | free | negligible on top of the base run | none | simulated reverberation is optimistic vs a real chamber PA; **do not** report augmented DER as a headline | [lhotse recipes](https://github.com/lhotse-speech/lhotse/tree/master/lhotse/recipes) |
| **Diario-aligned turn segmentation** | C | Congreso sessions with intervention boundaries from the record | speaker-count error (§4.2 `guard`), DER with roster ids | to build | §9.12 | as §1 | none | the record’s intervention boundaries are editorial, not acoustic → needs the verbatim layer to be a timing reference | [Diario](https://www.congreso.es/es/busqueda-de-publicaciones) |

* * *

### 9.4 Open-set speaker identification at ~350 enrolled speakers

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **VoxCeleb1-O / -E / -H** | A | en-dominant YouTube; official cleaned trial lists | EER, minDCF; cosine + AS-norm + QMF recipe | free | free | full-list scoring: minutes on 1 GPU after embedding extraction (~1–2 h for VoxCeleb1) | ReDimNet2-B6 **0.23/0.35/0.67**; W2V-BERT2.0-MFA **0.138/0.285/0.625**; CAM++ 0.659/0.803/1.569 (§3.5) | **closed-set verification**, not open-set identification; cross-condition internet audio; saturated | [WeSpeaker](https://github.com/wenet-e2e/wespeaker/tree/master/examples/voxceleb/v2), [ReDimNet2](https://github.com/PalabraAI/redimnet2) |
| **VoxWatch (open-set on VoxCeleb)** | A | watchlists scaled from tens to thousands; **trial and speaker lists released by the authors** (search) | FAR at fixed FRR vs watchlist size; the OSI curve this project needs | lists in the paper’s release (search) | free | scoring only: minutes | FAR at 5% FRR improved 21% rel. (3.7%→2.9%) at the largest watchlist by calibration + fusion; **AS-norm alone not guaranteed to help** (§3.5) | VoxCeleb conditions, not same-room; no repo located in this session (search) | [arXiv 2307.00169](https://arxiv.org/pdf/2307.00169) |
| **VoxBlink2 OSI benchmark** | A | 100k+ speakers; explicit open-set identification protocol | OSI accuracy / FAR–FRR | paper + corpus release (search) | free | large (embedding extraction dominates) | reported in-paper | scale far above 350; internet audio | [arXiv 2407.11510](https://arxiv.org/html/2407.11510v1) |
| **Novoselov et al. open-set scaling** | A(ref) | 100 → 700 enrolled speakers | EER vs enrolled-set size | paper only | n/a | n/a | **EER roughly doubles from 100 to 700 enrolled** (§3.5) — the sizing curve for a 350-seat roster | not reproducible without their data | [arXiv 1904.01269](https://arxiv.org/pdf/1904.01269) |
| **VoxSRC sets; CN-Celeb** | A | harder VoxCeleb-derived val/test trials; zh multi-genre cross-condition | EER/minDCF | free; OpenSLR 82 | free | ≤1 GPU-h each | challenge reports | both closed-set verification; CN-Celeb serves only as a domain-shift control | [VoxSRC 2022](https://arxiv.org/pdf/2302.10248), [OpenSLR 82](https://www.openslr.org/82/) |
| **Iberian cross-lingual SV set** | A/B | **bilingual same-speaker** trials built from Common Voice 25.0: es–ca **300 speakers**, es–eu 64, es–pt 40, es–gl 21 (search) | EER / cross-lingual transfer matrix under constant identity | release status not established; **reconstructible from CC0 Common Voice** | free (rebuild) | embedding extraction on ~few h: minutes | language switching shifts embeddings systematically; language mismatch dominates; es–gl smallest shift (§3.5) | read speech; small eu/gl speaker counts; the exact list may not be published | [arXiv 2607.01161](https://arxiv.org/html/2607.01161v1), [cv-dataset](https://github.com/common-voice/cv-dataset) |
| **Albayzin RTVE SDIAC (2018/2020/2022)** | A | es broadcast; **named, closed set (74 speakers in 2022)**; diarization + identity assignment | DER (mandatory) + identity assignment; official eval plan | **RTVE licence agreement, signed by a group representative** (search); free after signature | signature + waiting time | tens of hours → GPU-B <1 h | Intelligent Voice IberSPEECH 2022 system paper; 2022 overview in Applied Sciences (§3.5 refs) | broadcast, 74 not 350; a 2020 subset reportedly includes 2016 parliamentary material (§3.10) | [albayzin2022](https://catedrartve.unizar.es/albayzin2022.html), [SDIAC eval plan](https://catedrartve.unizar.es/reto2022/SDIAC2022_Evalplan.pdf), [results](https://catedrartve.unizar.es/albayzin2022results.html) |
| **Archive-enrollment trials from ParlamentParla / Basque Parliament** | B | speaker-labelled parliamentary corpora with disjoint splits | build N-speaker watchlists (50/150/350) from real chamber audio; EER, FAR@FRR, Cllr | CC BY 4.0 / HF | free | embedding extraction 611 h ≈ 1–2 GPU-h | none published | **same chamber, same mics** — the closest proxy to the §7 R4 regime; but ca/eu, not Congreso | [OpenSLR 59](https://www.openslr.org/59/), [HF](https://huggingface.co/datasets/gttsehu/basque_parliament_1) |
| **Congreso roster enrollment + session trials** | C | 350 archive-clip centroids + held-out session turns (§5.1 step 6) | **TAA@c** (`outcome`), FAR at fixed FRR, Cllr/minDCF/actDCF (`guard`) | to build | §9.12 | as §1 | none — this is H-001/H-004 | needs verified speaker labels on the eval side; enrollment must exclude eval sessions | [Archivo audiovisual](https://www.congreso.es/es/archivo-audiovisual) |

* * *

### 9.5 Speaker-attributed ASR (joint / cascade)

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **EMMA (JSALT25) leaderboard** | A | multi-domain multi-talker suite used by the DiCoW family; submission requires the model on the HF Hub (readme) | **tcpWER**, macro-averaged across domains | HF Space `BUT-FIT/EMMA_leaderboard` (search, not opened); submission script in TS-ASR-Whisper (readme) | free | dominated by the underlying sets (AMI, NOTSOFAR-1, LibriMix…) | SE-DiCoW **−52.4% rel. tcpWER** vs DiCoW; Dixtral macro cpWER **15.4%** vs Gemini 3.0 Flash 44.4% (§3.4) | English meeting-centric; no parliamentary domain; leaderboard submission couples eval to HF hosting | [Space](https://huggingface.co/spaces/BUT-FIT/EMMA_leaderboard), [gh](https://github.com/BUTSpeechFIT/TS-ASR-Whisper) |
| **NOTSOFAR-1 / CHiME-8 DASR** | A | see §3 | tcpWER, tcORC-WER via MeetEval; `chime-utils` scoring | MIT | free | eval 102 h → ≈4 GPU-h end to end | baseline 28.3; DiCoW 18.4 (§3.4) | far-field, 4–8 speakers, heavy overlap — **overweights overlap** relative to a plenary (§3.4) | [gh](https://github.com/microsoft/NOTSOFAR1-Challenge), [chime-utils](https://github.com/chimechallenge/chime-utils) |
| **AMI SDM/IHM cpWER** | A | see §3 | cpWER / tcpWER, 5 s collar (§4.6) | free | free | ≈9 h → 1 GPU-h | DiCoW cpWER **18.1% SDM**; SA-DiCoW 18.1%; TagSpeech best DER (§3.4) | as §3 | [MeetEval](https://github.com/fgnt/meeteval) |
| **LibriCSS / Libri2Mix / Libri3Mix / LibriSpeechMix** | A/B | simulated and replayed mixtures of LibriSpeech | cpWER, ORC-WER; separation SI-SNRi | MIT (LibriMix), repo licence (LibriCSS) | free | 10 h → GPU-A ≈17 min | SA-DiCoW cpWER **17.2% Libri3Mix** (§3.4) | fully synthetic overlap; **replayed-room LibriCSS is the only one with real acoustics**; no named speakers | [LibriMix](https://github.com/JorisCos/LibriMix), [Libri-CSS](https://github.com/chenzhuo1011/libri_css), [lhotse](https://github.com/lhotse-speech/lhotse/blob/master/lhotse/recipes/libricss.py) |
| **AliMeeting cpCER** | A | zh meetings | cpCER (M2MeT protocol) | OpenSLR 119 | free | 10 h → <1 GPU-h | MOSS Transcribe Diarize **cpCER 15.83 AISHELL-4**, 7.37 Podcast, 12.76 Movies (§3.4) | Mandarin; Podcast/Movies numbers are the nearest long-form low-overlap analogue | [M2MeT 2.0](https://arxiv.org/pdf/2309.13573) |
| **VoxPopuli-es cpWER pilot** | B | es 166 h with **speaker ids** (readme) — build SegLST references from the ASR tsv | cpWER with speaker ids as labels (§4.6: roster ids, not name strings) | CC0 | free | test split → GPU-A ≈10 min | **nobody reports cpWER on VoxPopuli or Europarl-ASR** (§3.4) — an easy first public number | speaker ids are per segment, not per session turn; segments are pre-cut, so turn segmentation is given away | [gh](https://github.com/facebookresearch/voxpopuli), [MeetEval](https://github.com/fgnt/meeteval) |
| **TTS multi-speaker session** | B | Piper `ca_ES` + `es_ES/es_AR/es_MX` voices reading Diario text in roster order (**no eu/gl Piper voices exist** — gh, VOICES.md) | cpWER/TAA against a perfect script | MIT | ~CPU-hours to synthesize | negligible | none | validates plumbing, turn logic and the scorer; **misleads on** acoustics, overlap, interjections, room, and enrollment realism; a system can score ~0 error here and fail on real audio | [piper](https://github.com/rhasspy/piper), [VOICES.md](https://github.com/rhasspy/piper/blob/master/VOICES.md) |
| **Diario-aligned cpWER/TAA benchmark** | C | §5.1 | **TAA@{100,95,90}%** (`outcome`), cpWER/tcpWER with roster ids, stratified by §4.3 | to build | §9.12 | as §1 | none (§8.1) | the deliverable | [agenda §5.1](research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md) |

* * *

### 9.6 Target-speaker extraction under the interpreter voice-over

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **VoxPopuli speech-to-speech interpretation** | A/B | **17.3k h, 15×15 directions**; target-Spanish interpretation totals ≈1.2k h (En→Es 441 h, De→Es 204, Fr→Es 187…) — real interpreters, real lag, aligned to source (readme) | mix source + Spanish interpretation at a chosen gain and 2–4 s lag → score WER/TAA on the source speaker, and interpreter-ID accuracy, against clean-channel references | CC0-style; download script | free | mixing is CPU-cheap; scoring a 10 h mixed set → GPU-A ≈17 min | none for this use | **the closest available surrogate for the Congreso overlay**; but source languages are not ca/eu/gl, and the mixing ratio is a free parameter that must be calibrated against a real Congreso clip | [gh](https://github.com/facebookresearch/voxpopuli) |
| **ESIC 1.0** | A | 370 EP speeches (**10 h**) en, with transcribed simultaneous interpreting into cs and de; disfluency and voice-mixing metadata, word timestamps; interpreter audio fetched by a bundled tool from the EP site (search) | source-vs-interpretation alignment; also the reference set for streaming latency (§9) | LINDAT, **CC BY-NC-SA 4.0** | free | 10 h → GPU-A ≈17 min | Whisper-Streaming **3.3 s latency** on EP speech (§3.9) | interpreter voices are not distributed inside the corpus; en→cs/de, not X→es; non-commercial | [LINDAT](https://lindat.mff.cuni.cz/repository/items/1200e094-1ae1-44ec-8fca-a4ff9d45cb08) |
| **EPIC** | A | it/en/es EP speeches + interpretations, 357 speeches / 177,295 words (search) | translation-studies annotations; audio/video not online (search) | **ELRA-S0323** catalogue; price not located | ELRA fee | small | built for interpreting studies, no ASR baselines | no ASR/diarization protocol; distribution restricted (§3.2) | [ELRA-S0323](https://catalogue.elra.info/en-us/repository/browse/ELRA-S0323/), [EPIC docs](https://docs.sslmit.unibo.it/doku.php?id=corpora:epic) |
| **Simulated overlay: es TTS over ca/eu/gl floor** | B | ParlamentParla (ca) / Basque Parliament (eu) / Nos_ParlaSpeech (gl) + a Spanish TTS or a second real speaker, summed at 2–4 s lag and a swept SNR | ΔWER and ΔTAA vs the isolated channel; SI-SNRi for the extraction stage; **H-005 criterion: recover ≥50% of the TAA loss** | all inputs CC BY 4.0 / CC0 | ~1 GPU-h to build a 10 h set | scoring 10 h → GPU-A ≈17 min + extraction cost | separation SOTA 23–26 dB SI-SNRi on synthetic 2-speaker mixtures, degrading sharply on real recordings (§3.2) | constant lag and linear summation — the **real broadcast mix has AGC, ducking, one encoder**; optimistic; calibrate against a real clip (`ffprobe` + listening, Q-003) | [OpenSLR 59](https://www.openslr.org/59/), [WeSep](https://github.com/wenet-e2e/wesep), [asteroid](https://github.com/asteroid-team/asteroid) |
| **Interpreter-voice enrolment set** | C | ~12 interpreters × several clips from archive sessions (§3.2 attack 2) | closed-set ID accuracy on interpreter turns; then use interpreter output as the Spanish translation layer | to build from public archive | ~1 annotator-day | negligible | none | requires identifying interpreter voices by hand; interpreter roster changes between contracts | [Archivo audiovisual](https://www.congreso.es/es/archivo-audiovisual) |
| **Real interpreted-segment subset** | C | the co-official-language interventions inside the 20–50 sessions, with both channel conditions kept (§5.1 step 2) | TAA and WER on the interpreted stratum, paired against isolated-channel turns | to build | §9.12 | as §1 | none — **no published work separates simultaneous interpretation from its source on real data** (§3.2) | the whole novelty claim of R5 rests on this subset; it may be only a few hours across 50 sessions | [BOE-B-2026-16554](https://www.boe.es/diario_boe/txt.php?id=BOE-B-2026-16554) |

* * *

### 9.7 Editorial normalization to the official record (incl. MT and punctuation/ITN)

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Europarl-ASR triple-tier transcripts** | A | en, 17.5 h with **official + manually verbatimized** transcripts (readme) | **editorial gap** = record-WER of the verbatim reference against the official text; HTER for post-editing effort | CC BY 4.0 | free | text-only scoring: seconds | filtering+verbatimization worth **9 WER points** (§3.11) | English; the only public corpus with both tiers manually produced | [readme](https://github.com/mllpresearch/Europarl-ASR) |
| **EuroSpeech CER tiers** | A | 22 parliaments; per-segment CER of record-vs-audio alignment | distribution of alignment CER as an editorial-gap proxy | HF (search, not opened) | free | text-only | **41% <10% CER, 65% <20%, 78% <30%** (§3.11) | Spain absent; CER of an alignment, not of an edit | [NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/file/58ea63de01321ee52d06b48026981c40-Paper-Datasets_and_Benchmarks_Track.pdf) |
| **ParlaMint-ES / -ES-CT / -ES-GA / -ES-PV 5.0** | A/B | text only, ~15M words (ES); speaker **name, party, gender, role**; TEI `vocal` (exclamations), `kinesic` (applause), `incident` (search) | text-only: chair-announcement coverage, agenda-constraint value, stage-direction base rates (§7 R3) | CC BY 4.0, CLARIN.SI | free | minutes on CPU | none for these tasks | **no audio alignment for any Spanish-state ParlaMint corpus** (§3.10); conventions vary by chamber | [CLARIN.SI](https://www.clarin.si/repository/xmlui/handle/11356/2004), [encoding](https://clarin-eric.github.io/ParlaMint/) |
| **NeMo-text-processing es ITN/TN test cases** | A | 22 Spanish test-case files (cardinal, date, decimal, money, ordinal, telephone, time, measure, whitelist…) (gh) | exact-match pass/fail per grammar; a CPU regression eval that runs in seconds | Apache-2.0 | free | **seconds on CPU** | grammars ship green | unit tests, not a corpus; says nothing about chamber-specific forms ("señoría", article numbers) | [tests/es](https://github.com/NVIDIA/NeMo-text-processing/tree/main/tests/nemo_text_processing/es) |
| **LibriSpeech-PC** | A | en; punctuation + capitalization benchmark | P/R/F1 per punctuation class | free | free | minutes | paper baselines (search) | English-only; **no Spanish equivalent located** | [arXiv 2310.02943](https://arxiv.org/pdf/2310.02943) |
| **FLORES-200 devtest / NTREX-128** | A | 1,012 (FLORES) and 1,997 (NTREX) parallel sentences; **cat, eus, glg, spa all present in NTREX** (gh) | BLEU/chrF/COMET for the ca/eu/gl→es translation the record requires | FLORES repo (archived); NTREX **CC BY-SA 4.0** | free | minutes on CPU | Softcatalà ca–es reports BLEU 87.5 internal vs **24.2 on FLORES-200** (§3.11) — the gap between domain and general test sets | news/wiki domain, not chamber register; sentence-level | [NTREX](https://github.com/MicrosoftTranslator/NTREX), [flores](https://github.com/facebookresearch/flores) |
| **Diario record layer** | C | published *Diario de Sesiones* HTML, with post-2023 original + Spanish-translation blocks (§3.10) | **record-WER** and **HTER against the record** (`outcome` for normalization work); edited-segment fraction | free HTML; scraping to build | parsing effort only (no annotation) | text-only: minutes | Estonia reports 93–95% against the final record; Portugal reports against the edited *Diário* (§3.11) | nearly free — **only the verbatim layer costs money**; alignment yield well under 100% (EuroSpeech tiers) | [Diario](https://www.congreso.es/es/busqueda-de-publicaciones), [ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner) |

* * *

### 9.8 Stage-direction (applause / laughter / murmur) detection

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **AudioSet eval (Applause, Laughter classes)** | A | ~20k eval clips, 527 classes, 10 s YouTube segments (search) | per-class AP; mAP overall | clip list free, audio via YouTube (fragile) | free but lossy (dead videos) | eval set → minutes on 1 GPU | PANNs CNN14 **mAP 0.439** overall (§3.11); per-class AP for applause/laughter not located | weak labels, 10 s granularity; **unbenchmarked on chamber acoustics** (§3.11) | [audioset_tagging_cnn](https://github.com/qiuqiangkong/audioset_tagging_cnn), [PANNs](https://arxiv.org/abs/1912.10211) |
| **ESC-50** | A | 2,000 clips, 50 classes (incl. clapping, laughing), 5-fold | accuracy, fixed folds | free, repo | free | seconds | many published | 5 s isolated clips, no PA, no overlap with speech | [gh](https://github.com/karolpiczak/ESC-50) |
| **ParlaMint stage-direction base rates** | B | TEI `kinesic`/`vocal`/`incident` marks across ParlaMint-ES (search) | prior/base-rate calibration and a text-side upper bound on recall | CC BY 4.0 | free | minutes | none | transcriber conventions are inconsistent and **under-record** events; a text mark is evidence of a noticed event, not of all events | [encoding guide](https://clarin-eric.github.io/ParlaMint/) |
| **Diario stage directions aligned to audio** | C | `(Aplausos.)`, `(Rumores.)`, `(Risas.)` in the record, time-anchored via the verbatim layer | precision/recall against the record’s annotations (§7 R9) | to build | ~0.2 h/session-hour on top of the verbatim pass | negligible | none | the record is the *only* reference and it is editorially filtered — recall against it is really “agreement with the stenographer” | [Diario](https://www.congreso.es/es/busqueda-de-publicaciones) |

* * *

### 9.9 Streaming latency and revision

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **ESIC unsegmented long-form** | A | 10 h EP speeches, word timestamps (search) | computation-aware AL/LAAL/StreamLAAL + WER; `--comp_unaware` gives the lower bound (readme) | LINDAT CC BY-NC-SA 4.0 | free | replay is wall-clock-bound: **10 h of audio takes ≥10 h** unless replayed faster than real time | Whisper-Streaming **3.3 s** mean latency; SimulStreaming ~5× faster (§3.9) | non-commercial; en source; latency replay does not parallelize cheaply | [LINDAT](https://lindat.mff.cuni.cz/repository/items/1200e094-1ae1-44ec-8fca-a4ff9d45cb08), [whisper_streaming](https://github.com/ufal/whisper_streaming) |
| **IWSLT simultaneous track sets** | A | unsegmented ACL talks, en→de/zh/ja, cs→en dev (search) | **StreamLAAL** + BLEU/COMET, scored with SimulEval | free (task data) | free | wall-clock-bound | published system reports each year | speech *translation*, not attribution; talks not chamber | [IWSLT 2026](https://iwslt.org/2026/simultaneous), [SimulEval](https://github.com/facebookresearch/SimulEval) (archived, CC-BY-SA-4.0, gh) |
| **Live-stream replay of Congreso sessions** | B/C | Canal Parlamento stream or archive MP4 replayed at 1× | **finalization lag p50/p95, name revision rate, normalized erasure, first-vs-final name accuracy** (§4.5); lags 30 s / 2 min / 5 min (R7) | free | free | 1× real time per session; 8 concurrent 5 s-window sessions sustained at **p95 2.36 s** on one 8 GB GPU (gh, `concurrency.json`) | none published for named attribution | an archive MP4 is not a live feed (no jitter, no encoder delay); H-007 needs the *stream* | [Canal Parlamento](https://www.youtube.com/@CanalParlamento-Congreso_Es), [SimulStreaming](https://github.com/ufal/SimulStreaming) |
| **Stability metrics on any of the above** | A(protocol) | any streaming log | Unstable Partial Word/Segment Ratio, Normalized Erasure, flicker (§3.9) | metric definitions only | free | negligible | Shangguan et al.; Arivazhagan et al. (§3.9 refs) | **no reference implementation located** that computes all of them — write it into the versioned harness (§4.6) | [arXiv 2006.01416](https://arxiv.org/abs/2006.01416), [meta-eval](https://arxiv.org/html/2509.17349v2) |

* * *

### 9.10 Calibration and abstention

| Eval | Class | Data | Metric & protocol | Access / licence | Cost to obtain | Compute per run (CPU / 1 GPU) | Reference results | Caveats | Links |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **VoxCeleb / VoxSRC trial scoring with Cllr, minDCF, actDCF** | A | any trial list above | **Cllr / minDCF / actDCF at the deployed threshold** (§4.2 `mechanism`/`guard`): actDCF ≫ minDCF means the threshold is wrong | free | free | scoring: seconds | trainable AS-norm **+4.1% EER, +10.6% minDCF** rel.; language-dependent s-norm ~3.3% (§3.5) | calibration transfers poorly across domains — the point of measuring it in-domain | [WeSpeaker](https://github.com/wenet-e2e/wespeaker), [Kiwano](https://arxiv.org/pdf/2606.22369) |
| **VoxWatch open-set operating points** | A | watchlist sweeps | FAR at fixed FRR vs watchlist size; the abstention threshold set to FAR < 1/N per session (§3.5) | free lists (search) | free | seconds after embeddings | calibration + fusion gave the largest gains, **not** AS-norm (§3.5) | not same-room; the curve shape is what transfers, not the values | [arXiv 2307.00169](https://arxiv.org/pdf/2307.00169) |
| **Coverage–TAA sweep on the in-domain benchmark** | C | the Congreso benchmark | sweep the abstention threshold; report **the full coverage–accuracy curve, its area, and TAA@{100,95,90}%**; ECE over turns (§4.4) | to build | included in §C | seconds once posteriors are logged | none | requires the system to emit a **distribution over the candidate set plus unknown mass**, not a top-1 label — a design constraint on every component, imposed before the first measurement | [agenda §4.4](research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md) |

* * *

### 9.11 Cross-cutting proxy and synthetic evals — cost, speed, validity

| Proxy | Build cost / speed | What it validly measures | How it misleads |
| --- | --- | --- | --- |
| **TTS-synthesized multi-speaker sessions** (Piper `ca_ES`, `es_ES/AR/MX`; **no eu or gl Piper voice exists**, gh) | CPU-hours; a 5 h “session” for pennies; regenerable on every commit | harness plumbing, SegLST/RTTM round-trips, turn logic, roster-id bookkeeping, scorer correctness, speaker-count guard, throughput | perfect ground truth and no room, no overlap, no interjections, no PA, no channel; embeddings of a single TTS voice are unnaturally tight, so **open-set FAR looks far better than it is** |
| **Interpreter overlay simulation** (Spanish TTS or a second real speaker summed over ca/eu/gl floor audio at 2–4 s lag, swept SNR) | ~1 GPU-hour to build 10 h; scoring adds ≈17 min on GPU-A | monotone degradation curves: ΔWER and ΔTAA vs mixing gain and lag; whether TSE with archive enrollment recovers the loss (H-005) | linear summation ≠ the broadcast chain’s AGC/ducking/single-encoder mix; constant lag ≠ a human interpreter’s variable lag; results will be optimistic — calibrate against one real clip (Q-003, `ffprobe` + listening) |
| **VoxPopuli S2S interpretation mixing** (1.2k h with Spanish as target, real interpreters, real lag) | free download; mixing is CPU-cheap | the *only* proxy with **real interpreter voices and real interpreting lag**; interpreter-enrollment ID (attack 2 of §3.2) | source languages are en/de/fr…, not ca/eu/gl; EP interpreting booth conditions differ from remote Congreso interpreters |
| **Replayed-room / RIR + MUSAN augmentation** | negligible on top of a base run (lhotse `rir_noise`, `musan`) | robustness ranking between systems under a fixed perturbation ladder | simulated RIRs are gentler than a real chamber PA + open mics; absolute augmented numbers are not comparable to published DER |
| **Text-only ParlaMint evals** (chair-announcement coverage, agenda constraint, stage-direction base rates) | minutes on CPU; free | **the cheapest decisive experiment in the programme**: R3’s parser is worth nothing if the chair announces speakers rarely — measure coverage before building it (H-003) | the record’s chair announcements are edited text, so coverage in the record is an upper bound on what is audible; no timing |
| **API-agreement evals (Parlamento.ai style)** | USD **0.71 per audio-hour** for paid APIs (gh); GPU-A time for the open model | drift detection, regression alarms, cheap coverage over many languages with no annotation | **agreement is not accuracy** — the operator says so explicitly (readme); on gl/eu the paid consensus may itself be wrong; no speaker metric at all; and the 168-clip sample is **not redistributable** (gh) |

* * *

### 9.12 Human-annotated benchmark to build (the Diario-aligned Congreso set, §5.1)

Published anchors: professional transcription **USD 1–3 per audio-minute (≈USD 60–180
per audio-hour)**, verbatim USD 1.75–3.50/min, timestamps +USD 0.25/min, crowd USD
0.05–0.25/min
([Rev calculator](https://www.rev.com/resources/transcription-cost-rate-calculator-with-estimates),
[GMR rates](https://www.gmrtranscription.com/prices),
[Ditto guide](https://www.dittotranscripts.com/blog/how-much-should-you-pay-for-transcription-factors-that-affect-transcription-rates/),
search); professional transcription runs **3–6× real time**, up to 8× with 5+ speakers,
and **manual speaker-boundary annotation to research precision 15–30× real time**
([Kili audio-annotation guide](https://kili-technology.com/blog/audio-annotation),
search); from-scratch high-quality transcription of a low-resource language measured
**30 h of labour per audio-hour in the lab, 36 h in the field**
([arXiv 2510.12781](https://arxiv.org/abs/2510.12781), search); post-editing ASR beats
re-transcription **only below ~25% WER**, and edit time scales with word edit distance
([Althingi, arXiv 1807.11893](https://arxiv.org/pdf/1807.11893), search); alignment
yield sets how much survives: **41% of EuroSpeech segments under CER 10%, 65% under 20%,
78% under 30%** (§3.11).

| Layer | Method | Effort per session-hour | Cost per session-hour | 5-session smoke set (≈20 h) | 50-session full set (≈250 h) | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| (i) **Verbatim transcript correction** | correct a `whisper-large-v3-LoS` draft against audio, then force-align (§5.1 step 5) | **2–4 h** post-editing at the in-domain WER Parlamento.ai implies (well under the 25% Althingi break-even), vs 3–6 h from scratch | ≈EUR 60–160 (at EUR 25–40/h) or USD 60–180 at market transcription rates | 40–80 h, **EUR 1.2k–3.2k**, 2–3 weeks with 2 annotators | 500–1,000 h, **EUR 15k–32k**, 3–6 months with 4 annotators | dominates the budget; correct **only the verbatim subset**, not all 50 sessions, and keep the record layer as the cheap tier |
| (ii) **Speaker-label verification** | accept/reject a proposed roster id per turn against the Diario’s own labels; hand-check only disagreements | **0.5–1.5 h** (verification, not annotation) | EUR 15–60 | 10–30 h, **EUR 0.3k–1.2k** | 125–375 h, **EUR 3k–15k** | the Diario gives the labels for free — this is *verification*; the expensive residue is interjections and unannounced interventions |
| (iii) **Timestamp verification** | spot-check forced-alignment boundaries; full manual boundary marking only on a sample | **0.3–1 h** for spot-checks; **15–30×** real time if boundaries were marked from scratch (Kili) | EUR 10–40 spot-check | 6–20 h, **EUR 0.2k–0.8k** | 75–250 h, **EUR 2k–10k** | never mark boundaries by hand at scale — align, then verify a stratified sample; report the sampled boundary error |
| (iv) **Stage directions** | mark applause/laughter/murmur spans, reconcile with the record’s `(Aplausos.)` marks | **0.2–0.5 h** | EUR 5–20 | 4–10 h, **EUR 0.15k–0.4k** | 50–125 h, **EUR 1.5k–5k** | cheap; do it in the same pass as (i) |
| **Second-annotator agreement sample** | 10% double-annotated (§4.1) | +10% of (i)–(iv) | +10% | +EUR 0.2k | +EUR 2k–5k | without it the reference has no error bar and §4.7’s bootstrap is over-confident |
| **Totals** | — | **3–7 annotator-hours per session-hour** | — | **≈EUR 2k–6k, 3–5 weeks** | **≈EUR 25k–65k, 4–8 months** | plus engineering: acquisition, Diario parsing, alignment, split freezing (§5.1 steps 1–4, 7–8) — weeks, not annotator-money |

Cheaper variants worth pricing before committing: **record-layer-only** (no verbatim
correction) costs the parsing effort alone and still supports TAA, record-WER and
chair-announcement evals — it just cannot separate the editorial gap; and
**alignment-filtered** sets (keep only segments under CER 10%, as EuroSpeech does) buy
41% of the material for zero annotation, at the price of a biased-easy test set.

* * *

### 9.13 Eval harness tooling

| Tool | Covers | Licence / status | Notes |
| --- | --- | --- | --- |
| [**MeetEval**](https://github.com/fgnt/meeteval) | cpWER, ORC-WER, MIMO-WER, **tcpWER / tcORC / tcMIMO with `--collar 5`**, greedy approximations, **DI-cpWER**, and **DER via a wrapped `md-eval-22`** (`meeteval-der dscore --collar .25`); STM/CTM/SegLST/RTTM I/O; error visualization (readme) | MIT, 178★, active (gh) | the natural home for §4.6’s single versioned harness; SegLST is the CHiME format |
| [**dscore**](https://github.com/nryant/dscore) | DER/JER, collar and overlap options (readme via MeetEval) | BSD-2, 273★ (gh) | the reference DER implementation the literature quotes |
| [**pyannote.metrics**](https://github.com/pyannote/pyannote-metrics) | DER components, identification error rate, purity/coverage | MIT, 259★ (gh) | needed for the *identification* error rate variants, which MeetEval does not do |
| [**lhotse**](https://github.com/lhotse-speech/lhotse) | recipes for **ami, ali_meeting, callhome_english, dihard3, fleurs, commonvoice, libricss, librimix, librispeechmix, mls, notsofar1, voxceleb, voxconverse, voxpopuli, musan, rir_noise, fisher_spanish, mtedx** (gh) | Apache-2.0, 1,149★ (gh) | **no recipe exists for ParlamentParla, Basque Parliament, Nos_ParlaSpeech-GL, 3CatParla or EuroSpeech** — writing those five manifests is the first harness task |
| [**Open ASR Leaderboard**](https://github.com/huggingface/open_asr_leaderboard) | end-to-end WER + **RTFx** scripts across many backends; multilingual and long-form suites | Apache-2.0, 245★ (gh) | reuse the normalizer and RTFx measurement; results CSV at `hf-audio/open-asr-leaderboard-results` (gh, cached in open-source-asr) |
| [**chime-utils**](https://github.com/chimechallenge/chime-utils) | CHiME-8 DASR data prep, manifests and **MeetEval-based scoring** for CHiME-6/DiPCo/Mixer6/NOTSOFAR-1 | MIT, 27★ (gh) | the closest thing to a turnkey SA-ASR scoring pipeline |
| [**NOTSOFAR1-Challenge**](https://github.com/microsoft/NOTSOFAR1-Challenge) | baseline system + tcpWER scoring | MIT, 66★ (gh) | tcpWER reference implementation in a full pipeline |
| [**TS-ASR-Whisper**](https://github.com/BUTSpeechFIT/TS-ASR-Whisper) | DiCoW/SE-DiCoW training and inference; `./scripts/create_emma_submission.sh` for the **EMMA JSALT25 benchmark** (readme) | Apache-2.0, 121★ (gh) | the only public path to comparable tcpWER on the DiCoW family |
| [**ESPnet egs2**](https://github.com/espnet/espnet) | `ami`, `commonvoice`, `fleurs`, `mls`, `chime6/7/8`, `fisher_callhome_spanish`, `slue-voxpopuli`, `slue-voxceleb`, `iwslt*` (gh) | Apache-2.0, 9,949★ (gh) | full recipes with published baselines; heavier setup than lhotse-only scoring |
| [**SpeechBrain recipes**](https://github.com/speechbrain/speechbrain) | `VoxCeleb`, `VoxLingua107`, `VoxPopuli`, `AMI`, `CommonVoice`, `LibriMix`, `LibriParty`, `DNS`, `WHAM` (gh) | Apache-2.0, 11,802★ (gh) | LID and SV recipes are the fastest route to a calibrated baseline |
| [**NVIDIA-NeMo/Speech**](https://github.com/NVIDIA-NeMo/Speech) + [**NeMo-text-processing**](https://github.com/NVIDIA/NeMo-text-processing) | ASR/diarization eval scripts; **Spanish ITN/TN grammars with 22 test-case files** (gh) | Apache-2.0, 18,376★ / 495★ (gh) | the es ITN tests are a seconds-long CPU regression gate |
| [**WeSpeaker**](https://github.com/wenet-e2e/wespeaker) / [**3D-Speaker**](https://github.com/modelscope/3D-Speaker) / [**redimnet2**](https://github.com/PalabraAI/redimnet2) | VoxCeleb trial lists, cosine + AS-norm + QMF scoring, pretrained extractors with published EERs | Apache-2.0 / Apache-2.0 / MIT (gh) | supplies both the extractors of §3.5 and the trial-scoring code for §10 |
| [**WeSep**](https://github.com/wenet-e2e/wesep) / [**asteroid**](https://github.com/asteroid-team/asteroid) | target-speaker extraction and separation training/eval (SI-SNRi, SDRi) | no licence declared / MIT (gh) | for the §6 overlay experiments; WeSep has no declared licence — check before use |
| [**SimulEval**](https://github.com/facebookresearch/SimulEval) + [**SimulStreaming**](https://github.com/ufal/SimulStreaming) / [**whisper_streaming**](https://github.com/ufal/whisper_streaming) | AL/LAAL/StreamLAAL/AP/DAL scoring; `--comp_unaware` computation-unaware lower bound (readme) | CC-BY-SA-4.0 **archived** / MIT / MIT (gh) | SimulEval is archived (last push 2024) — pin it, or reimplement the three metrics §4.5 actually needs |
| [**jiwer**](https://github.com/jitsi/jiwer) + HF `evaluate`/`datasets` | WER/CER primitives, dataset loading | Apache-2.0, 926★ (gh) | fine for speaker-agnostic WER only; **never** for cpWER |
| [**SCTK**](https://github.com/usnistgov/SCTK) | `sclite`, CTM/STM format definitions | NIST, 242★ (gh) | the format authority MeetEval defers to |
| [**ctc-forced-aligner**](https://github.com/MahmoudAshraf97/ctc-forced-aligner) / [**WhisperX**](https://github.com/m-bain/whisperX) | word-level alignment for es/ca/eu/gl; builds the timestamp layer of §4.1 | MIT / BSD-2 (gh) | MMS-300m windowed 30 s / 2 s overlap covers all four languages (§3.10) |
| [**EuroSpeech `alignment_pipeline`**](https://github.com/SamuelPfisterer/EuroSpeech) | VAD+diarization segmentation, Whisper draft, two-stage CER-based alignment to PDF/DOCX/HTML/SRT transcripts (readme) | licence **TBD** (readme) | the reusable part of EuroSpeech for §5.1 step 4; unresolved licence is a real adoption risk |
| **To be written** (§4.6, §7) | per-turn **TAA / coverage-curve scorer**; ECE over turns; **finalization lag, name revision rate, normalized erasure** logger; chair-announcement parser | — | no public implementation of any of these was located; they are the harness’s own deliverables |

* * *

### 9.14 Gaps — sub-problems with no credible public eval

1. **Named speaker attribution on Spanish parliamentary audio.** Nothing exists.
   REPERE (French TV, 2012–14, ELRA-paid) is the only public *named*-attribution
   benchmark in any language, and pyannote still reports DER on it — not EGER.
   Everything in §7 depends on building the §5.1 set.
2. **Open-set identification at ~350 speakers with same-room enrolment.** VoxWatch and
   VoxBlink2 measure cross-condition internet audio; Novoselov gives the 100→700 scaling
   shape. No public curve exists for the matched-condition regime every deployment lives
   in (§8.2).
3. **Separation of simultaneous interpretation from its source on real audio.** No
   dataset, no metric, no baseline.
   VoxPopuli’s 17.3k h of interpretation and ESIC are the closest surrogates, and
   neither carries the Congreso’s single mixed track.
4. **cpWER/tcpWER on parliamentary speech.** Every published SA-ASR number is meetings
   (AMI, NOTSOFAR-1, AliMeeting) or synthetic mixtures.
   A VoxPopuli-es cpWER pilot would be the first, and it is nearly free.
5. **Chair-announcement parsing as an attribution source.** No system, no eval set; the
   ParlaMint text-only coverage measurement has to be run before R3 is worth building.
6. **Stage-direction detection on chamber acoustics.** AudioSet/ESC-50 measure isolated
   clips; the Diario’s own marks are the only in-domain reference and they are
   editorially filtered.
7. **Spanish punctuation/capitalization benchmark.** LibriSpeech-PC is English-only; the
   NeMo es tests are unit tests.
   Nothing measures record-form punctuation in Spanish parliamentary register.
8. **Streaming stability metrics.** Definitions exist (UPWR, normalized erasure,
   flicker); no reference implementation was located, and SimulEval — the nearest
   toolkit — is archived.
9. **Conference-system metadata (R11).** Not testable on public data at all; the
   evaluation can only report what fraction of turns such metadata *would* resolve.

## 10. Cross-Service and Cross-Language ASR Evaluation Protocol

How to measure ASR quality per language and per service against ground truth, so that a
claim like “service X is best for Basque” carries a confidence interval and a
contamination flag. The agenda’s §11.5 summarises it and cites it as (inventory §10.n).

Drafted 2026-09-02 for the parliamentary-transcription research program.
Sources: the agenda (§3.3, §3.10, §4, §5), the briefs `inv-asr.md`,
`inv-data-services.md`, `inv-evals.md`, and the Parlamento.ai `open-source-asr`
repository read this session.
Tags: **(brief)**, **(agenda)**, **(checked)** = verified this session, **(search)** =
web snippet only, **(unverified)**.

#### 10.10.1 What we already have, honestly

| Source | Languages | Ground truth | Services | Why it does not answer the question |
| --- | --- | --- | --- | --- |
| Open ASR Leaderboard: English, multilingual (CoVoST-2 + FLEURS de/fr/it/es/pt), long-form (brief) | es, pt; **no ca/eu/gl** | yes, read speech | open models; a few APIs on English only (best open 4.46 vs paid 4.43, inv-evals) | FLEURS es is Latin-American read speech; nothing parliamentary; test sets sit in training mixes; the top spans under 1 point, inside normalizer noise |
| Artificial Analysis AA-WER v2 (brief) | English | yes | most of the list in scope | English only; half proprietary, not reproducible; a quarter VoxPopuli, which several models train on |
| Parlamento.ai `open-source-asr` (agenda §3.3; checked `comparacion.json`) | es (cl/pe/es pooled), ca, gl, eu, en, pt, EP mix; 24 five-minute chunks each | **no** — edit distance to gpt-4o-transcribe, gpt-transcribe, nova-3 | 3 paid + ~15 open | agreement, not accuracy; paid APIs disagree by a median 5.1 (es), 11.7 (ca), 13.0 (gl), 15.1 (eu), p90 42.6 on eu; Nova-3 excluded from the eu/gl reference; clips not redistributable; 24 clips → ±2–3 points |
| Vendor claims (brief; search): Nemotron 3.5 FLEURS es 4.11; Voxtral Transcribe 2 5.9 avg; Canary-1B-v2 8.1 avg; Scribe ca 3.1 FLEURS / 5.5 CV, no eu tier | per vendor | yes; normalizer and subset unstated | self only | self-selected read sets; no eu/gl figure from anyone; not comparable across vendors |
| Specialists’ WERs (brief): xezpeleta eu 4.84 CV18; HiTZ eu 10.62 CV13; 3catparla ca 0.96 in-domain broadcast; adriszmar es 5.34 CV17; Whisper-LM FLEURS ca 5.68 / gl 10.06 / es 15.01; BSC LoS none | one each | yes | none | different corpora, CV vintages, normalizers; parliamentary fine-tunes trained on the parliamentary corpora |
| Albayzin 2024 BBS-S2T (inv-evals §1) | eu+es, Basque Parliament | yes, audited | challenge systems only (PRHLT 3.44) | no service or generalist; one chamber |
| VoxPopuli / Europarl-ST / Europarl-ASR baselines (brief) | es (EP), en | yes; en has a manual verbatim 17.5 h layer | none | 2021 baselines (es 15.3); EP, not Congreso; non-verbatim except the en layer |
| BSC ca–es code-switching study (brief) | ca–es CS, 4 h 09 min | yes | none | Whisper variants only |

Nothing combines ground truth, the services, and the four Spanish-state languages on
parliamentary audio.
The eu/gl tails in the Parlamento.ai data sit exactly where open models and APIs
disagree most, and nobody knows which side is wrong.

#### 10.10.2 Sample design per language

#### 10.2.1 Ground-truth sources

Parliamentary first, read speech as a variety probe.
“Verbatim enough” means produced or audited against the audio, not edited for the
record.

| Language | Parliamentary, audited transcript | Read speech / variety metadata |
| --- | --- | --- |
| es-ES | **Congreso verbatim sample (build, §2.4)**; Basque Parliament 1 es-tagged segments (CC0, audited; brief); VoxPopuli es test (EP official text; licence CC0 vs CC-BY-NC unresolved; contaminated); Europarl-ST es (2008–12, NC) | CV es test ≈22 h, 11 accent tags; MLS es test (contaminated) |
| es-419 | **Chilean sample (build, same recipe)** — no public LatAm parliamentary set with audited text was found; Parlamento.ai’s Chile/Peru audio is not redistributable | FLEURS es_419 ≈1.5 h; CV es strata `mexicano`, `rioplatense`, `chileno`, `andino` — the only variety handle until the sample exists |
| ca | ParlamentParla v2 clean_test **5.2 h** (2.71 F + 2.52 M, speaker-disjoint, CC BY 4.0, 2007–18; checked README) — aligned to official text, so hand-audit a 1 h subset and report its residual WER; BSC CS test 4 h 09 min | CV ca ≈22 h; FLEURS ca_es; 3CatParla is broadcast, not chamber |
| eu | Basque Parliament 1 dev 7.6 h + test 9.6 h, audited, per-segment tag eu/es/bilingual, CC0; Albayzin eval 23.8 h — the best-licensed set in the program | CV eu ≈21 h; **no FLEURS eu** |
| gl | Nos_TranscriSpeech-GL debates/speeches subset (53 h total across 4 domains, manual, gated — request on day one; subset size unverified); Nos_ParlaSpeech-GL is auto-aligned under parliament terms, not ground truth | CV gl ≈22 h; FLEURS gl_es |
| en | Europarl-ASR 17.5 h manual verbatim + official layers (CC BY 4.0) — the two-layer shape the Congreso set needs; VoxPopuli en test | CV en |
| pt | FalAR (Portuguese parliament, 2026 preprint, access unconfirmed); Brazil is the chamber in the workload (agenda §3.3) | MLS pt test; CV pt with pt-BR/pt-PT tags; FLEURS pt_br |
| code-switched | Basque Parliament bilingual segments; BSC CS ca–es; Congreso co-official interventions (build) | — |

#### 10.2.2 How many hours give a ±1-point CI

Two arguments land on the same range.
**Word-level, ignoring clustering:** SE ≈ √(p(1−p)/W); at WER 0.10 a 95% half-width of 1
point needs ≈3,500 reference words (≈25 min at 140 wpm); at 0.20, ≈6,150 words (≈45
min). **Clip-level, which is what matters:** errors cluster by clip and speaker.
For 30 s clips at a mean of ~10 the per-clip standard deviation is typically 8–12
points; the Parlamento.ai tails say the same (es median 5.1 vs p90 13.6; eu 15.1 vs
42.6, checked).
A bootstrap over clips needs N ≥ (1.96·σ)²: σ = 8 → ~250 clips (2.1 h); σ
= 12 → ~550 clips (4.6 h) — a design effect of 4–8 over the word-level figure.

So **2–3 h per language for es, ca, en, pt** (expected WER 5–12) and **4–6 h for eu and
gl** (expected 10–25, heavier tails).
The σ values are assumptions; the first 50 pilot clips re-estimate them and the sample
is topped up.
A **paired** difference on identical clips has σ_diff ≈ 0.4–0.6 σ, so ±1 on
a difference costs a quarter to a third of the above — the reason the design is paired.
With 24 five-minute chunks the Parlamento.ai per-language CIs are ±2–3 points, which is
why its ca/gl/eu rankings cannot separate models.
Cluster controls: ≥25 speakers per language, no speaker above 8% of words, stratified
bootstrap (speakers, then clips).

#### 10.2.3 Clip selection

Unit: 20–60 s segments for the aligned public sets; 3–6 min turn-aligned chunks for
parliamentary audio (Parlamento.ai’s unit is 5 min, checked `audio-audit.json`), cut at
speaker turns so streaming is realistic.
Strata, seeded random draw: speaker (capped), duration (<10 s, 10–30, 30–120, >120),
noise tertile from an automatic audit (estimated SNR, silence fraction, longest pause —
what the Parlamento.ai `audio_audit` computes), gender, variety tag, language tag
(eu/es/bilingual), session date.
Publish the manifest (ids, offsets, strata), not the audio (agenda §5.1 step 8).

#### 10.2.4 The missing piece: a verbatim-corrected Congreso sample

- **Size:** 4 h — 3 h es from ≥12 sessions and ≥30 speakers (plenary and committee,
  question time and debate) as 36 five-minute chunks, plus 1 h of co-official
  interventions (≈20 min each ca/eu/gl) with both channel conditions kept (agenda §5.1
  step 2). A parallel 2 h Chilean sample if the second chamber matters.
- **Who corrects:** native transcribers per language correct a `whisper-large-v3-LoS`
  draft against the audio (agenda §5.1 step 5) under written verbatim conventions:
  disfluencies kept, numbers as spoken and spelled out, no ITN, tags for unintelligible,
  overlap, interpreter bleed, foreign words.
- **Double annotation at 100%**, not 10%, because 4 h is cheap: two independent
  corrections, inter-annotator WER reported, a third adjudicated version is the
  reference. Inter-annotator WER (expect 1–3 points) is the floor below which service
  differences are not interpretable.
- **Effort:** 3–4 annotator-hours per audio-hour (inv-evals §C) × 4 h × 2 annotators +
  ~6 h adjudication ≈ 34 h ≈ **EUR 1.0–1.4k** at EUR 30–40/h, two weeks; the Chilean
  sample adds EUR 0.5–0.7k.
- **Post-cutoff rule:** sessions after 2026-03 only, later than every disclosed training
  date in scope (LoS trained Nov 2025; NeMo v3 and Granite 4.1 early 2026; vendor
  cutoffs undisclosed); session dates in the manifest so the rule is re-applicable.

#### 10.2.5 Contamination rule

Declared training found this session: Granite Speech on CommonVoice 17, MLS, VoxPopuli
(search); Parakeet/Canary/Nemotron v3 on Granary — YODAS, YouTube-Commons and MOSEL,
which folds in VoxPopuli — plus an undisclosed NeMo ASR Set (search); xezpeleta eu on
Basque Parliament 2013–2022, the whole corpus (brief); Nós gl presumably on
Nos_ParlaSpeech-GL (brief); BSC LoS’s 8,110 h undisclosed at corpus level (unverified).
Whisper and every service are undisclosed; assume public test splits have been seen.

Each (system, set) cell carries a flag: **C0** post-cutoff or gated, **C1** undisclosed,
**C2** declared. Headlines come only from C0 sets; C2 cells are greyed.
Per set: CV and FLEURS → C1/C2 for all open models and for vendors quoting them, usable
only as a relative variety probe; VoxPopuli es → C2 for Granite and NeMo, C1 for
services on the AA-WER board; Basque Parliament test → C2 for xezpeleta and possibly
LoS, C0/C1 for services; ParlamentParla clean_test → C2 for the BSC Catalan line
(likely); Nos_TranscriSpeech-GL → C0 except Nós models; Congreso and Chilean samples →
C0 for all.

#### 10.10.3 Per-service run protocol

1. **Language conditions.** Two runs per clip: *hinted* (BCP-47 per clip: es-ES, es-CL,
   es-MX, es-PE, ca-ES, eu-ES, gl-ES, en-US, pt-BR) and *auto-detect*; report both, log
   the detected language.
   Support: AssemblyAI Universal and Soniox list ca/eu/gl (search); Speechmatics all
   three (brief); Amazon Transcribe streams all three (brief); Azure lists `ca-ES`,
   `eu-ES`, `gl-ES` (checked, raw docs); ElevenLabs Scribe lists ca and gl with WER
   tiers, eu without one (search); Rev.ai ca and gl, eu not found (search); **Deepgram
   Nova-3 lists none of the three** (search) — run es/en/pt hinted and everything auto,
   record the failure mode; Google Chirp 3 ca-ES GA, eu/gl unconfirmed (page unreadable
   here); OpenAI whisper-1 and gpt-4o-transcribe inherit Whisper’s declared 99; Gemini
   takes a prompt.
2. **Formatting off where a switch exists** (Deepgram `smart_format`/`punctuate`/
   `numerals` false; AssemblyAI `format_text`/`punctuate` false; Google punctuation off;
   Azure `lexical` output, the pre-ITN form).
   Keep the formatted output too.
   Where no switch exists (OpenAI, Gemini, Amazon) the normalizer does the work and the
   cell says so.
3. **One versioned normalizer on reference and hypothesis alike:** NFC; lowercase; unify
   apostrophes (`'`, `’`, `ʼ`) and ela geminada (U+00B7, U+2027, `l.l`, `l-l` → `l·l`);
   strip punctuation except intra-word apostrophes, hyphens, middle dots, then split on
   whitespace; digits, percentages, currency, ordinals → words per language in *both*
   texts (num2words for es/pt/ca; eu/gl coverage unverified, project table if missing);
   acronym periods removed; accents kept (the Parlamento.ai normalizer is right on this,
   checked `score.py`); Basque compounds never split — hyphen removed, one token;
   Galician contraction spellings that are orthographic equivalents (`ó`/`ao`) mapped
   through a versioned per-language equivalence list.
   Whisper’s `BasicTextNormalizer` runs as a stricter second column to show sensitivity.
4. **Diarization off; 16 kHz mono PCM WAV; identical bytes to every system**; no VAD
   trimming beyond leading and trailing silence.
5. **Pinning:** model id, response model/version fields, API version, region, SDK
   version, every request parameter, timestamp; open models: HF revision hash, runtime
   and version, compute type, beam, temperature 0, `condition_on_previous_text` off, VAD
   setting.
6. **Repeats:** three runs per clip for LLM-decoder services (gpt-4o-transcribe, Gemini,
   Scribe v2) and any open model above temperature 0; report mean and run-to-run spread;
   flag clips whose spread exceeds 5 points.
7. **Streaming and batch as separate conditions.** Streaming replays PCM in 100 ms
   frames at 1× wall-clock over the vendor socket; every partial and final logged with
   wall time; the final transcript is scored; finalization lag p50/p95
   (computation-aware, agenda §4.5) and normalized erasure (§3.9) reported.
   Batch logs turnaround.
8. **Timeouts, caps, cost.** 3 retries with backoff, then a logged failure; error and
   empty-output rates are results.
   Clips ≤5 min keep every system inside its synchronous limits except Google (60 s sync
   → batch recognize); OpenAI’s 25 MB cap is fine at 16 kHz PCM to ~13 min.
   Billed seconds or tokens from usage fields per request; realized $/h beside list
   price.

#### 10.10.4 Metrics and statistics

- **WER** primary for es, ca, gl, en, pt; **CER** primary for eu, WER secondary.
  Basque attaches case and number suffixes to the word, so one wrong suffix is a whole
  word error, tokens per minute are ~30% fewer and per-clip variance higher; CER has
  finer resolution and Albayzin reports it alongside.
  Both metrics for every language.
- **Intervals:** stratified bootstrap over clips within speakers, 10,000 resamples, WER
  recomputed as a ratio of sums, 95% percentile CI.
- **Paired comparison:** all systems on the same clips; per-clip differences with
  bootstrap CI, a sign test, win counts.
  Pre-register the primary contrasts (best service vs LoS per language; hinted vs auto;
  streaming vs batch) so the pairwise matrix stays descriptive rather than a
  multiple-testing exercise.
- **Stratified reporting:** variety (es-ES vs each CV accent; pt-BR vs pt-PT), duration,
  noise tertile, monolingual vs code-switched, hinted vs auto, batch vs streaming,
  contamination flag; each stratum with n and CI.
- **Code-switching:** (a) WER on embedded-language words (token-level tags from the
  reference, or lexicon LID where only segment tags exist, as in Basque Parliament); (b)
  switch-window WER on ±3 words around each switch; (c) language-collapse rate — share
  of CS clips with under 10% of hypothesis tokens in the embedded language, i.e.
  translated or dropped; (d) wrong-language rate overall.
- **Guards:** hallucination rate on non-speech clips (insertions per hour), empty-output
  rate, truncation rate (hypothesis/reference length ratio under 0.6; Parlamento.ai
  alerts at 0.45, checked `paid.py`).
- **“Accurate” means** 95% half-width ≤1.0 point on each language headline (CER for eu),
  ≤1.0 on each pre-registered paired difference, ≤2.0 per stratum.
  Cost: the hours in §2.2, and ≥60–100 clips for any stratum reported on its own.

#### 10.10.5 Cost and time budget

Audio, first pass: es-ES 3 h Congreso + 2 h Basque-Parliament/VoxPopuli es; es-419 2 h;
ca 5 h (clean_test + 2 h CS); eu 5 h; gl 4 h; en 2 h; pt 2 h — **≈25 h**. Full matrix
adds CV/FLEURS/MLS strata, the 23.8 h Albayzin eval and the Chilean sample: **≈60 h**.

| Service | List price per audio-hour | From the AWS+Google sandbox |
| --- | --- | --- |
| Amazon Transcribe | $1.44 batch and streaming (brief) | yes |
| Google Cloud STT Chirp 3 | $0.96; $0.18 dynamic batch (brief) | yes |
| Gemini 2.5 transcription | ≈$0.12–0.30 by audio tokens (unverified) | yes, via Vertex AI |
| Deepgram Nova-3 | $0.46 streaming (brief); batch ≈$0.26 (unverified) | no |
| AssemblyAI Universal-3.5 | $0.45 streaming (brief); async ≈$0.15–0.27 (unverified) | no |
| Speechmatics | ≈$0.30 batch / $0.40 realtime, sources conflict (brief) | no |
| ElevenLabs Scribe v2 | $0.22 batch / $0.39 realtime (brief) | no |
| OpenAI whisper-1, gpt-4o-transcribe | ≈$0.36 each (from $0.006/min, unverified); mini ≈$0.18 | no |
| Azure Speech | ≈$1.00 (unverified) | no |
| Gladia | $0.61 async / $0.75 realtime (brief) | no |
| Soniox | $0.10 async / $0.12 streaming (brief) | no |
| Rev.ai | $2.10 API; $1.20 Reverb (brief) | no |

Batch across the 13 rows ≈ $8 per audio-hour, streaming ≈ $5.5. First pass: 25 h × 2
language conditions × $8 ≈ $400, streaming ≈ $140, LLM repeats ≈ $50 — **≈$600**; full
matrix ≈ $1.5–2k. Ground truth EUR 1.5–2.1k (§2.4). Engineering 3–4 weeks for harness,
normalizer and manifests (no lhotse recipes for ParlamentParla, Basque Parliament or Nós
— inv-evals). GPU: 25 h through ~15 open models on the 8 GB reference card (turbo 35.6
audio-h/h, large-v3 and LoS 9.6, Qwen3-ASR 4.9, xezpeleta 5.0, Granite 133, Parakeet 87,
Nemotron 28; brief) ≈ 40 GPU-h, ≈6 h on an H100. Streaming replay is wall-clock bound:
25 h per system, 8 concurrent → ~3 h each.
Open models need Hugging Face Hub, which this environment lacks — mirror weights into
S3/GCS first.

**Cheapest credible first pass** (one week, ≈$60 API + 10 GPU-h): Basque Parliament
test, ParlamentParla clean_test, BSC CS, Europarl-ASR subset, CV strata for es-419/pt;
the three sandbox services plus turbo, large-v3 and LoS. It answers how far the
reachable services sit from LoS on eu/ca/gl.
The Congreso sample and the internet-box services follow in weeks two and three.

#### 10.10.6 Approaches compared

| Approach | Pros | Cons | Can conclude | Cannot conclude |
| --- | --- | --- | --- | --- |
| A. Public read-speech sets (CV, FLEURS, MLS) | free; variety tags; tiny compute | read, clean, single speaker; contaminated for most open models and quoted by vendors; no FLEURS eu | variety sensitivity; a sanity floor; whether a language code is accepted | in-domain WER; robustness to noise, overlap, interpreter bleed; any absolute ranking |
| B. In-domain parliamentary public sets | real chamber acoustics; audited text for eu and gl; CC0/CC-BY; 5–10 h per language now | not Congreso; ParlamentParla and VoxPopuli aligned to official text; contaminated for the specialists | the eu/ca/gl service-vs-open gap with ±1 CIs; auto-LID failure rates; CS behaviour | Congreso register and mic chain; es-419 |
| C. Verbatim-corrected Congreso sample | C0 for everyone; verbatim layer; what the agenda’s benchmark needs (§4.1, §5.1) | EUR 1–1.4k and two weeks; ±1.2–1.5 on es, wider on the 20-min co-official strata | the headline es-ES number; interpreter-channel effects; record-vs-verbatim gap on the same clips | co-official rankings on their own (B covers those) |
| D. API agreement, no ground truth (Parlamento.ai) | $0.71/h; no annotation; scales to every language and month | agreement ≠ accuracy; consensus wrong exactly on eu/gl; ±2–3 points | drift and regressions; candidate shortlist | which system is right; anything about the tail |
| E. Synthetic TTS clips | perfect script; regenerable | no room, disfluency or overlap; no eu/gl Piper voices (inv-evals §B); TTS is unnaturally easy | harness plumbing, normalizer round-trips, streaming timing | any quality number |

**Recommendation:** run B this week from the sandbox with the open baselines; build C in
parallel and run every service on it in weeks 2–3; run A only as the es-419/pt variety
probe and as a contamination check (a system whose A-rank and B-rank diverge sharply has
seen the read sets); keep D as a monthly drift monitor, never a ranking; use E for
harness tests and never report it.

#### 10.10.7 Pitfalls that make cross-service comparisons wrong

| Pitfall | What goes wrong | Harness control |
| --- | --- | --- |
| Normalizer asymmetry | spelled-out vs digit numbers, `l·l` variants, curly apostrophes, accents stripped on one side — several points on ca | one versioned normalizer on both sides (§3.3); strict column alongside; normalizer version in every row |
| Vendor-side ITN | numbers, dates, currency rewritten; no switch at OpenAI, Gemini, Amazon | lexical/raw output where possible; digits verbalized in both texts; a numbers-only error stratum |
| Punctuation and casing | punctuated outputs tokenize differently | strip after preserving apostrophes and hyphens; punctuation never inside WER — a separate P/C metric if the record needs it |
| Auto-LID on co-official languages | ca and gl decoded as es, eu as es or nothing; es–gl is the known weak pair (inv-evals §2) | hinted and auto both reported; wrong-language guard; detected language logged per clip |
| LLM transcribers paraphrasing or translating | fluent output in other words; co-official speech returned in Spanish; run-to-run variance | temperature 0, fixed prompt, three repeats; language-collapse and truncation guards; paraphrase flagged when WER is high but chrF low |
| Rate limits and length caps | partial coverage silently biases toward easy clips | a cell is reported only at 100% manifest coverage (the Parlamento.ai “piloto” rule, checked `METHODOLOGY.md`); failures logged as data |
| Sample rate, codec, chunk edges | MP3 to one vendor, WAV to another; SDK resampling; context loss and hallucinated openings at cuts | identical 16 kHz PCM bytes; codec and rate logged; cut at turns and trim identically; hallucination guard on silent clips |
| API drift mid-study | a vendor ships a new model | version fields per request; a 30-clip canary weekly; dated results |
| Contamination | inflated scores on public splits | C0/C1/C2 flag per cell (§2.5); headlines from C0 only |

## Methodology

**Inputs and passes.** Four inventory briefs were written in parallel on 2026-09-02 from
the agenda’s §3, §4, §6 and §7, each with a bounded scope (ASR; speaker; joint, text and
multimodal; data and services), each recording where every number came from.
A consolidation pass merged them into 266 rows in 20 sections with uniform columns,
applied two mechanical overrides (archived repository, or no push since 2025-09-02,
forces Legacy), and listed nine cross-brief conflicts and the rows without a verified
link. A review pass then read the label definitions against the rows, re-read the local
README copies and raw files where it could, ran seven web searches, and produced the
corrections, gaps, shortlist and avoid list that this report applies.
The evaluation inventory’s harness table was used to keep the scorer names in §3.10 and
§4 consistent with the measurement protocol.

**Signal sweep.** `ghmeta.sh` calls `gh api repos/<owner>/<repo>` for each repository
and records `stargazers_count`, `license.spdx_id`, `pushed_at` and `archived`; its
output is `repo-meta.jsonl`, 163 repositories, collected 2026-09-02. Signals cells quote
it as `N★ · pushed YYYY-MM-DD`, with `· archived` where set.
Rows whose repository is absent from the sweep carry the brief’s own text with
`(brief)`, or the review’s API check with `(review)`. Two repositories in the sweep
matched no brief row: `ibm-granite/granite-speech` is used as the Granite Speech signal;
`mistralai/mistral-common` has no row.
Eight repositories moved since the briefs were written and are linked at their new paths
(`NVIDIA/NeMo` → `NVIDIA-NeMo/Speech`, `argmaxinc/WhisperKit` →
`argmaxinc/argmax-oss-swift`, `lowerquality/gentle` → `strob/gentle`,
`xashru/punctuation-restoration` → `maveryn/punctuation-restoration`,
`fabiankueppers/calibration-framework` → `EFS-OpenSource/calibration-framework`,
`praat/praat` → `praat/praat.github.io`, `Wikidata/Wikidata-Toolkit` →
`Wikidata-Toolkit/Wikidata-Toolkit`, `FunAudioLLM/SenseVoice` → `QwenAudio/SenseVoice`).

**Label rules applied.** Legacy is used exactly as defined: archived, or no push in over
a year before 2025-09-02; the push date stays in the Signals cell so the margin is
visible (DiarizationLM misses by three weeks, ClearerVoice-Studio by 19 days).
Off-scale labels from the briefs ("Production (elsewhere)", “Established / Recent /
Restricted”, “Do not use”) are gone: datasets take an access class in §5, services take
an access class in §6, and retired services are listed in §7. The specific up- and
downgrades are recorded in the final message of the session that produced this report
and are visible by comparing the Label cells with the consolidated inventory.

**Network constraint.** Egress allowed GitHub hosts and web-search summaries only.
Figures marked (readme) were read from local copies of the pyannote, DiariZen,
WeSpeaker, ReDimNet2, MOSS-Transcribe-Diarize, TS-ASR-Whisper, SimulStreaming, MeetEval,
EuroSpeech, VoxPopuli, ParlamentParla and Parlamento-ai READMEs, and from raw GitHub
files for WhisperX, ctc-forced-aligner, WhisperLiveKit, WeSep, Dixtral, omnilingual-asr
and OpenParliamentTV-Tools.

**What could not be verified.**

- Any Hugging Face model card, hence the licence of every HF-only model (BSC-LT, HiTZ,
  xezpeleta, Proxecto Nós, projecte-aina, SalamandraTA, Nemotron 3.5) and the CC BY 4.0
  claim for the DiCoW weights.
- The Sortformer v2 CC BY 4.0 licence (card not opened) and the terms on v1 and
  multitalker-parakeet.
- Whether Speechmatics offers enrolment-based identification in ca, eu and gl in
  realtime, as opposed to STT in those languages.
- The open-weight status of Voxtral Mini Transcribe V2.
- The language list behind Qwen3-ForcedAligner’s “11 languages”.
- The Whisper large-v3 FLEURS es 15.01 figure, flagged implausible in the agenda §2.1
  and carried here only as the Whisper-LM paper’s baseline row.
- Whether Phi-4-multimodal or Gemma 3n diarize; Qwen3-Omni and Kimi-Audio were checked
  (neither README mentions diarization) and are omitted on purpose.
- Push dates and stars for the repositories the review added from its own API check
  without recording them (marked “signals not collected”).

## References

Papers cited in the tables, numbered in order of first appearance by section; links as
given in the source briefs.

1. [Whisper: Robust speech recognition via large-scale weak supervision, arXiv 2212.04356](https://arxiv.org/abs/2212.04356)
2. [Whisper-LM: language models for low-resource ASR, arXiv 2503.23542](https://arxiv.org/abs/2503.23542)
3. [Distil-Whisper, arXiv 2311.00430](https://arxiv.org/abs/2311.00430)
4. [CrisperWhisper, arXiv 2408.16589](https://arxiv.org/abs/2408.16589)
5. [Canary-1B-v2 and Parakeet TDT v3, arXiv 2509.14128](https://arxiv.org/abs/2509.14128)
6. [Qwen3-ASR, arXiv 2601.21337](https://arxiv.org/html/2601.21337v1)
7. [Voxtral, arXiv 2507.13264](https://arxiv.org/html/2507.13264v1)
8. [Scaling speech technology to 1,000+ languages (MMS), arXiv 2305.13516](https://arxiv.org/abs/2305.13516)
9. [OWSM v3.1, arXiv 2401.16658](https://arxiv.org/pdf/2401.16658)
10. [Moonshine v2, arXiv 2602.12241](https://arxiv.org/abs/2602.12241);
    [Moonshine, arXiv 2410.15608](https://arxiv.org/abs/2410.15608);
    [arXiv 2509.02523](https://arxiv.org/pdf/2509.02523)
11. [3CatParla corpus, IberSPEECH 2024](https://www.isca-archive.org/iberspeech_2024/hernandezmena24_iberspeech.pdf)
12. [Catalan–Spanish code-switching ASR, arXiv 2507.13875](https://arxiv.org/abs/2507.13875);
    [Interspeech 2025](https://www.isca-archive.org/interspeech_2025/mena25_interspeech.html)
13. [WhisperKit, arXiv 2507.10860](https://arxiv.org/html/2507.10860v1)
14. [SimulStreaming, arXiv 2506.17077 (IWSLT 2025)](https://arxiv.org/pdf/2506.17077)
15. [Cache-aware streaming FastConformer, arXiv 2312.17279](https://arxiv.org/pdf/2312.17279)
16. [Calm-Whisper, arXiv 2505.12969](https://arxiv.org/abs/2505.12969);
    [Interspeech 2025 PDF](https://www.isca-archive.org/interspeech_2025/wang25b_interspeech.pdf)
17. [SAE steering against Whisper hallucination, arXiv 2606.07473](https://arxiv.org/abs/2606.07473)
18. [Whisper hallucination detection from internals, arXiv 2606.23060](https://arxiv.org/pdf/2606.23060)
19. [pyannote.audio, arXiv 1911.01255](https://arxiv.org/abs/1911.01255)
20. [DiariZen, arXiv 2409.09408](https://arxiv.org/abs/2409.09408)
21. [Sortformer, arXiv 2409.06656](https://arxiv.org/abs/2409.06656)
22. [Streaming Sortformer, Interspeech 2025](https://www.isca-archive.org/interspeech_2025/medennikov25_interspeech.pdf)
23. [VBx, arXiv 2012.14952](https://arxiv.org/pdf/2012.14952)
24. [DVBx, ICASSP 2024](https://ieeexplore.ieee.org/document/10446119/)
25. [LS-EEND, arXiv 2410.06670](https://arxiv.org/abs/2410.06670)
26. [TS-SEP, arXiv 2303.03849](https://arxiv.org/abs/2303.03849)
27. [TS-VAD-plus, APSIPA 2025](http://www.apsipa.org/proceedings/2025/papers/APSIPA2025_P333.pdf)
28. [WeSpeaker, arXiv 2210.17016](https://arxiv.org/abs/2210.17016)
29. [3D-Speaker, arXiv 2306.15354](https://arxiv.org/pdf/2306.15354.pdf)
30. [ReDimNet, arXiv 2407.18223](https://arxiv.org/pdf/2407.18223)
31. [In defence of metric learning for speaker recognition (voxceleb_trainer), arXiv 2003.11982](https://arxiv.org/abs/2003.11982)
32. [Kiwano, arXiv 2606.22369](https://arxiv.org/abs/2606.22369)
33. [VoxWatch, arXiv 2307.00169](https://arxiv.org/abs/2307.00169)
34. [VoxBlink2, arXiv 2407.11510](https://arxiv.org/abs/2407.11510)
35. [BOSARIS toolkit, arXiv 1304.2865](https://arxiv.org/pdf/1304.2865)
36. [WeSep, arXiv 2409.15799](https://arxiv.org/abs/2409.15799);
    [WeSep 2026, arXiv 2607.27436](https://arxiv.org/html/2607.27436)
37. [ClearerVoice-Studio, Interspeech 2025](https://www.isca-archive.org/interspeech_2025/zhao25f_interspeech.pdf)
38. [Asteroid, arXiv 2005.04132](https://arxiv.org/abs/2005.04132)
39. [SepFormer, arXiv 2010.13154](https://arxiv.org/pdf/2010.13154)
40. [SpeakerBeam, arXiv 1904.09272](https://arxiv.org/abs/1904.09272)
41. [USEF-TSE, arXiv 2409.02615](https://arxiv.org/pdf/2409.02615)
42. [LExt, arXiv 2505.05114](https://arxiv.org/abs/2505.05114);
    [MC-LExt, arXiv 2510.15437](https://arxiv.org/abs/2510.15437)
43. [PixIT, arXiv 2403.02288](https://arxiv.org/html/2403.02288v1)
44. [AmberNet, arXiv 2210.15781](https://arxiv.org/abs/2210.15781)
45. [DiCoW, arXiv 2501.00114](https://arxiv.org/abs/2501.00114)
46. [SE-DiCoW, arXiv 2601.19194](https://arxiv.org/abs/2601.19194)
47. [Dixtral, arXiv 2606.18134](https://arxiv.org/abs/2606.18134)
48. [MOSS-Transcribe-Diarize, arXiv 2601.01554](https://arxiv.org/abs/2601.01554)
49. [VibeVoice-ASR technical report, arXiv 2601.18184](https://arxiv.org/abs/2601.18184)
50. [TagSpeech, arXiv 2601.06896](https://arxiv.org/abs/2601.06896)
51. [SpeakerLM, arXiv 2508.06372](https://arxiv.org/abs/2508.06372)
52. [Speaker-Reasoner, arXiv 2604.03074](https://arxiv.org/abs/2604.03074)
53. [A unified speech LLM for diarization and ASR, arXiv 2507.02927](https://arxiv.org/abs/2507.02927)
54. [DM-ASR, arXiv 2604.22467](https://arxiv.org/abs/2604.22467);
    [arXiv 2506.05796](https://arxiv.org/abs/2506.05796)
55. [SA-SOT, arXiv 2403.02010](https://arxiv.org/abs/2403.02010)
56. [DiarizationLM, arXiv 2401.03506](https://arxiv.org/abs/2401.03506)
57. [LLM-based diarization correction, ASR-agnostic, arXiv 2406.04927](https://arxiv.org/abs/2406.04927)
58. [Text-based speaker change detection, arXiv 2506.11344](https://arxiv.org/abs/2506.11344)
59. [Stanza, ACL 2020 demo](https://aclanthology.org/2020.acl-demos.14.pdf)
60. [Italian parliamentary speeches with vision-language models, arXiv 2603.28103](https://arxiv.org/abs/2603.28103)
61. [SPC_R, arXiv 2506.07726](https://arxiv.org/abs/2506.07726)
62. [TalkNet-ASD, arXiv 2107.06592](https://arxiv.org/pdf/2107.06592.pdf)
63. [TalkNCE, arXiv 2309.12306](https://arxiv.org/abs/2309.12306)
64. [Qwen3-VL, arXiv 2511.21631](https://arxiv.org/pdf/2511.21631)
65. [SenseVoice (FunAudioLLM), arXiv 2407.04051](https://arxiv.org/pdf/2407.04051)
66. [BEATs, arXiv 2212.09058](https://arxiv.org/abs/2212.09058)
67. [CLAP (LAION), arXiv 2206.04769](https://arxiv.org/pdf/2206.04769)
68. [WhisperD, Interspeech 2025](https://www.isca-archive.org/interspeech_2025/akinrintoyo25_interspeech.pdf)
69. [SalamandraTA at WMT25, arXiv 2508.12774](https://arxiv.org/html/2508.12774)
70. [Proxecto Nós machine translation, CEUR Vol-3224](https://ceur-ws.org/Vol-3224/paper07.pdf)
71. [Qwen3-ForcedAligner, arXiv 2601.18220](https://arxiv.org/abs/2601.18220)
72. [EuroSpeech, arXiv 2510.00514](https://arxiv.org/abs/2510.00514)
73. [MeetEval, arXiv 2307.11394](https://arxiv.org/abs/2307.11394);
    [CHiME 2023](https://www.isca-archive.org/chime_2023/neumann23_chime.pdf)
74. [Open ASR Leaderboard, arXiv 2510.06961](https://arxiv.org/html/2510.06961v1)
75. [VoxPopuli, ACL 2021](https://aclanthology.org/2021.acl-long.80.pdf)
76. [Europarl-ASR, Interspeech 2021](https://www.isca-archive.org/interspeech_2021/garcesdiazmunio21_interspeech.html)
77. [Europarl-ST, arXiv 1911.03167](https://arxiv.org/abs/1911.03167)
78. [ParlaSpeech 3.0, arXiv 2511.01619](https://arxiv.org/html/2511.01619v1)
79. [ParlamentParla, ParlaCLARIN 2022](https://aclanthology.org/2022.parlaclarin-1.18/)
80. [Basque Parliament speech corpus, Applied Sciences 14(5):1951](https://doi.org/10.3390/app14051951)
81. [BasqueParl, LREC 2022](https://aclanthology.org/2022.lrec-1.361/);
    [arXiv 2205.01506](https://arxiv.org/abs/2205.01506)
82. [Albayzin 2018 evaluation, Applied Sciences 9(24):5412](https://doi.org/10.3390/app9245412);
    [IberSPEECH-RTVE 2022 SD evaluation plan](https://catedrartve.unizar.es/reto2022/SDIAC2022_Evalplan.pdf)
83. [Finnish Parliament ASR corpus, arXiv 2203.14876](https://arxiv.org/pdf/2203.14876);
    [Language Resources and Evaluation](https://dl.acm.org/doi/abs/10.1007/s10579-023-09650-7)
84. [FT Speech, arXiv 2005.12368](https://arxiv.org/pdf/2005.12368)
85. [FalAR, arXiv 2605.27062](https://arxiv.org/abs/2605.27062)
86. [SloPal and SloPalSpeech, arXiv 2509.19270](https://arxiv.org/abs/2509.19270)
87. [NOTSOFAR-1, arXiv 2401.08887](https://arxiv.org/abs/2401.08887)
88. [DIHARD III, Interspeech 2021](https://www.ldc.upenn.edu/sites/default/files/interspeech21-dihard.pdf)
89. [VoxSRC, arXiv 2012.06867](https://arxiv.org/pdf/2012.06867)
90. [AVA-AVD, arXiv 2111.14448](https://arxiv.org/pdf/2111.14448)
91. [The REPERE corpus, LREC 2012](https://aclanthology.org/L12-1410/)
92. [EPIC, LREC 2006](https://aclanthology.org/L06-1093/)
93. [FLEURS and XTREME-S, arXiv 2203.10752](https://arxiv.org/pdf/2203.10752)
94. [Multilingual LibriSpeech, arXiv 2012.03411](https://arxiv.org/pdf/2012.03411)

<!-- This document follows common-doc-guidelines.md.
See github.com/jlevy/practical-prose and review guidelines before editing.
-->
