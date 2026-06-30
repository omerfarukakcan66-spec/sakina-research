# Sakina Research — Night Run 2026-06-30 00:36 UTC

**Mission:** Arabic Quran ASR • Flutter audio • Tarteel watch • New ML models • Open-source usables

---

## 1. Arabic Quran ASR — New Models & Papers

### 1a. Quranic Phonemizer (NeurIPS 2025 Muslims in ML Workshop) — MUST-USE TOOL
**Source:** https://github.com/Hetchy/Quranic-Phonemizer | Paper: https://openreview.net/pdf?id=hZt0JK28iV

`Hetchy/Quranic-Phonemizer` — Open-source Python API for **Hafs 'an 'Asim** recitation style. Converts Quranic text (with diacritics) to a **71-symbol phoneme inventory** that comprehensively encodes all Tajweed rules:
- Idgham, Iqlab, Ikhfa, Qalqalah, Tafkheem, Waqf, and all remaining rules
- Standard IPA Arabic phonemes + custom phonemes for Tajweed-specific articulation
- Modular Python API — `phonemize("بِسْمِ")` → phoneme sequence with Tajweed labels

**Benchmark results:**
- Transformer trained on phonemizer labels → **3.9% PER** on IqraEval Quran dev set
- **2.6% PER** on professional reciters dev set
- **84.9% accuracy** on QuranMB test set

**Web UI:** https://quranicphonemizer.com/ (live demo, no install needed to test)

**Why this matters for Sakina:** This is the G2P substrate the Tajweed correction pipeline needs. Pipeline becomes:
1. ASR transcription → text (e.g. whisper-base-quran-lora)
2. `Quranic-Phonemizer` → expected phoneme sequence for that ayah
3. Audio-to-phoneme alignment (e.g. CTC or forced alignment)
4. Diff expected vs. detected phonemes → per-rule error flags

No need to build the Tajweed phoneme system from scratch. This is the missing piece.

---

### 1b. TajweedAI — Hybrid ASR-Classifier at NeurIPS 2025 (100% Qalqalah Detection)
**Source:** https://neurips.cc/virtual/2025/loc/san-diego/133028 | Paper: https://openreview.net/pdf?id=AauWmDPOIf | GitHub: https://github.com/nmazid121/tajweedAI

NeurIPS 2025 Muslims in ML Workshop paper. **First published system combining ASR temporal alignment + binary Tajweed rule classifier** in a real-time feedback loop.

**Architecture:**
- Stage 1: ASR model provides temporal alignment (word/phoneme timestamps)
- Stage 2: Binary classifier per Tajweed rule, operating on the aligned audio segment
- Case study rule: **Qalqalah** (the plosive "echo" on letters ق ط ب ج د)

**Results:**
- **100% test accuracy** on Qalqalah classification
- Targeted training strategy — per-rule classifiers trained on isolated rule instances
- End-to-end pipeline: mic → ASR alignment → Qalqalah classifier → real-time feedback

**Why this matters for Sakina:**
- Proves the architectural approach: you don't need one giant model that understands all Tajweed
- Stack of small binary classifiers (one per rule) on top of ASR is both practical and accurate
- Qalqalah is one of the harder rules — 100% accuracy suggests classifiers for easier rules (Madd, Ikhfa) are feasible
- The GitHub repo is open — code can be adapted directly

---

### 1c. MaddoggProduction/whisper-l-v3-turbo-quran-lora — Turbo Architecture for Live Transcription
**Source:** https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix

LoRA fine-tune of `openai/whisper-large-v3-turbo` on a mixed Quran dataset.

**Key specs:**
- WER: **12.69%** on ahishamm/QURANICWhisperDataset test split (20%)
- Architecture: Turbo = full Large v3 encoder, **4-layer decoder** (vs. 32 in standard Large v3)
- Latency: Dramatically lower than full Large v3; suitable for live streaming transcription
- Trade-off: More prone to hallucination/repetition loops on long segments; best for chunk-based streaming

**Comparison to existing Sakina candidates:**
| Model | WER | Speed | Live streaming? |
|---|---|---|---|
| whisper-base-quran-lora (KheemP) | 5.98% | Medium | ⚠️ chunk-only |
| whisper-l-v3-turbo-quran-lora (MaddoggProduction) | 12.69% | Fast | ✅ designed for it |
| tarteel-ai/whisper-base-ar-quran | ~6% | Medium | ✅ |

For Sakina's live session use case (streaming while reciting), turbo architecture's lower latency may justify the higher WER — especially if combined with a confidence gate that escalates to teacher review when WER signal is low.

---

### 1d. yazinsai/offline-tarteel — QuranCLAP v2 Updated March 2026
**Source:** https://github.com/yazinsai/offline-tarteel | Research doc: https://github.com/yazinsai/offline-tarteel/blob/main/RESEARCH-audio-to-verse.md

Updated March 2026. Implements multiple approaches for offline Quran verse identification:

**QuranCLAP v2 (CLIP-style contrastive model):**
- Maps audio to 256-dim speaker-invariant embeddings
- Pre-computes FAISS index over all 6,236 verses × multiple reciters
- Inference: 1 forward pass + FAISS nearest-neighbor → verse ID
- Search time: <1ms (HNSW index)

**Two-stage retrieval variant (currently best accuracy):**
- Stage 1: faster-whisper for ASR (transcription)
- Stage 2: fine-tuned pruned CTC for re-scoring
- Results: **70% sequence accuracy**, **3.96s latency**, **306 MB total size**

**WavLink integration (from January 2026 paper):**
- WavLink global token → sub-100 dimension embeddings with minimal accuracy drop
- Pre-compute all 6,236 × 10 reciters = ~6–12 MB on-device FAISS index
- Bypasses ASR entirely for verse identification (fingerprinting approach)

---

## 2. Flutter Real-Time Audio Processing

### 2a. Current Package Landscape (as of June 2026)
No new major packages discovered since the audio_io v0.3.0 finding (2026-06-29). Status:

**Confirmed live candidates for Sakina:**
- `audio_io` v0.3.0 (pub.dev) — 1.5ms Realtime mode, Float64/48kHz, stream-based API
- `record` 7.1.0 — General-purpose, more stable but no latency mode control
- `flutter_soloud` — Low-latency via SoLoud C++ FFI; focused on DSP/effects, not raw mic capture

**Sherpa-ONNX Flutter integration confirmed active:**
- `sherpa_onnx` pub.dev package — full Flutter support, iOS/Android
- Example: `sherpa-onnx/flutter-examples/streaming_asr/` (k2-fsa GitHub)
- Can run fine-tuned Whisper ONNX models on-device; community implementations confirmed for Android
- No known Quran-specific ONNX model yet — Sakina would need to convert whisper-base-quran-lora to ONNX

---

## 3. Tarteel Watch — June 2026

### 3a. Latest Version: v5.77.3 (June 7, 2026)
- Release content: Eid mubarak messaging + bug fixes only
- **No new AI features in this release**
- Version trail: 5.75.0 → 5.76.5 → 5.77.3 (incremental patch releases)
- Latest modded APK trackers also show v5.78.2 available

### 3b. Tajweed Correction — STILL NOT SHIPPED
**Confirmed June 2026:** Tarteel's AI detects **word-level errors only** (missed, extra, wrong word). Tajweed phonetics (Madd length, Qalqalah echo, Ikhfa nasalization) are explicitly listed as a **future roadmap item** on their support docs.

> "Tarteel does not provide detailed correction for Tajweed (phonetic rules of pronunciation and intonation)."

This confirmation is from their own support documentation, not inferred.

### 3c. GCC Market Push Active
Tarteel is actively campaigning for "top 10 in GCC App Stores" (confirmed from prior research 2026-06-29). Despite this aggressive push, the product gap on phoneme-level feedback remains open.

### 3d. Competitor: TajweedMate v2.0.25
**Source:** https://tajweedmate.com/
A lesser-known competitor. TajweedMate 2.0.25 is listed on Soft112. No information on AI backend or whether it provides real-time audio feedback vs. static Tajweed highlighting. Worth monitoring but not yet a confirmed threat.

---

## 4. Open-Source Implementations Usable Today

### Priority Stack for Sakina's MVP

| Layer | Tool | Status | Link |
|---|---|---|---|
| Live ASR | `whisper-base-quran-lora` (5.98% WER) or `whisper-l-v3-turbo-quran-lora` (12.69%, faster) | Ready | HuggingFace |
| G2P / Tajweed phonemes | `Quranic-Phonemizer` (71 symbols) | Open source, NeurIPS 2025 | github.com/Hetchy/Quranic-Phonemizer |
| Tajweed rule classifier | TajweedAI architecture (hybrid ASR+binary classifier) | Open source, NeurIPS 2025 | github.com/nmazid121/tajweedAI |
| Verse fingerprinting | `offline-tarteel` QuranCLAP v2 + FAISS | Open source | github.com/yazinsai/offline-tarteel |
| Flutter audio capture | `audio_io` v0.3.0 (1.5ms Realtime) | pub.dev | pub.dev/packages/audio_io |
| On-device inference | `sherpa_onnx` Flutter package | pub.dev, active | pub.dev/packages/sherpa_onnx |
| On-device Arabic ASR (no Quran fine-tune) | Munsit Edge SDK (CNTXT AI, May 2026) | Commercial SDK | cntxt.ai |

---

## 5. Action Items for Sakina Team

**P0 (This Week):**
1. **Clone `Hetchy/Quranic-Phonemizer` and test it** — run it on 10 test ayahs, verify phoneme output matches what you'd expect for Tajweed rule labeling. This unlocks the entire Tajweed correction pipeline.
2. **Read TajweedAI paper (NeurIPS 2025, OpenReview AauWmDPOIf)** — the hybrid ASR-classifier architecture is the blueprint for Sakina's Tajweed module. Study the data collection strategy (they had to gather rule-specific audio examples).

**P1 (Next 2 Weeks):**
3. **Benchmark whisper-l-v3-turbo-quran-lora vs. whisper-base-quran-lora** on Sakina's streaming use case — 250ms chunks, real iOS/Android devices. Pick one for the MVP.
4. **Convert chosen ASR model to ONNX** for sherpa_onnx Flutter integration — this enables on-device inference without a cloud round-trip.
5. **Prototype Qalqalah binary classifier** following TajweedAI architecture — start with one rule, ship it as a "beta Tajweed feature" before Tarteel ships theirs.

**P2 (Next Month):**
6. Monitor `offline-tarteel` repo for further March 2026 updates — the QuranCLAP v2 FAISS fingerprinting approach enables "instant verse identification" before ASR completes.

---

## Sources

- [Quranic Phonemizer — GitHub](https://github.com/Hetchy/Quranic-Phonemizer)
- [Quranic Phonemizer — NeurIPS 2025 OpenReview](https://openreview.net/pdf?id=hZt0JK28iV)
- [TajweedAI — NeurIPS 2025](https://neurips.cc/virtual/2025/loc/san-diego/133028)
- [TajweedAI — OpenReview paper](https://openreview.net/pdf?id=AauWmDPOIf)
- [TajweedAI — GitHub](https://github.com/nmazid121/tajweedAI)
- [KheemP/whisper-base-quran-lora — HuggingFace](https://huggingface.co/KheemP/whisper-base-quran-lora)
- [MaddoggProduction/whisper-l-v3-turbo-quran-lora — HuggingFace](https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix)
- [yazinsai/offline-tarteel — GitHub](https://github.com/yazinsai/offline-tarteel)
- [WavLink — arXiv 2601.15118](https://arxiv.org/abs/2601.15118)
- [Tarteel v5.77.3 — APKMirror](https://www.apkmirror.com/apk/tarteel-inc/tarteel-ai-quran-memorization/tarteel-ai-quran-memorization-5-77-3-release/)
- [sherpa_onnx Flutter package — pub.dev](https://pub.dev/packages/sherpa_onnx)
- [Tarteel Features Help Center](https://support.tarteel.ai/en/collections/15105266-tarteel-features)
- [Northflank — Best open source STT model 2026 benchmarks](https://northflank.com/blog/best-open-source-speech-to-text-stt-model-in-2026-benchmarks)
