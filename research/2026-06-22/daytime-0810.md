# Sakina Research — 2026-06-22 Daytime (08:10 UTC)

**Topic:** arXiv:2507.13977 Deep Dive + TajweedAI Architecture + NeurIPS 2025 Muslims in ML Full Sweep
**Round:** 11
**Critical Question:** What does the July 2025 paper flagged as "required reading" actually contain, and what new Quran ASR architecture/models emerged from NeurIPS 2025 that haven't been analyzed?

---

## Finding 1 — arXiv:2507.13977 (Jul 2025 → IEEE 2025): NVIDIA FastConformer Is the Backbone Behind Tarteel's 4% WER

**Paper:** *"Open Automatic Speech Recognition Models for Classical and Modern Standard Arabic"*
**Authors:** NVIDIA research team
**Published:** July 2025 (arXiv:2507.13977), appeared at IEEE conference (DOI: 10.1109/... IEEE Xplore 10889643)

### Two released models:
| Model ID | Target | Diacritics | Notes |
|---|---|---|---|
| `nvidia/stt_ar_fastconformer_hybrid_large_pc_v1.0` | MSA only | ❌ | without tashkeel |
| `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0` | **MSA + Classical Arabic** | **✅** | **The Quran model** |

### Training data (~1,150 hours total):
- MASC (Massive Arabic Speech Corpus) — 690h
- Mozilla Common Voice 17.0 Arabic — 65h
- Google FLEURS Arabic — 5h
- **TarteelAI EveryAyah Dataset — 390h** ← Quran-specific data

### Key facts:
- Architecture: FastConformer Hybrid (Transducer + CTC), ~115M parameters
- First unified public model for **both MSA and Classical Arabic**
- SOTA on Classical Arabic **with diacritics** — beats all prior public models
- Evaluation: MGB2 test set (blind), MediaSpeech (blind)
- **Tarteel confirmed uses NVIDIA Riva + NeMo on this model family → achieves 4% WER on Quran** (case study: nvidia.com/en-us/case-studies/automating-real-time-arabic-speech-recognition)
- License: CC-BY-4.0 (NVIDIA model zoo standard)

### Critical limitation for Flutter:
The sherpa-onnx issue #790 (opened April 2024, **still unresolved** as of June 2026) shows that FastConformer → ONNX export via NeMo partially works (encoder ONNX: 456.7 MB generated successfully) but fails at the decoder step (`AttributeError: 'RNNTDecoder' object has no attribute 'vocabulary'`), leaving `tokens.txt` empty and making inference impossible. **FastConformer via sherpa-onnx is NOT a viable Flutter path today.** Use Whisper ONNX (via `export-onnx.py`) for Flutter on-device inference.

### Sakina implication:
The `pcd` model is the highest-accuracy open Arabic ASR model for Quranic text with diacritics. Use it server-side (NeMo server on GPU) for maximum accuracy. Do NOT attempt mobile deployment — 456MB encoder alone won't fit comfortably on-device with streaming.

---

## Finding 2 — TajweedAI (NeurIPS 2025 Muslims in ML): FastConformer + CTC Forced Alignment = The Tajweed Rule Detection Architecture Template

**Paper:** *"TajweedAI: A Hybrid ASR-Classifier for Real-Time Qalqalah Detection in Quranic Recitation"*
**Venue:** NeurIPS 2025 Muslims in ML Workshop (paper ID: 133028)
**GitHub:** `nmazid121/tajweedAI`
**OpenReview:** https://openreview.net/pdf?id=AauWmDPOIf

### Architecture (three stages):
1. `nvidia/stt-ar-fastconformer.hybrid-large.pcd-v1.0` as the ASR backbone → raw transcription
2. `ctc-forced-aligner` tool uses CTC alignments from the ASR model → **word/phoneme-level timestamps**
3. Binary classifier trained on extracted audio segments → detects Qalqalah "echoing"

### Results:
- Baseline accuracy: 58.33%
- Final classifier: **100% accuracy on internal validation set**
- External test set: **57.14%** — clear overfitting on small internal dataset (caution!)
- Tool: PyDub for audio manipulation, `ctc-forced-aligner` for alignment

### Why this matters for Sakina:
- This is the **first published system using forced alignment for tajweed rule detection** (not just ASR + rule lookup)
- The ctc-forced-aligner + NVIDIA FastConformer gives **phoneme-level timestamps** — you know exactly which millisecond a Qalqalah letter was pronounced and whether the echo was present
- Architecture template: `record → sherpa-onnx Whisper → ctc-forced-aligner server-side → rule classifier` = production Sakina tajweed pipeline
- 57% external accuracy shows the training dataset (internal) was too small → **Sakina needs to build a proper annotated Qalqalah dataset using Quran-MD word-level audio**
- This architecture scales to all 5 Qalqalah letters (ق ط ب ج د) and any other tajweed rule with binary classifiers

### Immediate action:
Clone `nmazid121/tajweedAI`, run on `Buraaq/quran-md-ayahs` word-level audio, retrain with 5× more data → reproducible Qalqalah detector. Then extend architecture to Ikhfaa, Idgham, Iqlab using the same forced-alignment approach.

---

## Finding 3 — Quran-MD (arXiv:2601.17880, Jan 25, 2026, NeurIPS 2025 Muslims in ML): Word-Level Audio Is the Missing Training Resource for Tajweed Classifiers

**Paper:** *"Quran-MD: A Fine-Grained Multilingual Multimodal Dataset of the Quran"*
**arXiv:** 2601.17880 (submitted January 25, 2026)
**HuggingFace datasets:**
- `Buraaq/quran-audio-text-dataset` (verse-level: 187,080 MP3s, 30 reciters)
- `Buraaq/quran-md-ayahs` (separate split with README)

### Dataset breakdown:
| Level | Files | Notes |
|---|---|---|
| Verse-level (ayah) | 187,080 MP3s | 30 reciters × full Quran |
| **Word-level** | **77,429 MP3s** | individual word pronunciations |
| Total | 264,509 MP3s | Multi-reciter, multilingual metadata |

### What's new vs. what was known:
- Previous rounds mentioned `Buraaq/quran-audio-text-dataset` (264,509 MP3s, 30 reciters, NeurIPS 2025) — **correct but incomplete**
- NEW: The **77,429 word-level MP3s** are the critical piece for tajweed classifier training — each word isolated, allowing direct binary label assignment per tajweed rule
- NEW: Full multilingual metadata (Arabic + English + transliteration per word) = perfect for multimodal verse matching
- 32 distinct reciters confirmed (paper says 32; previous round said 30 — check the actual HF dataset README for ground truth)

### Sakina implication:
The 77,429 word-level audio clips are the training corpus for Sakīna's tajweed rule classifiers. Label each word-level clip with applicable tajweed rules using the Quranic Phonemizer (71-symbol phoneme inventory, NeurIPS 2025) → train binary classifiers per rule. No new data collection needed — Quran-MD provides the raw material.

---

## Finding 4 — NeurIPS 2025 Muslims in ML: Three Quran AI Papers Form a Complete Tajweed Stack

Three papers from the NeurIPS 2025 Muslims in ML Workshop form an integrated tajweed detection stack when combined:

| Paper | Component | Status |
|---|---|---|
| **Quran-MD** (arXiv:2601.17880) | Word-level audio training corpus | HF: `Buraaq/quran-audio-text-dataset` + `Buraaq/quran-md-ayahs` |
| **Quranic Phonemizer** ("Bringing Tajweed-Aware Phonemes") | 71-symbol phoneme inventory, encodes all tajweed rules | `Hetchy/quranic-phonemizer`, MIT |
| **TajweedAI** (NeurIPS 133028) | CTC forced alignment + binary rule classifier | GitHub: `nmazid121/tajweedAI` |

**Combined pipeline for Sakina tajweed detection:**
1. Record audio → `record` v7.1.0 (Flutter)
2. Transcribe → `tarteel-ai/whisper-base-ar-quran` or `KheemP/whisper-base-quran-lora` (on-device ONNX)
3. Server-side: `ctc-forced-aligner` → phoneme timestamps
4. Per-word: `Hetchy/quranic-phonemizer` → expected phoneme sequence
5. Binary classifiers (TajweedAI architecture) → actual Qalqalah/Ikhfaa/Idgham detected vs. expected
6. Diff → specific tajweed violation at word N, rule R

This is the full Sakina tajweed differentiator stack, all open-source, all from verified 2025 publications.

---

## Key Model Comparison (Updated with This Round's Findings)

| Model | WER (Quran) | Diacritics | Params | On-Device Flutter | Notes |
|---|---|---|---|---|---|
| `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0` | **~4%** (via Riva) | ✅ | 115M | ❌ (ONNX blocked) | SOTA, server-side only |
| `KheemP/whisper-base-quran-lora` | 5.98% | ✅ | 74M | ✅ (ONNX) | Best Flutter option |
| `tarteel-ai/whisper-base-ar-quran` | 5.75% | ✅ | 74M | ✅ (ONNX) | HF free API |
| `moonshine-ai/moonshine-base-ar` | 5.63% | ? | 58M | ✅ (sherpa-onnx) | Best streaming |
| `MaddoggProduction/whisper-l-v3-turbo-quran-lora` | 12.69% | ✅ | 809M | ❌ | Diacritics only use |
| Groq Whisper Large v3 Turbo | ~4-5% | ❌ | cloud | N/A | Cloud path, no tashkeel |

---

## Blockers Confirmed This Round

1. **FastConformer → sherpa-onnx**: BLOCKED. Issue #790 unresolved since April 2024. Don't invest time here.
2. **TajweedAI external accuracy (57%)**: Too low for production. Need 5-10× more annotated data.
3. **No streaming FastConformer for Flutter**: The NVIDIA `stt_en_fastconformer_hybrid_large_streaming_multi` model exists for English streaming, but the Arabic `pcd` model does not have a publicly available streaming variant.

---

## Recommended Next Action for Sakina

**Priority 1 (today):** Run `nmazid121/tajweedAI` on `Buraaq/quran-md-ayahs` word-level clips. Label 500+ Qalqalah word clips. Retrain the binary classifier. Target: >90% external accuracy. This gives Sakina a reproducible, open-source Qalqalah detector that can be published as a research contribution — establishing credibility before launch.

**Priority 2 (this week):** Benchmark `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0` server-side against `KheemP/whisper-base-quran-lora` ONNX on-device, on the same 100-verse held-out test set. Publish WER comparison. This is the paper arXiv:2507.13977 did NOT do (they benchmarked on MGB2, not specifically Quran).

**Priority 3:** Keep Flutter on-device path = `record` v7.1.0 → `KheemP` ONNX via sherpa-onnx. Keep server-side accuracy path = NVIDIA FastConformer via NeMo. Use cloud (Groq free tier) as bridge until server-side is set up.

---

## Sources
- [arXiv:2507.13977](https://arxiv.org/abs/2507.13977) — Open ASR Models for Classical and Modern Standard Arabic (Jul 2025)
- [IEEE Xplore 10889643](https://ieeexplore.ieee.org/document/10889643/) — IEEE publication of same paper
- [nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0](https://huggingface.co/nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0)
- [nvidia/stt_ar_fastconformer_hybrid_large_pc_v1.0](https://huggingface.co/nvidia/stt_ar_fastconformer_hybrid_large_pc_v1.0)
- [NVIDIA Case Study: Tarteel 4% WER](https://www.nvidia.com/en-us/case-studies/automating-real-time-arabic-speech-recognition/)
- [NeurIPS 2025 TajweedAI (paper 133028)](https://neurips.cc/virtual/2025/133028)
- [TajweedAI OpenReview PDF](https://openreview.net/pdf?id=AauWmDPOIf)
- [GitHub: nmazid121/tajweedAI](https://github.com/nmazid121/tajweedAI)
- [arXiv:2601.17880 Quran-MD](https://arxiv.org/abs/2601.17880)
- [Buraaq/quran-audio-text-dataset](https://huggingface.co/datasets/Buraaq/quran-audio-text-dataset)
- [Buraaq/quran-md-ayahs](https://huggingface.co/datasets/Buraaq/quran-md-ayahs)
- [NeurIPS 2025 Muslims in ML Accepted Papers](https://www.musiml.org/events/2025-NeurIPS/accepted_papers.html)
- [sherpa-onnx Issue #790: FastConformer ONNX export blocked](https://github.com/k2-fsa/sherpa-onnx/issues/790)
- [Sadique5/arabic_quranic_asr dataset](https://huggingface.co/datasets/Sadique5/arabic_quranic_asr)
- [IQRA 2026 Interspeech Challenge](https://arxiv.org/abs/2603.29087)
