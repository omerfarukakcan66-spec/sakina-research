# Sakina Research — Night Report
**Date:** 2026-06-22 | **Time:** 21:32 UTC | **Round:** 19

---

## Summary of Key Findings

This round validates and extends prior research (Rounds 1–18). Core pipelines are stable. Three new actionable findings below.

---

## 1. Arabic Quran ASR — Current Model Landscape

### Active HuggingFace Models (confirmed 2025–2026)

| Model | WER | Notes |
|---|---|---|
| `KheemP/whisper-base-quran-lora` | 5.98% | LoRA on tarteel-ai/whisper-base-ar-quran, diacritic-sensitive |
| `tarteel-ai/whisper-base-ar-quran` | 5.75% | Tarteel's own open model, live HF Space |
| `tarteel-ai/whisper-tiny-ar-quran` | ~8–10% | Smaller, faster, lower accuracy |
| `Nuwaisir/Quran_speech_recognizer` | unpublished | HF, needs benchmark |
| `FaisaI/tadabur` (Tadabur-Whisper-Small) | unpublished | Fine-tuned on 1,400h/600 reciters |

### New Papers (2025–2026 only)

- **arXiv:2509.00094** (Aug 2025) — 98% automated pipeline, 850+ hours Quran audio, 300K utterances, multi-level CTC, **0.16% phoneme error rate** on Quranic recitation. Custom Quran Phonetic Script (QPS) encodes tajweed rules.
- **arXiv:2503.23470** (Mar 2026) — EfficientNet-B0 + Squeeze-Excitation blocks on mel-spectrograms, 95–99% on Al-Mad/Ghunnah/Ikhfaa using QDAT dataset (1,505 recordings). Vision-based tajweed classifier.
- **arXiv:2601.17880 / QURAN-MD** (Jan 2026, NeurIPS 2025 Muslims in ML) — First multimodal dataset: 77,429 word-level clips + 187,080 verse-level clips, 32 reciters, 36.1 GB. Available at `Buraaq/quran-audio-text-dataset`.
- **arXiv:2506.02627** (Jun 2025) — "Overcoming Data Scarcity in Multi-Dialectal Arabic ASR via Whisper Fine-Tuning" — cross-dialect fine-tuning technique directly applicable to Qira'at variation (Hafs/Warsh/Qaloon). Method: low-resource dialect augmentation via shared phoneme inventory.

---

## 2. Flutter Real-Time Audio — Confirmed Stack (Round 19 Validation)

| Component | Package | Version | Status |
|---|---|---|---|
| Mic streaming | `record` | v7.1.0 (Jun 2026) | Active, 717k downloads |
| On-device ASR | `sherpa_onnx` | v1.13.3 (Jun 15, 2026) | Active, 23.2k/week |
| Audio engine | `flutter_soloud` | latest | C++ FFI, low-latency DSP |
| PCM stream | `flutter_sound` | v9.30.0 | PCM Float32/Int16 to stream |

**sherpa-onnx v1.13.3** (published 7 days ago = Jun 15, 2026): 23.2K weekly downloads, 113 likes, 150 pub points. Supports Android/iOS/Linux/macOS/Windows/HarmonyOS. All architectures including arm32/arm64. Includes **Nemotron-3.5 streaming ASR** (PR #3671, merged ~May 2026) — already confirmed in Round 18. 

**No new Arabic-specific sherpa-onnx models found this round beyond what Round 18 documented.** Round 18's Nemotron-3.5 INT4 ONNX remains the leading streaming candidate.

---

## 3. Tarteel Current State (Round 19 Intelligence)

### GitHub Activity (as of Jun 22, 2026)

| Repo | Last Updated | Stars | License | Notes |
|---|---|---|---|---|
| `TarteelAI/quranic-universal-library` | **Jun 15, 2026** | 893 | **MIT** | Ruby, comprehensive Quran resources |
| `TarteelAI/celery-exporter` | Apr 15, 2026 | 0 | MIT | Prometheus/Celery infra |
| `TarteelAI/nemo2riva` | Feb 24, 2026 | 3 | MIT | NeMo→Riva conversion (confirms NVIDIA Riva backend) |
| `TarteelAI/fastlane` | Jan 27, 2026 | 3 | MIT | CI/CD (fork) |
| `TarteelAI/quran-ttx` | Jul 2, 2025 | 8 | — | Font TTX files |
| `TarteelAI/NeMo` | Jul 2, 2025 | — | — | Custom NeMo fork (FastConformer training) |

**Confirmed:** Tarteel runs FastConformer via NVIDIA Riva (nemo2riva pipeline active). Their most recently active public repo is `quranic-universal-library` — a Ruby MIT-licensed comprehensive Quran data library.

### NEW — Tarteel Mahraj Detection Timeline Confirmed
- **Beta testing: Q2 2026** (now or just passed)
- **Full release: Q4 2026** (Oct–Dec 2026)
- This is the first confirmed public date for Tarteel's phoneme-level Makharij (articulation point) feature
- **Sakīna competitive window: ~6 months** before Tarteel ships any tajweed correction

### Tarteel Scale: 15M+ users, 150+ countries, NVIDIA partnership, multi-cloud infrastructure

---

## 4. New ML — Multi-Dialectal Whisper Fine-Tuning (arXiv:2506.02627)

**Paper:** "Overcoming Data Scarcity in Multi-Dialectal Arabic ASR via Whisper Fine-Tuning" (Jun 2025)

**Key technique:** When per-dialect training data is scarce (< 10h), pooling dialects with a shared phoneme inventory and fine-tuning Whisper on the combined set improves WER over mono-dialect training.

**Quran application:** The 10 Qira'at styles (Hafs, Warsh, Qaloon, Shu'ba, etc.) are structurally similar to dialect variation — same phoneme inventory, different pronunciation rules. This technique suggests:
1. Pool existing Quran datasets (EveryAyah Hafs 390h + Tadabur 1,400h + QURAN-MD 32 reciters)
2. Fine-tune Whisper with qira'at-label conditioning
3. One model handles all 10 Qira'at (vs. 10 separate models)

**DeiT V3 already achieves 99.6% Qira'at identification** (Round 15) — can serve as the routing gate. This paper provides the single-model fine-tuning technique to pair with it.

---

## 5. Open Source Implementations — Active Repos

| Repo | Stars | Last Active | Description |
|---|---|---|---|
| `yazinsai/offline-tarteel` | 220+ | Mar 2026 | Verse ID from audio, ONNX, 3-phase pipeline |
| `yayaiu6/Real-Time-Quran-recitation-tracker-System` | 121 | 2026 | Word-level alignment, real-time tracking |
| `sayedmahmoud266/quran-ai-transcriping` | 25 | Oct 2025 | FastAPI, ayah matching + timestamps |
| `Abdelrahman47-code/Quranic-Verse-Recognition` | active | 2025-2026 | Verse recognition system |

**Key insight from offline-tarteel research docs (2025):** Whisper's internal aligner is the cleanest approach for verse recognition — no external models needed. The "promptable embeddings" discovery (embeddings conditional on both content AND instruction) means a single model can handle both transcription and verse ID.

---

## Round 19 — New vs. Confirmed

| Finding | Status |
|---|---|
| sherpa_onnx v1.13.3 active | Confirmed (Round 18) |
| KheemP WER 5.98% | Confirmed (Round 8+) |
| Nemotron-3.5 streaming best | Confirmed (Round 17+) |
| **TarteelAI/quranic-universal-library MIT Jun 15** | **NEW detail** |
| **Tarteel Mahraj Q4 2026 timeline** | **NEW — competitive urgency** |
| **arXiv:2506.02627 cross-dialect Whisper technique** | **NEW — Qira'at strategy** |
| QURAN-MD 77K word clips | Confirmed (Round 13) |
| IQRA 2026 whu-iasp champion | Confirmed (Round 12) |

---

## Recommended Actions (Round 19)

1. **Immediate (this week):** Tarteel's Mahraj detection beta is in Q2 2026 — Sakīna must treat phoneme/Makharij feedback as a sprint priority, not a roadmap item. The 6-month window to Q4 2026 full Tarteel release is the hard deadline.

2. **Model (this week):** Apply arXiv:2506.02627 pooling strategy to train a single Whisper variant on Tadabur + EveryAyah + QURAN-MD — one model covering all Qira'at styles. DeiT V3 routes the Qira'at; pooled fine-tune handles recognition.

3. **Data (today):** `TarteelAI/quranic-universal-library` is MIT-licensed and actively maintained (Jun 15, 2026, 893 stars). Contains comprehensive Quran resources usable without AGPL friction. Audit this Ruby library for data components that can replace Tarteel's AGPL datasets in Sakīna's pipeline.

---

## Sources
- [KheemP/whisper-base-quran-lora — HuggingFace](https://huggingface.co/KheemP/whisper-base-quran-lora)
- [tarteel-ai/whisper-base-ar-quran — HuggingFace](https://huggingface.co/tarteel-ai/whisper-base-ar-quran)
- [Nuwaisir/Quran_speech_recognizer — HuggingFace](https://huggingface.co/Nuwaisir/Quran_speech_recognizer)
- [arXiv:2509.00094 — Quran Pronunciation Error Detection](https://arxiv.org/abs/2509.00094)
- [arXiv:2503.23470 — Tajweed DNN Evaluation](https://arxiv.org/abs/2503.23470)
- [arXiv:2601.17880 — QURAN-MD Dataset](https://arxiv.org/abs/2601.17880)
- [arXiv:2506.02627 — Multi-Dialectal Arabic ASR](https://arxiv.org/pdf/2506.02627)
- [TarteelAI GitHub Organization](https://github.com/TarteelAI)
- [sherpa_onnx — pub.dev](https://pub.dev/packages/sherpa_onnx)
- [speech_to_text — pub.dev](https://pub.dev/packages/speech_to_text)
- [Flutter Audio-Driven Apps — DEV Community](https://dev.to/subhanumajumder/flutter-for-audio-driven-apps-real-time-sound-visualization-audio-processing-and-voice-2ba6)
- [Building Flutter Voice Assistants Local SR — dasroot.net](https://dasroot.net/posts/2025/12/building-flutter-voice-assistants-local-speech-recognition/)
- [offline-tarteel RESEARCH doc — GitHub](https://github.com/yazinsai/offline-tarteel/blob/main/RESEARCH-audio-to-verse.md)
- [QURAN-MD NeurIPS 2025 — HuggingFace Papers](https://huggingface.co/papers/2601.17880)
- [Tarteel AI App Store](https://apps.apple.com/us/app/tarteel-ai-quran-memorization/id1391009396)
- [Tarteel Review 2026 — aichief.com](https://aichief.com/ai-lifestyle-tools/tarteel/)
- [yayaiu6/Real-Time-Quran-recitation-tracker-System — GitHub](https://github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System)
