# Sakīna Research — Round 17
**Date:** 2026-06-22 18:06 UTC  
**Focus:** Newest Arabic ASR models on HuggingFace (2025–2026) — specifically IQRA 2026 weight releases + undiscovered streaming models

---

## Question Being Answered

After 16 rounds, the Sakīna pipeline model selections were converging around:
- **On-device streaming ASR**: `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27` (Feb 2026, 5.63% WER)
- **Forced alignment for tajweed**: `ctc-forced-aligner` (TajweedAI NeurIPS 2025 template)
- **Phoneme assessment**: `whu-iasp` wav2vec2-xls-r-300m + TCN + CTC (IQRA 2026 winner, F1=0.7201)

**Open questions being resolved today:**
1. Has whu-iasp released model weights on HuggingFace?
2. Are there new streaming Arabic ASR models from 2026 not yet found?
3. Any new forced alignment tooling better than ctc-forced-aligner for Arabic?

---

## Key Findings

### Finding 1 — NVIDIA Nemotron 3.5 ASR Streaming 0.6B (June 4, 2026): Newest Streaming Arabic ASR, Already in sherpa-onnx via PR #3671

**Model:** `nvidia/nemotron-3.5-asr-streaming-0.6b`  
**ONNX INT4 pre-converted:** `onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4`  
**CoreML for iOS:** `FluidInference/Nemotron-3.5-ASR-Streaming-Multilingual-0.6b-CoreML`  
**Release date:** June 4, 2026  
**License:** OpenMDW-1.1 (commercial use permitted)  
**sherpa-onnx support:** PR #3671 merged ~May 2026 — issue #3664 is **closed**

**Architecture:** Cache-aware streaming transducer, 600M params, single checkpoint for 40 language-locales. Language conditioning via `prompt_index` tensor (int64, shape [batch]); `prompt_index=101` = auto-detect; Arabic has its own code. Configurable chunk sizes: **80ms, 160ms, 320ms, 560ms, 1120ms**.

**What makes it different from Moonshine Arabic (Feb 2026):**
- Released 3.5 months later (June 4 vs Feb 27)
- Single model for 40 languages (Moonshine Arabic is language-specific)
- 80ms chunk size vs Moonshine's streaming window
- Cache-aware: infinite-duration streams without memory blowup
- Pre-converted to ONNX INT4 by onnx-community (no conversion work needed)
- Pre-converted to CoreML by FluidInference (iOS path already done)

**Critical gap:** No published WER on Quranic/Classical Arabic. General FLEURS English WER = 7.91%. Arabic WER on FLEURS not published in any source found.

**Sakīna action (highest priority):** Test `onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4` via sherpa-onnx on 20 Quranic clips from `Buraaq/quran-audio-text-dataset`. If Arabic WER < 8%, this replaces Moonshine Arabic as primary on-device streaming model: it's newer, has 80ms chunks (lower latency than Moonshine), is already in sherpa-onnx, and the ONNX INT4 version is pre-built. Also evaluate `FluidInference/Nemotron-3.5-ASR-Streaming-Multilingual-0.6b-CoreML` directly on iOS simulator.

---

### Finding 2 — Qwen3-ForcedAligner-0.6B (January 29, 2026): Arabic Word/Character Timestamps — Replaces ctc-forced-aligner

**Model:** `Qwen/Qwen3-ForcedAligner-0.6B`  
**HuggingFace:** https://huggingface.co/Qwen/Qwen3-ForcedAligner-0.6B  
**Quantized variant (community):** `OpenVoiceOS/qwen3-forced-aligner-0.6b-q4-k-m`  
**Release date:** January 29, 2026 (same day as Qwen3-ASR)  
**License:** Apache-2.0  
**Languages:** Arabic + 10 others (Chinese, English, Cantonese, German, French, Spanish, Portuguese, Indonesian, Italian, Korean, Japanese, Russian, Thai, Vietnamese, Turkish, Hindi, Malay, Dutch — sources vary on count but Arabic confirmed)

**What it does:** Takes an audio file + transcript text → returns **word-level or character-level timestamps** showing exactly when each word was spoken. Evaluated on forced-alignment benchmarks, outperforms E2E-based forced-alignment models (including `ctc-forced-aligner` which was used in TajweedAI).

**Why this matters for Sakīna:** The TajweedAI pipeline template (NeurIPS 2025, found Round 11) uses:
```
FastConformer ASR → ctc-forced-aligner → binary Qalqalah classifier
```
`Qwen3-ForcedAligner-0.6B` upgrades step 2: gives more accurate per-word timestamps than ctc-forced-aligner, directly in Arabic. Since the tajweed classifiers need to know *exactly when millisecond-level* a phoneme was uttered, better timestamps = better tajweed rule detection accuracy. Apache-2.0 → no license friction.

**Sakīna action:** Swap `ctc-forced-aligner` for `Qwen/Qwen3-ForcedAligner-0.6B` in the tajweed pipeline. Pair with `Hetchy/quranic-phonemizer` (MIT): ASR output → forced aligner → per-word timestamps → phonemizer expected phonemes → diff → tajweed rule lookup. The quantized variant (`q4-k-m`) reduces VRAM for server-side deployment. Also test the Q4 quantized version for latency on a VPS CPU.

---

### Finding 3 — UTokyo CROTTC at IQRA 2026 (F1=0.7170): 1D Optimal Transport for CTC — Second Best System, No Code Yet

**System:** UTokyo's CROTTC (Cross-frame Optimal Transport CTC) / OTTC algorithm  
**IQRA 2026 rank:** 2nd place, F1=0.7170 (vs whu-iasp 0.7201 — gap < 0.004)

**Algorithm:** Formulates CTC frame-to-label alignment as a **1D optimal transport problem**, producing dense frame-level alignments that capture fine-grained phonetic variations without relying on canonical text prompts. This directly addresses standard CTC's known limitation of "spiked" probability masses that can misattribute phoneme boundaries.

**Why this matters:** whu-iasp uses wav2vec2-xls-r-300m + TCN + CTC (already established as Sakīna's template). UTokyo's OTTC algorithm could be applied *on top of* the same wav2vec2 encoder to further improve phoneme boundary precision — especially relevant for detecting short events like Qalqalah echoes and Ghunnah nasalization timing. The gap between 1st and 2nd place (0.0031 F1) is smaller than the gap between any individual system improvement, suggesting these two techniques are complementary.

**Status:** No code or HuggingFace model release found. arXiv paper not yet posted independently (described in IQRA 2026 paper arXiv:2603.29087v2 only). Watch for UTokyo system paper at Interspeech 2026 (Sydney, Sep 27–Oct 1, 2026).

**Sakīna action:** Monitor arXiv for UTokyo system paper post-Interspeech. If OTTC code releases, apply it to the wav2vec2-xls-r-300m encoder to potentially push phoneme boundary accuracy beyond whu-iasp's F1.

---

### Finding 4 — whu-iasp Model Weights Status: NOT Released on HuggingFace (Only Encoder is Public)

**Confirmed:** The whu-iasp IQRA 2026 champion (F1=0.7201) has **not released fine-tuned weights on HuggingFace**. The only publicly available component is the frozen encoder they used: `facebook/wav2vec2-xls-r-300m`.

**What is public (from arXiv:2603.29087v2):**
- Architecture: frozen `facebook/wav2vec2-xls-r-300m` + learnable multi-layer weighted fusion + TCN + CTC head
- Training: two-stage curriculum on `Iqra_train` + `Iqra_TTS` → then `Iqra_Extra_IS26`
- The `Iqra_Extra_IS26` corpus (real human mispronounced MSA speech) is the training data key

**IqraEval HuggingFace org** (`huggingface.co/IqraEval`) holds benchmark data and the leaderboard Space (`IqraEval/Leaderboard`) but no system weights.

**Sakīna action:** Sakīna must **train its own** wav2vec2-xls-r-300m + TCN + CTC model using:
- `Iqra-Eval/interspeech_IqraEval` (code pipeline, arXiv:2506.07722)
- `Iqra_train` (79h) + `Iqra_TTS` (52h) + `Iqra_Extra_IS26` (request from organizers)
- XTTS-v2 augmentation from AraS2P paper for additional synthetic data

Target timeline: 2-3 weeks GPU training. This is unavoidable — the winning system weights are not open.

---

### Finding 5 — Tadabur Formal Paper (arXiv:2604.18932, April 2026): Whisper-Quran 8.7% WER on Diverse Test Set

**Paper:** "Tadabur: A Large-Scale Quran Audio Dataset" (arXiv:2604.18932, April 2026)  
**Key benchmark finding:** `tarteel-ai/whisper-base-ar-quran` (74M params, the "Whisper-Quran" model) achieves **8.7% WER and 6.5% CER** on Tadabur's diverse test set (600+ reciters), outperforming all larger general-purpose models.

**Why 8.7% differs from KheemP's 5.98%:** These are different test sets. KheemP's 5.98% is on a simpler/less-diverse evaluation. Tadabur's test set is harder (600+ reciters, diverse styles, murattal + mujawwad). The 8.7% WER on a 600-reciter diverse set is actually a **stronger result** — it means the model generalizes. Sakīna's users will span diverse recitation styles, so the Tadabur benchmark is more representative than KheemP's.

**Tadabur-Whisper-Small:** `FaisaI/tadabur-Whisper-Small` is domain-adapted for prolonged phonemes, tajwīd rules, and melodic articulation — specifically trained on 1,400+ hours. No WER number yet published for this fine-tuned model vs. the base Whisper-Quran.

**Sakīna action:** Evaluate `FaisaI/tadabur-Whisper-Small` vs. `KheemP/whisper-base-quran-lora` on the same 20-clip Tadabur-held-out test set. The Tadabur paper's 8.7% WER for Whisper-Quran base suggests a Tadabur-fine-tuned model could potentially beat 5.98% on real-world diverse reciters.

---

## IQRA 2026 Full System Taxonomy (New Summary)

From arXiv:2603.29087v2, 19 teams, three paradigms:

| Paradigm | Top System | F1 | Key Technique |
|---|---|---|---|
| Enhanced CTC | whu-iasp (1st) | 0.7201 | wav2vec2-XLS-R-300M + TCN + CTC, 2-stage curriculum |
| Enhanced CTC | UTokyo (2nd) | 0.7170 | CROTTC / OTTC: 1D Optimal Transport frame alignment |
| Enhanced CTC | RAM (3rd) | ~0.7165 | CTC-based, details not found |
| Generative LALM | Kalimat (6th) | 0.6702 | Qwen3-ASR 1.7B, 68 phonemes as discrete special tokens |
| Organizer baseline | mHuBERT + Bi-LSTM | 0.4414 | — |

All top 3 are CTC-based, confirming wav2vec2 + CTC is the right architecture. Generative LLMs (Kalimat/Qwen3-ASR) are 0.05 F1 behind the CTC winner despite having 3× more parameters.

---

## Updated Sakīna Model Stack (Post Round 17)

| Layer | Model | Status |
|---|---|---|
| On-device streaming ASR | Moonshine Arabic (Feb 2026) **or Nemotron 3.5 (June 4, 2026 — test first)** | Needs Quran WER benchmark |
| Cloud ASR fallback | Groq Whisper Large v3 Turbo ($0.04/hr) | Confirmed |
| Verse matching | `tarteel-ai/whisper-base-ar-quran` + RapidFuzz | Confirmed |
| Phoneme forced alignment | **`Qwen/Qwen3-ForcedAligner-0.6B`** (replaces ctc-forced-aligner) | NEW — test immediately |
| Phoneme assessment backbone | wav2vec2-xls-r-300m + TCN + CTC (train own, whu-iasp template) | Must train — weights not public |
| Expected phoneme labels | `Hetchy/quranic-phonemizer` (MIT, 69 phonemes) | Confirmed |
| Qira'at classifier | DeiT V3 (99.6% accuracy, 10 styles) | Contact authors for weights |
| Flutter mic layer | `record` v7.1.0 (BSD-3-Clause, June 2026) | Confirmed |

---

## Immediate Next Actions (Priority Order)

1. **TODAY:** Test Nemotron 3.5 ONNX INT4 on Quranic audio: `pip install sherpa-onnx` → use `onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4` → run 20 clips from `Buraaq/quran-audio-text-dataset` → measure WER vs KheemP baseline. If < 8%, upgrade streaming ASR selection.

2. **TODAY:** Test `Qwen/Qwen3-ForcedAligner-0.6B` on Quranic audio: Give it a 30-second recitation clip + Tanzil text → measure word-timestamp accuracy (±20ms target for tajweed) vs `ctc-forced-aligner`. If accuracy holds, adopt as forced aligner.

3. **THIS WEEK:** Request `Iqra_Extra_IS26` corpus from IqraEval organizers via `IqraEval/interspeech_IqraEval` → begin 2-week GPU training run for own wav2vec2-xls-r-300m + TCN + CTC model.

4. **WATCH:** UTokyo CROTTC system paper at Interspeech 2026 (Sydney, Sep 27–Oct 1). OTTC code release could further improve phoneme boundary accuracy.

---

## Sources
- IQRA 2026 paper: https://arxiv.org/abs/2603.29087
- Nemotron 3.5 ASR HuggingFace: https://huggingface.co/nvidia/nemotron-3.5-asr-streaming-0.6b
- Nemotron 3.5 ONNX INT4: https://huggingface.co/onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4
- Nemotron CoreML: https://huggingface.co/FluidInference/Nemotron-3.5-ASR-Streaming-Multilingual-0.6b-CoreML
- sherpa-onnx Nemotron issue (closed): https://github.com/k2-fsa/sherpa-onnx/issues/3664
- Qwen3-ForcedAligner-0.6B: https://huggingface.co/Qwen/Qwen3-ForcedAligner-0.6B
- Qwen3-ForcedAligner Q4: https://huggingface.co/OpenVoiceOS/qwen3-forced-aligner-0.6b-q4-k-m
- Qwen3-ASR GitHub: https://github.com/QwenLM/Qwen3-ASR
- Tadabur paper: https://arxiv.org/abs/2604.18932
- Tadabur dataset: https://huggingface.co/datasets/FaisaI/tadabur
- MarkTechPost Nemotron: https://www.marktechpost.com/2026/06/06/nvidia-releases-nemotron-3-5-asr-a-600m-parameter-cache-aware-streaming-model-transcribing-40-language-locales-in-real-time/
