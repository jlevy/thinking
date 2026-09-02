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

Licence matters as much as maturity: a **non-commercial weight licence** (DiariZen’s CC
BY-NC 4.0, several challenge-derived models) is fine for the benchmark and the research
campaign and disqualifying for a product, and the tables say which.
A **no-licence** repository is not open source, whatever its README says.
Commercial services and datasets do not take a maturity label; they take an access class
instead.

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
| 3 Diarization | [pyannote.audio 4.x](https://github.com/pyannote/pyannote-audio) + `HF: pyannote/speaker-diarization-community-1`; its masks, not DiariZen’s, feed DiCoW | permissive code (MIT); weights CC BY 4.0, gated | language-agnostic | Production | local README: multi-corpus DER table, 31 s per audio hour; repo-meta 10,500★ pushed 2026-09-02 | R1, R7, R8 |
| 4 Embeddings | [WeSpeaker](https://github.com/wenet-e2e/wespeaker): extractors, AS-norm, QMF, calibration and DET scripts | permissive (Apache-2.0; weights follow VoxCeleb CC BY 4.0) | no Iberian evaluation | Production | `examples/voxceleb/v2/README.md` (raw); `wespeaker/bin` files (gh) | R1, R2, R4, R5 |
| 4 Embeddings | [ReDimNet2](https://github.com/PalabraAI/redimnet2)-B6 `vb2+vox2` LM checkpoint as the extractor | permissive (MIT) | no Iberian evaluation | Mature research | local README: 0.29 EER Vox2-only, 0.23 with the LM checkpoint; repo-meta 85★ pushed 2026-08-28 | R1, R2, R4 |
| 5 Scoring | [pyannote.metrics](https://github.com/pyannote/pyannote-metrics) `IdentificationErrorRate`; [MeetEval](https://github.com/fgnt/meeteval) cpWER and tcpWER; [dscore](https://github.com/nryant/dscore) DER | permissive (MIT; MIT; BSD-2) | n/a | Production | gh tree (`identification.py` present); local MeetEval README; repo-meta | every R |
| 5 Scoring | [PYLLR](https://github.com/bsxfan/PYLLR) and [expected_cost](https://github.com/luferrer/expected_cost) for Cllr; [net:cal](https://github.com/EFS-OpenSource/calibration-framework) for ECE | permissive (MIT; MIT; Apache-2.0) | n/a | Legacy; Legacy; Production | repo-meta: pushed 2023-02-21, 2025-06-11, 2026-04-16 | R2, R3, R4 |
| 6 Alignment | torchaudio [`MMS_FA`](https://github.com/pytorch/audio) or [ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner), benchmark only | permissive API (BSD-2; BSD per README); default MMS weights CC BY-NC 4.0 | MMS covers es, ca, eu, gl | Production; Mature research | README §License (raw); `LICENSE` returns 404 at `main`; repo-meta | R1 |
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
| 13 Multimodal | [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) PP-OCRv5; [facenet-pytorch](https://github.com/timesler/facenet-pytorch) instead of InsightFace’s NC weights | permissive (Apache-2.0; MIT) | Spanish explicit; Latin recogniser covers ca/gl/eu orthography (search) | Production; Mature research | repo-meta 88,695★; review API check, pushed 2025-09-16 | R3, R10 |
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

| Direction | Shortlist tools | Data | Scorer | Open licence question |
| --- | --- | --- | --- | --- |
| **R1** in-domain benchmark and baseline | Congreso open data + Diario HTML + Archivo MP4; OpenParliamentTV-Tools ES as merge template; yt-dlp, FFmpeg; MMS_FA or ctc-forced-aligner; lhotse + pyannote.database; Label Studio; baseline `whisper-large-v3-LoS-punctuated`, pyannote community-1, ReDimNet2-B6 cosine | 20–50 Congreso sessions to be built (§5.1 of the agenda); basque_parliament_1 and ParlamentParla v2 for adaptation | pyannote.metrics identification error rate; MeetEval cpWER and tcpWER (fixed-label variant to be written); dscore DER; per-turn TAA and coverage scorer (to be written) | MMS aligner weights are CC BY-NC (benchmark only); community-1 weights are gated; Diario text terms for redistribution of manifests |
| **R2** candidate-set constraint, normalization, calibration | WeSpeaker AS-norm, QMF, `score_calibration.py`; ReDimNet2; PYLLR and expected_cost for Cllr; net:cal for ECE | R1 trials; agenda (orden del día) as the candidate set; VoxWatch protocol as the reference curve | Cllr, min-Cllr, actDCF vs minDCF; FAR at fixed FRR; coverage–TAA curve | none blocking; PYLLR and expected_cost are Legacy (MIT, dormant) |
| **R3** structured-context fusion | chair-announcement parser (to be written); spaCy es and ca, Stanza; RapidFuzz; SPARQLWrapper and Wikidata Toolkit; PaddleOCR for the overlay; Salamandra, ALIA or Latxa as the LLM fuser in the DiarizationLM pattern | Congreso roster and intervention open data; ParlaMint ES metadata; the on-screen overlay where present (existence unverified) | TAA by turn type with per-source ablation; chair-announcement coverage | DiarizationLM weights carry Llama licences and are English-only (Legacy); Salamandra and ALIA Apache-2.0, Latxa MIT |
| **R4** weakly supervised in-domain speaker modelling | WeSpeaker training recipes; ReDimNet2 training pipeline; clovaai voxceleb_trainer; VoxBlink2 OSI scripts (Legacy) | Diario-aligned trials from R1; basque_parliament_1 speaker labels; VoxCeleb2 and VoxBlink2 for pretraining | FAR at fixed FRR against watchlist size vs the VoxWatch curve; EER and minDCF via WeSpeaker `compute_det.py` | VoxCeleb CC BY 4.0; VoxBlink2 licence not confirmed; VoxWatch has no code |
| **R5** interpretation-mixed tail | interpreter enrolment with WeSpeaker; WeSep as a reimplementation target; SpeechBrain SepFormer and ESPnet TF-GridNet as blind baselines; asteroid for SI-SNRi | VoxPopuli speech-to-speech pairs (es ≈1.6k h source) to synthesize overlays; real interpreted Congreso segments; EPIC (ELRA, paid) only if licensed | MeetEval cpWER and tcpWER; TAA recovered vs isolated floor audio; SI-SNRi on synthetic mixes | WeSep has no licence and no released weights; USEF-TSE NOASSERTION and Legacy; LExt has no code; VoxPopuli models CC BY-NC (data CC0) |
| **R6** record-form normalization | `whisper-large-v3-LoS-punctuated`; NeMo-text-processing ITN es; punctuators; SalamandraTA for the co-official leg; Salamandra or Latxa fine-tune; Apertium as auditable baseline; Europarl-ASR verbatimization recipe (Legacy) and SPC_R recipe (paper only) | (verbatim ASR, Diario) pairs from R1; EuroSpeech CER distribution; Europarl-ASR transcript layers | record-WER and HTER with jiwer under a versioned normalizer; sacrebleu and COMET on the translation leg | ITN has no ca/eu/gl grammars; SalamandraTA card not opened; Apertium GPL-2.0 applies on distribution; Europarl-ASR NOASSERTION |
| **R7** bounded-lag architecture with revision accounting | SimulStreaming inside WhisperLiveKit; Streaming Sortformer v2 with state reset per window; NeMo cache-aware FastConformer (es only via Nemotron); diart; Silero VAD | R1 sessions replayed at wall-clock rate | SimulEval AL and LAAL (archived; vendor or reimplement); finalization lag, name revision rate, normalized erasure logger (to be written) | SimulEval CC BY-SA 4.0 and archived; Sortformer v2 CC BY 4.0 per card (search), v2.1 NVIDIA OML; Nemotron licence unverified |
| **R8** enrolment-conditioned joint SA-ASR | TS-ASR-Whisper (DiCoW, SE-DiCoW) with pyannote masks; MOSS-Transcribe-Diarize baseline; Dixtral for the LLM decoder; multitalker-parakeet as the English streaming comparison | R1 benchmark; AMI and NOTSOFAR-1 for sanity; EMMA leaderboard submission script | MeetEval tcpWER; pyannote.metrics identification error rate on roster ids; TAA | DiCoW weights CC BY 4.0 per HF card, unverified; DiariZen weights CC BY-NC (avoid); NOTSOFAR data licence and HF token; multitalker-parakeet NVIDIA OML |
| **R9** stage-direction detection | PANNs (has the Applause, Laughter, Chatter, Hubbub classes; Legacy); BEATs; EfficientAT (Legacy, low compute); CLAP zero-shot (Legacy, CC0); SenseVoice inline events (no Iberian ASR) | Diario stage directions from R1 as reference; AudioSet class set | precision and recall per class against the record’s annotations (scorer to be written) | BEATs checkpoint terms separate from the MIT repo; SenseVoice model under the FunASR model licence; AST weights on Dropbox |
| **R10** multimodal identity | Light-ASD (weights in-repo, Legacy); facenet-pytorch and DeepFace for face matching; face-alignment; PaddleOCR or Tesseract for the overlay; Qwen3-VL for layout-plus-text reasoning | Archivo Audiovisual video; member photo gallery from Congreso open data; AVA-AVD; REPERE (ELRA) as the precedent | TAA lift on interjections and bench replies; ASD mAP on a labelled subset | InsightFace pretrained models are non-commercial; LoCoNet has no licence; overlay presence on every intervention unverified |
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

## 7. Withdrawn and Avoid

The short list from §6.4 of the agenda, then the complete register of non-commercial
weights, unlicensed repositories, non-OSI model terms and archived dependencies, so the
reasons survive edits to the tables.

### Attractive but avoid

| Item | Why | Use instead |
| --- | --- | --- |
| DiariZen weights | CC BY-NC 4.0 (README §License), inherited by DiCoW’s bundled pipeline | pyannote community-1 masks into DiCoW |
| InsightFace pretrained models | research-only terms, including the pip auto-downloads (readme) | facenet-pytorch (MIT), DeepFace |
| Meta MMS, XLS-R, `mms-lid` | weights CC BY-NC 4.0; fairseq host repo archived 2025-09-30 | Omnilingual ASR (Apache-2.0); VoxLingua107 ECAPA for LID |
| Speechmatics, pyannoteAI or OpenAI enrolment as the identifier | caps of 50, unpublished, and 4 enrolled speakers; vendor lock | a roster matcher on WeSpeaker or ReDimNet2; keep one vendor as a comparison row |
| Azure AI Speaker Recognition | retired 2025-09-30; APIs no longer accessible (search) | the open stack above; Picovoice Eagle if on-device closed weights are acceptable |
| Amazon Connect Voice ID | closed to new customers 2025-05-20; end of support 2026-05-20 (search) | same |
| WeSep, USEF-TSE, LExt for R5 | no licence and no released weights; NOASSERTION and Legacy; no code | reimplement in WeSep’s architecture set; SepFormer and TF-GridNet as blind baselines |
| Moonshine for Spanish | non-English models under the Moonshine Community Licence, non-commercial below USD 1M revenue (search) | `whisper-large-v3-turbo` on faster-whisper |
| Kyutai STT; Canary, Parakeet, Nemotron for the co-official tail | en and fr only; no ca/eu/gl | SimulStreaming; LoS, BSC, HiTZ and Nós specialists |
| `xezpeleta/whisper-large-v3-eu` as an evaluation model | trained on Basque Parliament plenary audio; no temporal hold-out | `HiTZ/whisper-large-v3-eu` (Common Voice 13) with a held-out period |
| 3CatParla as parliamentary data | broadcast television despite the name | ParlamentParla v2 |
| whisper-timestamped, aeneas, tssep, audapolis, LinTO inside a hosted service | AGPL-3.0 propagates into the service | CrisperWhisper or WhisperX timestamps; CTC aligners; Label Studio; Apertium’s GPL bites only on distribution |

### Retired services

| Service | Status | Source |
| --- | --- | --- |
| Azure AI Speaker Recognition | retired 2025-09-30; APIs no longer accessible; remove from any design and from the agenda’s references | Microsoft release notes (search) |
| Amazon Connect Voice ID | closed to new customers 2025-05-20; end of support 2026-05-20 | AWS end-of-support notice (search) |
| Google Cloud Speaker ID (CCAI) | restricted availability, contact-centre scoped, no public limits; not retired but not procurable on demand | coverage article (search) |

### Non-commercial or non-OSI weights

| Item | Terms | Consequence |
| --- | --- | --- |
| DiariZen weights (`BUT-FIT/diarizen-*`) | CC BY-NC 4.0, inherited from RAMC, MSDWild and DIHARD-3 (README) | benchmark and research only; DiCoW’s bundled pipeline inherits it, so feed DiCoW pyannote masks |
| Meta MMS, wav2vec2-XLS-R, `mms-lid-*`, and the `MMS_FA` aligner weights behind torchaudio and ctc-forced-aligner | CC BY-NC 4.0 (search; README §License for the aligner) | alignment for the benchmark only; Omnilingual ASR (Apache-2.0) and VoxLingua107 ECAPA for anything shipped |
| VoxPopuli code and pretrained models | CC BY-NC 4.0 (data CC0) (local README) | use the data, not the models |
| InsightFace pretrained models and data | research only, including pip auto-downloads (readme) | facenet-pytorch or DeepFace |
| Moonshine non-English models | Moonshine Community Licence, non-commercial below USD 1M revenue (search) | not for Spanish in a product |
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

### Copyleft that propagates into a hosted service

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
