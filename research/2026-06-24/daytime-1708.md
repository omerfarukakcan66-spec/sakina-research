# Sakina Research — Round 38
**Date:** 2026-06-24 17:08 UTC
**Topic:** Newest Arabic ASR model on HuggingFace (2025–2026) — deep-dive on `CohereLabs/cohere-transcribe-03-2026`
**Status:** MAJOR NEW FINDING — 37-round blind spot closed

---

## Executive Summary

`CohereLabs/cohere-transcribe-03-2026` (March 26, 2026, Apache 2.0) is a 2B-parameter Conformer ASR model that ranks **#1 on the HuggingFace Open ASR Leaderboard** (5.42% average WER across 8 benchmarks) and was **completely absent from all 37 prior research rounds**. On Quranic Arabic specifically, it achieves **11.2% WER on the Tadabur benchmark** — the best performance of any *general-purpose* model not trained on Quran data, though still behind domain-fine-tuned Whisper variants (tadabur-Whisper-Small: 8.7%, KheemP: 5.98%). The key Sakina angle: as an Apache 2.0 Conformer with ONNX exports already available and potential sherpa-onnx integration in progress, it is the **strongest fine-tuning target** for producing an on-device Quranic Conformer ASR — a Conformer trained on `obadx/muaalem-annotated-compressed-v3` (848h, MIT) could outperform all existing Whisper fine-tunes.

---

## Finding 1 — `CohereLabs/cohere-transcribe-03-2026` (March 26, 2026): Apache 2.0 Conformer, #1 Open ASR Leaderboard, 11.2% WER on Quran OOTB — 37-Round Blind Spot, Now the Best Fine-Tuning Backbone

**Model:** `huggingface.co/CohereLabs/cohere-transcribe-03-2026`  
**Released:** March 26, 2026 by Cohere Labs  
**License:** Apache 2.0 — fully commercial, fine-tunable, redistributable (NO restrictions)  
**Architecture:** 2B parameter Conformer encoder + lightweight Transformer decoder, **trained from scratch** (NOT a Whisper variant or fine-tune)  
**Parameters:** 2B (comparable to Whisper Large v3 in parameter count)

### Benchmark performance:
| Benchmark | WER |
|---|---|
| HF Open ASR Leaderboard (8 EN benchmarks, average) | **5.42%** — #1 (beats Whisper Large v3 at 7.44%, ElevenLabs Scribe v2 at 5.83%, Qwen3-ASR at 5.76%) |
| Tadabur Quranic benchmark (OOTB, no Quran fine-tune) | **11.2%** — best general-purpose model on this benchmark |
| Tadabur Quranic benchmark: tadabur-Whisper-Small (domain-fine-tuned) | 8.7% WER (for comparison) |
| Tadabur Quranic benchmark: KheemP (domain-fine-tuned) | 5.98% WER (for comparison) |

**Tadabur paper quote (arXiv:2604.18932):** *"Cohere Transcribe achieves 11.2% WER and performs most competitively among general-purpose models — notably, Cohere Transcribe was not trained on any Qur'anic data, yet achieves strong results, reflecting the strength of its large-scale multilingual pre-training."* Key conclusion from same paper: **"domain adaptation outweighs model size in Qur'anic ASR."**

### Speed:
- **524x real-time factor (RTFx)** — roughly **3x faster** than Whisper Large v3 at similar accuracy
- Can be self-hosted on **consumer-grade GPU**; INT8 ONNX runs on CPU

### Available deployment formats (all from March–April 2026):
- **Original weights:** `huggingface.co/CohereLabs/cohere-transcribe-03-2026`
- **ONNX (FP32):** `huggingface.co/onnx-community/cohere-transcribe-03-2026-ONNX`
- **ONNX INT8 quantized (CPU-deployable):** `huggingface.co/vigneshlabs/cohere-transcribe-03-2026-int8-onnx`
- **GGUF:** `huggingface.co/cstr/cohere-transcribe-03-2026-GGUF`
- **GitHub mirror:** `github.com/jasonclaw001/cohere-transcribe-03-2026`

### sherpa-onnx integration status:
- **Issue #3442** opened March 30, 2026: requesting Cohere Transcribe ONNX support in sherpa-onnx (k2-fsa repo)
- **PR #3458** merged: JavaScript API for Cohere Transcribe added
- **Flutter status:** NOT confirmed in sherpa-onnx v1.13.3 (the current pub.dev package as of June 16, 2026). The JS API addition suggests C++ core support was added — **monitor for v1.14.x Flutter package** which may include native Cohere Transcribe support.

### Free API access:
- Available via Cohere platform API at **$0.00 cost** (as of March 2026)
- API endpoint documented at `docs.cohere.com/v2/docs/transcribe`
- Also available in **Microsoft Azure AI Foundry** (announced May/June 2026)

### Why this is a blind spot that matters more than Whisper variants:

| Criterion | Whisper-based Quran models | Cohere Transcribe |
|---|---|---|
| Architecture | Encoder-decoder Transformer (non-causal) | Conformer encoder (local self-attention) + Transformer decoder |
| Streaming suitability | Poor (Whisper is non-streaming by design; chunking adds latency) | Better (Conformer local attention = natural streaming) |
| On-device ONNX | Possible (whisper.cpp, whisper_ggml_plus) | Already available: int8 ONNX, future sherpa-onnx |
| Fine-tune friendliness | Whisper fine-tunes exist (tadabur, KheemP) | Apache 2.0 Conformer backbone — NOT YET FINE-TUNED on Quran data |
| License | Whisper (MIT for base models); fine-tunes vary | Apache 2.0 (original weights) |

**The gap nobody has filled yet:** A Conformer-architecture model fine-tuned on Quranic data. All Quran fine-tunes so far use Whisper (Transformer encoder-decoder). The 11.2% OOTB gap vs 5.98% KheemP closes significantly with domain fine-tuning — and Conformer may outperform Whisper post-fine-tune due to better local phonemic context modeling.

---

## Finding 2 — Monitors: Tarteel Still v5.78.2 (Day 5), `uzair0/quran-asr` Still Training

### Tarteel:
- **Still v5.78.2** (June 19, 2026) — no v5.79+ found across any source
- Google Play, Uptodown, Softonic: all confirm v5.78.2 as latest Android
- 5-day stall continues (hotfix sprint ended June 19)
- No Makharij/Mahraj announcement found
- No iOS update found (iOS remains behind Android)
- **Status:** Monitor trigger NOT met. Q3 2026 Sakina ship window still open.

### `uzair0/quran-asr` (Apache 2.0, 6.31GB):
- HuggingFace page still shows recent checkpoint saves
- No WER published, no training completion signal
- **Status:** Monitor trigger NOT met. Check daily for commit frequency drop (daily → weekly = done).

---

## Finding 3 — `Sadique5/arabic_quranic_asr` (HuggingFace): New Quran ASR Dataset With Every Ayah + 10K Unique Words — Not in Prior Rounds

`huggingface.co/datasets/Sadique5/arabic_quranic_asr` — a Quranic ASR dataset containing recitations of every ayah (verse) plus 10,000 unique Quran words, explicitly designed "to train ASR models for teaching beginners to recite Quran." Upload date, license, and audio hours unknown (HuggingFace access blocked); check model card directly. Potential use: supplementary beginner-vocabulary training data for Sakina's ASR layer (10K unique words would give model coverage of the full Quranic lexicon at word level).

---

## Strategic Assessment

### Immediate action (P1 addition):

**Fine-tune `cohere-transcribe-03-2026` on Quranic data (Apache 2.0 ← MIT data = fully open stack):**
```
1. Download `obadx/muaalem-annotated-compressed-v3` (MIT, 848h, 286K utterances — available since June 23, 2026)
2. Fine-tune cohere-transcribe-03-2026 (Apache 2.0 Conformer) on these utterances
3. Export to ONNX INT8 (path already established by `vigneshlabs/cohere-transcribe-03-2026-int8-onnx`)
4. Integrate into sherpa-onnx (monitor Issue #3442 / v1.14.x)
5. Expected result: 11.2% → likely 5-8% WER (domain adaptation; Tadabur confirms this gap is closeable)
```
If the resulting Quranic Conformer achieves <5.98% WER:
- First open-source Conformer fine-tuned on Quran
- Better streaming latency than Whisper-based alternatives
- Apache 2.0 (no NC clause like tadabur-Whisper-Small)
- ONNX-native → sherpa-onnx → Flutter on-device deployment

### Use now (free, no cost):
- Add Cohere Transcribe API as Arabic ASR option alongside Groq Whisper: `POST /v1/transcribe`, model: `cohere-transcribe-03-2026`
- At $0 cost vs Groq Whisper's $0.04/hr, use Cohere as the **free fallback** for non-time-critical Arabic recitation transcription

### Monitor (next 2–4 weeks):
- sherpa-onnx v1.14.x: watch for Cohere Transcribe ONNX support in the Flutter package
- `uzair0/quran-asr`: training completion → benchmark immediately
- Tarteel: v5.79+ → check for Makharij/Mahraj announcement

---

## Sources
- `huggingface.co/CohereLabs/cohere-transcribe-03-2026`
- `huggingface.co/blog/CohereLabs/cohere-transcribe-03-2026-release`
- `arxiv.org/abs/2604.18932` (Tadabur paper, WER comparison including Cohere Transcribe)
- `huggingface.co/onnx-community/cohere-transcribe-03-2026-ONNX`
- `huggingface.co/vigneshlabs/cohere-transcribe-03-2026-int8-onnx`
- `huggingface.co/cstr/cohere-transcribe-03-2026-GGUF`
- `github.com/k2-fsa/sherpa-onnx/issues/3442` (sherpa-onnx Cohere Transcribe support request)
- `pub.dev/packages/sherpa_onnx/changelog` (v1.13.3 current)
- `artificialanalysis.ai/speech-to-text/models/cohere-transcribe`
- `reeboot.fr/en/blog/cohere-transcribe-03-2026/`
- `theplanettools.ai/blog/cohere-transcribe-open-source-asr-beats-whisper`
- `huggingface.co/datasets/Sadique5/arabic_quranic_asr`
- `tarteel.en.uptodown.com/android/versions` (Tarteel version tracking)
